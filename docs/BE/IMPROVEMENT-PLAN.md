# Riwayat Perbaikan Starter

Dokumen ini mencatat pekerjaan blueprint sebelum adopsi Seller Hub. Pernyataan tidak ada runtime di log adalah status historis blueprint, bukan keadaan repository Seller Hub saat ini. Untuk pekerjaan proyek, gunakan TODO.md dan ALIGNMENT-REVIEW.md.

Daftar ini mencatat hasil review struktur kode, keamanan, dan workflow agent. Semua item masih rencana kecuali statusnya menyatakan selesai. Daftar ini tidak menggantikan standar yang berlaku dan tidak membuktikan pengamanan runtime sudah tersedia.

## Cara pengerjaan

Kebutuhan fitur aplikasi wajib dilacak terpisah di [BASELINE-FEATURES.md](BASELINE-FEATURES.md) dan checklist [TODO.md](TODO.md). Status selesai di rencana perbaikan ini menunjukkan pekerjaan dokumentasi, bukan implementasi fitur aplikasi.

- Kerjakan bertahap sesuai urutan di bawah; selesaikan satu item sebelum berpindah kecuali pengguna mengubah prioritas.
- Untuk item yang membutuhkan keputusan, ajukan pilihan dan rekomendasi kepada pengguna sebelum menetapkan atau menerapkannya. Jangan menganggap rekomendasi sebagai persetujuan.
- Tanyakan keputusan saat item akan dikerjakan, bukan seluruh pertanyaan sekaligus. Keputusan khusus produk dapat ditunda sampai proyek konkret tersedia.
- Setelah keputusan disepakati, perbarui dokumen sumber dan semua rujukan yang terdampak. Catat keputusan arsitektur yang berdampak melalui ADR.
- Tandai selesai hanya setelah hasil dan kriteria selesai diperiksa. Catat bukti pemeriksaan serta keterbatasannya di log kemajuan.

Status: **STR-01–STR-04, SEC-01–SEC-05, AGT-01–AGT-04, DOC-01 dan DOC-02 selesai pada tingkat dokumentasi**. Detail **AUD-01 dan LOG-01 ditunda ke adopsi proyek**. Tidak ada runtime/pipeline yang diimplementasikan atau diuji; pekerjaan aplikasi dilacak terpisah pada TODO.md.

## Tahap 1 — Struktur folder dan penulisan kode

| ID | Perbaikan atau penegasan | Keputusan yang perlu ditanyakan | Dokumen target | Kriteria selesai |
| --- | --- | --- | --- | --- |
| STR-01 | Selesai: nama file/tipe operation, jumlah tipe per file dan namespace ditetapkan. | Disepakati: nama lengkap, satu tipe utama per file, nested hanya digunakan induknya, namespace mengikuti root namespace proyek dan folder. Tidak ada keputusan tersisa untuk item ini. | docs/CODING-STANDARDS.md, docs/ARCHITECTURE.md | Terpenuhi: contoh satu operation memuat nama file, tipe, namespace, dan pengecualian nested yang disepakati. |
| STR-02 | Selesai: subfolder feature dan Infrastructure, lokasi domain/repository, DI dan batas Common diperjelas. | Disepakati: Domain opsional, Repositories dalam feature, Persistence/{Feature} dan Integrations dalam Infrastructure, DI awal di Program.cs, Common lintas feature saja. | docs/ARCHITECTURE.md, docs/adr/0001-feature-folder-structure.md | Terpenuhi: pohon folder dan tanggung jawab tersedia, folder dibuat saat diperlukan, tetap satu proyek API. |
| STR-03 | Selesai: susunan folder dan penamaan test ditetapkan. | Disepakati: folder mengikuti feature/operation, nama file lengkap seperti CreateOrderUseCaseTests.cs dan CreateOrderEndpointTests.cs, metode Method_Kondisi_Hasil. | docs/CODING-STANDARDS.md, .agents/testing.md | Terpenuhi: contoh lokasi, file/tipe, namespace dan metode unit/integration test tersedia; framework tetap belum dipilih. |
| STR-04 | Selesai: penamaan schema/tabel/SP dan script migrasi berurutan ditetapkan. | Disepakati: dbo, tabel plural, SP Resources_Operation, script baru untuk setiap perubahan yang sudah diterapkan. Tool migrasi tetap diputuskan saat bootstrap. | docs/ARCHITECTURE.md, .agents/database.md, .agents/skills/stored-procedure/SKILL.md, docs/adr/0002-database-naming-and-migrations.md | Terpenuhi: contoh script, urutan, kompatibilitas dan pemulihan terdokumentasi; tidak ada perubahan database live. |

