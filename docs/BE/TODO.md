# Status dan TODO Seller Hub Backend

Panduan agent terbaru sudah diadopsi. Konteks bisnis berasal dari legacy; status di bawah mencerminkan kode aktif setelah penyelarasan, bukan deployment. Lihat [hasil pemeriksaan](ALIGNMENT-REVIEW.md), [produk](../PRODUCT.md) dan [backlog bisnis](../BUSINESS-BACKLOG.md).

## Selesai pada perubahan ini

- [x] Pulihkan konteks bisnis dan visi tanpa membawa klaim implementasi lama.
- [x] Terapkan panduan terbaru; tandai agent legacy sebagai arsip.
- [x] Keluarkan slice login placeholder dan SystemChecks beserta struktur Modules dari kode aktif.
- [x] Selaraskan fondasi Controllers, Common, Infrastructure dan DI di Program.cs.
- [x] Pertahankan identitas migrasi dan konfigurasi lokal; tidak menjalankan SQL.
- [x] Pisahkan kontrak aktif dari kontrak legacy; bukti verifikasi dicatat di ALIGNMENT-REVIEW.md.

## Kemampuan dasar

| ID | Status aktif | Pekerjaan berikutnya |
| --- | --- | --- |
| BASE-01 | Sebagian: envelope/error, Controller liveness dan OpenAPI Development | Validasi dan kontrak feature bisnis; dokumentasikan outcome per operasi. |
| BASE-02 | Sebagian: validasi JWT dan fallback authorization | Identity self-managed, ICurrentUser, permissions, seller/store scope, test akses bisnis. |
| BASE-03 | Belum: audit data bisnis | Tetapkan cakupan/schema dan implementasikan melalui SP atomik bersama mutasi. |
| BASE-04 | Sebagian: pencatatan error fondasi | Pilih provider/sink dan log proses bisnis, redaksi serta kebijakan gangguan. |
| BASE-05 | Sebagian: trace ID response/error | Korelasi audit/request/job saat fitur dibuat. |
| BASE-06 | Sebagian: konfigurasi startup bertipe | Parameter operasional dan pengelolaannya sesuai kebutuhan; UI admin belum dipilih. |
| BASE-07 | Belum | Kebijakan retensi mengikuti penyimpanan terpilih; profil WIB/admin tidak otomatis berlaku. |

## Prioritas berikutnya

1. Pastikan lingkungan dapat memakai SDK yang dikunci pada global.json; verifikasi di versi tersebut masih diperlukan bila belum tersedia.
2. Spesifikasikan konteks Seller: seller/store, membership, customer binding dan akses objek. Jangan mengarang model tenant.
3. Pilih operasi Commerce pertama beserta kontrak dan acceptance criteria. Products, Customers, Carts dan Orders belum diimplementasikan.
4. Bangun slice sesuai starter, termasuk audit untuk mutasi dan test negatif akses yang relevan.

Login Platform Admin ditunda. Self-managed Identity tetap arah produk; user ikut coding, agent membantu desain/review. WhatsApp, AI, Payment, Shipping, Seller Operations, CRM, Storefront, marketplace dan mobile mengikuti roadmap, bukan pekerjaan yang otomatis diotorisasi sekarang.

## Verifikasi dan delivery yang belum tersedia

- [ ] Build/test pada SDK yang dikunci apabila lingkungan belum memenuhi.
- [ ] SQL Server nyata: migrasi, mapping, rollback, least privilege dan kompatibilitas journal.
- [ ] Provider identitas nyata, discovery dan rotasi key.
- [ ] Authorization/ownership bisnis, logging lengkap dan audit data.
- [ ] CI secrets/dependency/build/test, deployment, TLS/proxy dan operasi produksi.

[IMPROVEMENT-PLAN.md](IMPROVEMENT-PLAN.md) adalah riwayat perbaikan blueprint, bukan checklist implementasi Seller Hub. Folder legacy menyimpan status historis untuk penelusuran saja. Tidak commit/push tanpa permintaan pengguna.

## Koordinasi fitur

Status BE/FE/integrasi berada di [FEATURE-STATUS.md](../FEATURE-STATUS.md). Dokumen ini mencatat pekerjaan teknis BE; keputusan bisnis dan prioritas baru diperbarui pada sumber bersama. Pemisahan repository dokumentasi tidak mengubah hasil verifikasi runtime sebelumnya.
