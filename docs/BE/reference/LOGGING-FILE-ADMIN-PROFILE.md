# Profil referensi — File log dan konfigurasi admin

Status: contoh pilihan dari diskusi terdahulu, bukan kewajiban semua proyek. Detail di bawah hanya berlaku setelah diadopsi proyek. ADR 0012 mengubah cakupannya menjadi referensi.

# Logging aplikasi

Konsep ini telah disepakati; belum ada implementasi runtime. Logging mencakup proses normal, kejadian penting dan kegagalan, bukan hanya exception.

## Cakupan

| Kategori | Informasi untuk tracing |
| --- | --- |
| Request HTTP | Method, route template, status respons dan durasi; hindari URL/query mentah yang memuat data sensitif. |
| Proses bisnis penting | Operasi dimulai, berhasil atau ditolak dengan alasan aman. Catat sukses setelah hasil/commit diketahui. |
| Exception | Operasi, tipe exception dan detail/stack trace internal yang diperiksa terhadap kebocoran data sensitif. |
| Database | Identitas SP, durasi, kegagalan atau kelambatan; jangan menyalin semua parameter/result set. |
| Integrasi | Layanan/operasi tujuan, durasi, hasil, timeout dan retry tanpa credentials atau payload privat. |
| Keamanan | Login gagal dan akses ditolak; identitas hanya bila tersedia dan diperlukan. |
| Background job | Mulai, selesai, gagal dan retry serta kaitan dengan tindakan asal bila ada. |
| Operasional | Startup/shutdown dan konfigurasi penting tidak valid, tanpa nilai secrets. |

Pilih kejadian yang membantu menjelaskan proses; jangan mencatat setiap baris kode atau seluruh payload. Penolakan bisnis yang diharapkan bukan otomatis exception.

## Structured logging dan korelasi

Simpan waktu UTC, level, nama kejadian/operasi, service/environment dan konteks sebagai properti yang dapat dicari. Tambahkan durasi dan hasil/status ketika relevan. Nama field final dan konfigurasi level mengikuti keputusan implementasi.

- ID operasi menghubungkan log dengan audit perubahan data dari satu tindakan pengguna. ID ini bukan bukti authorization atau jaminan idempotency.
- TraceId menghubungkan eksekusi request/pemanggilan layanan. Proses lanjutan dapat memiliki trace berbeda, tetapi tetap mempertahankan hubungan ke ID operasi asal bila relevan.
- Identitas berasal dari server yang tervalidasi. Correlation ID tidak membuat actor/tenant dari client dapat dipercaya.
- Catat exception lengkap sekali pada boundary yang menanganinya. Jangan menggandakan stack trace exception yang sama pada tiap lapisan; kejadian retry yang berbeda tetap dapat dicatat sesuai kebutuhan.

Contoh ilustratif: request pembatalan diterima, operasi ditolak karena status pesanan, request selesai dengan status sesuai kontrak dan durasi. Semua dapat ditelusuri bersama tanpa menganggap penolakan sebagai exception tak terduga.

## Hubungan dengan audit

Audit menjelaskan perubahan data; log menjelaskan proses, termasuk percobaan gagal atau ditolak. Audit melalui SP tetap satu transaksi dengan data bisnis. Log diagnostik terpisah dari transaksi tersebut agar rollback bisnis tidak dengan sendirinya menghapus jejak kegagalan.

Operasi yang dibatalkan tidak boleh menghasilkan audit yang mengklaim perubahan berhasil. Kegagalannya dicatat lewat logging aplikasi. Pemisahan ini tidak menjamin pengiriman log selalu berhasil: buffer, retry, kehilangan log dan dampak gangguan tujuan logging masih perlu diputuskan.

## Perlindungan data

Jangan mencatat password, token, cookie, connection string, snapshot audit atau seluruh request/response secara otomatis. Pilih metadata aman dan minimum; periksa juga pesan exception serta data layanan eksternal. Stack trace tidak dikirim ke client. Akses dan retensi log harus diputuskan sebelum penggunaan produksi.

## Penyimpanan file harian

Log aplikasi disimpan ke file saja, bukan tabel database. Pisahkan file menurut hari agar catatan untuk tanggal tertentu mudah ditemukan. Contoh ilustratif: `application-2026-11-10.log` dan `application-2026-11-11.log`; nama final dan direktori belum dipilih. Audit perubahan data tetap tersimpan dalam tabel audit melalui SP, sesuai keputusan terpisah.

Pergantian file dan tanggal pada nama file mengikuti WIB (UTC+7), dengan batas hari pukul 00.00 WIB. Konfigurasikan secara eksplisit, jangan bergantung pada zona waktu mesin. Timestamp setiap kejadian tetap UTC sesuai konsep logging; tanggal file menunjukkan hari WIB. Contoh: 10 November pukul 17.00 UTC sudah masuk file tanggal 11 November WIB. Jika aplikasi restart pada hari yang sama, jangan menimpa atau mengosongkan log yang sudah ada.

