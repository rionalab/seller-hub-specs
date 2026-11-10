# COM-FOUNDATION — Commerce Foundation

Status kebutuhan: Arah/prioritas disepakati; spesifikasi operasi belum disepakati.
Sumber: PRODUCT.md dan BUSINESS-BACKLOG.md yang dipindahkan dari backend; prioritas Commerce-first dipertahankan.

## Tujuan dan scope

Menyediakan fondasi transaksi Seller, Product, Customer, Cart dan Order sebelum kanal WhatsApp/AI. Rincian operasi pertama belum dipilih. Ini bukan persetujuan mengimplementasikan semua entity/menu sekaligus.

## Penghalang keputusan

- Hubungan seller/store, membership, customer binding, ownership dan permissions.
- Urutan operasi pertama serta input/output dan acceptance criteria.
- Harga/currency, stok/reservasi, idempotency, lifecycle/pembatalan order.
- Pencatatan pembayaran dan pemenuhan dasar sebelum integrasi Payment/Shipping.
- Bagian FE yang dibutuhkan untuk operasi pertama; keberadaan folder Admin/Seller tidak menetapkan prioritas baru.

## Dampak dan status

BE menegakkan aturan serta akses data; FE mengonsumsi kontrak dan menampilkan outcome sesuai spesifikasi. Kontrak endpoint bisnis belum tersedia. Login Platform Admin tetap ditunda. Lihat [status bersama](../FEATURE-STATUS.md); jangan menggunakan contoh endpoint atau UI sebagai acceptance criteria bisnis.
