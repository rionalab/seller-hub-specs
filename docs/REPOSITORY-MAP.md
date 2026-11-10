# Sumber utama dan kontrak lintas repository

| Informasi | Sumber |
| --- | --- |
| Produk, business rules, prioritas, acceptance criteria | PRODUCT.md, BUSINESS-BACKLOG.md dan features/ di specs |
| Progres fitur lintas aplikasi | FEATURE-STATUS.md; bukti BE, FE dan integrasi dipisah |
| Arsitektur/kode/keamanan/pengujian BE | [AGENTS backend](../../seller-hub-backend/AGENTS.md) dan docs/ backend |
| Arsitektur/kode/keamanan/pengujian FE | [AGENTS frontend](../../seller-hub-frontend/AGENTS.md) dan docs/ frontend |
| Kontrak API implementasi | [API Contract](../../seller-hub-backend/docs/API-CONTRACT.md), [OpenAPI](../../seller-hub-backend/docs/openapi.json) backend |
| Hasil verifikasi lokal dan backlog teknis | TODO.md dan catatan verifikasi masing-masing repository |

Spesifikasi fitur menyebut kebutuhan dan outcome; detail wire contract tetap di backend. FE tidak boleh menebak endpoint, field, status error atau permission dari mock. Mock harus ditandai sebagai stand-in dengan kesenjangan kontraknya, bukan bukti integrasi.

Jika requirement dan kontrak aktif berbeda, catat ketidaksesuaian dan tentukan perubahan yang diperlukan sebelum bagian implementasi terkait. Jangan diam-diam menjadikan perilaku kode sebagai aturan bisnis baru. Perubahan breaking membutuhkan pembahasan kompatibilitas BE/FE.

Saat memulai fitur, catat revisi specs dan kontrak BE yang digunakan (commit bila tersedia; perubahan lokal belum committed ditandai eksplisit). Periksa ulang perubahan sebelum menyatakan selesai. Specs yang belum ada/tidak terjangkau adalah prasyarat yang perlu dipulihkan; jangan membuat PRODUCT baru di BE/FE sebagai fallback. Operasi teknis independen tetap boleh dilanjutkan.
