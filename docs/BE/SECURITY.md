# Security

Dokumen ini adalah persyaratan review, bukan klaim keamanan yang sudah diimplementasikan.

## Routing pemeriksaan keamanan

Sebelum mengubah implementasi, identifikasi kategori perubahan berikut dan baca persyaratan terkait pada bagian Checklist. Pemeriksaan dilakukan sebagai bagian dari pekerjaan feature, tanpa perlu permintaan review keamanan terpisah. Jika beberapa kategori terdampak, gabungkan pemeriksaan tanpa mengulang pekerjaan yang sama. Pilih berdasarkan perilaku/data yang berubah, bukan hanya nama folder.

| Perubahan | Persyaratan yang diperiksa bila relevan |
| --- | --- |
| Feature/UseCase atau akses data objek, termasuk endpoint baca | Authorization: policy serta ownership/tenant; Input; Audit bila diwajibkan produk; Errors/logs. |
| Endpoint, request/response atau HTTP pipeline | Authentication dan Authorization sesuai akses endpoint; Input; Errors/logs; Rate limit untuk operasi mahal/berisiko; CORS/CSRF bila alur browser atau konfigurasi terkait berubah. |
| Schema, SP atau repository | SQL; scope ownership/tenant pada akses objek; Secrets jika koneksi/akun berubah; Audit jika data audit terdampak. |
| Upload, download atau penyimpanan file | File; Authorization termasuk ownership/tenant; Input; Errors/logs agar isi atau lokasi privat tidak bocor. |
| Integrasi eksternal atau URL tujuan | Integrasi; Secrets; Errors/logs; Authorization bila data pengguna/tenant dikirim atau diambil. |
| Login, token, cookie, identity atau policy | Authentication; Authorization; Secrets; Errors/logs; Rate limit dan CSRF/CORS sesuai alur. |
| Konfigurasi, logging, dependency atau deployment | Secrets, Errors/logs, Dependency atau Transport/firewall sesuai bagian yang diubah. |

Perubahan dokumentasi/format tanpa dampak perilaku tidak mewajibkan pengujian keamanan runtime. Jika dokumentasi mengubah aturan keamanan, periksa konsistensinya dengan implementasi yang tersedia dan tandai bagian yang belum diimplementasikan. Jangan memperluas perubahan kecil menjadi audit seluruh aplikasi tanpa alasan terkait risiko perubahan.

Jika model akses atau fakta produk yang diperlukan belum tersedia, tanyakan kepada pengguna sebelum menetapkan kebijakan; lanjutkan inspeksi independen yang tidak bergantung pada jawaban. Jangan menganggap contoh sebagai permissions atau model tenant produk.

Saat selesai, laporkan pemeriksaan yang relevan beserta bukti lokasi/hasil, temuan dan bagian yang belum dapat diperiksa. Bedakan inspeksi kode, pengujian yang benar-benar dijalankan, dan persyaratan yang baru terdokumentasi. Untuk review khusus atau penelusuran temuan, ikuti [Panduan kerja agent Seller Hub](../AGENT-GUIDE.md). Ikuti bagian Penanganan temuan untuk tingkat dampak, status dan penerimaan risiko.

## Pembagian enforcement akses

| Bagian | Tanggung jawab |
| --- | --- |
| Authentication pipeline dan controller | Memvalidasi identitas dan menerapkan policy endpoint. Akses anonymous harus eksplisit sesuai keputusan produk. |
| UseCase | Memeriksa apakah identitas tersebut berhak menjalankan operasi pada objek yang dituju sebelum menghasilkan data terlindungi atau melakukan perubahan. Jangan mengandalkan controller sebagai satu-satunya pemeriksaan akses objek. |
| ICurrentUser | Menyediakan konteks identitas dari principal server yang sudah tervalidasi; tidak membentuk identitas dari body, query atau header mentah. UseCase tidak membaca HttpContext langsung. |
| Repository/SP | Menerapkan pembatasan data sesuai scope ownership/tenant yang diperlukan dan ditentukan server, pada read maupun write. Gunakan parameter; jangan mengambil seluruh data lintas scope lalu hanya memfilter response. |
| Request DTO | Menerima hanya field yang boleh ditentukan pengguna. Jangan memetakan request secara massal ke field sensitif seperti actor audit, role, owner atau tenant. Perubahan field tersebut memerlukan operasi dan izin eksplisit jika produk memang mendukungnya. |

`ICurrentUser` bukan pengganti authorization policy. Identitas valid tidak berarti seluruh objek boleh diakses. Identitas yang tidak tersedia tidak boleh berubah menjadi pengguna admin atau akses tanpa batas. Tenant yang dipilih client, bila alur produk mengizinkannya, harus diverifikasi terhadap hak pengguna sebelum menjadi scope server. Claim mapping dan sumber keanggotaan/permissions ditetapkan per produk; jangan mengarang nama claim atau model tenant.

Untuk mutasi, pembatasan data harus tetap berlaku saat write dijalankan, bukan hanya saat objek pertama kali dibaca. Contoh ilustratif bila produk menggunakan ownership/tenant:

- Pengguna A meminta objek milik B: policy endpoint yang lulus tidak cukup; jangan mengembalikan atau mengubah objek tanpa izin yang sesuai.
- Pengguna tenant A mengirim ID objek tenant B atau mengganti tenant pada request: scope server tidak boleh otomatis mengikuti input itu.
- Client mengirim actor audit atau field hak akses: nilai tersebut tidak boleh menimpa identitas server atau izin yang sudah ditetapkan.
- List, detail, download dan mutasi harus mematuhi scope yang sama sesuai aturan produk. Akses lintas pemilik/tenant untuk admin, bila diperlukan, harus merupakan izin eksplisit, bukan pengecualian bawaan.

