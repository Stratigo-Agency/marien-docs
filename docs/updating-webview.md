# Update the WebView

The **WebView** is the part of an Android screen that shows websites. When it is too old, newer websites can look broken. This page shows how to update it from a laptop over Wi-Fi, with no Play Store and no Google account on the screen.

Do this for every Android screen **before it ships to the client**, if the screen will show a website.

## Why it matters

A website built with newer technology (for example Tailwind v4) needs **WebView 111 or newer**. On an older WebView the page looks like it has no design: oversized pictures, a messy layout and grey boxes over the screen. The same website looks right on WebView 155.

The same website on the same screen:

**WebView 91 (too old)**

![The website broken: huge picture and a grey box over the screen](images/webview/01-webview-91-broken.png)

**WebView 155 (fine)**

![The same website laid out correctly](images/webview/02-webview-155-ok.png)

!!! warning "A screen can have two WebViews"
    Some screens have an old and a new WebView at the same time. Installing the new one is not enough. You also need to make sure **the new one is the one in use** (step 7).

## What you need

- A laptop and the screen on the **same Wi-Fi or network**.
- About 15 minutes for the first screen. The next ones are much faster.

## Steps

### 1. Install adb on the laptop

adb is a small tool that lets a laptop manage an Android screen. You only install it once per laptop.

=== "Windows"

    1. Click **Start**, type `PowerShell`, and press **Enter**. A blue window opens. This is where you type the commands.
    2. Type this and press **Enter**:
        ```powershell
        winget install Google.PlatformTools
        ```
    3. If it asks you to agree, type `Y` and press **Enter**.
    4. **Close PowerShell and open it again.** This is needed so the laptop finds adb.
    5. Check that it worked:
        ```powershell
        adb version
        ```
        If you see `Android Debug Bridge version ...`, adb is ready.

    !!! note "If `winget` is not recognised"
        Download **SDK Platform-Tools for Windows** from the official Android Developers site and extract it to `C:\platform-tools`. Open PowerShell, type `cd C:\platform-tools`, and write `.\adb` (with the dot and slash) for every `adb` command on this page.

=== "Mac"

    1. Open **Terminal** (press `Cmd + Space`, type `Terminal`, press **Enter**).
    2. Type this and press **Enter**:
        ```bash
        brew install --cask android-platform-tools
        ```
    3. Check that it worked:
        ```bash
        adb version
        ```

### 2. Turn on Wi-Fi debugging on the screen

This lets the laptop manage the screen. Menu names can differ a little between brands.

1. On the screen, open **Settings → About** (or **About device**).
2. Find **Build number** and **tap it 7 times** until it says "You are now a developer".
3. Go back to **Settings** and open **Developer options**.
4. Turn on **USB debugging**.
5. Look for **Wireless debugging** and turn it on.
    - **Is the menu there?** Your screen is Android 11 or newer. In step 3, follow **Android 11 or newer**.
    - **Not there?** Your screen is Android 10 or older. In step 3, follow **Android 10 or older**.
6. Note the screen's **IP address** under **Settings → Network** (numbers like `10.200.0.44`). If you cannot find it, it is also shown on the Wireless debugging screen.

### 3. Connect the laptop to the screen

=== "Android 11 or newer"

    The first time, the laptop and the screen need to be "paired" with a code.

    1. On the screen, open **Developer options → Wireless debugging → Pair device with pairing code**. It shows an **address with a port** (for example `10.200.0.44:37123`) and a **6-digit code**. Leave this screen open.
    2. In PowerShell (or Terminal on a Mac), type this with the address from the screen, then press **Enter**:
        ```bash
        adb pair 10.200.0.44:37123
        ```
    3. When it asks, type the **6-digit code** and press **Enter**. You should see `Successfully paired`.
    4. Go back to the main **Wireless debugging** screen. It shows an IP address and port that are **different** from before (for example `10.200.0.44:41234`). Connect to that one:
        ```bash
        adb connect 10.200.0.44:41234
        ```

=== "Android 10 or older"

    This screen needs a **USB cable just once**.

    1. Plug a USB cable from the laptop into the screen. Use a cable that carries data, not a charging-only cable.
    2. If the screen asks **Allow USB debugging?**, tick **Always allow** and choose **OK**.
    3. In PowerShell (or Terminal on a Mac), type:
        ```bash
        adb tcpip 5555
        ```
    4. Unplug the cable, then connect over Wi-Fi with the screen's IP address:
        ```bash
        adb connect 10.200.0.44:5555
        ```

Then check that the screen is connected:

```bash
adb devices
```

Your screen should be in the list with the word **device** next to it. If it says `unauthorized`, look at the screen and choose **Allow**.

### 4. Check the WebView version on the screen

```bash
adb shell dumpsys webviewupdate
```