## Tambahan prioritas — Audit trail dan logging

Pembahasan ini sempat diprioritaskan sebelum SEC-04. Sesuai arahan terbaru, blueprint tetap agnostik: kemampuan umum dicatat, detail diputuskan saat adopsi proyek (ADR 0012). Detail audit/logging tidak menghalangi perbaikan workflow agent.

| ID | Kebutuhan dan status | Keputusan terbuka | Kriteria selesai |
| --- | --- | --- | --- |
| AUD-01 | Detail ditunda ke proyek: referensi audit pasangan via SP (ADR 0004), transaksi atomik (ADR 0005), cakupan interaksi pengguna (ADR 0006), snapshot JSON sebelum/sesudah + identitas/waktu/ID operasi (ADR 0007). | Nama/kolom dan tipe fisik, proses otomatis independen/pemeliharaan, perlindungan audit, akses dan retensi. | Architecture, Security, panduan database dan skill selaras dengan desain yang disepakati; rencana verifikasi mencakup mutasi dan kegagalan. |
| LOG-01 | Detail ditunda ke proyek: referensi konsep proses/korelasi (ADR 0008), file harian WIB dengan timestamp UTC (ADR 0009), retensi via admin/database, minimal 1 hari tanpa maksimum bisnis dan opsi cleanup nonaktif (ADR 0010), cleanup 01.00 WIB, awal nonaktif dan tanpa pengejaran jadwal terlewat serta lanjut saat gagal hapus per file (ADR 0011). | Library/provider, direktori/format/nama, batas ukuran/instance, level/field, korelasi, schema/tipe/refresh konfigurasi, scheduler/koordinasi instance/arsip, akses dan perilaku gangguan logging. | Konsep logging dan keputusan yang ditunda ke deployment terdokumentasi; audit data dibedakan dari log diagnostik. |

## Tahap 2 — Penjagaan keamanan

| ID | Perbaikan atau penegasan | Keputusan yang perlu ditanyakan | Dokumen target | Kriteria selesai |
| --- | --- | --- | --- | --- |
| SEC-01 | Selesai: routing keamanan berdasarkan bagian implementasi yang berubah ditambahkan ke instruksi utama dan workflow feature. | Tidak ada pilihan produk baru ditetapkan. | AGENTS.md, .agents/skills/backend-feature/SKILL.md, docs/SECURITY.md | Terpenuhi: feature, endpoint, database, upload dan integrasi dirutekan ke checklist relevan; pekerjaan lintas kategori tidak mengulang pemeriksaan. |
| SEC-02 | Selesai: enforcement controller, UseCase, repository/SP, ICurrentUser dan DTO diperjelas. | Disepakati: identitas server lewat ICurrentUser, izin objek di UseCase, scope data di repository/SP. Roles, permissions, tenant dan anonymous tetap diputuskan per produk. | docs/SECURITY.md, docs/ARCHITECTURE.md, docs/PRODUCT.template.md, docs/adr/0003-access-enforcement-and-current-user.md | Terpenuhi: pembagian tanggung jawab dan skenario lintas pemilik/tenant terdokumentasi tanpa mengarang model akses produk. |
| SEC-03 | Selesai: test akses negatif dan bukti verifikasinya menjadi kriteria selesai sesuai risiko perubahan. | Tidak ada pilihan produk baru; kontrak akses dan framework/fixture mengikuti proyek. | .agents/testing.md, .agents/skills/integration-test/SKILL.md, .agents/skills/backend-feature/SKILL.md | Terpenuhi: skenario akses dipetakan ke boundary yang sesuai, termasuk pembanding sukses dan alasan skenario tidak relevan/belum terverifikasi. |
| SEC-04 | Selesai: tetapkan cara mencatat tingkat dampak, status, bukti, dan penyelesaian temuan keamanan. | Disepakati: Critical/High wajib diperbaiki sebelum selesai/siap merge; Medium/Low diperbaiki atau diterima eksplisit oleh pemilik proyek dengan alasan dan tindak lanjut. Agent tidak menerima risiko atas nama pengguna. | .agents/security.md, docs/SECURITY.md | Format temuan dan aturan penyelesaian disepakati; build lulus tidak dianggap menyelesaikan temuan keamanan. |
| SEC-05 | Selesai untuk panduan: rencanakan pemeriksaan secrets dan dependency serta pengujian keamanan dalam CI setelah runtime tersedia. | Cakupan agnostik: secrets, dependency, build/test dan keamanan sesuai perubahan. Platform/alat ditunda ke adopsi; penanganan temuan mengikuti SEC-04. | docs/SETUP.md, docs/TODO.md; konfigurasi CI saat tersedia | Prasyarat dan pemeriksaan wajib terdokumentasi; implementasi baru dinyatakan selesai setelah ada hasil run nyata. |

