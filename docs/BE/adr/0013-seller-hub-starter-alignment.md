# 0013 - Penyelarasan Seller Hub dengan starter terbaru

Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna mengganti panduan aktif dengan blueprint terbaru, memindahkan panduan sebelumnya ke agent legacy, meminta pemulihan konteks bisnis dan mengeluarkan fitur kode yang tidak sesuai starter. Ini menggantikan pendekatan sebelumnya untuk mempertahankan seluruh pola teknis lama.

## Decision

Gunakan Controllers, Features/{Feature}/{Operation}, nama tipe/file lengkap, UseCase ExecuteAsync, Common dan Infrastructure sesuai starter. Pertahankan satu Seller.Api, Dapper/SP, FluentValidation yang sudah dipilih, xUnit, dan Seller.Database/DbUp sebagai tool migrasi terpisah. Registrasi API di Program.cs.

Arsipkan Modules/Identity (login hanya validasi input dengan 501), Modules/Commerce/SystemChecks (contoh teknis), scope-only folder bisnis dan test lama di agent legacy/runtime-before-alignment. Tidak ada business CRUD yang sudah tersedia. Kebutuhan bisnis dipulihkan di PRODUCT.md, PRODUCT-VISION.md dan BUSINESS-BACKLOG.md. Commerce Foundation tetap prioritas; login Platform Admin tetap ditunda.

Pertahankan JWT validation, fallback authorization, error envelope, CORS allowlist, rate limit dan liveness; /health kini memakai Controller. Jangan menyalin policy teknis backend.read menjadi permission bisnis. Test policy menggunakan controller khusus test yang tidak masuk aplikasi.

Pertahankan identitas SDK, solution, proyek test, UserSecretsId dan journal migrasi. Pindahkan sumber SQL ke database/migrations tanpa mengubah isi/LogicalName script historis; urutan numerik mengakomodasi prefix lama 001 dan baru 0002. Penghapusan endpoint tidak menghapus SP database. Tidak menjalankan migrasi atau mengubah database live.

## Consequences

Kontrak /api/v1/authentications dan /api/v1/system-checks lama tidak aktif. Client yang memakai kontrak contoh tersebut harus berhenti mengandalkannya. OpenAPI aktif hanya mencakup liveness; dokumen kontrak legacy tetap arsip. Feature bisnis akan dibangun ulang setelah keputusan seller/store, ownership dan kontraknya jelas.

## Validation

Lihat ALIGNMENT-REVIEW.md untuk hasil build/test dan batas SDK/database/provider. Ketersediaan panduan tidak membuktikan fitur selesai.

## Supersedes

Menggantikan pola Minimal APIs/Modules/Handler pada ADR legacy 0001–0003 dan keputusan mempertahankan scaffold Identity pada legacy ADR 0004. Prioritas bisnis Commerce-first tetap berlaku. ADR 0001–0012 di docs/BE/adr adalah keputusan blueprint yang diadopsi; nomor legacy tidak sama dengan nomor blueprint.
