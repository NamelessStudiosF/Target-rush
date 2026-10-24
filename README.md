# Target Rush — first-person aim trainer for Android

**Deliverable: an Android Studio / Gradle project (not a prebuilt APK).** The game is pure HTML5 (WebGL, no external libraries or assets;
all graphics are procedural and all sounds are synthesized with WebAudio, so there are no licensing concerns) hosted in a native
fullscreen landscape `WebView` activity. The build environment used to create this project had no Android SDK and no access to Google/Maven
hosts, so the APK itself has **not** been compiled here. The game logic was tested headless in Chromium (see "Testing").

## Build the APK

### Option A: Android Studio (easiest)
1. Install Android Studio (Koala or newer); it brings JDK 17+ and the SDK.
2. `File > Open` this folder, let Gradle sync (it installs SDK Platform 34 if prompted).
3. `Build > Build Bundle(s) / APK(s) > Build APK(s)`.
4. APK: `app/build/outputs/apk/debug/app-debug.apk`. Copy to a phone and install, or press Run with a device/emulator attached.

### Option B: command line
```bash
# JDK 17+ and the Android SDK command-line tools required
export ANDROID_HOME=$HOME/Android/Sdk
sdkmanager "platforms;android-34" "build-tools;34.0.0" "platform-tools"
echo "sdk.dir=$ANDROID_HOME" > local.properties
./gradlew assembleDebug        # -> app/build/outputs/apk/debug/app-debug.apk
./gradlew assembleRelease      # -> app/build/outputs/apk/release/app-release.apk (debug-signed; add your own keystore for Play Store)
adb install -r app/build/outputs/apk/debug/app-debug.apk
```
Requirements: minSdk 26 (Android 8.0), targetSdk 34, no third-party dependencies.

## Play it in a browser without building
Open `app/src/main/assets/www/index.html` in Chrome. Add `?touch=1` to the URL to force the touch HUD on desktop.
Desktop controls: WASD move, mouse aim (click to lock), LMB fire, RMB scope/ADS, R reload, 1/2/3 weapons, Esc pause.

## Game summary
* **5 levels**, destroy every target before the timer ends. Each level raises target count (5 > 16), speed, shrinks targets, shortens
  direction-change intervals, shortens the exposed window (shield cycle) and adds more sudden dashes. Targets are glowing high-contrast
  discs; a radar shows where they are.
* **HUD:** level, targets left, score, accuracy, timer, combo, ammo. Level-complete screen (with stars), victory screen after level 5,
  and a fail screen with **Retry Level / Change Weapon / Menu**.
* **Weapons** (pick before play, switch any time): **AK-47** full-auto, big magazine, climbing recoil; **Desert Eagle** heavy single shots,
  slow cadence, strong kick; **Kar98** bolt-action, slow reload, precise, with scope view. Each has its own synthesized sound, muzzle
  flash, recoil animation and reload behaviour.
* **Touch controls:** virtual joystick, drag-to-aim, large Fire / Reload / ADS / weapon buttons. Settings: sensitivity, ADS sensitivity,
  invert Y, UI scale, control opacity, FPS cap, drag-to-reposition control layout. Pause menu; the game auto-pauses when the app is backgrounded; Android Back = pause.
* **Persistence:** settings, unlocked levels, stars and best scores in `localStorage` (inside the app's WebView storage).

## Layout
```
app/src/main/assets/www/   index.html + js/{engine,audio,data,game,ui}.js   <- the game (edit here)
app/src/main/java/.../MainActivity.java                                    <- fullscreen landscape WebView host
_old_web_prototype/        earlier, superseded prototype (not used by the app)
```

## Testing performed
Headless Chromium (software GL) smoke tests: menu, all 5 levels start and run with all three weapons, no console/page errors,
level-complete, victory (after level 5) and time-out fail screens appear, Retry restarts the level, progress is written to localStorage.
Not tested: real-device touch feel/performance and the Gradle build itself.