## Tahap 3 — Workflow agent dan skill

| ID | Perbaikan atau penegasan | Keputusan yang perlu ditanyakan | Dokumen target | Kriteria selesai |
| --- | --- | --- | --- | --- |
| AGT-01 | Selesai: lengkapi routing backend-feature ke checklist endpoint, database, keamanan dan pengujian berdasarkan perubahan. | Tidak ada; gunakan tanggung jawab dan standar yang sudah disepakati. | AGENTS.md, .agents/skills/backend-feature/SKILL.md | Feature menyeluruh memiliki rujukan bersyarat; tidak menjalankan workflow yang sama berulang atau membentuk rujukan melingkar. |
| AGT-02 | Selesai: jadikan panduan testing pintu masuk pemilihan unit versus integration test. Batasi kebutuhan SQL Server pada test persistence/SP nyata. | Tidak ada untuk perbaikan routing; pemilihan framework ditunda ke bootstrap. | AGENTS.md, .agents/testing.md, .agents/skills/integration-test/SKILL.md | Unit test tidak diarahkan otomatis ke integration skill; test HTTP tidak selalu diwajibkan memakai database; mock tidak diklaim sebagai verifikasi DB. |
| AGT-03 | Selesai: kurangi duplikasi aturan antara panduan peran, skill dan dokumen standar; tetapkan sumber utama tiap aturan. | Disepakati: pertahankan panduan peran sebagai tanggung jawab/fokus; skill berisi langkah kerja; dokumen standar menjadi sumber aturan; AGENTS sebagai pintu masuk/routing. | .agents/*.md, .agents/skills/*/SKILL.md, AGENTS.md | Tidak ada aturan yang bertentangan; rujukan tetap valid; penjelasan bahwa panduan peran bukan subagent otomatis dipertahankan. |
| AGT-04 | Selesai: tambahkan workflow bootstrap agar repo tanpa runtime memiliki langkah awal yang jelas. | Disepakati: gunakan docs/SETUP.md tanpa skill bootstrap tambahan. Validator, authentication, test framework dan paket diputuskan saat bootstrap sesuai kebutuhan. | docs/SETUP.md, AGENTS.md | Workflow mencakup inspeksi SDK, keputusan terbuka, scaffold, konfigurasi, build/test dan pelaporan; tidak membuat domain atau authentication bypass. |

## Tahap 4 — Pemeriksaan konsistensi

| ID | Perbaikan atau penegasan | Keputusan yang perlu ditanyakan | Dokumen target | Kriteria selesai |
| --- | --- | --- | --- | --- |
| DOC-01 | Selesai: sinkronkan petunjuk adopsi, contoh prompt, status starter dan rujukan setelah perbaikan selesai. | Tidak ada. | README.md, docs/SETUP.md, docs/TODO.md, AGENTS.md | Semua path rujukan tersedia; contoh sesuai workflow final; status membedakan dokumen tersedia dan implementasi yang sudah diverifikasi. |
| DOC-02 | Selesai untuk inspeksi dokumen: periksa workflow dengan contoh tugas: feature baru, endpoint saja, SP saja, unit test, integration test dan review keamanan. | Tidak ada untuk pemeriksaan dokumen; pengujian eksekusi memerlukan runtime yang tersedia. | Dokumen/skill terkait dan log kemajuan di bawah | Setiap contoh menemukan jalur yang jelas; hasil pemeriksaan dokumen dibedakan dari hasil build/test runtime. |

