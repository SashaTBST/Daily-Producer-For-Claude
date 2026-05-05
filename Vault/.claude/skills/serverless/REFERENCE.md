# serverless — Reference

## Platform Comparison

| | Lambda (SAM) | Cloud Run | Azure Functions v4 |
|---|---|---|---|
| Cold start (Node.js) | ~375ms arm64 optimised | ~1–2s (container pull) | ~500ms–1.5s |
| Max timeout | 15 min | 60 min | 10 min (Consumption) |
| Concurrent requests | 1 per instance | Up to 1000 per instance | 1 per instance (default) |
| Stateful workflows | Step Functions / Lambda Durable | Cloud Workflows (separate) | Azure Durable Functions |
| Default tooling | AWS SAM | gcloud CLI + service.yaml | Azure Functions Core Tools v4 |
| Best for | AWS-native, event-driven | GCP, containers, long-running | Azure ecosystem |

---

## BUILD — Lambda (SAM + TypeScript)

```typescript
// src/handler.ts — HTTP API trigger
import { APIGatewayProxyEventV2, APIGatewayProxyResultV2 } from 'aws-lambda'

export const handler = async (
  event: APIGatewayProxyEventV2
): Promise<APIGatewayProxyResultV2> => {
  const body = event.body ? JSON.parse(event.body) : {}

  // Validate at boundary — never trust input
  if (!body.userId || typeof body.userId !== 'string') {
    return { statusCode: 400, body: JSON.stringify({ error: 'userId required' }) }
  }

  return {
    statusCode: 200,
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ ok: true }),
  }
}
```

```typescript
// src/sqsHandler.ts — SQS trigger
import { SQSHandler, SQSRecord } from 'aws-lambda'

export const handler: SQSHandler = async (event) => {
  for (const record of event.Records) {
    const message = JSON.parse(record.body)
    // process — throw to trigger retry + DLQ
    await processMessage(message)
  }
}

async function processMessage(msg: unknown) {
  // idempotency: check processed set before acting
}
```

```yaml
# template.yaml (AWS SAM)
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31

Globals:
  Function:
    Runtime: nodejs22.x
    Architectures: [arm64]        # Graviton3 — always
    MemorySize: 1024              # 2.3GHz CPU; cold start -40% vs 512MB
    Timeout: 30
    Environment:
      Variables:
        NODE_ENV: production

Resources:
  ApiHandler:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: dist/
      Handler: handler.handler
      Events:
        Api:
          Type: HttpApi
          Properties:
            Path: /
            Method: post
      # Secrets via SSM — never hardcode
      # aws ssm put-parameter --name /myapp/DB_URL --value "..." --type SecureString

  SqsHandler:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: dist/
      Handler: sqsHandler.handler
      Events:
        Queue:
          Type: SQS
          Properties:
            Queue: !GetAtt MyQueue.Arn
            BatchSize: 10
            FunctionResponseTypes: [ReportBatchItemFailures]

  MyQueue:
    Type: AWS::SQS::Queue
    Properties:
      RedrivePolicy:
        deadLetterTargetArn: !GetAtt DLQ.Arn
        maxReceiveCount: 3

  DLQ:
    Type: AWS::SQS::Queue
```

```bash
# Deploy
sam build && sam deploy --guided
# Subsequent: sam deploy (uses samconfig.toml)
```

---

## BUILD — Cloud Run (Node.js)

```typescript
// src/index.ts
import express from 'express'

const app = express()
app.use(express.json())

app.post('/process', (req, res) => {
  const { userId } = req.body
  if (!userId || typeof userId !== 'string') {
    return res.status(400).json({ error: 'userId required' })
  }
  res.json({ ok: true })
})

const port = parseInt(process.env.PORT ?? '8080')
app.listen(port, () => console.log(`listening on ${port}`))
```

