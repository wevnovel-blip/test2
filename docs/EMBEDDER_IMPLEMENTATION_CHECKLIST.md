# Custom Android Chromium embedder checklist

Work required before a genuine reusable AAR can be built. Do not mark a box complete based only on a Content Shell or Chrome APK build.

- [ ] Choose and pin a Chromium commit and compatible depot_tools revision.
- [ ] Implement a custom Android content embedder using supported `//content/public/android` APIs where possible; document any Chromium-internal API dependency.
- [ ] Add the embedder's GN target under the Chromium checkout and verify all native dependencies are linked for `arm64`.
- [ ] Implement C++ lifecycle and WebContents ownership, plus Java/Kotlin public API and JNI bindings.
- [ ] Implement Android manifest entries, browser/renderer process and service requirements, startup initialization and crash handling.
- [ ] Define the runtime resource contract: `.pak` files, ICU data, V8 snapshots, locale resources and any other files required by the exact build. Do not assume all runtime data belongs inside the AAR.
- [ ] Build a sample host APK that proves initialization, rendering, navigation, JavaScript, back/forward, cookies/storage and process recreation.
- [ ] Package Java/Kotlin API and JNI `.so` files into an Android Gradle library AAR; package large runtime assets as a version-matched companion archive if necessary.
- [ ] Install the AAR and asset bundle into a clean sample app and run device/emulator integration tests.
- [ ] Publish immutable SDK versions containing Chromium commit, toolchain, GN args, ABI and asset-manifest metadata.
- [ ] Only then add a GitHub Actions job that fetches the pinned Chromium revision, applies embedder sources/patches, compiles the GN target, runs Gradle packaging and uploads the verified AAR plus runtime bundle.
