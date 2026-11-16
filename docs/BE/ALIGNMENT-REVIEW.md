# Pemeriksaan keselarasan Seller Hub

Tanggal: 2026-11-10. Cakupan: pemulihan konteks bisnis legacy, adopsi starter terbaru dan pengeluaran feature lama dari kode aktif. Ini bukan audit keamanan penuh atau deployment.

## Temuan dan tindakan

| Bagian sebelumnya | Ketidaksesuaian/status | Tindakan |
| --- | --- | --- |
| Minimal APIs, Modules, Endpoint/Handler, Request/Validator generik | Berbeda dari Controllers, Features/UseCase dan nama lengkap starter | Modules diarsipkan; Program memakai Controllers; belum membangun ulang feature bisnis. |
| Login Development | Validasi input saja, selalu 400/501; bukan login/token issuance | Diarsipkan bersama test lama. Prioritas bisnis login tetap ditunda. |
| SystemChecks | Contoh teknis dengan repository/SP, bukan Commerce bisnis | Route dan policy contoh dikeluarkan; SQL historis tetap dipertahankan. |
| Product/Customer/Cart/Order | Folder scope saja | Catatan disimpan di arsip dan kebutuhan dipindahkan ke PRODUCT/BUSINESS-BACKLOG. |
| Shared, registrasi module DI, beberapa tipe per file | Tidak sesuai lokasi dan konvensi starter | Common dan Infrastructure/Persistence, satu tipe utama per file, DI di Program.cs. |
| JWT, fallback auth, CORS, rate limit, error envelope | Fondasi masih relevan | Dipertahankan; test-only policy/controller menguji batas akses tanpa membuka endpoint bisnis. |
| Script SQL historis | Nama/lokasi lama, mungkin sudah dijournal | Sumber dipindah ke database/migrations; SQL dan LogicalName tidak diubah; urutan numerik diuji. Tidak menjalankan migrasi. |
| Kontrak lama | Endpoint lama tidak lagi tersedia | OpenAPI diekspor ulang; Postman/kontrak legacy tidak menjadi kontrak aktif. |

Arsip kode: agent legacy/runtime-before-alignment. Arsip tidak masuk solution atau compile glob proyek aktif. ADR 0013 menjelaskan perubahan; keputusan bisnis Commerce-first dan self-managed Identity tetap dipertahankan.

## Temuan dependency saat verifikasi

Restore mendeteksi System.Security.Cryptography.Pkcs transitif 9.0.11/9.0.13 dengan advisory High dan Moderate. Pin langsung 9.0.21 pada Seller.Api dan Seller.Database memperbarui dependency beserta lock files tanpa menonaktifkan audit NuGet. Referensi patch: [High advisory](https://github.com/advisories/GHSA-846f-f764-76mx) dan [Moderate advisory](https://github.com/advisories/GHSA-2fmv-rv2c-59x7). Restore dan build setelah patch berhasil; ini tidak membuktikan seluruh aplikasi bebas kerentanan.

## Bukti verifikasi

- SDK proyek tetap 10.0.400 dengan latestPatch. SDK ini belum tersedia di laptop; menjalankan dotnet dari repository berhenti pada resolusi SDK.
- Validasi dijalankan pada salinan sementara src/tests/database/solution dengan SDK lokal 10.0.102. global.json proyek asli tidak diubah. Tidak menyalin secrets atau menjalankan database.
- Restore dan build Release seluruh solution berhasil, 0 warning dan 0 error.
- 17 test lulus, 0 gagal/skipped: liveness, JWT issuer/audience/expiry/signature, 401/403, success/error envelope, rate limit, validation Controller, route legacy tidak tersedia, kontrak aktif dan urutan nomor migrasi lama/baru.
- Test ekspor kontrak dijalankan terpisah dan lulus; docs/openapi.json hanya memuat /health. Controller khusus test tidak ikut kontrak aplikasi.
- 8 skenario tests/Tooling/DatabaseScripts.Tests.ps1 lulus. SQL, perintah dotnet dan penulisan User Secrets di-stub; tidak mengubah database/secret asli.
- Pemeriksaan dependency langsung/transitif setelah patch melaporkan tidak ada paket rentan menurut sumber NuGet saat run; bukan jaminan bebas kerentanan baru.
- Pemeriksaan diff dan tautan dokumen aktif lulus; tidak ada rujukan namespace Modules/Shared lama pada kode API aktif.
- Isi script migrasi dibandingkan dengan versi Git sebelumnya (normalisasi line ending): sama. LogicalName historis dipertahankan dalam project file.

## Batas dan pekerjaan tersisa

Verifikasi pada SDK 10.0.400 belum dilakukan. SQL Server nyata, migrasi/journal pada database yang pernah digunakan, provider identitas/discovery/key rotation, authorization bisnis dan CI/deployment belum diverifikasi. Rate limit masih global per proses dan CORS hanya mengizinkan GET untuk fondasi; sesuaikan ketika kontrak bisnis/deployment ditetapkan. Belum ada ICurrentUser, audit bisnis, provider logging/retensi lengkap atau endpoint Commerce. Jangan menganggap arsip/test lama sebagai bukti fitur aktif.

Langkah produk berikutnya: tentukan seller/store, membership, customer binding dan kontrak operasi Commerce pertama. Tidak commit/push atau mengubah frontend dalam pekerjaan ini.
