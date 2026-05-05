# message-queues — Reference

## Docker Compose — All Brokers

```yaml
# Kafka (with Redpanda as drop-in alternative)
services:
  kafka:
    image: confluentinc/cp-kafka:latest
    environment:
      KAFKA_NODE_ID: 1
      KAFKA_PROCESS_ROLES: broker,controller
      KAFKA_LISTENERS: PLAINTEXT://0.0.0.0:9092,CONTROLLER://0.0.0.0:9093
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_CONTROLLER_QUORUM_VOTERS: 1@kafka:9093
      CLUSTER_ID: MkU3OEVBNTcwNTJENDM2Qg
    ports: ['9092:9092']

  # Redpanda alternative (Kafka-compatible, no ZooKeeper):
  # image: redpandadata/redpanda:latest

  rabbitmq:
    image: rabbitmq:3-management
    ports: ['5672:5672', '15672:15672']
    environment:
      RABBITMQ_DEFAULT_USER: guest
      RABBITMQ_DEFAULT_PASS: guest  # change in production

  nats:
    image: nats:latest
    command: ['-js']  # enable JetStream
    ports: ['4222:4222', '8222:8222']
```

---

## BUILD — Kafka (Confluent CJSK)

```bash
npm install @confluentinc/kafka-javascript
```

```ts
// producer.ts
import { Kafka } from '@confluentinc/kafka-javascript'

const kafka = new Kafka({ brokers: ['localhost:9092'] })
const producer = kafka.producer({ idempotent: true }) // enable idempotence

await producer.connect()
await producer.send({
  topic: 'orders',
  messages: [{ key: orderId, value: JSON.stringify(payload) }],
})
await producer.disconnect()
```

```ts
// consumer.ts
const consumer = kafka.consumer({ groupId: 'order-processor' })
await consumer.connect()
await consumer.subscribe({ topic: 'orders', fromBeginning: false })

await consumer.run({
  eachMessage: async ({ message }) => {
    const payload = JSON.parse(message.value!.toString())
    // Always idempotent: check dedup store before processing
    if (await isDuplicate(message.offset)) return
    await processOrder(payload)
    await markProcessed(message.offset)
  },
})
```

---

## BUILD — RabbitMQ (amqplib + amqp-connection-manager)

```bash
npm install amqplib amqp-connection-manager
```

```ts
import { connect } from 'amqp-connection-manager'

const connection = connect(['amqp://guest:guest@localhost'])
const channel = connection.createChannel({
  setup: (ch: Channel) => Promise.all([
    ch.assertQueue('orders', { durable: true, deadLetterExchange: 'orders.dlx' }),
    ch.assertExchange('orders.dlx', 'fanout', { durable: true }),
    ch.assertQueue('orders.dlq', { durable: true }),
    ch.bindQueue('orders.dlq', 'orders.dlx', ''),
    ch.prefetch(1), // competing consumers — one message at a time
  ]),
})

// Producer
await channel.sendToQueue('orders', Buffer.from(JSON.stringify(payload)), {
  persistent: true,
  messageId: crypto.randomUUID(), // idempotency key
})

// Consumer
await channel.consume('orders', async (msg) => {
  if (!msg) return
  try {
    await processOrder(JSON.parse(msg.content.toString()))
    channel.ack(msg)
  } catch {
    const retries = (msg.properties.headers['x-retries'] ?? 0) as number
    if (retries >= 3) {
      channel.nack(msg, false, false) // send to DLQ
    } else {
      setTimeout(() => channel.nack(msg, false, true), 2 ** retries * 1000) // backoff
    }
  }
})
```

---

## BUILD — NATS JetStream

```bash
npm install nats
```

```ts
import { connect, StringCodec } from 'nats'

const nc = await connect({ servers: 'nats://localhost:4222' })
const js = nc.jetstream()
const jsm = await nc.jetstreamManager()
const sc = StringCodec()

// Create stream
await jsm.streams.add({ name: 'ORDERS', subjects: ['orders.>'] })

// Producer
await js.publish('orders.created', sc.encode(JSON.stringify(payload)))

// Consumer (pull — recommended for horizontal scaling)
const consumer = await js.consumers.get('ORDERS', 'order-processor')
const messages = await consumer.consume()

for await (const msg of messages) {
  try {
    await processOrder(JSON.parse(sc.decode(msg.data)))
    msg.ack()
  } catch {
    msg.nak(2000) // requeue after 2s backoff
  }
}
```

---

## PATTERNS — Kafka Exactly-Once (EOS)

Requires: `idempotent: true` + `transactionalId` (must be stable per partition).

```ts
const producer = kafka.producer({
  idempotent: true,
  maxInFlightRequests: 1,
  transactionalId: `order-processor-${topicName}-${partitionId}`, // stable ID
})

await producer.connect()
const transaction = await producer.transaction()
try {
  await transaction.send({ topic: 'processed-orders', messages: [{ value }] })
  await transaction.sendOffsets({
    consumerGroupId: 'order-processor',
    topics: [{ topic: 'orders', partitions: [{ partition, offset: String(+offset + 1) }] }],
  })
  await transaction.commit()
} catch {
  await transaction.abort()
}
```

**Gotcha:** `transactionalId` must be unique per input partition. Encode `${topic}-${partition}` in the ID. Reusing the same ID across partitions causes transaction conflicts.

---

## PATTERNS — Retry with Exponential Backoff

```ts
async function withRetry<T>(
  fn: () => Promise<T>,
  { maxAttempts = 3, baseDelayMs = 1000 } = {}
): Promise<T> {
  for (let attempt = 1; attempt <= maxAttempts; attempt++) {
    try {
      return await fn()
    } catch (err) {
      if (attempt === maxAttempts) throw err
      const delay = baseDelayMs * 2 ** (attempt - 1) + Math.random() * 100 // jitter
      await new Promise(r => setTimeout(r, delay))
    }
  }
  throw new Error('unreachable')
}
```

---

## PATTERNS — Idempotency Key Store (Redis)

```ts
import { createClient } from 'redis'
const redis = createClient()

async function isDuplicate(key: string, ttlSeconds = 86400): Promise<boolean> {
  const result = await redis.set(key, '1', { NX: true, EX: ttlSeconds })
  return result === null // null = already existed = duplicate
}
// Usage: if (await isDuplicate(`order:${messageId}`)) return
```

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| Broker auth | SASL/TLS (Kafka), user/pass over TLS (RabbitMQ), NKey/JWT (NATS) in production |
| Consumer idempotent | Dedup check before processing; Redis or DB key store |
| DLQ configured | Poison messages routed to DLQ after max retries |
| Max retry limit | Retry counter bounded; no infinite loops |
| Message size limits | `message.max.bytes` (Kafka), `maxMessageSize` (RabbitMQ) configured |
| No secrets in payloads | Payloads contain IDs only; secrets/PII encrypted or excluded |
| KafkaJS absent | `package.json` has no `kafkajs` dependency |
| EOS transactionalId stable | Per-partition ID if using Kafka transactions |
