# Perbarui WebView

**WebView** adalah bagian di layar Android yang menampilkan website. Kalau WebView-nya terlalu lama, website baru bisa tampil berantakan. Halaman ini menunjukkan cara memperbaruinya dari laptop lewat Wi-Fi, tanpa Play Store dan tanpa memasukkan akun Google di layar.

Lakukan ini untuk setiap layar Android **sebelum dikirim ke client**, kalau layar itu akan menampilkan website.

## Kenapa penting

Website yang dibuat dengan teknologi baru (misalnya Tailwind v4) butuh **WebView versi 111 ke atas**. Kalau WebView-nya lebih lama, tampilannya seperti tanpa desain: gambar membesar, tata letak berantakan, dan ada kotak abu-abu menutupi layar. Website yang sama tampil normal di WebView 155.

Website yang sama di layar yang sama:

**WebView 91 (terlalu lama)**

![Website rusak: gambar membesar dan ada kotak abu-abu di layar](images/webview/01-webview-91-broken.png)

**WebView 155 (aman)**

![Website yang sama tampil rapi](images/webview/02-webview-155-ok.png)

!!! warning "Satu layar bisa punya dua WebView"
    Beberapa layar punya WebView lama dan baru sekaligus. Memasang yang baru saja belum cukup. Kamu juga harus memastikan **yang baru yang dipakai** (langkah 7).

## Yang perlu disiapkan

- Laptop dan layar yang tersambung ke **Wi-Fi atau jaringan yang sama**.
- Waktu sekitar 15 menit untuk layar pertama. Layar berikutnya jauh lebih cepat.

## Langkah-langkah

### 1. Pasang adb di laptop

adb adalah alat kecil yang membuat laptop bisa mengatur layar Android. Cukup dipasang sekali per laptop.

=== "Windows"

    1. Klik tombol **Start**, ketik `PowerShell`, lalu tekan **Enter**. Jendela berwarna biru akan terbuka. Di sinilah kamu mengetik perintah.
    2. Ketik perintah ini, lalu tekan **Enter**:
        ```powershell
        winget install Google.PlatformTools
        ```
    3. Kalau muncul pertanyaan untuk menyetujui, ketik `Y` lalu **Enter**.
    4. **Tutup PowerShell, lalu buka lagi.** Ini penting supaya laptop mengenali adb.
    5. Cek berhasil atau tidak:
        ```powershell
        adb version
        ```
        Kalau muncul tulisan `Android Debug Bridge version ...`, adb sudah siap.

    !!! note "Kalau `winget` tidak dikenali"
        Unduh **SDK Platform-Tools for Windows** dari situs resmi Android Developers, lalu ekstrak ke `C:\platform-tools`. Buka PowerShell, ketik `cd C:\platform-tools`, dan tulis `.\adb` (pakai titik dan garis miring) di setiap perintah `adb` di halaman ini.

=== "Mac"

    1. Buka aplikasi **Terminal** (tekan `Cmd + Spasi`, ketik `Terminal`, lalu **Enter**).
    2. Ketik perintah ini, lalu tekan **Enter**:
        ```bash
        brew install --cask android-platform-tools
        ```
    3. Cek berhasil atau tidak:
        ```bash
        adb version
        ```

### 2. Hidupkan Wi-Fi debugging di layar

Ini membuat layar mau diatur dari laptop. Nama menu bisa sedikit berbeda di tiap merek.

1. Di layar, buka **Settings → About** (atau **About device**).
2. Cari **Build number**, lalu **ketuk 7 kali** sampai muncul tulisan "You are now a developer".
3. Kembali ke **Settings**, lalu buka **Developer options**.
4. Hidupkan **USB debugging**.
5. Cari **Wireless debugging** dan hidupkan.
    - **Ada menunya?** Layarmu Android 11 ke atas. Di langkah 3, ikuti bagian **Android 11 ke atas**.
    - **Tidak ada?** Layarmu Android 10 atau lebih lama. Di langkah 3, ikuti bagian **Android 10 atau lebih lama**.
6. Catat **alamat IP** layar di **Settings → Network** (angkanya seperti `10.200.0.44`). Kalau tidak ketemu, alamat itu juga tampil di layar Wireless debugging.

### 3. Sambungkan laptop ke layar