```yaml
# service.yaml
apiVersion: serving.knative.dev/v1
kind: Service
metadata:
  name: my-service
spec:
  template:
    metadata:
      annotations:
        autoscaling.knative.dev/minScale: "0"   # scale to zero
        autoscaling.knative.dev/maxScale: "10"
    spec:
      containerConcurrency: 80
      timeoutSeconds: 300
      containers:
        - image: gcr.io/PROJECT_ID/my-service
          env:
            - name: DB_URL
              valueFrom:
                secretKeyRef:
                  name: db-url          # Secret Manager
                  key: latest
          resources:
            limits:
              cpu: "1"
              memory: 512Mi
```

```bash
gcloud run deploy my-service --source . --region us-central1 --allow-unauthenticated
# Or deploy from service.yaml:
gcloud run services replace service.yaml
```

---

## BUILD — Azure Functions v4 (TypeScript)

```typescript
// src/functions/httpTrigger.ts
import { app, HttpRequest, HttpResponseInit, InvocationContext } from '@azure/functions'

app.http('httpTrigger', {
  methods: ['POST'],
  authLevel: 'function',
  handler: async (req: HttpRequest, context: InvocationContext): Promise<HttpResponseInit> => {
    const body = await req.json() as { userId?: string }

    if (!body.userId || typeof body.userId !== 'string') {
      return { status: 400, jsonBody: { error: 'userId required' } }
    }

    context.log('Processing userId:', body.userId)
    return { jsonBody: { ok: true } }
  },
})
```

```json
// host.json
{
  "version": "2.0",
  "logging": { "applicationInsights": { "samplingSettings": { "isEnabled": true } } },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
```

```json
// package.json (key fields)
{
  "main": "dist/src/functions/*.js",
  "dependencies": {
    "@azure/functions": "^4.0.0"   // dependencies, NOT devDependencies
  }
}
```

```bash
func azure functionapp publish <APP_NAME>
```

---

## Durable / Stateful Patterns

**Step Functions — when to use Standard vs Express:**

| | Standard | Express |
|---|---|---|
| Duration | Up to 1 year | Up to 5 minutes |
| Semantics | Exactly-once | At-least-once |
| Cost | Per state transition | Per duration + invocations |
| Best for | Order processing, approval flows | High-volume event processing |

**Lambda Durable Functions (2026 — verify GA before using):**
```typescript
// Native stateful workflow inside Lambda — automatic checkpointing
import { workflow, activity } from 'aws-lambda-durable' // ⚠ verify package name at GA

export const myWorkflow = workflow(async (ctx, input: { orderId: string }) => {
  const validated = await ctx.callActivity(validateOrder, input.orderId)
  await ctx.sleep('24h')   // long wait — checkpointed, no cost while sleeping
  await ctx.callActivity(fulfillOrder, validated)
})

const validateOrder = activity(async (orderId: string) => {
  // runs as normal Lambda invocation
  return { orderId, valid: true }
})
```

Use Step Functions for complex orchestration with visual debugging. Use Lambda Durable Functions for simple linear workflows where you want to stay in one Lambda codebase.

---

## Cold Start — Azure Functions

- **Consumption Plan**: cold starts 500ms–2s; use Premium Plan for latency-sensitive workloads
- **Premium Plan**: pre-warmed instances; no cold starts; fixed minimum cost
- **Application Insights**: required for production monitoring; add `applicationinsights` npm package

---

## REVIEW — Security Checklist

| Check | Pass condition |
|---|---|
| No hardcoded secrets | All secrets via SSM Parameter Store / Secret Manager / Key Vault |
| IAM least privilege | Function role has only required permissions; no AdministratorAccess |
| Input validated | All trigger inputs validated/sanitised before processing |
| Function stateless | No in-memory state between invocations; DynamoDB/Redis for shared state |
| Timeout set explicitly | Not default (Lambda default = 3s — almost always wrong) |
| Memory configured | Not left at 128MB default; sized for actual workload |
| arm64 used (Lambda) | Architectures: [arm64] in SAM template |
| DLQ configured | SQS/SNS triggers have dead-letter queue; Lambda destination for async failures |
| Retry logic | Idempotency key checked before processing; duplicate events handled safely |
| No ts-node in production | tsconfig/build outputs JS; ts-node absent from production dependencies |
