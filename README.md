# Bitcoin Core: Test The Test

The goal of these exercises is to learn the Bitcoin Core development environment
through the Bitcoin Test Framework.

## Set Up

1. Clone the [Bitcoin Core repository](https://github.com/bitcoin/bitcoin).
2. Checkout the `v29.1` tag (commit `fd784f277427aea7b25a8cdcd328b18a9fa64c0d`).
3. Build Bitcoin Core
    - The [docs in the repo](https://github.com/bitcoin/bitcoin/tree/v29.1/doc#building) should provide sufficient guidance for this
    - If you have any problems, search the [issues on GitHub](https://github.com/bitcoin/bitcoin/issues) or try [Bitcoin Stack Exchange](https://bitcoin.stackexchange.com/)
    - Recommended minimal configure options: `cmake -B build -DBUILD_GUI=OFF -DBUILD_TESTS=ON`
4. Run all the [functional tests](https://github.com/bitcoin/bitcoin/tree/v29.1/test): `build/test/functional/test_runner.py`
    - All tests should pass! (You can ignore "skipped" tests)
    - You may be able to speed up the test suite using a [RAM disk](https://github.com/bitcoin/bitcoin/tree/v29.1/test#speed-up-test-runs-with-a-ram-disk)

## Challenge

1. Choose a target test from `https://github.com/bitcoin/bitcoin/tree/v29.1/test/functional`.
    - Don't pick an ignored test.
2. Write a minimal commit in `src/` (`*.cpp` or `*.h` files only) that makes this one single test fail, and no others!
    - You should not modify the python code of the tests.
    - Your change SHOULD NOT break more than one test.
3. Submit your commit as a diff using `git show <commit hash>`
    - Submit this diff as a text file (e.g. break_feature_abortnode.diff) in the main branch of your classroom repository.

You do NOT need to run the [unit tests](https://github.com/bitcoin/bitcoin/blob/v29.1/src/test/README.md) or the extended tests (`test_runner --extended`) for this exercise.

## Example

### Target test

`test/functional/feature_abortnode.py`

### Commit

```diff
commit 9f08a08d9203638e3b68ae599d92013f1acc5d4b (HEAD)
Author: Edil Medeiros <jose.edil@gmail.com>
Date:   Thu Sep 11 17:12:43 2025 -0300

    Break feature_abortnode test

diff --git a/src/validation.cpp b/src/validation.cpp
index fde064458d..49822920c9 100644
--- a/src/validation.cpp
+++ b/src/validation.cpp
@@ -3369,7 +3369,7 @@ bool Chainstate::ActivateBestChainStep(BlockValidationState& state, CBlockIndex*
             // If we're unable to disconnect a block during normal operation,
             // then that is a failure of our local system -- we should abort
             // rather than stay on a less work chain.
-            FatalError(m_chainman.GetNotifications(), state, _("Failed to disconnect block."));
+            // FatalError(m_chainman.GetNotifications(), state, _("Failed to disconnect block."));
             return false;
         }
         fBlocksDisconnected = true;
```

### Functional test results

Example output (truncated):

```
$ build/test/functional/test_runner.py --cachedir=/Volumes/ramdisk/cache --tmpdir=/Volumes/ramdisk/tmp --jobs=10
Temporary test directory at /Volumes/ramdisk/tmp/test_runner_₿_🏃_20250911_162802
1/318 - wallet_conflicts.py --legacy-wallet skipped (BDB has not been compiled.)
2/318 - p2p_node_network_limited.py --v1transport passed, Duration: 11 s
3/318 - p2p_node_network_limited.py --v2transport passed, Duration: 12 s
4/318 - feature_taproot.py passed, Duration: 24 s
...
317/318 - wallet_orphanedreward.py passed, Duration: 10 s
Remaining jobs: [p2p_permissions.py]
318/318 - p2p_permissions.py passed, Duration: 11 s

TEST                                                                 | STATUS    | DURATION

example_test.py                                                      | ✓ Passed  | 1 s
feature_abortnode.py                                                 | ✓ Passed  | 1 s
feature_addrman.py                                                   | ✓ Passed  | 3 s
feature_anchors.py                                                   | ✓ Passed  | 3 s
...
wallet_txn_doublespend.py --descriptors                              | ✓ Passed  | 1 s
wallet_txn_doublespend.py --mineblock                                | ✓ Passed  | 3 s
feature_bind_extra.py                                                | ○ Skipped | 0 s
feature_bind_port_discover.py                                        | ○ Skipped | 0 s
...
wallet_watchonly.py --legacy-wallet                                  | ○ Skipped | 0 s
wallet_watchonly.py --usecli --legacy-wallet                         | ○ Skipped | 1 s

ALL                                                                  | ✓ Passed  | 1621 s (accumulated)
Runtime: 175 s
```
