# Menggunakan Starter

Dokumen ini adalah panduan starter yang diadopsi Seller Hub. Repository ini sudah memiliki fondasi runtime; lihat README.md dan ALIGNMENT-REVIEW.md untuk kondisi aktual, jangan scaffold ulang. Dokumen ini adalah workflow bootstrap; tidak diperlukan skill bootstrap tersendiri. Mengedit blueprint tidak berarti menjalankan bootstrap atau membuat runtime di repository ini.

## Adopsi ke proyek

1. Gunakan starter sebagai template pada repository tujuan yang sudah ditentukan pengguna. Periksa lokasi tujuan dan isi yang sudah ada sebelum menyalin; jangan menimpa pekerjaan lokal atau membawa domain proyek lain.
2. Baca [AGENTS.md](../../../seller-hub-backend/AGENTS.md), [Architecture](ARCHITECTURE.md) dan [Coding Standards](ARCHITECTURE.md). Jalankan agent dari root repository tujuan.
3. Untuk Seller Hub, checkout seller-hub-specs sejajar dengan backend dan baca [produk bersama](../PRODUCT.md). Jangan membuat salinan PRODUCT dari template di backend. Template hanya referensi adopsi proyek lain.
4. Petakan [kemampuan dasar](BASELINE-FEATURES.md) ke backlog sesuai cakupan proyek. Detail logging/audit diputuskan ketika implementasinya diperlukan; profil pada reference/ hanya berlaku jika diadopsi secara eksplisit.

Pilih checklist tugas di [Panduan kerja agent Seller Hub](../AGENT-GUIDE.md) sesuai rujukan AGENTS.md.

## Workflow bootstrap runtime

### 1. Inspeksi lingkungan dan pekerjaan yang tersedia

Periksa status perubahan lokal, solution/project, global.json, konfigurasi paket, fixture test dan CI bila ada. Gunakan SDK/toolchain yang sudah ditetapkan proyek; jangan melakukan scaffold ulang atas aplikasi yang sudah tersedia.

Periksa SDK .NET yang terpasang, misalnya melalui dotnet --info dan dotnet --list-sdks. Cocokkan dengan target dalam Architecture. Verifikasi versi paket, kompatibilitas dan dukungannya dari sumber resmi saat bootstrap, lalu pin versi SDK/paket yang dipilih pada konfigurasi proyek. Jangan menebak versi dari ingatan atau memasang paket yang belum dibutuhkan.

Jika SDK, package source atau akses jaringan tidak tersedia, laporkan prasyarat dan lanjutkan inspeksi/dokumentasi yang independen. Jangan mengklaim build telah lulus.

### 2. Selesaikan keputusan yang diperlukan

| Keputusan | Saat diperlukan | Sumber catatan |
| --- | --- | --- |
| Repository tujuan, nama solution/project dan root namespace | Sebelum scaffold | PRODUCT.md atau keputusan proyek |
| Validator dan paket yang diperlukan | Sebelum implementasi validasi | Architecture dan ADR proyek |
| Authentication/provider, model akses dan konfigurasi identitas | Sebelum endpoint terlindungi | PRODUCT.md, Security dan ADR proyek |
| Framework test dan fixture | Sebelum membuat proyek/test | Keputusan proyek dan standar Testing |
| Koneksi SQL Server test, runner dan riwayat migrasi | Sebelum menerapkan/menguji persistence | Architecture dan ADR proyek |
| Logging, konfigurasi dan kebutuhan operasional | Sebelum implementasi bagian terkait | PRODUCT.md dan Logging |

Gunakan keputusan yang sudah disetujui tanpa menanyakannya lagi. Jika keputusan belum ada, ajukan pilihan beserta rekomendasi hanya untuk langkah yang bergantung padanya. Lanjutkan bagian independen. Jangan menjadikan domain lengkap, UI admin, jadwal retensi atau seluruh schema sebagai prasyarat membuat kerangka aplikasi.

### 3. Buat atau lengkapi kerangka aplikasi

Pada repository tujuan, buat solution dan satu proyek API berbasis Controllers sesuai [Architecture](ARCHITECTURE.md). Gunakan nama proyek/namespace yang telah ditentukan. Sesuaikan scaffold dengan [Coding Standards](ARCHITECTURE.md); hapus endpoint/domain demo bawaan yang tidak termasuk kebutuhan produk.

