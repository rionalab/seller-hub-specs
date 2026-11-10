# Backlog bisnis Seller Hub

Dipindahkan dari legacy dengan mempertahankan kebutuhan dan keputusan bisnis. Prioritas aktif: Commerce Foundation. Daftar berikut adalah backlog, bukan otorisasi membangun semua fitur sekaligus. Struktur kode mengikuti arsitektur lokal BE/FE; kata modul di konteks bisnis tidak mewajibkan folder Modules atau service tersendiri. Status lama scaffold tidak dibawa sebagai fitur aktif.

## Rancangan Identity/Auth — ditunda setelah prioritas Commerce

- [x] User memilih membangun Identity provider sendiri.
- [x] Scope coding awal disepakati: login Platform Admin; bukan registrasi publik admin.
- [ ] **Perlu keputusan:** pisahkan admin platform, anggota seller, customer dan aplikasi layanan dalam model identitas.
- [ ] **Perlu keputusan:** hubungan seller/store, membership dan apakah satu seller memiliki banyak store.
- [ ] **Perlu keputusan:** komponen hashing/password dan penerbitan/validasi token standar yang dipakai.
- [ ] **Perlu keputusan:** kontrak login, refresh, logout, me, forgot-password dan reset-password.
- [ ] **Perlu keputusan:** umur token, penyimpanan token di browser, transport sesi serta proteksi yang sesuai.
- [ ] **Perlu keputusan:** bootstrap admin pertama, MFA dan pemulihan akses.
- [ ] **Perlu keputusan:** suspension/perubahan permission, pencabutan sesi dan access token yang masih berlaku.
- [ ] Dokumentasikan keputusan bisnis Identity di decisions/ dan kontrak teknis pada backend sebelum implementasi.

Daftar endpoint pada diskusi masih rancangan, belum kontrak final atau API yang berjalan.

## Akun, role dan permission

- [ ] Rancang tabel akun, role, permission dan relasinya.
- [ ] Rancang seller/store membership setelah model tenancy diputuskan.
- [ ] Buat migrasi dan stored procedure Identity.
- [ ] Implementasikan hashing dan verifikasi password menggunakan komponen teruji.
- [ ] Bootstrap admin pertama tanpa kredensial bawaan.
- [ ] Audit perubahan akun, role, permission dan status akses.

## Login dan profil admin

- [ ] Validasi request login serta status akun.
- [ ] Pembatasan percobaan login dan respons yang tidak membocorkan keberadaan akun.
- [ ] Penerbitan access token dengan claim yang dibutuhkan, issuer, audience dan expiry.
- [ ] Endpoint profil dan permission pengguna saat ini.
- [ ] Tes sukses, kredensial salah, akun nonaktif, input invalid dan throttling.

## Sesi, recovery dan MFA

- [ ] Simpan hash refresh token dan metadata sesi.
- [ ] Refresh-token rotation dan penanganan reuse secara atomik.
- [ ] Logout sesi saat ini dan seluruh sesi.
- [ ] Terapkan aturan reset password/suspension terhadap sesi aktif.
- [ ] Forgot/reset password dengan respons aman dan token sekali pakai.
- [ ] MFA admin dan recovery sebelum production.
- [ ] Tes expiry, reuse, concurrent refresh, pencabutan dan recovery.

## Integrasi Identity ke Commerce

- [ ] Konfigurasikan Commerce untuk memvalidasi token provider Identity milik kita.
- [ ] Tentukan serta terapkan permission bisnis per operasi.
- [ ] Validasi seller/store membership dan resource ownership di backend.
- [ ] Tes 401, 403, akses lintas seller/customer, issuer/audience salah dan key rotation.
- [ ] Perbarui contract, contoh Postman dan dokumentasi onboarding.

## Fondasi bisnis Commerce

- [ ] Tetapkan kontrak, schema/SP, aturan tenant dan acceptance criteria tiap feature sebelum coding.
- [ ] Products: pencarian, detail, varian dan ketersediaan stok.
- [ ] Customers: identitas serta pemetaan kontak dalam konteks seller.
- [ ] Carts: lihat, tambah, ubah dan hapus item.
- [ ] Orders: buat, lihat, status dan pembatalan.
- [ ] Putuskan harga/currency, reservasi stok, idempotency dan lifecycle order; PendingPayment baru usulan awal.
- [ ] Uji isolasi seller/customer dan konsistensi transaksi pada SQL Server test asli.

## Auth integrasi eksternal (opsional, bukan syarat antarmodul)

