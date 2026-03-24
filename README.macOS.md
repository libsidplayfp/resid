# Building reSID on macOS

## Problem

On modern macOS (26+) with Apple's Command Line Tools, building reSID fails with:

```
fatal error: 'cassert' file not found
```

This happens because Apple's Clang no longer places the C++ standard library headers in the default toolchain include path (`/Library/Developer/CommandLineTools/usr/include/c++/v1/`). The headers exist inside the SDK, but are not picked up automatically.

## Prerequisites

- macOS (tested on macOS 26.3.1)
- Xcode Command Line Tools:
  ```sh
  xcode-select --install
  ```

## Build Steps

1. **Configure with the SDK's C++ include path:**

   ```sh
   SDK_PATH=$(xcrun --show-sdk-path)
   ./configure CXXFLAGS="-I${SDK_PATH}/usr/include/c++/v1 -g -Wall -O3 -fno-exceptions"
   ```

2. **Build:**

   ```sh
   make
   ```

This produces `libresid.a` in the current directory.

## Why This Works

`xcrun --show-sdk-path` returns the active macOS SDK path (e.g. `/Library/Developer/CommandLineTools/SDKs/MacOSX.sdk`). Adding `-I<sdk>/usr/include/c++/v1` explicitly tells Clang where to find C++ standard library headers like `<cassert>`, `<cstdlib>`, etc.
