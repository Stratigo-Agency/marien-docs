# Device Owner lewat Wi-Fi

Halaman ini untuk pengguna yang sudah terbiasa dengan perintah di komputer (command line), atau untuk dibantu tim IT. Device Owner adalah mode yang mengunci perangkat supaya hanya menjalankan Marien Player. Kamu bisa mengaktifkannya di layar yang sudah menjalankan Marien Player, tanpa kabel dan tanpa reset ulang.

1. Pasang adb di komputer (alat untuk mengatur perangkat Android dari komputer): `brew install --cask android-platform-tools`
2. Sambungkan komputer ke perangkat (harus di Wi-Fi yang sama):
    ```bash
    adb connect <device-ip>:5555
    ```
    Kalau gagal (Android 11 ke atas): di perangkat, buka **Developer options → Wireless debugging → Pair device with pairing code**. Lalu jalankan `adb pair <ip>:<port>` dan masukkan kode tadi, kemudian `adb connect` ke alamat yang tampil di layar utama Wireless debugging.
    Android 10 atau yang lebih lama perlu kabel USB satu kali: jalankan `adb tcpip 5555`, lalu cabut kabelnya.
3. Hapus semua akun di perangkat (**Settings → Accounts**). Periksa dengan:
    ```bash
    adb shell dumpsys account | grep "Account {"
    ```
4. Aktifkan Device Owner, lalu mulai ulang aplikasinya:
    ```bash
    adb shell dpm set-device-owner \
      com.fortu.player/com.fortu.player.kiosk.DeviceAdminReceiver
    adb shell am force-stop com.fortu.player
    adb shell monkey -p com.fortu.player -c android.intent.category.LAUNCHER 1
    ```
5. Cek hasilnya: buka diagnostics (tahan sudut kiri atas layar) dan cari tulisan `device owner (full kiosk)`.

!!! note
    Device Owner hanya bisa dilepas dengan factory reset (mengembalikan perangkat ke pengaturan awal).