Skenario ini adalah persyaratan perilaku untuk diperiksa saat implementasi, bukan hasil pengujian yang sudah lulus. Pilihan respons penolakan yang konsisten, roles, permissions, kebutuhan tenant, dan endpoint anonymous dicatat pada PRODUCT.md serta kontrak API sebelum implementasi terkait. Actor untuk proses non-HTTP juga memerlukan keputusan eksplisit; jangan memakai identitas palsu sebagai fallback.

## Checklist

- Authentication: validasi issuer, audience, signature dan expiry untuk bearer; konfigurasi cookie Secure, HttpOnly, SameSite sesuai alur bila cookie dipilih.
- Authorization: deny by default, policy-based melalui [Authorize(Policy = "...")]; [AllowAnonymous] hanya eksplisit. Periksa ownership/tenant setiap akses objek; UI tidak menjadi batas keamanan.
- Input: batas panjang, payload/file, paging dan format; validasi bisnis di server.
- SQL: parameter SP melalui Dapper; dynamic SQL di dalam SP tetap wajib parameterized. Allowlist sort identifiers. Gunakan akun database least privilege.
- Rate limit: partition sesuai user/IP yang dipercaya, khususnya login dan operasi mahal; tentukan kebijakan serta respons 429.
- CORS: allowlist origin; jangan wildcard origin dengan credentials.
- CSRF: gunakan perlindungan untuk credential yang dikirim browser otomatis seperti cookie. CORS bukan perlindungan CSRF. Tentukan token/antiforgery sesuai alur.
- Secrets: environment/secret manager; tidak di repo, sample atau log. Konfigurasi contoh memakai placeholder.
- Errors/logs: tidak membocorkan stack trace ke client; trace ID untuk diagnosis; redaksi data sensitif. Ikuti docs/BE/LOGGING.md untuk cakupan, korelasi audit, pengecualian secrets dan keputusan akses/retensi yang masih terbuka.
- Audit: actor dari identitas server, UTC timestamp, action, target ID, outcome dan trace ID; jangan mempercayai actor dari request.
- Transport/firewall: HTTPS, pembatasan akses DB, trusted reverse proxy. Firewall adalah konfigurasi infrastruktur, bukan atribut controller.
- File: batas ukuran, tipe yang tervalidasi, storage access policy dan nama aman; jangan mengeksekusi konten upload.
- Integrasi: timeout/cancellation, validasi URL tujuan, hindari SSRF dan retry mutasi yang tidak idempotent.
- Dependency: verifikasi versi, patch dan lisensi saat install; jangan menganggap daftar ini membuktikan produksi aman.

Review harus menyebut lokasi bukti, temuan, dampak dan perbaikan. Jika belum ada implementasi, tandai belum tersedia.

## Penanganan temuan

Catat setiap temuan pada catatan review atau tracker proyek yang dapat dirujuk kembali. Jangan memasukkan secrets atau data privat ke bukti; gunakan redaksi dan langkah reproduksi yang aman.

| Field | Isi |
| --- | --- |
| Identitas dan lokasi | ID/judul, path dan baris atau komponen terdampak. |
| Bukti | Perilaku teramati, kondisi pemicu, hasil inspeksi/test dan batas verifikasinya. Bedakan dugaan dari temuan terkonfirmasi. |
| Dampak dan tingkat | Critical, High, Medium atau Low beserta alasan berdasarkan dampak, cakupan akses/data dan kondisi eksploitasi pada proyek. Jangan mengarang skor atau menurunkan tingkat agar lolos pemeriksaan. |
| Perbaikan | Tindakan yang diperlukan dan penanggung jawab bila sudah diketahui. |
| Status | Terbuka, Dalam perbaikan, Menunggu verifikasi, Selesai terverifikasi, Risiko diterima, atau Bukan temuan dengan bukti pendukung. |
| Verifikasi/keputusan | Lokasi perubahan dan hasil pemeriksaan; untuk penerimaan risiko, pemilik proyek yang menyetujui, tanggal, alasan, risiko tersisa dan tindak lanjut. |

Critical/High wajib diperbaiki dan diverifikasi sebelum pekerjaan dinyatakan selesai atau siap digabung. Medium/Low wajib diperbaiki atau diterima risikonya secara eksplisit oleh pemilik proyek dengan alasan dan tindak lanjut yang dicatat. Agent tidak dapat menerima risiko atas nama pengguna. Diam, pilihan awal yang belum dikirim, atau build lulus bukan persetujuan penerimaan risiko.

Perbaikan kode tanpa verifikasi masih berstatus Menunggu verifikasi. Jika verifikasi relevan tidak dapat dijalankan, laporkan prasyaratnya; jangan menandai Selesai terverifikasi. Risiko diterima tetap terlihat sebagai risiko tersisa, bukan dianggap sudah diperbaiki. Klasifikasi Bukan temuan membutuhkan bukti bahwa kondisi yang dilaporkan memang tidak berlaku, bukan cara melewati aturan penyelesaian.

Jika bukti belum cukup untuk menentukan tingkat atau validitas, catat ketidakpastian dan lanjutkan penelusuran yang diperlukan. Jangan menyatakan aman atau siap digabung berdasarkan asumsi. Build/test umum yang lulus tidak menutup temuan tanpa bukti yang relevan terhadap masalahnya.

Aturan ini berlaku pada penanganan temuan dalam lingkup pekerjaan. Tidak mewajibkan audit penuh untuk setiap perubahan atau menetapkan platform tracker, alat pemindai maupun model bisnis tertentu. Cakupan pemeriksaan CI mengikuti bagian Pemeriksaan CI saat adopsi proyek di docs/BE/SETUP.md; platform, alat dan pemetaan hasilnya ditetapkan pada proyek konkret.