=== "Android 11 ke atas"

    Kali pertama, laptop dan layar harus "dipasangkan" dulu dengan kode.

    1. Di layar, buka **Developer options → Wireless debugging → Pair device with pairing code**. Akan muncul **alamat dengan port** (misalnya `10.200.0.44:37123`) dan **kode 6 angka**. Biarkan layar ini terbuka.
    2. Di PowerShell (atau Terminal di Mac), ketik perintah ini dengan alamat dari layar, lalu **Enter**:
        ```bash
        adb pair 10.200.0.44:37123
        ```
    3. Saat diminta, ketik **kode 6 angka** tadi, lalu **Enter**. Hasilnya harus `Successfully paired`.
    4. Kembali ke layar utama **Wireless debugging**. Di sana ada alamat IP dan port **yang berbeda** dari tadi (misalnya `10.200.0.44:41234`). Sambungkan ke alamat itu:
        ```bash
        adb connect 10.200.0.44:41234
        ```

=== "Android 10 atau lebih lama"

    Layar ini perlu disambungkan dengan **kabel USB satu kali** saja.

    1. Colok kabel USB dari laptop ke layar. Pakai kabel yang mendukung data, bukan kabel pengisi daya saja.
    2. Di layar, kalau muncul pertanyaan **Allow USB debugging?**, centang **Always allow**, lalu pilih **OK**.
    3. Di PowerShell (atau Terminal di Mac), ketik:
        ```bash
        adb tcpip 5555
        ```
    4. Cabut kabelnya, lalu sambungkan lewat Wi-Fi dengan alamat IP layar:
        ```bash
        adb connect 10.200.0.44:5555
        ```

Setelah itu, cek apakah layar sudah tersambung:

```bash
adb devices
```

Di daftar harus ada layarmu dengan tulisan **device** di sebelahnya. Kalau tertulis `unauthorized`, lihat layar dan pilih **Allow**.

### 4. Cek versi WebView di layar

```bash
adb shell dumpsys webviewupdate
```

Cari baris **Current WebView package**. Isinya nama dan versi, misalnya `com.google.android.webview, 91.0.4472.114`.

Lihat juga baris **Valid package**. Baris-baris itu mendaftar semua WebView yang ada di layar, lengkap dengan versinya.

- **Yang dipakai sudah 111 atau lebih tinggi:** sudah aman. Kamu selesai, tidak perlu apa-apa lagi.
- **Yang dipakai di bawah 111, tapi di daftar sudah ada `com.google.android.webview` versi 111 ke atas:** WebView barunya sudah terpasang, hanya belum dipakai. Kamu tidak perlu mengunduh apa-apa. Langsung ke langkah 7.
- **Tidak ada yang 111 ke atas di daftar:** lanjut ke langkah berikutnya.

!!! example "Yang kami temukan di panel Lango 6760V100"
    Layar memakai `com.android.webview` bawaan versi **91**, sementara `com.google.android.webview` versi **155** sudah terpasang tapi tidak dipakai. Panel seperti ini cukup mengikuti langkah 7.

### 5. Siapkan file WebView yang baru

**Untuk panel Lango 6760V100, atau layar dengan prosesor arm64 dan Android 10 ke atas**, pakai file siap pakai dari kami. Ini **Android System WebView 155.0.8059.46** buatan Google sendiri, diambil dari panel yang berjalan normal.

