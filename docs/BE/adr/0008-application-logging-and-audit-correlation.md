# 0008 - Logging proses dan korelasi audit

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna meminta logging dan memastikan cakupannya tidak hanya exception, lalu meminta melanjutkan konsep request, proses bisnis, database, integrasi, keamanan, background job dan operasional.

## Decision

Gunakan structured logging sesuai docs/BE/LOGGING.md. Hubungkan log dengan audit melalui ID operasi; gunakan TraceId untuk eksekusi request/pemanggilan layanan. Log diagnostik terpisah dari transaksi bisnis sehingga rollback tidak menghapusnya melalui transaksi yang sama. Catat exception lengkap sekali pada boundary penanganan dan hindari payload/secrets otomatis.

Library, tujuan penyimpanan, level/retensi dan perilaku gangguan logging belum dipilih.

## Alternatives

Logging hanya exception tidak dipilih karena penolakan yang diharapkan dan proses lambat juga penting untuk tracing. Mencatat setiap baris kode atau seluruh payload menambah noise dan risiko kebocoran.

## Consequences

Implementasi perlu meneruskan korelasi dan membedakan hasil bisnis dari exception. Pengiriman log dapat gagal secara terpisah; kebijakan penanganannya memerlukan keputusan lanjutan. Tidak ada klaim kesiapan produksi.

## Validation

Konsep diselaraskan dengan audit SP dan transaksi atomik. Rencana verifikasi ada di LOGGING.md; belum ada runtime/provider logging untuk diuji.

## Supersedes

Tidak ada; melengkapi hubungan logging dengan ADR audit sebelumnya.
