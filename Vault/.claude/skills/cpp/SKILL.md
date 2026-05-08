---
name: cpp
description: Security-first C++23 builder covering modern patterns, CMake build config, Catch2 testing, and memory safety. Use when writing C++, scaffolding a CMake project, writing Catch2 tests, or reviewing C++ for undefined behaviour and memory issues.
argument-hint: "[mode: write|cmake|test|review] [description] â€” add 'raw' for clean output without narration"
model: sonnet
allowed-tools: [Read, Edit, Write, Bash, Glob, Grep]
---

## Audience
Default: NON-DEV MODE â€” every output includes plain-English explanation and inline comments.
Override: include `raw` for clean output without narration (for developers).

## Scope
/cpp = C++23, CMake, Catch2, general-purpose CLI tools and libraries.
Embedded/bare-metal, game engines (Unreal, Unity), and template metaprogramming are out of scope.
/csharp for .NET. /node for JavaScript servers.

## Non-Negotiable
No raw pointers for ownership â€” use `std::unique_ptr` or `std::shared_ptr`. No manual `new`/`delete`.
No raw arrays for strings/buffers â€” use `std::string` and `std::vector`.
ASAN + UBSAN in dev builds â€” sanitizers catch UB and memory errors at runtime.
`-std=c++23` minimum â€” never produce C++14/17 output unless explicitly requested.
`-fstack-protector-strong -D_FORTIFY_SOURCE=2` in release builds.

## Modes

**WRITE** â€” Classes, functions, RAII wrappers, services.
C++23 defaults: `std::format` (not printf/sprintf), `std::expected<T,E>` for error handling, ranges for iteration, `constexpr` for compile-time evaluation.
Smart pointers for all heap allocation. RAII for all resources (files, locks, connections).
NON-DEV: explain smart pointers and RAII on first use in each session.
End with: propose `/cpp cmake` to wire the build.

**CMAKE** â€” CMake 3.28+ project scaffolding.
Output: `CMakeLists.txt` + `CMakePresets.json` (abstracts toolchain for non-devs).
Defaults: `target_compile_features(target PRIVATE cxx_std_23)`, sanitizer preset for debug builds.
Preset targets: `debug` (ASAN + UBSAN enabled), `release` (stack protector + fortify).
End with: propose `/cpp test` to add Catch2.

**TEST** â€” Catch2 v3 test scaffolding.
Framework: Catch2 v3 (`TEST_CASE`, `SECTION`, `REQUIRE`). Single-header or CMake FetchContent.
Pattern: Arrange â†’ Act â†’ Assert inside `SECTION` blocks.
Run with ASAN: `cmake --preset debug && ctest` to catch memory errors in tests.
End with: propose `/cpp review` when coverage looks complete.

**REVIEW** â€” Memory safety + UB audit.
Checklist from REFERENCE.md. Report each: PASS / WARN / FAIL.
Flags: raw owning pointers, manual new/delete, C-style arrays for strings, printf/scanf, missing RAII, integer overflow risk, signed/unsigned mismatch, uninitialized reads.
End with: propose running clang-tidy and ASAN. Propose `/qa` when clean.

## Security Gate
Every WRITE and CMAKE response ends with this line â€” never skip it:
`Security pass: âœ“ no raw ownership pointers âœ“ no manual new/delete âœ“ RAII applied âœ“ std::format not printf âœ“ sanitizers in debug preset`
Flag any item that cannot be confirmed. Stop and fix before presenting output.

## Cross-Skill Integration
- `/csharp` â€” .NET equivalent; propose if operator is on a Windows/.NET stack
- `/node` â€” JavaScript equivalent for scripting or web servers
- `/qa` â€” propose after REVIEW passes clean

## Pipeline Connections
WRITE complete â†’ propose `/cpp cmake`
CMAKE complete â†’ propose `/cpp test`
TEST complete â†’ propose `/cpp review`
REVIEW clean â†’ propose `/qa`
/qa passes â†’ portable sync + commit

## Anti-patterns
âœ— Never use raw owning pointers â€” always smart pointers
âœ— Never use `new`/`delete` directly â€” RAII and smart pointers handle lifetime
âœ— Never use `char[]` buffers or C-style string functions (strcpy, sprintf)
âœ— Never use `printf`/`scanf` â€” use `std::format` and `std::cin`
âœ— Never omit sanitizer flags in debug/test builds
âœ— Never ignore compiler warnings â€” treat `-Wall -Wextra` warnings as errors
âœ— Never use bare `catch (...)` without rethrowing or logging
âœ— Never scope in embedded, bare-metal, or game engine (Unreal/Unity) work
âœ— Never skip the security gate line on WRITE or CMAKE output


## QA
Before closing this skill session:
- [ ] Mode/language/framework confirmed with operator if ambiguous
- [ ] Output matches stated intent (code runs, no placeholders)
- [ ] Security surface reviewed (injection, auth, input validation)
- [ ] Every response ended with NEXT MOVE

Every response ends with NEXT MOVE.