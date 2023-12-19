# Bitcoin Core: Test The Test

The goal of these exercises is to learn the Bitcoin Core development environment
through the Bitcoin Test Framework.

## Set Up

1. Clone the Bitcoin Core repository
2. Checkout the `26.0` tag (commit `44d8b13c81e5276eb610c99f227a4d090cc532f6`)
3. Build Bitcoin Core
    - The [docs in the repo](https://github.com/bitcoin/bitcoin/tree/master/doc#building) should provide sufficient guidance for this
    - If you have any problems, search the [issues on GitHub](https://github.com/bitcoin/bitcoin/issues) or try [Bitcoin Stack Exchange](https://bitcoin.stackexchange.com/)
    - Recommended minimal configure options:
        - `./configure --disable-bench --disable-fuzz-binary --enable-debug --without-gui --enable-suppress-external-warnings`
4. Run all the [functional tests](https://github.com/bitcoin/bitcoin/blob/master/test/functional/README.md#functional-tests)
    - All tests should pass!
    - You may be able to speed up the test suite using a [RAM disk](https://github.com/bitcoin/bitcoin/tree/master/test#speed-up-test-runs-with-a-ram-disk)

## Challenge

1. Choose a target test from https://github.com/bitcoin/bitcoin/tree/44d8b13c81e5276eb610c99f227a4d090cc532f6/test/functional
2. Write a minimal commit in `src/` (`*.cpp` or `*.h` files only) that makes this one single test fail, and no others!
3. Submit your commit as a diff using `git show <commit hash>`

- You do NOT need to run the [unit tests](https://github.com/bitcoin/bitcoin/blob/master/src/test/README.md)
 or the extended tests (`test_runner --extended`) for this exercise.

## Example

Target test: `test/functional/feature_abortnode.py`

Commit:
```diff
commit 47e782ade192846393786881c3c5f30c080d21ff
Author: Matthew Zipkin <pinheadmz@gmail.com>
Date:   Wed Nov 29 10:32:16 2023 -0500

    make feature_abortnode.py fail

diff --git a/src/validation.cpp b/src/validation.cpp
index 1fd8f0e326..8b6494a1d7 100644
--- a/src/validation.cpp
+++ b/src/validation.cpp
@@ -3000,7 +3000,7 @@ bool Chainstate::ActivateBestChainStep(BlockValidationState& state, CBlockIndex*
             // If we're unable to disconnect a block during normal operation,
             // then that is a failure of our local system -- we should abort
             // rather than stay on a less work chain.
-            AbortNode(state, "Failed to disconnect block; see debug.log for details");
+            // AbortNode(state, "Failed to disconnect block; see debug.log for details");
             return false;
         }
         fBlocksDisconnected = true;
```