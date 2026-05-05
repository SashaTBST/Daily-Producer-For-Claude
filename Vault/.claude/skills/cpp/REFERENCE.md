# C++ Skill — Reference
C++23 / CMake 3.28+ / Catch2 v3 | Last verified: 2026-04-30

## Research Sources
- C++23 support in MSVC: https://devblogs.microsoft.com/cppblog/c23-support-in-msvc-build-tools-14-51/
- Compiler support for C++26: https://en.cppreference.com/cpp/compiler_support/26
- Modern C++ features 2026: https://netalith.com/blogs/cpp-tutorial/modern-cpp-features-2026-cpp23-cpp26-guide
- CMake vs Meson 2026: https://linux-digest.com/cmake-vs-meson-the-2026-reality-check
- C++ testing frameworks 2026: https://hackingcpp.com/cpp/tools/testing_frameworks
- Sanitizers: https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html
- Buffer overflow mitigation: https://www.parasoft.com/blog/battling-buffer-overflows-other-memory-management-bugs/

---

## C++23 Patterns — Use by Default

### Smart Pointers (no raw ownership)
```cpp
#include <memory>

// unique_ptr — single owner, auto-deleted when out of scope
auto config = std::make_unique<Config>("settings.json");

// shared_ptr — shared ownership, deleted when last owner releases
auto shared = std::make_shared<Logger>("app.log");

// Pass by reference to avoid unnecessary ref-counting
void process(const Config& cfg);  // ✓
void process(std::shared_ptr<Config> cfg);  // ✗ only when sharing ownership
```

### RAII — Resources Acquired in Constructor, Released in Destructor
```cpp
// File handle managed by RAII — automatically closed
class FileReader {
    std::ifstream file_;
public:
    explicit FileReader(const std::string& path) : file_(path) {
        if (!file_) throw std::runtime_error("Cannot open: " + path);
    }
    // destructor closes file_ automatically — no manual close needed
    std::string readLine() {
        std::string line;
        std::getline(file_, line);
        return line;
    }
};
```

### std::expected — Error Handling Without Exceptions
```cpp
#include <expected>
#include <string>

// Return value OR error — no try/catch needed
std::expected<int, std::string> parsePort(const std::string& input) {
    try {
        int port = std::stoi(input);
        if (port < 1 || port > 65535)
            return std::unexpected("Port out of range: " + input);
        return port;
    } catch (...) {
        return std::unexpected("Not a number: " + input);
    }
}

// Usage
auto result = parsePort("8080");
if (result) {
    // *result is the int value
} else {
    // result.error() is the string message
}
```

### std::format — String Formatting
```cpp
#include <format>

// Replaces printf/sprintf entirely
std::string msg = std::format("User {} connected from {}", userId, ipAddress);
// Compile-time format string validation — wrong arg count is a compile error
```

### Ranges
```cpp
#include <ranges>
#include <vector>
#include <algorithm>

std::vector<int> nums = {1, 2, 3, 4, 5, 6};

// Lazy pipeline — filter then transform, no intermediate vector
auto result = nums
    | std::views::filter([](int n){ return n % 2 == 0; })
    | std::views::transform([](int n){ return n * n; });
// result is lazy — only evaluates when iterated
```

---

## CMake Template

```cmake
cmake_minimum_required(VERSION 3.28)
project(MyApp VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)  # enables clang-tidy

add_executable(myapp src/main.cpp src/config.cpp)

target_compile_options(myapp PRIVATE
    $<$<CONFIG:Debug>:-fsanitize=address,undefined -fno-omit-frame-pointer>
    $<$<CONFIG:Release>:-fstack-protector-strong -D_FORTIFY_SOURCE=2>
    -Wall -Wextra -Wpedantic
)

target_link_options(myapp PRIVATE
    $<$<CONFIG:Debug>:-fsanitize=address,undefined>
)

# Tests
include(CTest)
add_subdirectory(tests)
```

```json
// CMakePresets.json
{
  "version": 6,
  "configurePresets": [
    {
      "name": "debug",
      "binaryDir": "build/debug",
      "cacheVariables": { "CMAKE_BUILD_TYPE": "Debug" }
    },
    {
      "name": "release",
      "binaryDir": "build/release",
      "cacheVariables": { "CMAKE_BUILD_TYPE": "Release" }
    }
  ]
}
```

**Commands:**
```bash
cmake --preset debug && cmake --build build/debug    # Debug + ASAN
cmake --preset release && cmake --build build/release  # Release
ctest --test-dir build/debug                           # Run tests with ASAN
```

---

## Catch2 v3 Test Template

```cmake
# tests/CMakeLists.txt
Include(FetchContent)
FetchContent_Declare(
  Catch2
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG v3.8.0
)
FetchContent_MakeAvailable(Catch2)

add_executable(tests test_config.cpp test_parser.cpp)
target_link_libraries(tests PRIVATE mylib Catch2::Catch2WithMain)
include(Catch)
catch_discover_tests(tests)
```

```cpp
// test_config.cpp
#include <catch2/catch_test_macros.hpp>
#include "config.hpp"

TEST_CASE("Config loads from file", "[config]") {
    SECTION("valid file returns expected value") {
        // Arrange
        Config cfg("test_data/valid.json");
        // Act
        auto port = cfg.getPort();
        // Assert
        REQUIRE(port == 8080);
    }

    SECTION("missing file throws") {
        REQUIRE_THROWS_AS(Config("nonexistent.json"), std::runtime_error);
    }
}
```

---

## REVIEW Checklist

| # | Check | What to look for | Report |
|---|-------|-----------------|--------|
| 1 | No raw owning pointers | Raw pointer + new without smart pointer wrapper | PASS/FAIL |
| 2 | No manual new/delete | Direct `new` or `delete` calls (except placement new) | PASS/FAIL |
| 3 | No C-style strings | `char[]`, `strcpy`, `sprintf`, `gets` — use std::string + std::format | PASS/FAIL |
| 4 | No printf/scanf | Use `std::format` / `std::cin` / structured IO | PASS/WARN |
| 5 | RAII applied | Resources (files, sockets, mutexes) wrapped in RAII types | PASS/WARN |
| 6 | Signed/unsigned mismatch | Comparing signed and unsigned integers without cast | PASS/WARN |
| 7 | Uninitialized vars | Variables declared without initialization | PASS/WARN |
| 8 | Integer overflow risk | Arithmetic on user-controlled ints without range check | PASS/WARN |
| 9 | Sanitizer flags present | CMake debug preset includes -fsanitize=address,undefined | PASS/FAIL |
| 10 | -Wall -Wextra enabled | Compiler warning flags present in CMake config | PASS/WARN |
