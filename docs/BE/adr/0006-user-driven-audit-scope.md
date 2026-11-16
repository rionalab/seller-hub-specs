# 0006 - Cakupan audit berdasarkan interaksi pengguna

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna memperjelas bahwa data yang berubah berdasarkan interaksi pengguna harus tercatat, dengan tujuan tracing jika ditemukan kejanggalan. Cakupan perlu dijelaskan berdasarkan tindakan dan dampaknya, bukan kategori tabel teknis semata.

## Decision

Setiap tabel yang mengalami perubahan akibat interaksi pengguna masuk cakupan audit pasangan melalui SP. Ini mencakup insert, update dan delete serta perubahan turunan pada tabel utama, detail dan penghubung. Audit harus mendukung penelusuran pelaku, waktu, record yang berubah, perubahan sebelum/sesudah dan hubungan dengan tindakan asal. Detail schema dan representasi belum dipilih.

Proses asynchronous yang menjalankan dampak tindakan pengguna tetap perlu mempertahankan keterkaitan dengan tindakan asal. Proses otomatis independen dan perubahan pemeliharaan belum diputuskan cakupannya.

## Alternatives

Mengaudit hanya tabel yang diedit langsung pada layar tidak cukup untuk tujuan tracing karena satu tindakan dapat mengubah beberapa tabel. Pengecualian umum berdasarkan nama tabel belum disepakati.

## Consequences

Desain tiap operation harus menelusuri seluruh perubahan data yang diakibatkannya. Mekanisme SP dan transaksi atomik pada ADR 0004/0005 tetap berlaku. Perlindungan data audit, retention, representasi data dan logging masih dibahas terpisah.

## Validation

Saat implementasi tersedia, telusuri satu tindakan yang mengubah beberapa tabel dan pastikan setiap perubahan dapat dikaitkan dengan pelaku dan tindakan asal. Saat ini hanya dokumentasi; belum ada test runtime.

## Supersedes

Tidak ada; memperjelas cakupan yang masih terbuka pada ADR 0004 dan ADR 0005.
