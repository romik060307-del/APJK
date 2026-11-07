# OHLI Social — Android Client v1.0.7

Proyek ini membuat **APK klien OHLI Social**. APK membuka website/server OHLI yang sudah berjalan melalui Android WebView. **Server tidak dibundel, tidak dijalankan, dan tidak diubah oleh proyek ini.**

## Yang sudah disiapkan

- Nama aplikasi: **OHLI Social**.
- Membuka alamat OHLI langsung di dalam aplikasi, bukan melempar halaman utama ke Chrome.
- Splash screen saat aplikasi menghubungkan ke server.
- JavaScript, DOM storage, cookie, dan cache WebView untuk kebutuhan aplikasi web.
- Izin kamera dan mikrofon diminta sesuai kebutuhan, dan hanya untuk origin HTTPS OHLI yang dikonfigurasi.
- Pemilih berkas dari Android untuk upload foto/video/dokumen yang diminta website.
- Tombol kembali Android mengikuti riwayat halaman; ada layar coba lagi ketika koneksi gagal.
- Tautan HTTPS di luar domain OHLI dibuka dengan aplikasi eksternal; skema berisiko diblokir.

## Alamat server

Alamat saat ini disetel di `app/build.gradle` melalui `OHLI_URL`:

`https://cartoon-advisory-stories-marion.trycloudflare.com/`

Bila alamat tunnel berubah, ubah nilai `OHLI_URL` sebelum build. Tunnel Cloudflare quick-tunnel harus tetap aktif agar teman-teman bisa membuka server dari internet. Untuk pemakaian publik jangka panjang, pakai hostname/domain tetap.

## Pemeriksaan kestabilan klien

- Jika proses WebView Android berhenti/crash, aplikasi mencoba membuat ulang WebView satu kali; bila gagal lagi, aplikasi menampilkan layar pemulihan agar tidak terus-menerus crash-loop.
- Kegagalan memuat halaman utama tidak ditimpa oleh callback selesai-muat halaman error; tombol **Coba lagi** memulai pemuatan ulang.
- Callback upload dibersihkan dengan aman saat pemilih file gagal atau Activity ditutup.

## Membuat APK melalui GitHub Actions

1. Buat repository GitHub baru.
2. Upload seluruh isi folder proyek ini (folder `app`, `.github`, dan file Gradle) ke root repository.
3. Buka tab **Actions**.
4. Pilih workflow **Build OHLI Social Client APK** lalu tekan **Run workflow**.
5. Setelah workflow selesai, buka hasil run dan unduh artifact **OHLI-Social-Client-APK**.
6. Ekstrak artifact ZIP; file yang dipasang di Android adalah `app-debug.apk`.

Workflow menghasilkan **debug APK** untuk uji coba dan pemasangan langsung. Untuk rilis Play Store atau distribusi release resmi, perlu konfigurasi signing key release yang disimpan sebagai GitHub Secrets.

## Batas fitur yang perlu diketahui

APK ini adalah pembungkus klien Android, bukan implementasi ulang semua fitur secara native. Chat, Story, Reels, Feed, musik, login, dan data tetap berasal dari website/backend OHLI yang ada. Integrasi kamera/mikrofon dan pemilih file disediakan dari sisi Android, tetapi keberhasilan fitur tetap bergantung pada implementasi website, HTTPS, browser engine Android System WebView, dan server.

- Cache WebView membantu memuat ulang sebagian aset, tetapi **bukan** database chat offline dan tidak membuat pesan otomatis tersinkron ketika internet kembali.
- Izin notifikasi Android saja tidak menghasilkan push notification. Push chat/panggilan/follow memerlukan layanan push (misalnya FCM), token perangkat, dan integrasi event di backend. Itu belum ditambahkan agar server tetap tidak diubah.
- Autoplay dengan suara dapat dibatasi kebijakan Android/WebView dan interaksi pengguna; pengaturan aplikasi tidak bisa menjamin semua perangkat mengizinkannya.
- Server dan tunnel harus aktif dan dapat dijangkau. APK tidak membawa salinan server.

## Persyaratan build

- JDK 17
- Android SDK Platform 35 dan Build Tools 35.0.0
- Gradle 8.9
- Java compatibility: `JavaVersion.VERSION_1_8` (kompatibel dengan Gradle 8.9; jangan gunakan `JavaVersion.VERSION_8`)


## Versi 1.0.4 — tombol Kembali Android

Tombol/gesture Kembali bawaan Android sekarang diproses lebih dulu oleh navigasi OHLI Social: menutup Story atau dialog, kembali ke halaman OHLI sebelumnya (termasuk halaman yang dibuka dari navigasi bawah), lalu kembali dari area Chat/Kontak/Panggilan ke OHLI Social. Jika sudah di halaman utama, aplikasi meminta konfirmasi sebelum keluar. Tombol panah kembali di dalam halaman tidak perlu dipakai untuk navigasi normal.

Catatan: perilaku pada setiap layar perlu diuji pada APK hasil build dan perangkat Android; layar khusus yang tidak memakai riwayat navigasi OHLI atau tombol kembali legacy mungkin tetap memerlukan penyesuaian.


## v1.0.5 fixes
- Android system Back closes Story/dialogs and returns from OHLI Social tabs to Home before asking to exit.
- WebView geolocation permission is handled for the trusted OHLI HTTPS origin with Android runtime permission prompts.
- Added MODIFY_AUDIO_SETTINGS plus camera/microphone permission handling for WebRTC calls.
- Audio/video capture still depends on Android WebView version, device microphone availability, and the web call service; verify on a real device.


## v1.0.6 — Long-press photo crash guard
- Consumes Android WebView's native long-press context menu only when the hit target is an image (or an image link).
- Prevents the browser's default context-menu and drag-start actions on image/media elements while preserving the site's ordinary touch gestures.
- Keeps the OHLI server URL and backend unchanged.

This is a defensive client-side mitigation. It must still be built and tested on the affected Android device to confirm whether the crash is resolved.


## v1.0.7 — stabilitas foto dan navigasi di dalam APK
- Menonaktifkan menu tekan-lama bawaan Android WebView yang dapat memicu masalah pada gambar berukuran besar di perangkat/WebView lama. Event sentuh halaman tetap diteruskan agar gestur milik OHLI tetap dapat bekerja.
- Menonaktifkan menu konteks dan drag bawaan browser pada foto/video/canvas melalui pengaman halaman.
- Tautan HTTPS dari tombol/fitur OHLI sekarang dinavigasikan pada WebView yang sama, termasuk tautan `target="_blank"` dan pemanggilan `window.open`, bukan dilempar ke Chrome. Aksi `tel:` dan `mailto:` tetap memakai aplikasi Android yang sesuai.
- Mengganti alamat server APK ke tunnel terbaru yang diberikan: `https://cartoon-advisory-stories-marion.trycloudflare.com/`.
- Menaikkan versi APK menjadi 1.0.7 (versionCode 8).

Catatan pengujian: URL tunnel sudah dipasang di konfigurasi, tetapi status halaman dan login server perlu diuji dari perangkat karena tunnel Cloudflare dapat berubah atau menerapkan pemeriksaan akses.
