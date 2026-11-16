# 0009 - Log aplikasi dalam file harian

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Setelah membahas pilihan file, tabel atau kombinasi, pengguna memilih file saja dengan pemisahan pencatatan berdasarkan hari.

## Decision

Log aplikasi ditulis ke file yang dipisahkan menurut hari. Tidak menggunakan tabel database untuk log aplikasi. Audit perubahan data tetap berada pada tabel audit pasangan melalui SP; keputusan ini tidak mengubahnya.

Zona waktu pergantian hari, direktori, nama final, format, library/provider, batas ukuran, retensi dan perilaku kegagalan masih terbuka. Timestamp kejadian tetap UTC sesuai konsep sebelumnya. Restart tidak boleh menimpa log yang sudah tersimpan.

## Alternatives

Tabel log dan kombinasi tabel/file tidak dipilih sesuai preferensi pengguna.

## Consequences

Penulisan log tidak bergantung pada ketersediaan database bisnis, tetapi bergantung pada akses dan kapasitas penyimpanan file. Rotasi harian tidak menyelesaikan retensi atau batas volume. Pencarian lintas file/server memerlukan pertimbangan saat deployment berkembang.

## Validation

Periksa konsistensi dokumentasi. Saat runtime tersedia, uji pergantian hari, restart tanpa kehilangan catatan, serta gangguan penulisan sesuai kebijakan yang dipilih. Belum ada provider yang dipasang atau test runtime yang dijalankan.

## Keputusan lanjutan

Setelah keputusan file harian, pengguna memilih WIB untuk pergantian hari. Tanggal file dan batas pergantian mengikuti pukul 00.00 WIB (UTC+7), terlepas dari zona waktu mesin. Timestamp kejadian tetap UTC. Keputusan ini menutup pilihan zona waktu yang sebelumnya terbuka; detail penyimpanan lainnya tetap belum dipilih. Verifikasi batas hari WIB tercantum pada docs/BE/LOGGING.md.

## Supersedes

Tidak ada; melengkapi keputusan tujuan penyimpanan yang terbuka pada ADR 0008.
