# Frontend API Integration

Sumber wire contract: [API Contract backend](../BE/API-CONTRACT.md) dan [OpenAPI backend](../../../seller-hub-backend/docs/openapi.json). Jangan menyalin spesifikasi ke FE. Catat revisi kontrak yang digunakan; perubahan lokal belum committed harus disebutkan.

Saat inspeksi, backend hanya menyediakan liveness /health dan OpenAPI Development. Tidak ada endpoint Commerce atau login aktif. src/shared/api/http-client.ts dan api-error.ts masih kosong; jangan menyatakan client/envelope sudah diimplementasikan.

Sebelum menghubungkan fitur, cocokkan method/path, DTO/nullability, status/envelope, error validasi, paging dan akses dengan kontrak. Scope seller/actor bukan hak yang dapat diberikan oleh client. Jangan mengarang endpoint atau format token.

Gunakan TanStack Query untuk kebutuhan server-state bila sesuai pola proyek. Keputusan cache key dan invalidation harus memperhatikan scope pengguna/seller agar data tidak terbawa antaridentitas; mutasi tidak di-retry otomatis tanpa kontrak idempotency. Tangani pembatalan request dan response yang datang terlambat bila relevan.

Authentication, penyimpanan token/cookie, refresh dan perilaku 401/403 memerlukan keputusan produk/keamanan sebelum implementasi sesi. Jangan memakai data dummy/token palsu sebagai fallback produksi. Mock hanya stand-in berlabel; hasil mock tidak dinyatakan integrasi BE–FE selesai.