## Log kemajuan

- Daftar awal dibuat berdasarkan review repository. Belum ada standar baru yang ditetapkan, implementasi runtime dibuat, atau item perbaikan ditandai selesai.
- STR-01: pengguna memilih nama lengkap. Aturan dan contoh file/tipe request, response, validator opsional, serta UseCase ditambahkan ke CODING-STANDARDS.md dan ARCHITECTURE.md. Contoh menggunakan Order hanya sebagai ilustrasi, bukan keputusan domain.
- Pemeriksaan dokumentasi: contoh file dan tipe konsisten dengan pola nama lengkap; tidak ada runtime yang dibuat atau test runtime yang dijalankan. STR-01 belum selesai karena jumlah tipe per file, pengecualian nested dan namespace masih menunggu keputusan pengguna.
- STR-01 selesai setelah pengguna menyetujui satu tipe utama per file, nested hanya digunakan induknya, dan namespace mengikuti folder. CODING-STANDARDS.md dan contoh ARCHITECTURE.md telah diselaraskan. Pemeriksaan contoh memastikan nama file/tipe, namespace dan pengecualian konsisten; ini pemeriksaan dokumentasi, bukan pengujian runtime.
- Berikutnya: STR-02, susunan subfolder feature dan Infrastructure, lokasi domain, repository abstraction, DI serta batas Common. Pilihan struktur perlu dibahas dengan pengguna sebelum diterapkan.
- STR-02 selesai setelah pengguna menyetujui susunan folder. ARCHITECTURE.md diperbarui dengan pohon folder, namespace dan pembagian UseCase/Domain/repository; keputusan dicatat di ADR 0001. Pemeriksaan konsistensi dokumen dilakukan; tidak ada scaffold runtime atau test runtime yang dijalankan.
- Berikutnya: STR-03, struktur folder test serta penamaan file dan metode test; menunggu keputusan pengguna.
- STR-03 selesai setelah pengguna menyetujui struktur folder dan penamaan test. CODING-STANDARDS.md memuat contoh unit/integration test dan .agents/testing.md merujuk aturan tersebut. Contoh diselaraskan dengan aturan nama lengkap, namespace dan akhiran Async yang sudah berlaku. Pemeriksaan dokumentasi dilakukan; tidak ada framework dipasang atau test runtime dijalankan.
- Berikutnya: STR-04, penamaan schema/SP dan versi script database. Pilihan ini belum ditetapkan dan perlu dibahas dengan pengguna.

- STR-04 selesai setelah pengguna menyetujui konvensi database. Architecture, panduan database dan skill stored-procedure diselaraskan; keputusan disimpan di ADR 0002. Pemeriksaan dokumentasi dilakukan; penerapan migrasi dan upgrade belum diuji karena runtime/database test serta runner belum tersedia.
- Berikutnya: SEC-01, routing pemeriksaan keamanan sesuai jenis perubahan. Tidak memerlukan keputusan produk baru.
- SEC-01 selesai: SECURITY.md memuat tabel pemicu dan checklist terkait, AGENTS.md mengarahkan perubahan implementasi ke tabel tersebut, dan backend-feature memuat pemeriksaan serta pelaporannya. Penelusuran dokumen untuk contoh endpoint baca, SP paging, upload, integrasi dan perubahan format menunjukkan routing yang sesuai tanpa memaksakan audit penuh. Validasi skill dan pemeriksaan diff dijalankan; tidak ada runtime atau pengujian keamanan aplikasi yang dijalankan.
- Berikutnya: SEC-02, pembagian enforcement akses dan konteks identitas. Pola komponen memerlukan keputusan pengguna; roles/permissions/tenant tetap diputuskan per produk.


