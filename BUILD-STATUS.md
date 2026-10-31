# Build status

- Build workflow: builds a Chromium Android ARM64 APK (Content Shell, Chrome Public or System WebView), resumable across runs through ccache, published as a GitHub Release. Validates the toolchain; it is not the engine SDK.
- Engine SDK: not started. The readiness workflow is a deliberate gate; no AAR is claimed until the custom `//content` embedder, GN target, JNI bridge, Android wrapper and runtime asset rules exist.
- Nothing runs automatically on ordinary pushes.
