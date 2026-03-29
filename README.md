# `cargo update` non-deterministic collateral changes

rustc 1.94.1 (e408947bf 2026-03-25), cargo 1.94.1 (29ea6fb6a 2026-03-24)

## Repro

```
git checkout -- Cargo.lock
cargo update zip
git diff Cargo.lock       # zip updated + windows-core updated in iana-time-zone and wmi
cargo update zip
git diff Cargo.lock       # windows-core reverted, only zip update remains
cargo update zip
git diff Cargo.lock       # again, zip updated + windows-core updated in iana-time-zone and wmi
cargo update zip
git diff Cargo.lock       # again, windows-core reverted, only zip update remains
```

Starting state: `iana-time-zone` and `wmi` depend on `windows-core 0.62.2`.

First `cargo update zip`: bumps zip 8.2.0 to 8.4.0 **and** updates `iana-time-zone` and `wmi` from `windows-core 0.62.2` to `0.61.2`. Neither crate is a dependency of zip.

Second `cargo update zip`: zip is already at 8.4.0 so nothing to update. The `windows-core` flip is reverted - `iana-time-zone` and `wmi` go back to `0.62.2`.

Subsequent runs - flipping between the states.

## Full transcript

```
[master][tmp/cargo-flickering] git diff
[master][tmp/cargo-flickering] cargo update zip
    Updating crates.io index
     Locking 1 package to latest Rust 1.94.0 compatible version
    Updating zip v8.2.0 -> v8.4.0
note: pass `--verbose` to see 1 unchanged dependencies behind latest
[master][tmp/cargo-flickering] git diff --no-ext-diff
diff --git a/Cargo.lock b/Cargo.lock
index 7aa2033..f22cdfa 100644
--- a/Cargo.lock
+++ b/Cargo.lock
@@ -200,7 +200,7 @@ dependencies = [
  "js-sys",
  "log",
  "wasm-bindgen",
- "windows-core 0.61.2",
+ "windows-core 0.62.2",
 ]
 
 [[package]]
@@ -661,14 +661,14 @@ dependencies = [
  "serde",
  "thiserror",
  "windows 0.61.3",
- "windows-core 0.61.2",
+ "windows-core 0.62.2",
 ]
 
 [[package]]
 name = "zip"
-version = "8.2.0"
+version = "8.4.0"
 source = "registry+https://github.com/rust-lang/crates.io-index"
-checksum = "b680f2a0cd479b4cff6e1233c483fdead418106eae419dc60200ae9850f6d004"
+checksum = "7756d0206d058333667493c4014f545f4b9603c4330ccd6d9b3f86dcab59f7d9"
 dependencies = [
  "chrono",
  "crc32fast",
[master][tmp/cargo-flickering] cargo update zip      
    Updating crates.io index
     Locking 0 packages to latest Rust 1.94.0 compatible versions
note: pass `--verbose` to see 1 unchanged dependencies behind latest
[master][tmp/cargo-flickering] git diff --no-ext-diff
diff --git a/Cargo.lock b/Cargo.lock
index 7aa2033..3c601fe 100644
--- a/Cargo.lock
+++ b/Cargo.lock
@@ -666,9 +666,9 @@ dependencies = [
 
 [[package]]
 name = "zip"
-version = "8.2.0"
+version = "8.4.0"
 source = "registry+https://github.com/rust-lang/crates.io-index"
-checksum = "b680f2a0cd479b4cff6e1233c483fdead418106eae419dc60200ae9850f6d004"
+checksum = "7756d0206d058333667493c4014f545f4b9603c4330ccd6d9b3f86dcab59f7d9"
 dependencies = [
  "chrono",
  "crc32fast",
[master][tmp/cargo-flickering] cargo update zip      
    Updating crates.io index
     Locking 0 packages to latest Rust 1.94.0 compatible versions
note: pass `--verbose` to see 1 unchanged dependencies behind latest
[master][tmp/cargo-flickering] git diff --no-ext-diff
diff --git a/Cargo.lock b/Cargo.lock
index 7aa2033..f22cdfa 100644
--- a/Cargo.lock
+++ b/Cargo.lock
@@ -200,7 +200,7 @@ dependencies = [
  "js-sys",
  "log",
  "wasm-bindgen",
- "windows-core 0.61.2",
+ "windows-core 0.62.2",
 ]
 
 [[package]]
@@ -661,14 +661,14 @@ dependencies = [
  "serde",
  "thiserror",
  "windows 0.61.3",
- "windows-core 0.61.2",
+ "windows-core 0.62.2",
 ]
 
 [[package]]
 name = "zip"
-version = "8.2.0"
+version = "8.4.0"
 source = "registry+https://github.com/rust-lang/crates.io-index"
-checksum = "b680f2a0cd479b4cff6e1233c483fdead418106eae419dc60200ae9850f6d004"
+checksum = "7756d0206d058333667493c4014f545f4b9603c4330ccd6d9b3f86dcab59f7d9"
 dependencies = [
  "chrono",
  "crc32fast",
[master][tmp/cargo-flickering] 
```
