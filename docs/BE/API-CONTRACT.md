# API Contract

## Routing
Gunakan /api/v1/{resources}, lowercase, plural nouns; identifier sebagai route segment.
GET membaca, POST membuat atau command bisnis, PUT mengganti, PATCH perubahan parsial bila kontraknya ditetapkan, DELETE menghapus.
Command bisnis boleh /api/v1/{resources}/{id}/approve jika memang tidak cocok dengan resource biasa. Jangan mengubah state melalui GET.

## Response standar
Target bentuk JSON ApiResponse<T>:
- success: boolean
- message: string
- data: T atau null
- errors: array objek { code, field, message }; kosong pada sukses
- traceId: string

Dokumentasikan nullability dan field wajib pada OpenAPI. Jangan mengembalikan HTTP 200 untuk kegagalan.
GET/PUT/PATCH: 200; POST create: 201 dan Location; DELETE: 204 tanpa body atau 200 dengan envelope bila memerlukan payload. Pilih satu pola per proyek.
400 input/business request tidak valid, 401 belum terautentikasi, 403 tidak diizinkan, 404 tidak ditemukan, 409 konflik, 429 throttled, 500 unexpected error dengan pesan aman.
Exception middleware memakai envelope yang sama. Jika framework menghasilkan ProblemDetails, konfigurasi jalur tersebut agar mengikuti keputusan kontrak; jangan mencampur tanpa dokumentasi.

## Pagination
Query page (mulai 1), pageSize (default 20, maksimum 100), search, sortBy, sortDirection.
Data: { items, page, pageSize, totalCount }. Reject nilai di luar batas. Allowlist sortBy dan asc/desc; jangan memasukkan nama kolom mentah ke SQL.
SP menerima offset/limit atau start/length yang dipetakan oleh repository.

## Kontrak feature
Saat endpoint pertama dibuat, catat method/path, autentikasi/policy, request, response, status, contoh serta aturan validasi. Template starter bukan daftar endpoint yang sudah berjalan.
OpenAPI harus mengikuti implementasi; regenerate client ketika kontrak berubah. Perubahan breaking memerlukan strategi kompatibilitas/versioning.

## Kontrak aktif Seller Hub setelah penyelarasan

| Method/path | Akses | Respons |
| --- | --- | --- |
| GET /health | Anonymous eksplisit, liveness infrastruktur | 200 ApiResponse dengan data { status: "healthy" }; tidak memeriksa SQL/provider. |
| GET /openapi/v1.json | Anonymous hanya Development | Dokumen OpenAPI standar, bukan envelope bisnis. Tidak tersedia di Production. |

Liveness /health adalah pengecualian operasional dari prefix resource bisnis /api/v1. /api/v1/authentications dan /api/v1/system-checks lama tidak terdaftar; request terautentikasi memperoleh 404, anonymous tetap terkena fallback authentication. Tidak ada kontrak bisnis aktif atau token issuance. OpenAPI/Postman di agent legacy adalah sejarah, bukan kontrak FE saat ini.

Error validation controller menggunakan envelope yang sama. Policy bisnis, DTO, outcome dan akses lintas seller harus disepakati sebelum endpoint bisnis pertama dibangun.
