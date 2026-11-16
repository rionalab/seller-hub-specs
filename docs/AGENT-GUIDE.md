# Panduan kerja agent Seller Hub

Pilih bagian yang sesuai tugas. Path `docs/FE/...` dan `docs/BE/...` dihitung dari root seller-hub-specs. Baca `AGENTS.md` repo tujuan dan standar teknis terkait. Untuk fitur atau perubahan bisnis, gunakan [alur fitur](FEATURE-WORKFLOW.md) untuk analisis, keputusan dan kriteria selesai.

Inspeksi kode, konfigurasi yang relevan dan perubahan lokal sebelum mengedit. Ikuti stack dan tooling yang digunakan repo. Cocokkan perubahan dengan bagian relevan di [keamanan FE](FE/SECURITY.md) atau [keamanan BE](BE/SECURITY.md) sebelum implementasi. Untuk review keamanan khusus, telusuri input, identitas, data dan efek samping. Laporkan lokasi, bukti, dampak dan perbaikan mengikuti standar repo.

## Fitur backend

- Periksa batas controller, UseCase, Domain dan repository di `docs/BE/ARCHITECTURE.md`. Implementasikan perubahan melalui operation di `Features/{Feature}/{Operation}`, validator dan UseCase yang dipakai proyek. Perbarui DI bila perlu.
- Jika perubahan menyentuh kontrak atau persistence, gunakan bagian Endpoint atau Database di bawah. Cocokkan perubahan dengan routing `docs/BE/SECURITY.md`, termasuk akses objek, upload atau integrasi bila terdampak.

## Endpoint backend

- Gunakan `docs/BE/API-CONTRACT.md` untuk method, route, versi, DTO, nullability, validasi, status dan envelope, termasuk error dari framework. Tentukan policy dan ownership per outcome. Ambil identitas dan tenant dari konteks tepercaya.
- Jaga controller tipis dan teruskan cancellation token ke UseCase. Selaraskan OpenAPI, dokumentasikan breaking change, dan regenerasi client bila toolchain tersedia.

## Database backend

- Gunakan `docs/BE/ARCHITECTURE.md`, `docs/BE/DATABASE.md` dan `docs/BE/SECURITY.md` untuk migrasi, query, audit dan batas transaksi. Cocokkan tipe, ukuran, nullability, parameter, result set, affected rows dan error dengan mapping repository.
- Ikuti urutan dan riwayat migrasi yang sudah dipakai. Gunakan kolom eksplisit, SET NOCOUNT ON dan CREATE OR ALTER jika target mendukung. Untuk transaksi di SP, tangani error dan rollback sesuai pemilik transaksi. Laporkan risiko kompatibilitas, kebutuhan deploy dan pemulihan.

## Fitur frontend

- Periksa entry point, paket dan pola di `docs/FE/ARCHITECTURE.md`. Cocokkan alur, komponen, validasi, loading, empty, error, success dan akses keyboard/fokus dengan spesifikasi sesuai dampak.
- Gunakan `docs/FE/SECURITY.md` untuk trust boundary browser, sesi dan data privat. Backend menegakkan authorization. Gunakan bagian Integrasi API jika koneksi atau state server terdampak.

## Integrasi API frontend

- Gunakan `docs/FE/API-INTEGRATION.md` dan kontrak aktif di backend. Cocokkan route dan method, DTO, respons/error, pagination, autentikasi dan validasi. Catat revisi kontrak dan tandai endpoint yang belum tersedia sebagai dependensi.
- Ikuti pola client dan cache yang dipakai repo. Periksa scope cache, invalidation setelah mutasi, pembatalan, duplikasi dan retry. Gunakan fixture sesuai kontrak saat perlu dan labeli batas verifikasinya.

## Verifikasi

- Gunakan [pengujian FE](FE/TESTING.md) atau [pengujian BE](BE/TESTING.md). Pilih boundary dan fixture sesuai risiko. Uji akses ditolak dan akses yang diizinkan sebagai pembanding jika relevan. Periksa kebocoran data dan efek samping; pertahankan authorization saat pengujian.
- **Backend** — Pilih unit, HTTP, persistence atau gabungan. Gunakan host test untuk binding, policy, status, envelope dan DTO. Persistence memerlukan SQL Server test terisolasi dengan fixture deterministik dan cleanup. Periksa mapping, konflik, paging, atomicity dan rollback bila relevan, termasuk commit data/audit bersama dan kegagalan audit yang membatalkan perubahan bisnis. Bedakan handler autentikasi test dari verifikasi token nyata.
- **Frontend** — Pilih unit, komponen, client atau alur nyata. Periksa input invalid, error, akses ditolak dan keyboard/fokus sesuai perubahan. Laporkan lint, typecheck dan build terpisah dari test perilaku. Fixture membuktikan interaksi dengan fixture, sedangkan klaim integrasi memerlukan BE nyata.
- Jalankan pemeriksaan yang tersedia. Catat boundary, hasil, kegagalan dan prasyarat untuk bagian yang belum dapat diuji. Lanjutkan pemeriksaan independen yang masih bisa dijalankan.
