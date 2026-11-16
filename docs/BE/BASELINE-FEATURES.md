# Fitur Dasar Blueprint

Daftar ini berasal dari blueprint lintas proyek dan diadopsi Seller Hub. Status implementasi per kemampuan ada di TODO.md; sebagian fondasi sudah tersedia, fitur bisnis belum. Wajib dipetakan ke backlog/acceptance criteria saat adopsi; detail produk ditetapkan dalam PRODUCT.md dan ADR proyek. Status bersyarat dicatat beserta alasan, bukan diimplementasikan tanpa kebutuhan.

| ID | Kemampuan dasar | Kriteria umum | Detail saat adopsi |
| --- | --- | --- | --- |
| BASE-01 | Kontrak API, validasi, error handling dan OpenAPI | Respons konsisten, status sesuai outcome, tidak membocorkan error internal; pagination bila diperlukan. | Endpoint, DTO, validator dan kompatibilitas. |
| BASE-02 | Identitas dan pemeriksaan akses | Endpoint terlindungi memakai policy; izin objek/data ditegakkan; actor terpercaya; test negatif relevan. | Provider, roles, permissions, anonymous dan kebutuhan tenant. Tidak otomatis membangun manajemen user sendiri. |
| BASE-03 | Audit perubahan data untuk tracing | Perubahan pengguna dan dampak turunannya dapat ditelusuri; actor, waktu, before/after dan korelasi; data/audit konsisten; secrets dikecualikan. Default pola SP dan transaksi mengikuti architecture. | Schema/representasi audit, cakupan proses independen, akses dan retensi. |
| BASE-04 | Logging proses dan kegagalan | Kejadian relevan, durasi/outcome, redaksi, tidak duplikatif; jejak kegagalan terpisah dari transaksi bisnis. | Provider, file/tabel/layanan, format, rotasi, timezone, ukuran dan perilaku gangguan. |
| BASE-05 | Korelasi log dan audit | Tindakan dan perubahan terkait bisa ditelusuri; korelasi bukan bukti authorization. | Identifier dan propagasi sesuai request/job/layanan yang tersedia. |
| BASE-06 | Pengelolaan konfigurasi operasional | Perubahan tervalidasi, berizin dan dapat diaudit jika melalui pengguna. | Parameter, file/database/config service, API atau halaman admin sesuai proyek. UI admin tidak wajib untuk setiap backend. |
| BASE-07 | Siklus hidup data log | Retensi, arsip/penghapusan dan kegagalan memiliki kebijakan eksplisit; tidak menghapus di luar cakupan. | Jadwal, timezone, batas hari/ukuran, kondisi awal, recovery dan siapa yang mengelola. Implementasi mengikuti penyimpanan terpilih. |

## Pelacakan implementasi

Untuk tiap ID, catat cakupan proyek, keputusan yang belum tersedia, penanggung jawab, lokasi kode/kontrak dan bukti verifikasi. Fitur belum selesai jika hanya dokumentasinya tersedia. UI dan backend dilacak terpisah jika keduanya dibutuhkan. Pilih fitur bisnis dari PRODUCT.md; Orders hanyalah ilustrasi.

Dokumen sumber: [Architecture](ARCHITECTURE.md), [Security](SECURITY.md), [API Contract](API-CONTRACT.md), [Logging](LOGGING.md). Daftar ini menjaga kebutuhan umum tetap terlihat, bukan mengunci desain semua proyek.

## Profil referensi

Pembahasan sebelumnya tentang file harian WIB, admin retensi dalam database dan cleanup 01.00 disimpan pada [profil logging](reference/LOGGING-FILE-ADMIN-PROFILE.md). Profil tidak otomatis berlaku: adopsi, ubah atau abaikan detailnya melalui keputusan proyek. Pilihan schema audit fisik, UI, scheduler dan provider ditunda sampai konteks proyek tersedia.

Halaman audit viewer, log viewer, dashboard, alerting, manajemen user/role dan modul contoh tidak menjadi kewajiban tanpa kebutuhan proyek. Menambahkan kemampuan umum yang disetujui harus memperbarui daftar ini, TODO serta sumber terkait.