**a. Unduh:** [base.apk di Google Drive](https://drive.google.com/file/d/1vRVMlytRjbcQTuFRljyqvoU2nWa7kao6/view?usp=sharing) (260 MB). Google bilang file sebesar ini tidak bisa dipindai virus. Itu normal. Pilih **Download anyway**.

**b. Pastikan filenya utuh dan tidak diubah:**

=== "Windows"

    ```powershell
    Get-FileHash "$HOME\Downloads\base.apk" -Algorithm SHA256
    ```

=== "Mac"

    ```bash
    shasum -a 256 ~/Downloads/base.apk
    ```

Hasilnya harus sama persis dengan ini:

```text
83fd3471d99b249cc53ded2d44e2d84885ac2dc11bec92c0360a0deda53cb84c
```

Kalau berbeda, jangan dipasang. Unduh ulang, atau tanya tim.

**c. Lanjut ke langkah 6.** Nama filenya `base.apk`.

**Untuk layar lain**, tanyakan dulu ke layar jenisnya supaya kamu mengunduh file yang cocok:

```bash
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.build.version.release
```

Jawaban pertama (`arm64-v8a` atau `armeabi-v7a`) adalah jenis prosesor layar. Jawaban kedua adalah versi Android.

Lalu unduh **Android System WebView** yang cocok dengan kedua jawaban itu, dari sumber yang dipercaya tim, misalnya APKMirror. Pilih versi terbaru yang masih mendukung versi Android layarmu. Simpan filenya di folder **Downloads**.

!!! note
    Sebagian versi juga butuh **Trichrome Library**. Kalau begitu, pasang itu lebih dulu dengan cara yang sama seperti di langkah 6. File berakhiran `.apkm` atau `.xapk` adalah paket gabungan. Ekstrak dulu, lalu pasang semua file `.apk` di dalamnya sekaligus dengan `adb install-multiple -r file1.apk file2.apk`.

### 6. Pasang WebView yang baru

Ganti `NamaFile.apk` dengan nama file yang kamu unduh (`base.apk` kalau kamu memakai file siap pakai dari kami).

=== "Windows"

    ```powershell
    adb install -r "$HOME\Downloads\NamaFile.apk"
    ```

=== "Mac"

    ```bash
    adb install -r ~/Downloads/NamaFile.apk
    ```

Tanda `-r` membuat aplikasi dan pengaturan yang sudah ada di layar tetap aman. Kalau berhasil, muncul tulisan `Success`.

### 7. Jadikan WebView yang baru sebagai yang dipakai

```bash
adb shell cmd webviewupdate set-webview-implementation com.google.android.webview
```

Kalau muncul pesan bahwa paketnya tidak valid, atur langsung di layar: **Settings → Developer options → WebView implementation**, lalu pilih yang baru.

### 8. Cek lagi

```bash
adb shell dumpsys webviewupdate
```

Versi di baris **Current WebView package** sekarang harus **111 atau lebih tinggi**.

### 9. Mulai ulang Marien Player

Supaya player memakai WebView yang baru:

```bash
adb shell am force-stop com.fortu.player
adb shell monkey -p com.fortu.player -c android.intent.category.LAUNCHER 1
```

### 10. Tes dengan website sungguhan

Di Marien CMS, tambahkan item **Website** ke playlist seperti di [Buat Playlist](creating-a-playlist.md), lalu putar di layar ini. Tampilannya harus sama seperti saat dibuka di browser biasa.

!!! tip
    Catat versi akhirnya di daftar periksa yang diserahkan ke client, misalnya "WebView 155".

## Kalau ada yang tidak berhasil

| Yang kamu lihat | Artinya dan yang dilakukan |
|---|---|
| `adb is not recognized` (Windows) | Laptop belum mengenali adb. Tutup PowerShell dan buka lagi. Kalau masih sama, ikuti cara manual di langkah 1. |
| `cannot connect` atau `timed out` | Laptop dan layar belum di jaringan yang sama, atau Wi-Fi-nya memisahkan perangkat (sering terjadi di Wi-Fi tamu). Samakan jaringannya. Kalau Windows menampilkan pertanyaan firewall, pilih **Allow**. |
| `unauthorized` di `adb devices` | Layar belum mengizinkan laptop ini. Lihat layar dan pilih **Allow**. |
| `INSTALL_FAILED_VERSION_DOWNGRADE` | File yang kamu unduh lebih lama dari yang sudah terpasang. Unduh versi yang lebih baru. |
| `INSTALL_FAILED_OLDER_SDK` | File itu butuh Android yang lebih baru dari layarmu. Unduh versi yang cocok dengan Android layar. |
| `INSTALL_FAILED_UPDATE_INCOMPATIBLE` | File ini tidak cocok untuk menggantikan yang terpasang. Unduh versi buatan Google untuk paket yang sama, atau pakai nama paket yang satunya di langkah 7. |
| Versi masih lama setelah langkah 8 | WebView lama masih dipakai. Ulangi langkah 7 lewat **Developer options**, lalu nyalakan ulang layarnya. |
| Website masih rusak | Buka website yang sama di Chrome pada layar yang sama. Kalau di sana juga rusak, masalahnya ada di websitenya, bukan di layar. |
