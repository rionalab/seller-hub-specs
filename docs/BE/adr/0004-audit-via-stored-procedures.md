# 0004 - Audit trail melalui stored procedure

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna meminta setiap tabel memiliki tabel audit pasangan serta pembahasan logging, lalu secara eksplisit memilih penulisan audit melalui SP.

## Decision

Audit perubahan data ditulis oleh stored procedure mutasi, bukan trigger. Keputusan ini hanya menetapkan mekanisme penulisan. Pengecualian cakupan, nama/kolom, representasi data, atomicity, perilaku kegagalan, akses dan retensi masih perlu disepakati.

## Alternatives

Trigger tidak dipilih sesuai preferensi pengguna. Jangan menambah trigger audit untuk melengkapi SP tanpa perubahan keputusan.

## Consequences

SP mutasi harus menyertakan audit untuk tabel terkait. SQL langsung yang melewati SP tidak tercatat otomatis; job, import dan pemeliharaan perlu masuk pembahasan cakupan. Logging aplikasi belum ditetapkan oleh keputusan ini.

## Validation

Architecture, panduan database dan skill stored-procedure diselaraskan. Belum ada schema/SP audit atau pengujian runtime; detail desain masih terbuka.

## Supersedes

Tidak ada.
