# Update the WebView

Websites on a screen are drawn by the device's **WebView**. A WebView that is too old shows modern websites broken. This page updates it from a computer with adb, with no Play Store and no Google account on the device.

Do this for every Android screen **before it ships**, if the screen will show a website.

## Why it matters

A website built with newer technology (for example Tailwind v4) needs **WebView 111 or newer**. On an older WebView the page looks like it has no styling: oversized pictures, a messy layout and grey boxes over the screen. The same website looks right on WebView 155.

The same website on the same panel:

| WebView 91 (too old) | WebView 155 (fine) |
|---|---|
| ![The website broken: huge picture and a grey box over the screen](images/webview/01-webview-91-broken.png) | ![The same website laid out correctly](images/webview/02-webview-155-ok.png) |

!!! warning "Many panels carry two WebViews"
    A panel can have an old one and a new one installed at the same time. Installing a new one is not enough: make sure the **new one is the active one** (step 5).

## Before you start

- A computer with adb: `brew install --cask android-platform-tools`
- The device and the computer on the same Wi-Fi, or a USB cable.
- The WebView `.apk` file for the device (step 3).

## Steps

1. Connect to the device. Same Wi-Fi:
    ```bash
    adb connect <device-ip>:5555
    ```
    If that fails, or the device is on Android 11 or newer, follow step 2 of [Device Owner over Wi-Fi](remote-device-owner-setup.md).
2. Check which WebView is active and how old it is:
    ```bash
    adb shell dumpsys webviewupdate
    ```
    Look at the line **Current WebView package**. It shows a package name and a version, like `com.google.android.webview, 91.0.4472.114`.
    - **111 or higher:** nothing to do. Stop here.
    - **Below 111:** carry on.
3. Get the right file. Ask the device what it needs:
    ```bash
    adb shell getprop ro.product.cpu.abi
    adb shell getprop ro.build.version.release
    ```
    Download **Android System WebView** that matches both answers (`arm64-v8a` or `armeabi-v7a`, and the Android version) from a source your team trusts, such as APKMirror. Pick the newest build that still lists your Android version.
    !!! note
        Some builds ask for **Trichrome Library** as well. Install that first. A `.apkm` or `.xapk` file is a bundle: unzip it and use `adb install-multiple -r *.apk`.
4. Install it. `-r` keeps the apps and settings already on the device:
    ```bash
    adb install -r android-system-webview.apk
    ```
    You should see `Success`.
5. Make the new WebView the active one:
    ```bash
    adb shell cmd webviewupdate set-webview-implementation com.google.android.webview
    ```
    If it says the package is not valid, set it by hand on the device: **Settings → Developer options → WebView implementation**, and pick the new one.
6. Check again. The version should now be 111 or higher:
    ```bash
    adb shell dumpsys webviewupdate
    ```
7. Restart the player so it picks up the new WebView:
    ```bash
    adb shell am force-stop com.fortu.player
    adb shell monkey -p com.fortu.player -c android.intent.category.LAUNCHER 1
    ```
8. Test with a real website. Add a **Website** item to a playlist, as in [Create a Playlist](creating-a-playlist.md), and play it on this screen. The page should look the same as in a normal browser.

## If it does not work

| What you see | What to do |
|---|---|
| `INSTALL_FAILED_VERSION_DOWNGRADE` | The file is older than what is installed. Download a newer build. |
| `INSTALL_FAILED_OLDER_SDK` | The file needs a newer Android. Pick a build for your Android version. |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | The installed copy is signed differently. Choose the Google build for the package you are replacing, or use the other package name. |
| Version stays old after step 6 | The old WebView is still active. Repeat step 5 and use **Developer options**. Restart the device if needed. |
| Website still looks broken | Open the same website in Chrome on the same screen. If it is broken there too, the problem is the website, not the screen. |

!!! tip
    Write the final version in the device checklist you hand over, for example "WebView 155 on com.google.android.webview".
