# Siapkan Perangkat

Pilih salah satu player. Keduanya berakhir di sebuah kode pairing (kode untuk menyambungkan layar ke akunmu). Setelah itu, [hubungkan layarnya](connecting-a-screen.md).

| | Cocok untuk | Cara pasang |
|---|---|---|
| [Web player](#web-player) | Smart TV (Samsung, LG), semua browser | Tidak perlu pasang apa pun |
| [Android player](#android-player) | TV box, stick, dan tablet Android | Pasang aplikasinya. Bonusnya: update otomatis tanpa suara, layar mati sesuai jadwal, dan aplikasi terkunci agar tidak bisa ditutup. |

![Kode pairing di layar Marien](images/pairing/player-pairing-code.png){ width="300" }

## Web player

1. Buka **https://player.marien.co.id** di browser TV.
2. Tekan **OK** di remote satu kali untuk menyembunyikan bilah browser.
3. Jadikan alamat itu sebagai halaman beranda (home page) browser, dan matikan screensaver TV.

!!! note
    Video akan tetap tanpa suara sampai kamu mengizinkan autoplay dengan suara di pengaturan browser.

## Android player

1. Di perangkatnya: buka **Play Store** → ikon profil → **Play Protect** → ikon roda gigi → matikan **Scan apps with Play Protect**.
2. Pasang aplikasinya. Pilih salah satu cara:
    - **Langsung di perangkat:** buka [Versi Player](player-releases.md) di browser perangkat, ketuk **Download**, buka file yang terunduh, lalu izinkan pemasangan dari sumber tidak dikenal (unknown sources).
    - **Dari komputer (USB debugging harus aktif):**
      ```bash
      curl -L -o marien-player.apk https://api.marien.co.id/player/download
      adb install -r marien-player.apk
      ```
3. Buka **Marien Player**. Layar akan menampilkan kode pairing.

Supaya aplikasi tidak bisa ditutup oleh siapa pun, lihat [Device Owner lewat Wi-Fi](remote-device-owner-setup.md).
