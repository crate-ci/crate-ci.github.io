# Prebuilt Binaries

Pre-built binaries are a basic step you can take to help users of your application
- They are easy to create
- `cargo` (with `install`) is not meant as a binary distribution channel and has deficiencies
  - `Cargo.lock` is not respected
- Users wanting to use your tool in their CI will see major slow downs and require workarounds.

## Uploading Binaries to Github Releases

Pre-requisites:
- [Testing](pr/testing.html) is setup.
- Jobs exist for each supported target.