Ditunda sampai ada client/service eksternal yang nyata. Komunikasi internal dalam monolith tidak memerlukan client credentials.

- [ ] Daftarkan WA Service sebagai aplikasi layanan, terpisah dari akun admin.
- [ ] Implementasikan alur client credentials memakai komponen/protokol standar.
- [ ] Scope/audience minimum serta batas akses seller bagi token layanan.
- [ ] Rotasi dan pencabutan kredensial layanan.
- [ ] Tetapkan bagaimana konteks seller/customer diteruskan dan diverifikasi; token layanan tidak otomatis memberi akses semua customer.
- [ ] Tes kredensial salah/dicabut, scope salah, dan pemalsuan konteks seller/customer.

## Integrasi WhatsApp

- [ ] **Perlu keputusan:** provider WhatsApp; penjelasan sebelumnya mengasumsikan Cloud API resmi Meta.
- [ ] Buat integrasi WhatsApp dalam aplikasi; worker terpisah hanya bila ada kebutuhan dan keputusan baru.
- [ ] Simpan kredensial provider di secret storage.
- [ ] Verifikasi awal webhook serta autentisitas setiap delivery sesuai provider.
- [ ] Mapping nomor bisnis penerima ke seller/store terdaftar.
- [ ] Mapping pengirim ke customer dalam konteks seller.
- [ ] Deduplication/idempotency webhook dan pengelolaan retry.
- [ ] Panggil kontrak aplikasi Commerce in-process dengan konteks seller/customer tervalidasi; jangan membuka database ke AI.
- [ ] Pengiriman balasan WhatsApp dan penanganan kegagalan.
- [ ] Tes end-to-end dengan akun/provider test.

## AI runtime untuk WhatsApp

- [ ] **Perlu keputusan:** model/provider LLM dan kebijakan penyimpanan percakapan.
- [ ] Conversation context dan tool calling melalui kontrak aplikasi Commerce.
- [ ] Allowlist tool serta validasi argument; akses tidak ditentukan oleh instruksi pengguna/LLM.
- [ ] Harga, stok, customer dan status order berasal dari backend yang berwenang.
- [ ] Konfirmasi aksi transaksi, batas biaya/pemakaian dan human handover.
- [ ] Evaluasi jawaban, prompt injection, akses lintas seller dan kegagalan layanan.

## Platform Admin — backlog kasar, belum implementasi

Daftar ini adalah cakupan produk awal untuk dirinci, bukan janji semua masuk MVP atau satu microservice per menu.

- [ ] Seller Management: registration, verification, activation, suspension dan profile.
- [ ] Subscription: plans, billing, invoice dan usage.
- [ ] Platform Monitoring: stores, orders, transactions dan system health operasional.
- [ ] AI Management: usage, token/cost, model configuration dan safety.
- [ ] Integration Management: WhatsApp, payment gateway, shipping dan marketplace.
- [ ] Support: tickets dan seller issues.
- [ ] Analytics: active sellers, GMV, MRR, churn dan AI usage; definisi metrik belum ditetapkan.
- [ ] Pertimbangkan usulan tambahan: audit, akses admin, quota/entitlements dan kebijakan data/privacy.

Liveness /health yang sudah tersedia belum memenuhi seluruh Platform Monitoring.

## Kesiapan delivery/production

- [ ] Sinkronisasi OpenAPI/Postman setiap endpoint bertambah atau berubah.
- [ ] SQL integration tests, migrasi dan verifikasi least-privilege.
- [ ] Review secret/log redaction, authorization dan boundary data untuk fitur nyata.
- [ ] Konfigurasi TLS/proxy, key rotation, distributed throttling sesuai deployment.
- [ ] Recovery/backup, pemantauan, alert dan prosedur operasi.
- [ ] CI/CD dan deployment setelah diminta/disepakati; belum dibuat.


## Urutan terdekat

Tentukan konteks seller/store, membership, customer binding dan kontrak fitur Commerce pertama. Rincikan harga/currency, stok, lifecycle order, idempotency serta pencatatan pembayaran/pemenuhan dasar sebelum schema atau endpoint terkait. Login Platform Admin tetap ditunda; akses data bisnis tidak boleh dibuka tanpa keputusan authorization.

Validasi bisnis: wawancara sekitar 10 seller, cari 2–3 pilot, uji willingness to pay dan definisikan repeat GMV beserta metrik pendukung. Harga/target pada PRODUCT.md masih hipotesis.
