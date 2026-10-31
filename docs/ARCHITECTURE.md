# Architecture and build stages

## Stage 0 - Chromium build baseline
Build `content_shell_apk` with `target_os="android"`, `target_cpu="arm64"` (workflow `build-chromium.yml`). This proves checkout, dependencies, GN and the Android toolchain work. It is only a test APK.

## Stage 1 - Custom embedder (required before AAR)
Implement an Android embedder against Chromium `//content` public Android APIs and lower layers. Do not copy Chrome's browser UI or call Content Shell an SDK. Decide and document the embedder's lifecycle, browser context/profile, WebContents ownership, render process behavior, permissions, downloads, file chooser, dialogs, SSL handling, networking, cookies/storage, accessibility, media, crash recovery and security boundaries.

The implementation needs:
- C++ embedder entry points and GN build definitions integrated into the pinned Chromium checkout.
- Java/Kotlin API and JNI bindings.
- Android manifest/components and any required isolated renderer/service process declarations.
- Native library packaging for `arm64-v8a`.
- Runtime assets/data files copied from the exact same Chromium build.
- A sample host APK that loads a page, navigates, executes script, handles back/forward, and survives process recreation.

## Stage 2 - SDK packaging
Create an Android Gradle library module that packages the public Java/Kotlin API and JNI libraries. If Chromium runtime data is too large or must be extracted to app-private storage, publish it as a companion versioned asset archive and expose an installer/loader API. An AAR alone is not guaranteed to contain every required runtime asset.

## Stage 3 - compatibility and releases
Pin Chromium commit, depot_tools revision, Android SDK/NDK, build args and SDK API version in a manifest. Build only on explicit engine release or Chromium revision update. Test the AAR in a clean sample host app before publishing. Main app updates consume the existing SDK version and do not invoke Chromium compilation.

## Why not use the other official targets?
- `content_shell_apk`: test shell APK; not an AAR.
- `chrome_public_apk`: standalone browser APK; not an embeddable engine.
- `system_webview_apk`: system WebView provider APK; Android only accepts eligible provider packages/signatures. It is not a private engine library for arbitrary apps.
