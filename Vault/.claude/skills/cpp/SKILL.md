---
name: cpp
description: Security-first C++23 builder covering modern patterns, CMake build config, Catch2 testing, and memory safety. Use when writing C++, scaffolding a CMake project, writing Catch2 tests, or reviewing C++ for undefined behaviour and memory issues.
argument-hint: "[mode: write|cmake|test|review] [description] — add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE — every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/cpp = C++23, CMake, Catch2, general-purpose CLI tools and libraries.
Embedded/bare-metal, game engines (Unreal, Unity), and template metaprogramming are out of scope.
/csharp for .NET. /node for JavaScript servers.

## Non-Negotiable
No raw pointers for ownership — use `std::unique_ptr` or `std::shared_ptr`. No manual `new`/`delete`.
No raw arrays for strings/buffers — use `std::string` and `std::vector`.
ASAN + UBSAN in dev builds — sanitizers catch UB and memory errors at runtime.
`-std=c++23` minimum — never produce C++14/17 output unless explicitly requested.
`-fstack-protector-strong -D_FORTIFY_SOURCE=2` in release builds.

## Modes

**WRITE** — Classes, functions, RAII wrappers, services.
C++23 defaults: `std::format` (not printf/sprintf), `std::expected<T,E>` for error handling, ranges for iteration, `constexpr` for compile-time evaluation.
Smart pointers for all heap allocation. RAII for all resources (files, locks, connections).
NON-DEV: explain smart pointers and RAII on first use in each session.
End with: propose `/cpp cmake` to wire the build.

**CMAKE** — CMake 3.28+ project scaffolding.
Output: `CMakeLists.txt` + `CMakePresets.json` (abstracts toolchain for non-devs).
Defaults: `target_compile_features(target PRIVATE cxx_std_23)`, sanitizer preset for debug builds.
Preset targets: `debug` (ASAN + UBSAN enabled), `release` (stack protector + fortify).
End with: propose `/cpp test` to add Catch2.

**TEST** — Catch2 v3 test scaffolding.
Framework: Catch2 v3 (`TEST_CASE`, `SECTION`, `REQUIRE`). Single-header or CMake FetchContent.
Pattern: Arrange → Act → Assert inside `SECTION` blocks.
Run with ASAN: `cmake --preset debug && ctest` to catch memory errors in tests.
End with: propose `/cpp review` when coverage looks complete.

**REVIEW** — Memory safety + UB audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: raw owning pointers, manual new/delete, C-style arrays for strings, printf/scanf, missing RAII, integer overflow risk, signed/unsigned mismatch, uninitialized reads.
End with: propose running clang-tidy and ASAN. Propose `/qa` when clean.

## Security Gate
Every WRITE and CMAKE response ends with this line — never skip it:
`Security pass: ✓ no raw ownership pointers ✓ no manual new/delete ✓ RAII applied ✓ std::format not printf ✓ sanitizers in debug preset`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/csharp` — .NET equivalent; propose if operator is on a Windows/.NET stack
- `/node` — JavaScript equivalent for scripting or web servers
- `/qa` — propose after REVIEW passes clean

## Pipeline Connections
WRITE complete → propose `/cpp cmake`
CMAKE complete → propose `/cpp test`
TEST complete → propose `/cpp review`
REVIEW clean → propose `/qa`
/qa passes → portable sync + commit

## Anti-patterns
✗ Never use raw owning pointers — always smart pointers
✗ Never use `new`/`delete` directly — RAII and smart pointers handle lifetime
✗ Never use `char[]` buffers or C-style string functions (strcpy, sprintf)
✗ Never use `printf`/`scanf` — use `std::format` and `std::cin`
✗ Never omit sanitizer flags in debug/test builds
✗ Never ignore compiler warnings — treat `-Wall -Wextra` warnings as errors
✗ Never use bare `catch (...)` without rethrowing or logging
✗ Never scope in embedded, bare-metal, or game engine (Unreal/Unity) work
✗ Never skip the security gate line on WRITE or CMAKE output