Tambahkan paket hanya untuk komponen yang dikerjakan. Hubungkan dependency di composition root dan buat folder saat ada kode yang membutuhkannya. Proyek test dibuat ketika boundary, framework dan kebutuhan test sudah jelas; tidak perlu membuat folder kosong atau feature contoh untuk melengkapi pohon direktori.

Implementasikan komponen dasar yang masuk cakupan bootstrap, misalnya kontrak response/error dan OpenAPI, mengikuti [API Contract](API-CONTRACT.md) serta [Security](SECURITY.md). Catat kemampuan dasar yang belum dikerjakan sebagai backlog. Jangan membuat authentication bypass, token palsu, domain, schema atau permissions agar scaffold terlihat lengkap. Bila authentication belum diputuskan, tunda endpoint terlindungi; jangan menjadikannya anonymous sebagai jalan pintas.

### 4. Siapkan konfigurasi dan persistence sesuai kebutuhan

Gunakan konfigurasi bertipe dan validasi sesuai Coding Standards. Contoh konfigurasi hanya berisi placeholder; secrets memakai mekanisme lingkungan/proyek sesuai Security. Jelaskan konfigurasi minimum untuk menjalankan aplikasi tanpa menampilkan nilai rahasia.

Jika persistence dibutuhkan, ikuti panduan database dan skill stored-procedure untuk koneksi, mapping, migrasi dan transaksi. Terapkan script hanya pada target pengembangan/test yang memang diotorisasi. Belum tersedianya database tidak menghalangi pemeriksaan unit atau HTTP yang independen.

### 5. Verifikasi hasil yang benar-benar dibuat

- Jalankan restore dan build pada solution/project tujuan dengan SDK yang dipilih.
- Jalankan test yang relevan dan pemeriksaan wajib proyek sesuai [Testing](TESTING.md). Tidak adanya test bukan hasil test lulus; catat cakupan yang belum diverifikasi.
- Bila aplikasi sudah dapat dijalankan, lakukan pemeriksaan startup dan perilaku HTTP yang tersedia, termasuk kontrak error/akses yang diimplementasikan. Hentikan proses pengujian setelah selesai.
- Jika persistence berubah, verifikasi migrasi, mapping dan transaksi pada SQL Server test terisolasi. Mock bukan bukti perilaku database.
- Periksa perubahan agar tidak membawa secrets, domain demo atau modifikasi di luar cakupan. Bedakan hasil restore/build, test, pemeriksaan startup dan inspeksi dokumen.

Kegagalan atau dependency yang belum tersedia tetap dicatat sebagai belum selesai; jangan menyamakan skipped dengan passed. Bootstrap yang dapat dibangun belum membuktikan seluruh kemampuan dasar atau keamanan produksi selesai.

### 6. Catat status dan langkah berikutnya

Laporkan lokasi proyek, komponen yang dibuat, SDK/paket yang dipilih, konfigurasi yang diperlukan, hasil pemeriksaan serta keterbatasannya. Perbarui backlog proyek dan ADR yang relevan. Daftar [TODO blueprint](TODO.md) menunjukkan kondisi starter; proyek turunan menyesuaikannya berdasarkan implementasi dan bukti nyata.

Lanjutkan feature pertama dengan bagian Fitur backend di [Panduan kerja agent](../AGENT-GUIDE.md) hanya setelah scope, acceptance criteria dan kontrak bagian terkait jelas. Keputusan yang belum diperlukan tetap terbuka, bukan diisi dengan asumsi bisnis.

## Pemeriksaan CI saat adopsi proyek

Panduan ini menentukan cakupan pemeriksaan, bukan platform atau alat tertentu. Pada proyek konkret, inspeksi pipeline yang tersedia terlebih dahulu. Putuskan platform CI, runner, alat pemindai dan versinya, sumber advisory dependency, akses package source, serta fixture/database test sebelum mengonfigurasi bagian terkait. Catat keputusan dan cara menjalankannya pada dokumentasi proyek. Jangan membuat konfigurasi vendor atau memasang alat secara otomatis pada blueprint ini.

