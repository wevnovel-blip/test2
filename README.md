# Chromium Engine SDK (Android ARM64)

This repository builds Chromium for Android ARM64 on GitHub Actions. The phone only creates and manages the repository; Chromium is fetched and compiled on the configured runner.

## Workflows
- **Build Chromium (Android ARM64)** (`build-chromium.yml`): fetches a pinned Chromium tag (default: latest Stable Android), builds `content_shell_apk`, `chrome_public_apk` or `system_webview_apk`, and publishes the APK(s) as a GitHub Release. On a 6-hour-limit runner it stops before the limit, saves its ccache, and dispatches itself again until the build finishes. See `docs/BUILD_STRATEGY.md`.
- **Chromium runner capacity probe**: prints CPU, RAM and disk of the configured runner.
- **Check custom Chromium Engine SDK readiness**: guard that explains what is missing before a real embedder AAR can be built.

## Runner
Repository variable `CHROMIUM_RUNNER_JSON` holds the runner label(s) (default `ubuntu-24.04`, the free standard runner). Change it in Settings > Secrets and variables > Actions > Variables to use a larger or self-hosted runner. Set `CHROMIUM_MAXIMIZE_DISK` to `false` to skip the disk-merging step on a hosted runner that already has enough disk.

## Important engineering truth
Chromium does **not** publish an official, drop-in Android `ChromiumEngine.aar` target. `content_shell_apk` is a test APK around the Content module, not an embeddable AAR or a production browser. `chrome_public_apk` is a standalone browser APK. `system_webview_apk` is an Android WebView provider APK and is subject to Android provider rules; it is not a private in-app engine AAR.

A reusable AAR requires a maintained custom Android content embedder: Java/Kotlin public API, JNI bridge, C++ browser/content integration, GN targets, Android process/service integration, native `.so` libraries, and Chromium runtime resources. See `docs/ARCHITECTURE.md` and `docs/EMBEDDER_IMPLEMENTATION_CHECKLIST.md`.

## Official references
- https://chromium.googlesource.com/chromium/src/+/main/docs/android_build_instructions.md
- https://chromium.googlesource.com/chromium/src/+/main/docs/android_native_libraries.md
- https://chromium.googlesource.com/chromium/src/+/main/content/public/android/README.md
- https://chromium.googlesource.com/chromium/src/+/main/android_webview/docs/architecture.md
- https://chromium.googlesource.com/chromium/src/+/main/android_webview/docs/build-instructions.md
