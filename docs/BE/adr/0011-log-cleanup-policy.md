# 0011 - Kebijakan pembersihan file log

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna menyetujui paket aturan perilaku awal, jadwal, penerapan perubahan retensi, pembacaan konfigurasi gagal dan batas umur file setelah memilih pengaturan retensi melalui admin.

## Decision

Sebelum retensi ditetapkan admin, log tetap dicatat dan cleanup belum aktif. Setelah ditetapkan valid, cleanup berjalan terjadwal pukul 01.00 WIB setiap hari. Nilai yang berubah berlaku pada jadwal berikutnya; penurunan retensi dijelaskan dampaknya pada halaman admin. Jika konfigurasi gagal dibaca, cleanup dilewati dan kegagalan dicatat.

Retensi N hari mencakup hari ini dan N-1 hari sebelumnya berdasarkan tanggal WIB. Hanya file log aplikasi bertanggal sebelum batas itu yang boleh dihapus; file hari berjalan/aktif dan file di luar cakupan tidak dihapus. Retensi ini tidak berlaku untuk tabel audit.

## Alternatives

Default angka retensi otomatis, penghapusan langsung saat simpan pengaturan dan menggunakan nilai lama saat pembacaan gagal tidak dipilih. Menghitung berdasarkan jumlah file tidak mewakili hari ketika satu hari dapat memiliki beberapa file.

## Consequences

File dapat terus bertambah sebelum admin mengatur retensi. Retensi yang diperkecil dapat menghapus lebih banyak file lama pada jadwal berikutnya; memperbesar retensi tidak mengembalikan file yang sudah dihapus. Rentang nilai, penanganan jadwal terlewat/partial failure, scheduler, koordinasi instance dan opsi menonaktifkan kembali cleanup masih terbuka.

## Validation

Contoh hitungan kalender diverifikasi: 10 November 2026 dengan N=30 mempertahankan tanggal 12 Oktober–10 November. Uji runtime untuk jadwal, batas umur, perubahan konfigurasi, kegagalan pembacaan dan perlindungan file belum dijalankan karena implementasi belum tersedia.

## Keputusan lanjutan — Jadwal terlewat

Pengguna memilih menunggu jadwal 01.00 WIB berikutnya jika aplikasi mati saat jadwal cleanup. Tidak ada cleanup susulan saat startup atau pengejaran setiap run yang terlewat. Run terjadwal berikutnya memakai tanggal WIB dan konfigurasi yang berlaku saat itu. Ini menutup keputusan jadwal terlewat yang sebelumnya terbuka; kegagalan hapus parsial dan mekanisme scheduler masih belum ditetapkan.

Verifikasi saat runtime tersedia: startup setelah melewati satu/beberapa jadwal tidak menghapus file dan run berikutnya menunggu 01.00 WIB.

## Supersedes

Tidak ada; melengkapi aturan penghapusan yang masih terbuka pada ADR 0010.

## Keputusan lanjutan — Kegagalan per file

Pengguna menyetujui pencatatan file yang gagal dihapus beserta penyebabnya, melanjutkan file lain yang memenuhi syarat, serta mencoba kembali pada jadwal hari berikutnya. Kelayakan penghapusan dihitung ulang menggunakan konfigurasi dan keadaan file saat run berikutnya; kegagalan sebelumnya bukan perintah hapus tanpa syarat. Tidak ada retry langsung atau saat startup. Kegagalan per file tidak boleh dilaporkan sebagai sukses penuh.

Verifikasi saat runtime tersedia mencakup satu file gagal di tengah run, file lain tetap diproses, dan perubahan retensi sebelum jadwal berikutnya. Kebijakan ini menutup keputusan kegagalan hapus parsial yang sebelumnya terbuka; mekanisme scheduler dan kegagalan jalur logging masih perlu dipilih.
