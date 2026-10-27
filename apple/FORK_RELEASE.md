# OmniParser XLS maintenance release

This fork is based on upstream v7.5.2, commit
`77e2a0a6e4b33c33a5aee64551fcd0d7493c9101`. Release: `v7.5.2-omni.1`.

## Change and scope

The BIFF worksheet reader now tracks nested BOF/EOF substreams. Cell-like
records inside chart caches cannot replace worksheet cells, and a chart EOF
cannot stop the rest of the worksheet. An in-memory regression covers nested
chart caches and worksheet cells after their EOF. OmniParser separately checks
an existing real XLS fixture against the original A1:A3 strings.

The artifact contains iOS arm64 and iOS Simulator arm64/x86_64. macOS is not
advertised by this fork's Swift manifest. No document format is reimplemented
in OmniParser and no binary is committed to its Git repository.

## Rebuild

Use Xcode 27.1 / Apple clang 21, Python 3.12 and Conan 2.33.0. Install the
repository Conan profiles into an isolated CONAN_HOME; the Apple profile uses
four build jobs and no ccache launcher. Native slices retain iOS 15 deployment;
OmniParser retains iOS 27 / Swift language mode 5.

```sh
export ODR_GIT_HEAD=v7.5.2-omni.1
# ODR_CONAN points to the isolated conan executable.
for profile in apple-ios-armv8 apple-iossim-armv8 apple-iossim-x86_64; do
  python3.12 apple/build_xcframework.py --conan "$ODR_CONAN" slice --profile "$profile"
done
python3.12 apple/build_xcframework.py assemble \
  --slice ios-arm64 --slice ios-arm64_x86_64-simulator
swift package compute-checksum OdrCoreObjC.xcframework.zip
```

The embedded Git identifier is the release tag. Apple bundle versions use its
numeric component `7.5.2`; the suffix identifies this fork rather than an
upstream release. The Swift manifest and source tag must match the artifact.
The native GoogleTest suite was not executed in this build environment;
OmniParser's existing real-document XCTest suite is the consumer regression.

## Licenses and corresponding source

The original MPL-2.0 LICENSE and notices remain unchanged. This is not an MIT
relicensing. Modified engine and build sources are available in this tag.
`release-notices/` preserves the license files from the isolated Conan host
requirements and their exact recipe/package revisions. It includes build/test
requirements and is not a symbol-level assertion that every entry is linked.

In particular uchardet 0.0.8 is recorded by its Conan recipe as MPL-1.1. Its
corresponding downloaded source is attached as
`uchardet-0.0.8-corresponding-source.tar.gz` to this release. Original source
headers/notices remain in that archive. The release also includes
`OdrCore-licenses.zip` and the build manifest. Application distributors must
retain the applicable notices and corresponding-source access; this release
is not a blanket certification of every application's distribution obligations.

The 0.0.0 local verification artifact used before this release has a different
checksum and is not the asset referenced by this tag.