- SEC-02 selesai setelah persetujuan pengguna. Architecture dan Security memuat pembagian enforcement serta ICurrentUser; template produk memuat pertanyaan akses/identitas yang masih terbuka. ADR 0003 mencatat keputusan. Pemeriksaan konsistensi dan skenario dokumen dilakukan; belum ada implementasi atau test runtime.
- Berikutnya: SEC-03, test akses negatif sebagai kriteria selesai sesuai risiko feature; tidak memerlukan pilihan produk baru untuk mendokumentasikan skenario.
- SEC-03 selesai: panduan testing memuat matriks akses negatif, boundary pembuktian, pemeriksaan efek samping dan kriteria pelaporan; workflow backend-feature serta integration-test merujuknya. Validasi kedua skill dan pemeriksaan diff dijalankan. Skenario dokumen ditelusuri untuk endpoint terlindungi, ownership, tenant dan field sensitif; ini belum merupakan hasil pengujian aplikasi.
- Berikutnya: SEC-04, tingkat dampak temuan, aturan yang memblokir penyelesaian/merge dan pihak yang dapat menerima risiko tersisa. Memerlukan keputusan pengguna sebelum ditetapkan.


- Arahan terbaru: repo menjadi blueprint agnostik lintas proyek. ADR 0012 menetapkan batas penerapan; BASELINE-FEATURES memuat kemampuan umum, LOGGING memuat panduan umum, dan profil file harian/admin dipindahkan ke docs/reference/LOGGING-FILE-ADMIN-PROFILE.md. Riwayat keputusan tetap disimpan; detail operasional tidak otomatis diwajibkan.
- AGT-01 selesai: backend-feature menjadi alur utama dengan checklist endpoint, persistence, keamanan dan test sesuai perubahan. Skill spesialis tidak memulai ulang workflow feature.
- AGT-02 selesai: .agents/testing.md menjadi pintu pemilihan unit, HTTP, database atau gabungan. SQL Server hanya diperlukan untuk skenario persistence nyata; mock tidak dihitung sebagai bukti database.
- Pemeriksaan jalur dokumen: feature dengan DB menuju backend-feature lalu spesialis; endpoint saja menuju api-endpoint; unit test menuju testing tanpa integration skill; HTTP-only menuju integration-test tanpa kewajiban DB; SP saja menuju stored-procedure; review keamanan menuju security. Ini pemeriksaan routing dokumen, bukan hasil test aplikasi.
- Berikutnya AGT-03: putuskan apakah panduan peran dipertahankan dengan batas tanggung jawab yang tegas atau digabung ke skill/dokumen. Jangan kembali ke detail audit/logging sebelum ada kebutuhan proyek.
- Verifikasi akhir perubahan ini: keempat skill lulus quick_validate, tautan Markdown lokal tidak ada yang putus, dan git diff --check lulus. Repo masih dokumentasi saja; tidak ada build/test runtime yang dijalankan.

- AGT-03: pengguna menyetujui pembagian sumber aturan. AGENTS memuat peta kepemilikan; empat panduan peran dipersempit ke tanggung jawab/fokus; standar pengujian dipindahkan ke docs/TESTING.md; skill merujuk standar dan mempertahankan langkah kerja. Ringkasan aturan kode, akses dan database yang berulang diganti dengan rujukan. Tidak ada perubahan keputusan stack atau fitur produk.
- Berikutnya AGT-04: pilih cukup panduan setup atau skill bootstrap-backend tersendiri. Rekomendasi awal: gunakan docs/SETUP.md sebagai workflow bootstrap karena repo masih blueprint dan belum memerlukan skill baru. Keputusan ini belum ditetapkan.
- Verifikasi AGT-03: keempat skill lulus validator; git diff --check lulus; tautan Markdown lokal tidak putus. Penelusuran feature, endpoint, SP, unit, integration dan review keamanan tetap mencapai standar yang relevan. Tidak ada runtime test dijalankan karena perubahan dokumentasi.

- AGT-04: pengguna menyetujui workflow bootstrap melalui docs/SETUP.md tanpa skill baru. Panduan mencakup inspeksi SDK/repository, keputusan bertahap, scaffold di proyek tujuan, konfigurasi, persistence bersyarat, restore/build/test/startup dan pelaporan bukti. AGENTS, README dan TODO diselaraskan. Tidak ada runtime dibuat pada blueprint.
- Berikutnya SEC-04: sepakati format temuan keamanan, aturan yang memblokir penyelesaian dan pihak yang menerima risiko tersisa. SEC-05 serta DOC-01/DOC-02 tetap terbuka; detail audit/logging tetap ditunda ke adopsi proyek.
- Verifikasi AGT-04: git diff --check lulus dan tautan Markdown lokal valid. Jalur dokumen diperiksa untuk repository baru, aplikasi yang sudah tersedia, keputusan authentication tertunda dan database test belum tersedia. Tidak ada restore/build/test runtime dijalankan karena pekerjaan ini hanya menyusun panduan blueprint.