| Pemeriksaan wajib | Cakupan dan bukti |
| --- | --- |
| Secrets | Periksa perubahan/file yang akan digabung, termasuk konfigurasi, contoh dan script. Catat cakupan pemindaian serta hasil tanpa menampilkan nilai rahasia. Cakupan history diputuskan saat adopsi. |
| Dependency | Periksa dependency langsung dan transitif yang tersedia terhadap sumber advisory alat terpilih. Catat versi, sumber/tanggal pemeriksaan dan keterbatasan cakupan. |
| Restore dan build | Gunakan SDK/paket yang ditetapkan proyek; simpan hasil run pada revisi yang diuji. |
| Test perilaku dan keamanan | Jalankan suite relevan sesuai [Testing](TESTING.md) dan kategori perubahan pada [Security](SECURITY.md), termasuk skenario akses negatif bila berlaku. Database nyata hanya untuk boundary persistence. |

Jalankan pemeriksaan yang berlaku sebagai bagian validasi perubahan sebelum dinyatakan siap digabung. Perubahan dokumentasi tanpa runtime tetap diperiksa untuk secrets dan konsistensi rujukan; build/test atau dependency yang tidak berlaku dicatat dengan alasan, bukan diklaim lulus. Ketika runtime tersedia, konfigurasi pipeline harus benar-benar menjalankan pemeriksaan yang diwajibkan proyek.

Temuan dan ambang penyelesaian mengikuti bagian Penanganan temuan di [Security](SECURITY.md). Petakan tingkat hasil alat ke kebijakan tersebut dan dokumentasikan alasannya; jangan mengubah ambang untuk menyembunyikan temuan. False positive memerlukan bukti, dan penerimaan risiko harus tercatat sesuai SEC-04. Jangan menonaktifkan pemeriksaan atau mengabaikan semua hasil agar pipeline terlihat hijau.

Pemeriksaan wajib yang gagal berjalan, terlewat atau kehilangan dependency belum memberi bukti lulus. Catat prasyarat dan lanjutkan checks independen; jangan menyatakan validasi lengkap sebelum pemeriksaan wajib terpenuhi. Build lulus tidak menggantikan hasil pemindaian atau test keamanan.

Gunakan kredensial test dengan akses minimum, secret storage milik lingkungan CI, dan database terisolasi. Jangan memberikan secrets produksi kepada job pengujian atau kode dari sumber yang tidak dipercaya. Simpan hasil yang sudah direduksi data sensitifnya beserta revisi, konfigurasi alat dan keputusan tindak lanjut sehingga dapat ditelusuri.

Implementasi CI baru dinyatakan tersedia setelah ada konfigurasi di proyek dan bukti run nyata. Dokumen ini tidak menyediakan pipeline, status check wajib pada platform, deployment atau bukti keamanan runtime.

## Contoh permintaan

- Baca AGENTS.md dan docs/BE/SETUP.md. Bootstrap proyek pada repository tujuan yang saya tentukan; gunakan keputusan yang sudah tersedia dan tanyakan keputusan yang menghalangi scaffold.
- Gunakan $backend-feature untuk implementasi feature sesuai PRODUCT.md dan acceptance criteria berikut ...
- Gunakan $api-endpoint untuk perubahan kontrak endpoint berikut ... tanpa memperluas domain.
- Baca docs/BE/TESTING.md untuk unit test UseCase berikut ...
- Baca docs/BE/SECURITY.md untuk review perubahan berikut ...; catat bukti dan batas pemeriksaan.
- Gunakan $stored-procedure untuk membuat SP paging sesuai schema berikut ...
- Gunakan $integration-test untuk memverifikasi endpoint ... termasuk forbidden dan konflik.

## Update standar

Menyalin template menghasilkan snapshot. Perubahan starter tidak otomatis memperbarui repository turunan; review diff dan terapkan perubahan yang relevan. Pertahankan keputusan khusus proyek ketika menyelaraskan panduannya.

## Workspace Seller Hub

Ikuti [peta sumber bersama](../REPOSITORY-MAP.md). Clone specs, backend dan frontend sebagai folder sejajar. Remote specs belum ditetapkan; jangan menebak URL. Perubahan bisnis dan panduan teknis berada di specs. Backend tetap menghasilkan artefak OpenAPI aktif di repo kodenya.
