# Bitcoin Core: Test The Test

The goal of these exercises is to learn the Bitcoin Core development environment
through the Bitcoin Test Framework.

## Set Up

1. Clone the Bitcoin Core repository
2. Checkout the `v27.1` tag (commit `1088a98f5aad080cc6cca2da174f206509fcda6c2M`)
3. Build Bitcoin Core
    - The [docs in the repo](https://github.com/bitcoin/bitcoin/tree/v27.1/doc#building) should provide sufficient guidance for this
    - If you have any problems, search the [issues on GitHub](https://github.com/bitcoin/bitcoin/issues) or try [Bitcoin Stack Exchange](https://bitcoin.stackexchange.com/)
    - Recommended minimal configure options:
        - `./configure --disable-bench --disable-fuzz-binary --enable-debug --without-gui --enable-suppress-external-warnings`
4. Run all the [functional tests](https://github.com/bitcoin/bitcoin/blob/v27.1/test/functional/README.md)
    - All tests should pass! (You can ignore "skipped" tests)
    - You may be able to speed up the test suite using a [RAM disk](https://github.com/bitcoin/bitcoin/tree/v27.1/test#speed-up-test-runs-with-a-ram-disk)

## Challenge

1. Choose a target test from https://github.com/bitcoin/bitcoin/tree/1088a98f5aad080cc6cca2da174f206509fcda6c/test/functional
2. Write a minimal commit in `src/` (`*.cpp` or `*.h` files only) that makes this one single test fail, and no others!
    - You can ignore "skipped" tests
3. Submit your commit as a diff using `git show <commit hash>`

- You do NOT need to run the [unit tests](https://github.com/bitcoin/bitcoin/blob/v27.1/src/test/README.md)
 or the extended tests (`test_runner --extended`) for this exercise.

## Example

### Target test

`test/functional/feature_abortnode.py`

### Commit

```diff
commit 14683cab0e91f1e7984c61fb4b8b74574fb4b339
Author: Matthew Zipkin <pinheadmz@gmail.com>
Date:   Sat Dec 30 13:04:24 2023 +0000

    make feature_abortnode.py fail

diff --git a/src/validation.cpp b/src/validation.cpp
index a6cab6b095..df093c9c34 100644
--- a/src/validation.cpp
+++ b/src/validation.cpp
@@ -3115,7 +3115,7 @@ bool Chainstate::ActivateBestChainStep(BlockValidationState& state, CBlockIndex*
             // If we're unable to disconnect a block during normal operation,
             // then that is a failure of our local system -- we should abort
             // rather than stay on a less work chain.
-            FatalError(m_chainman.GetNotifications(), state, "Failed to disconnect block; see debug.log for details");
+            // FatalError(m_chainman.GetNotifications(), state, "Failed to disconnect block; see debug.log for details");
             return false;
         }
         fBlocksDisconnected = true;
```

Submit this diff as a file (e.g. `break_feature_abortnode.diff`) in your classroom repository.

### Functional test results

Example output (truncated):

```
...
wallet_txn_doublespend.py --mineblock                  | ✓ Passed  | 3 s
wallet_watchonly.py --legacy-wallet                    | ✓ Passed  | 3 s
wallet_watchonly.py --usecli --legacy-wallet           | ✓ Passed  | 3 s
...
wallet_backwards_compatibility.py --legacy-wallet      | ○ Skipped | 0 s
wallet_inactive_hdchains.py --legacy-wallet            | ○ Skipped | 0 s
wallet_upgradewallet.py --legacy-wallet                | ○ Skipped | 0 s
feature_abortnode.py                                   | ✖ Failed  | 6 s

ALL                                                    | ✖ Failed  | 4009 s (accumulated) 
Runtime: 714 s
```
