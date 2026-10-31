# Android wrapper module placeholder

The Gradle Android library module is created only after the native embedder API and its runtime asset contract are implemented. Its AAR should contain the stable Java/Kotlin API and JNI libraries; large Chromium data assets may need a separately versioned asset archive installed into app-private storage. Do not publish an empty AAR as an engine.