Rotasi harian tidak otomatis membatasi ukuran total atau menghapus file lama. Lama penyimpanan diatur melalui konfigurasi admin sesuai bagian berikut. Batas ukuran, format file dan penanganan beberapa instance masih perlu ditetapkan. Pemilihan provider harus mendukung keputusan file harian ini. Lihat [ADR 0009](../adr/0009-daily-file-logging.md).

## Retensi melalui konfigurasi admin

Admin yang berwenang mengatur lama penyimpanan file log dalam satuan hari melalui halaman konfigurasi admin aplikasi. Nilai aktif disimpan di database dan dibaca/diubah melalui repository Dapper dan SP. Halaman admin tidak mengedit appsettings.json atau web.config. Pengaturan ini berlaku untuk file log aplikasi, bukan otomatis untuk tabel audit bisnis.

Perubahan konfigurasi adalah interaksi pengguna yang mengubah data, sehingga perubahan nilai dan audit sebelum/sesudah, actor serta ID operasi disimpan atomik melalui SP. Pemeriksaan izin dan validasi nilai dilakukan di server; nama role/policy serta schema tabel konfigurasi belum dipilih. Isi log tetap disimpan di file; hanya nilai pengaturannya yang berada di database.

Konfigurasi teknis seperti direktori dan provider logging dikelola melalui appsettings.json atau environment variables; web.config digunakan untuk hosting IIS bila dipakai. Pengaturan tersebut tidak otomatis menjadi field yang dapat diedit melalui halaman admin. Nilai retensi aktif tidak memiliki sumber edit kedua di file konfigurasi. Detail refresh/cache dan koordinasi antar-instance mengikuti kebijakan cleanup berikut; mekanisme implementasinya belum dipilih.

Saat admin belum menetapkan retensi, logging tetap berjalan dan penghapusan otomatis belum aktif. Jangan memasang angka retensi default yang mengaktifkan cleanup diam-diam. Retensi yang ditetapkan harus berupa bilangan bulat minimal 1 hari, tanpa batas maksimum bisnis. Tolak nol, negatif dan pecahan melalui validasi server; jangan menambahkan batas seperti 365 hari tanpa keputusan pengguna. Nilai harus dapat direpresentasikan secara konsisten oleh API, aplikasi dan database; jangan membulatkan, memotong atau menyebabkan overflow pada input besar. Pemilihan tipe fisik tetap bagian desain schema.

Perhitungan kelayakan file harus aman untuk retensi besar, misalnya membandingkan selisih tanggal harian dengan N, bukan selalu mengurangi N dari tanggal sekarang hingga keluar dari rentang tanggal platform. Retensi tanpa maksimum bisnis bukan berarti kapasitas penyimpanan file tidak terbatas; opsi menonaktifkan cleanup tetap terpisah dari angka retensi.

Halaman admin menyediakan pilihan Nonaktifkan penghapusan otomatis. Status aktif/nonaktif dinyatakan secara eksplisit, bukan menggunakan angka nol sebagai kode khusus. Ketika nonaktif, log tetap dicatat dan dirotasi harian, tetapi run cleanup tidak menghapus file. Mengaktifkan kembali memerlukan retensi valid dan tidak memicu penghapusan langsung; perubahan mengikuti jadwal berikutnya sesuai kebijakan yang berlaku. Perubahan status juga disimpan dan diaudit seperti perubahan nilai retensi. Lihat [ADR 0010](../adr/0010-admin-configurable-log-retention.md).

## Kebijakan pembersihan file log

- Setelah admin menetapkan retensi yang valid, cleanup dijadwalkan setiap hari pukul 01.00 WIB.
- Perubahan retensi dipakai pada jadwal cleanup berikutnya, bukan memicu penghapusan saat tombol simpan ditekan. Halaman admin harus menjelaskan dampak penurunan retensi terhadap file lama.
- Pada tiap run, baca nilai yang berlaku dari sumber konfigurasi database. Jika pembacaan gagal, lewati cleanup dan catat kegagalannya; jangan menggunakan nilai cache lama atau default untuk tetap menghapus file.
- Retensi N hari menyimpan hari ini dan N-1 hari sebelumnya menurut tanggal WIB. Batas tanggal yang disimpan adalah `tanggalHariIniWib - (N - 1)`; hanya file dengan tanggal harian lebih awal dari batas tersebut yang memenuhi syarat dihapus. Jangan menghitung dari jumlah file atau waktu terakhir file disalin/diubah.
- File hari berjalan tidak dihapus. Cleanup hanya menyentuh file log aplikasi yang dikenali dalam direktori terkonfigurasi; bukan tabel audit atau file lain. File yang masih aktif tidak dihapus.
- Contoh: pada 10 November 2026, retensi 30 hari menyimpan file bertanggal 12 Oktober sampai 10 November; tanggal 11 Oktober dan sebelumnya memenuhi batas umur penghapusan.

