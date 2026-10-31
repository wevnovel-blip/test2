# Custom embedder source goes here

This folder is an implementation boundary, not a completed engine. The actual C++ embedder must be added to the Chromium source checkout and wired into GN. A custom target named `chromium_engine_sdk` is intentionally not fabricated here: Chromium has no stock target with that name.

The readiness workflow refuses to build/package until the project provides:
1. `sdk/engine/BUILD.gn` defining `chromium_engine_sdk`;
2. the Java/Kotlin/JNI and native embedder implementation;
3. `sdk/android-wrapper/build.gradle.kts` Gradle library module;
4. `sdk/engine/build-sdk.sh`, a sample host app and runtime asset packaging rules.

This guard prevents a Content Shell APK or unrelated `.so` files from being mislabeled as a reusable engine.