- SEC-04: pengguna menyetujui aturan penanganan temuan. SECURITY.md memuat format bukti, tingkat, status, perbaikan dan penerimaan risiko; panduan security merujuk sumber tersebut. Build lulus bukan bukti penutupan temuan. Preferensi pilihan keputusan interaktif dicatat di AGENTS.md dengan fallback teks bila tidak tersedia.
- Berikutnya SEC-05: sepakati cakupan panduan CI yang tetap agnostik; pemilihan platform dan alat ditunda ke proyek konkret.
- Verifikasi SEC-04: git diff --check lulus; rujukan aktif tidak lagi menyatakan SEC-04 tertunda. Skenario Critical/High terbuka, Medium/Low diterima eksplisit, perbaikan belum diverifikasi dan build lulus tanpa bukti penutupan ditelusuri pada aturan dokumen. Tidak ada pengujian keamanan runtime karena repo masih blueprint dokumentasi.

- SEC-05: dilanjutkan sesuai arahan pengguna. SETUP.md memuat cakupan CI agnostik, prasyarat alat/runner, bukti hasil, penanganan kegagalan scan dan rujukan SEC-04. Platform/alat tetap keputusan proyek; tidak ada pipeline dibuat.
- DOC-01: AGENTS, README, SETUP, TODO dan rujukan Security diselaraskan; template produk tidak lagi menyebut domain proyek terdahulu. Status membedakan panduan tersedia dari runtime/CI yang belum tersedia. Catatan kemajuan sebelumnya dipertahankan sebagai riwayat, bukan status terkini.

### Pemeriksaan akhir DOC-02

Pemeriksaan berikut adalah penelusuran instruksi, bukan simulasi agent independen atau pengujian aplikasi.

| Contoh tugas | Jalur yang diperiksa | Hasil inspeksi |
| --- | --- | --- |
| Feature baru dengan endpoint dan persistence | AGENTS → backend → backend-feature → api-endpoint/stored-procedure → testing/Security | Spesialis digunakan bersyarat dalam satu alur; fakta produk yang belum ada ditanyakan. |
| Endpoint saja | AGENTS → api-endpoint → API Contract/Security → testing | Tidak memulai ulang seluruh feature; HTTP-only tidak mewajibkan DB. |
| SP saja | AGENTS → database → stored-procedure → Architecture/Security | Kontrak schema diperiksa, migrasi berversi dan transaksi sesuai standar; bukti DB memakai SQL Server test. |
| Unit test UseCase | AGENTS → testing → TESTING/Coding Standards | Tidak diarahkan ke integration skill atau memerlukan DB. |
| Integration HTTP atau DB | AGENTS → testing → integration-test | Boundary dipilih; mock tidak membuktikan DB; dependency tidak tersedia dilaporkan belum terverifikasi. |
| Review keamanan | AGENTS → security → Security/Testing | Temuan memakai bukti dan status SEC-04; build lulus tidak menutup temuan. |
| Bootstrap dan CI | AGENTS → SETUP → standar terkait | Keputusan diperlukan bertahap; tidak membuat domain, bypass atau konfigurasi vendor tanpa konteks proyek. |

- DOC-02: semua jalur di atas dapat ditelusuri tanpa workflow implementasi yang berulang. Rujukan balik ke AGENTS/standar adalah pembacaan aturan, bukan instruksi memulai ulang tugas. Validasi eksekusi tetap memerlukan proyek konkret.
- Verifikasi akhir SEC-05/DOC-01/DOC-02: git diff --check lulus; empat skill lulus quick_validate; tautan Markdown dan rujukan path dokumen diperiksa (PRODUCT.md memang baru dibuat saat adopsi). Tidak ada build, test aplikasi, pemindaian keamanan atau run CI yang diklaim. Daftar perbaikan awal ditutup pada tingkat dokumentasi; detail produk tetap ditunda.