Jika aplikasi tidak aktif saat jadwal cleanup, lewati jadwal tersebut dan tunggu pukul 01.00 WIB berikutnya setelah aplikasi kembali aktif. Jangan menjalankan cleanup susulan saat startup atau mengejar setiap jadwal yang terlewat. Pada run terjadwal berikutnya, gunakan tanggal WIB saat itu dan konfigurasi terbaru; file lama yang sudah memenuhi batas retensi dapat dibersihkan pada run tersebut.

Jika satu file yang memenuhi syarat gagal dihapus, catat identitas file yang aman dan penyebab kegagalannya, lalu lanjutkan file lain yang memenuhi syarat. Jangan menganggap run berhasil penuh bila ada kegagalan. Tidak ada retry penghapusan langsung atau saat startup; file yang gagal dipertimbangkan kembali pada jadwal 01.00 WIB berikutnya berdasarkan konfigurasi, tanggal dan status file saat itu. Jika tidak lagi memenuhi syarat, jangan hapus hanya karena sebelumnya gagal.

Koordinasi antar-instance dan arsip masih terbuka. Kebijakan melanjutkan file lain berlaku untuk kegagalan per file, bukan alasan meneruskan cleanup jika konfigurasi gagal dibaca atau cakupan direktori tidak dapat dipastikan. Keputusan kebijakan ini tercatat pada [ADR 0011](../adr/0011-log-cleanup-policy.md); belum ada scheduler atau penghapusan yang dijalankan.

## Keputusan implementasi yang masih terbuka

- Library/provider, direktori development/produksi dan format file.
- Nama file, batas ukuran per file dan pemisahan file antar-instance bila diperlukan; zona waktu rotasi sudah ditetapkan WIB.
- Level, filter, sampling, batas volume dan ambang operasi lambat.
- Nama/tipe field final, propagasi korelasi antar-request/job dan redaksi sesuai data produk.
- Detail retensi admin: tipe representasi nilai; scheduler, koordinasi antar-instance dan arsip; pihak yang boleh membaca log serta hak mengubah konfigurasi. Minimum 1 hari tanpa maksimum bisnis, opsi menonaktifkan cleanup, perilaku awal, jadwal/cutoff, waktu penerapan perubahan, jadwal terlewat dan kegagalan hapus parsial sudah disepakati.
- Perilaku saat tujuan log gagal: buffer/retry, sinyal gangguan dan kategori yang perlu memblokir operasi, jika ada.

Jangan memilih opsi ini diam-diam atau menyamakan kebijakan kegagalan log dengan audit yang sudah disepakati.

## Verifikasi saat runtime tersedia

Telusuri satu tindakan dari request sampai SP/integrasi dan audit yang committed. Periksa log penolakan tanpa perubahan data, log error setelah rollback, tidak ada duplikasi exception/secrets dan perilaku gangguan tujuan log sesuai keputusan. Saat ini hanya konsistensi dokumentasi yang diperiksa.

Untuk file harian, uji batas 23.59.59 WIB ke 00.00 WIB dan restart pada hari yang sama tanpa kehilangan catatan, termasuk ketika zona waktu mesin berbeda. Uji juga direktori tidak dapat ditulis atau media penuh sesuai kebijakan gangguan yang nantinya disepakati.

Untuk konfigurasi retensi, uji izin admin, penolakan nilai tidak valid, persistensi dan audit perubahan nilai. Uji kondisi belum diatur tanpa penghapusan, jadwal 01.00 WIB, batas tanggal N hari, perubahan nilai yang berlaku di jadwal berikutnya, serta pembacaan konfigurasi gagal tanpa penghapusan. Pastikan file aktif/hari berjalan dan file di luar cakupan tidak terhapus. Uji rentang tanggal melewati pergantian bulan/tahun setelah runtime tersedia.

Simulasikan aplikasi mati melewati satu atau beberapa jadwal: startup tidak menghapus file; cleanup berikutnya baru berjalan pada 01.00 WIB dengan konfigurasi dan tanggal run saat itu.

Simulasikan satu file gagal dihapus: kegagalan dicatat, file lain yang memenuhi syarat tetap diproses, dan tidak ada retry langsung. Pada jadwal berikutnya periksa kembali kelayakan file, termasuk saat admin memperbesar retensi sehingga file yang sebelumnya gagal kini harus disimpan. Perilaku ketika penulisan log kegagalan itu sendiri gagal mengikuti keputusan gangguan logging yang masih terbuka.

Uji retensi 1 hari hanya mempertahankan tanggal hari ini, penolakan nol/negatif/pecahan, penonaktifan cleanup tanpa menghentikan logging/rotasi, audit perubahan status, serta pengaktifan kembali yang menunggu jadwal berikutnya dengan retensi valid.

Uji nilai retensi besar yang didukung tipe terpilih tanpa batas bisnis buatan; pastikan tidak ada pembulatan, overflow atau penghapusan akibat kesalahan hitung tanggal. Nilai yang tidak dapat direpresentasikan harus menghasilkan validasi yang jelas tanpa mengubah konfigurasi lama.
