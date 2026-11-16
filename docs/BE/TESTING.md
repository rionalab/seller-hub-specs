# Testing Standards

Pilih test yang memverifikasi perilaku; jangan menambah test yang hanya menyalin implementasi. Pilih berdasarkan perilaku dan risiko, bukan sekadar file yang berubah. Periksa sukses, invalid, missing, conflict dan forbidden sesuai operasi. Gunakan fixture deterministik dan cleanup bila ada state; jangan menyentuh produksi. Catat test skipped beserta alasannya. Jangan mengubah expected output sekadar agar test lulus.

Untuk penempatan dan penamaan test, ikuti bagian Struktur dan penamaan test di docs/BE/ARCHITECTURE.md. Konvensi folder tidak menentukan framework test.

## Pemilihan jenis pengujian

| Perilaku yang ingin dibuktikan | Pengujian | Kebutuhan database |
| --- | --- | --- |
| Invariant domain, validasi atau keputusan/alur UseCase | Unit test; gunakan dependency pengganti bila diperlukan | Tidak perlu database atau integration skill. |
| Binding, routing, middleware, envelope/status dan policy HTTP | Integration HTTP lewat host test; gunakan integration-test skill | Hanya bila skenario turut menguji persistence. Dependency pengganti diperbolehkan dengan batas bukti yang jelas. |
| Mapping Dapper, parameter/result SP, scope query, transaksi atau audit persistence | Integration database nyata lewat integration-test skill | SQL Server test terisolasi dengan schema/SP relevan. Mock bukan bukti perilaku DB. |
| Satu alur HTTP sampai penyimpanan | Integration gabungan bila risiko menuntutnya | Host test dan database test terisolasi. |

Inspeksi framework/fixture proyek; jangan memilih atau memasang framework baru hanya karena panduan ini. Jika DB tidak tersedia, unit/HTTP yang independen tetap dapat dijalankan; tandai hanya verifikasi DB sebagai belum dijalankan. Tugas dokumentasi saja tidak memerlukan runtime test. Perluas pengujian bila perubahan menyentuh boundary lain, bukan otomatis menjalankan seluruh kategori.

## Test akses negatif sebagai kriteria selesai

Untuk feature atau perubahan yang memengaruhi akses, pilih skenario berikut berdasarkan kontrak dan model akses produk. Sertakan skenario akses yang diizinkan sebagai pembanding agar test tidak lulus hanya karena semua permintaan ditolak. Gunakan test yang sudah ada jika sudah mencakup perilaku yang berubah; tidak perlu menduplikasinya.

| Skenario | Kapan diperlukan | Bukti perilaku |
| --- | --- | --- |
| Belum login | Endpoint mensyaratkan authentication | Respons sesuai kontrak untuk pengguna anonymous, tanpa data terlindungi atau perubahan bisnis. |
| Policy/permission tidak terpenuhi | Operasi dibatasi policy/permission | Pengguna yang terautentikasi tetapi tidak berhak ditolak; operasi tidak dijalankan. |
| Objek milik pengguna lain | Ada batas ownership | Detail/download atau mutasi objek di luar izin tidak membocorkan data dan tidak mengubah objek; list tidak memuat objek di luar scope. |
| Tenant berbeda atau tenant input dipalsukan | Produk memakai batas tenant | Scope server tetap berlaku saat membaca dan menulis meskipun ID objek atau tenant pada input diganti. |
| Field sensitif dikirim client | Input dapat memengaruhi actor, owner, tenant atau hak akses | Request ditolak atau field diabaikan sesuai kontrak; nilai sensitif tidak tertimpa. Actor audit tetap berasal dari server bila audit berlaku. |

Pilih boundary yang membuktikan aturan: unit test UseCase untuk keputusan izin objek, test HTTP untuk authentication/policy/binding, dan integration SQL Server nyata untuk pembatasan scope read/write di repository/SP. Pengganti ICurrentUser pada unit test atau authentication handler test tidak membuktikan validasi token produksi. Jika pipeline authentication diubah, uji perilaku validasi yang berubah dengan konfigurasi test yang sesuai.

Untuk penolakan mutasi, periksa bahwa perubahan bisnis dan efek eksternal yang dilarang tidak terjadi, bukan hanya status HTTP. Pencatatan audit penolakan yang memang diwajibkan tetap diperbolehkan. Status serta envelope mengikuti kontrak API; jangan menetapkan 403/404 untuk akses objek tanpa keputusan kontrak produk.

Kriteria verifikasi akses dinyatakan terpenuhi hanya jika skenario yang relevan sudah diuji dan lulus. Laporkan lokasi test, boundary, hasil run dan batas verifikasi. Tandai skenario yang tidak relevan beserta alasan singkat, misalnya produk tidak menggunakan tenant. Jika aturan akses belum diputuskan, tanyakan; jika runtime/fixture tidak tersedia, tandai verifikasi belum selesai dengan prasyaratnya. Test gagal atau skipped tidak dihitung lulus, dan inspeksi dokumen tidak menggantikan pengujian runtime.
