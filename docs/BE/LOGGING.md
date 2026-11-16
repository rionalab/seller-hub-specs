# Logging â€” Blueprint lintas proyek

Dokumen ini menentukan tujuan dan kriteria umum, bukan provider atau kebijakan operasional suatu aplikasi. Belum ada implementasi runtime.

## Kemampuan umum

- Catat request, proses bisnis penting, exception, database, keamanan dan operasional yang relevan. Integrasi/background job hanya dicatat jika ada.
- Gunakan structured logging dengan waktu, level, kejadian/operasi, outcome, durasi dan konteks aman yang diperlukan.
- Hubungkan proses dengan audit melalui ID operasi; TraceId membantu menelusuri request/panggilan layanan. Korelasi tidak memberikan izin akses.
- Catat exception lengkap sekali pada boundary penanganan; penolakan bisnis yang diharapkan bukan otomatis exception.
- Pisahkan log diagnostik dari transaksi bisnis agar rollback tidak menghapus jejak kegagalan melalui transaksi yang sama.
- Hindari password, token, cookie, connection string, snapshot audit serta payload penuh otomatis. Periksa pesan exception dan data eksternal.
- Pilih event bermakna, bukan setiap baris kode. Jangan mengklaim pengiriman log terjamin tanpa desain dan pengujian.

## Keputusan per proyek

Saat logging akan diimplementasikan, tetapkan sumber/tujuan penyimpanan, provider, format, lokasi, level/filter, batas volume, timezone, retensi, akses, arsip/penghapusan, korelasi dan perilaku gangguan. Jika admin dapat mengubah konfigurasi, tentukan policy, validasi, penyimpanan, audit serta waktu penerapan. Tidak ada kewajiban universal untuk halaman admin, tabel konfigurasi, WIB atau scheduler tertentu.

Tanyakan hanya keputusan yang menghalangi pekerjaan yang sedang dilakukan. Detail deployment tidak perlu dituntaskan saat merapikan blueprint/agent. Catat hasilnya pada PRODUCT.md dan ADR proyek.

## Contoh profil, bukan default wajib

[Profil file harian dengan admin retensi](reference/LOGGING-FILE-ADMIN-PROFILE.md) menyimpan pilihan dari diskusi sebelumnya. Adopsi profil harus eksplisit; membaca profil tidak mengotorisasi pembuatan UI, schema, provider atau penghapusan file.

## Verifikasi

Uji logging dan redaksi pada boundary yang dibangun, korelasi sampai audit, kegagalan setelah rollback serta gangguan tujuan logging. Bila proyek memilih file, uji rotasi/restart sesuai timezone proyek. Bila retensi diterapkan, uji batas umur/cakupan, konfigurasi dan kegagalannya. Tidak mengharuskan database atau runtime untuk review dokumentasi saja.
