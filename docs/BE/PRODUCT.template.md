> Referensi template starter untuk proyek lain. Seller Hub memakai ../seller-hub-specs/docs/PRODUCT.md (dari root repository); jangan menghasilkan salinan lokal.

# Product Context Template

Salin menjadi PRODUCT.md pada proyek baru. Ganti tiap [ISI]; keputusan belum dibuat ditulis Belum diputuskan. Jangan membawa domain proyek lain otomatis.

## Identitas
Nama: [ISI]
Tujuan/masalah: [ISI]
Pengguna utama: [ISI]
Ukuran keberhasilan: [ISI]

## Scope
Modul awal: [ISI]
Di luar scope: [ISI]
Prioritas pertama: [ISI]

## Fitur dasar wajib

Acuan: docs/BE/BASELINE-FEATURES.md. Masukkan BASE-01 sampai BASE-07 ke backlog proyek beserta acceptance criteria, keputusan terbuka dan status implementasinya. Fitur wajib yang belum diputuskan detailnya tidak boleh dianggap dikecualikan atau sudah tersedia.

Pemetaan ID fitur dasar ke pekerjaan/penanggung jawab: [ISI]
Pengelolaan konfigurasi/retensi: file, database atau layanan; perlu halaman admin atau tidak: [ISI]
Profil referensi yang diadopsi beserta penyesuaiannya: [ISI]
Keputusan terbuka yang menghalangi implementasi: [ISI]
Perubahan cakupan yang secara eksplisit disetujui pengguna, jika ada: [ISI]

## Aturan bisnis
Istilah domain dan artinya: [ISI]
Invariant/status/transisi: [ISI]
Kondisi konflik dan approval: [ISI]

## Data dan akses
Sumber data dan ownership: [ISI]
Roles serta permissions tiap operasi: [ISI]
Aturan tenant/ownership objek: [ISI]
Kebutuhan tenant (ada/tidak), sumber keanggotaan dan cara memilih scope yang tervalidasi: [ISI]
Izin akses lintas pemilik/tenant, bila diperlukan: [ISI]
Endpoint anonymous beserta alasannya: [ISI]
Field request yang boleh diubah dan operasi khusus perubahan owner/hak akses, bila ada: [ISI]
Respons penolakan akses objek sesuai kontrak API: [ISI]
Retensi data/audit: [ISI]

## Integrasi dan operasional
Integrasi eksternal: [ISI]
Authentication model: [ISI]
Sumber identitas tervalidasi, mapping claims dan field/tipe identifier ICurrentUser: [ISI]
Identitas actor untuk proses non-HTTP, bila ada: [ISI]
Deployment target: [ISI]
Kebutuhan performa/availability: [ISI]

## Acceptance criteria
Skenario sukses: [ISI]
Validasi/gagal/forbidden: [ISI]
