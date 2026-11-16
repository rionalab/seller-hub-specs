# 0002 - Penamaan database dan script migrasi

Status: Accepted
Tanggal: 2026-11-10

## Context

Starter memakai SQL Server dan Dapper/SP, tetapi sebelumnya hanya mensyaratkan script berversi tanpa konvensi penamaan atau urutan. Pengguna menyetujui usulan STR-04: dbo, tabel plural, SP resource + operasi, dan script berurutan yang tidak diubah setelah diterapkan.

## Decision

Gunakan schema awal dbo, tabel plural PascalCase seperti dbo.Orders, serta SP seperti dbo.Orders_Create dan dbo.Orders_GetById. Script berada di database/migrations/ dengan nomor urut unik dan deskripsi kebab-case, misalnya 0001-create-orders.sql. Terapkan sesuai urutan dan dependency. Script yang sudah diterapkan tetap utuh; perubahan selanjutnya memakai script baru, termasuk perubahan SP.

Tool migrasi dan mekanisme riwayat penerapan ditunda sampai bootstrap. Dokumentasikan kompatibilitas API/SP dan pemulihan setiap perubahan. Setelah kegagalan, periksa keadaan database sebelum melanjutkan atau mengulang penerapan.

## Alternatives

Mengedit script yang sudah diterapkan tidak dipilih karena isi versi yang sama akan berbeda antara lingkungan. Schema per modul atau konvensi nama lain tidak menjadi default; perubahan dari keputusan ini perlu pembahasan sesuai kebutuhan proyek.

## Consequences

Penamaan dan urutan perubahan dapat ditelusuri. Riwayat script bertambah untuk tiap perubahan SP, tetapi perubahan yang sudah diterapkan tetap dapat diperiksa. Nomor urut harus unik saat pekerjaan digabungkan. Konvensi file sendiri belum menyediakan migrasi otomatis atau rollback otomatis.

## Validation

Periksa konsistensi ARCHITECTURE.md, panduan database dan skill stored-procedure serta contoh urutannya. Verifikasi penerapan dari database kosong dan upgrade database yang sudah ada dilakukan ketika runtime/database test serta runner tersedia; belum dilakukan pada tahap dokumentasi.

## Supersedes

Tidak ada.