Find the line **Current WebView package**. It shows a name and a version, like `com.google.android.webview, 91.0.4472.114`.

Also look at the **Valid package** lines. They list every WebView on the screen, with its version.

- **The one in use is 111 or higher:** you are fine. You are done, nothing more to do.
- **The one in use is below 111, but a `com.google.android.webview` of 111 or higher is already in the list:** the new WebView is installed, just not in use. You do not need to download anything. Go straight to step 7.
- **Nothing in the list is 111 or higher:** go on to the next step.

!!! example "What we see on the Lango 6760V100 panels"
    The screen uses the built-in `com.android.webview` **91**, while `com.google.android.webview` **155** is installed but not in use. These panels only need step 7.

### 5. Get the new WebView file

**For Lango 6760V100 panels, or any screen with an arm64 processor and Android 10 or newer**, use our ready file. It is Google's own **Android System WebView 155.0.8059.46**, taken from a working panel.

**a. Download it:** [base.apk on Google Drive](https://drive.google.com/file/d/1vRVMlytRjbcQTuFRljyqvoU2nWa7kao6/view?usp=sharing) (260 MB). Google says it cannot scan files this big for viruses. That is normal. Choose **Download anyway**.

**b. Check that the file is complete and untouched:**

=== "Windows"

    ```powershell
    Get-FileHash "$HOME\Downloads\base.apk" -Algorithm SHA256
    ```

=== "Mac"

    ```bash
    shasum -a 256 ~/Downloads/base.apk
    ```

The result must be exactly this:

```text
83fd3471d99b249cc53ded2d44e2d84885ac2dc11bec92c0360a0deda53cb84c
```

If it is different, do not install it. Download it again, or ask the team.

**c. Go to step 6.** The file is called `base.apk`.

**For other screens**, first ask the screen what kind it is, so you download the right file:

```bash
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.build.version.release
```

The first answer (`arm64-v8a` or `armeabi-v7a`) is the screen's processor type. The second is its Android version.

Then download **Android System WebView** that matches both answers, from a source your team trusts, such as APKMirror. Pick the newest version that still supports your screen's Android version. Save the file in your **Downloads** folder.

!!! note
    Some versions also need **Trichrome Library**. If so, install that first, the same way as in step 6. A file ending in `.apkm` or `.xapk` is a bundle. Unzip it first, then install all the `.apk` files inside together with `adb install-multiple -r file1.apk file2.apk`.

### 6. Install the new WebView

Replace `FileName.apk` with the name of the file you downloaded (`base.apk` if you used our ready file).

=== "Windows"

    ```powershell
    adb install -r "$HOME\Downloads\FileName.apk"
    ```

=== "Mac"

    ```bash
    adb install -r ~/Downloads/FileName.apk
    ```

The `-r` keeps the apps and settings already on the screen safe. When it works, you see `Success`.

### 7. Make the new WebView the one in use

```bash
adb shell cmd webviewupdate set-webview-implementation com.google.android.webview
```

If it says the package is not valid, set it on the screen instead: **Settings → Developer options → WebView implementation**, and choose the new one.

### 8. Check again

```bash
adb shell dumpsys webviewupdate
```

The version on the **Current WebView package** line should now be **111 or higher**.

### 9. Restart Marien Player

So the player uses the new WebView:

```bash
adb shell am force-stop com.fortu.player
adb shell monkey -p com.fortu.player -c android.intent.category.LAUNCHER 1
```

### 10. Test with a real website

In Marien CMS, add a **Website** item to a playlist as in [Create a Playlist](creating-a-playlist.md) and play it on this screen. It should look the same as in a normal browser.

!!! tip
    Write the final version in the checklist you hand to the client, for example "WebView 155".

## If something does not work

| What you see | What it means and what to do |
|---|---|
| `adb is not recognized` (Windows) | The laptop does not know adb yet. Close PowerShell and open it again. If it is still the same, use the manual way in step 1. |
| `cannot connect` or `timed out` | The laptop and screen are not on the same network, or the Wi-Fi keeps devices apart (common on guest Wi-Fi). Use the same network. If Windows asks about the firewall, choose **Allow**. |
| `unauthorized` in `adb devices` | The screen has not allowed this laptop yet. Look at the screen and choose **Allow**. |
| `INSTALL_FAILED_VERSION_DOWNGRADE` | The file you downloaded is older than what is installed. Download a newer version. |
| `INSTALL_FAILED_OLDER_SDK` | The file needs a newer Android than your screen has. Download the version that matches the screen's Android. |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | This file cannot replace what is installed. Download the Google version for the same package, or use the other package name in step 7. |
| Version still old after step 8 | The old WebView is still in use. Repeat step 7 through **Developer options**, then restart the screen. |
| Website still looks broken | Open the same website in Chrome on the same screen. If it is broken there too, the problem is the website, not the screen. |
