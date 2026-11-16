# 0005 - Transaksi atomik data dan audit

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

ADR 0004 menetapkan audit melalui SP dan meninggalkan atomicity sebagai keputusan terbuka. Pengguna kemudian menyetujui poin 2: perubahan bisnis dan audit dalam satu transaksi, dengan pembatalan perubahan bisnis jika audit gagal. Persetujuan ini tidak menetapkan pengecualian cakupan tabel pada poin 1.

## Decision

Data bisnis dan audit terkait ditulis dalam transaksi yang sama. Kegagalan audit menggagalkan operasi atomik; SP meneruskan error dan tidak mengembalikan sukses. Hormati ownership transaksi: bila caller memiliki transaksi, kegagalan diteruskan agar caller membatalkan operasi. Tidak boleh ada commit data dan audit secara terpisah.

## Alternatives

Audit best-effort atau penulisan audit setelah commit tidak dipilih karena memungkinkan data berubah tanpa audit yang diwajibkan.

## Consequences

Kegagalan penyimpanan audit dapat menggagalkan perubahan bisnis. Audit yang berada dalam transaksi batal ikut dibatalkan; keputusan ini tidak menjadikan audit sebagai log kegagalan. Logging diagnostik tetap dibahas terpisah.

## Validation

Saat database test tersedia, uji commit keduanya pada sukses, tidak ada perubahan bisnis tersisa saat audit gagal, dan pembatalan keduanya saat transaksi luar rollback. Saat ini baru dokumentasi; tidak ada pengujian database yang dijalankan.

## Supersedes

Tidak menggantikan ADR 0004; melengkapi keputusan atomicity yang sebelumnya terbuka.
