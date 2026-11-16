# Architecture

## Default dan batas

Blueprint ini agnostik terhadap domain dan detail operasional proyek. Default stack serta konvensi kode tetap berlaku; contoh nama/domain dan profil audit/logging tidak menetapkan schema, UI, timezone, provider atau jadwal lintas proyek. Detail dipilih saat adopsi, bukan saat memperbaiki struktur agent. Lihat ADR 0012.

Target .NET 10, Controllers, modular monolith awal, feature-based Vertical Slice. Ini standar yang diadopsi Seller Hub; fondasi runtime dan paket sudah tersedia. Fitur bisnis belum diimplementasikan.
Pisahkan microservice hanya jika ada kebutuhan batas domain, ownership, scaling, atau deployment yang nyata. Jangan membuat layanan baru otomatis.

## Struktur kode saat implementasi
- src/Seller.Api/Features/{Feature}/{Operation}/: request, response, validator bila dipakai, UseCase.
- src/Seller.Api/Features/{Feature}/: controller milik feature.
- src/Seller.Api/Features/{Feature}/Domain/: entities/value objects dengan aturan bisnis, hanya bila diperlukan.
- src/Seller.Api/Features/{Feature}/Repositories/: repository abstraction milik feature.
- src/Seller.Api/Infrastructure/Persistence/: connection factory; implementasi repository Dapper dikelompokkan dalam subfolder {Feature}/.
- src/Seller.Api/Infrastructure/Integrations/: implementasi integrasi eksternal sesuai kebutuhan.
- src/Seller.Api/Common/: ApiResponse, AppMessages, exception middleware dan utilitas lintas feature yang benar-benar diperlukan.
- database/migrations/: perubahan schema dan stored procedure dalam script berversi berurutan.
- tests/Backend.UnitTests/ dan tests/Backend.IntegrationTests/.

File dan tipe dalam folder operation memakai nama lengkap sesuai [standar kode](#standar-kode). Contoh ilustratif, bukan domain bawaan starter:

```text
src/Seller.Api/
├── Program.cs
├── Features/
│   └── Orders/
│       ├── OrdersController.cs
│       ├── Domain/                  (bila ada perilaku/invariant domain)
│       │   └── Order.cs
│       ├── Repositories/
│       │   └── IOrderRepository.cs
│       └── CreateOrder/
│           ├── CreateOrderRequest.cs
│           ├── CreateOrderResponse.cs
│           ├── CreateOrderValidator.cs    (bila validator dipakai)
│           └── CreateOrderUseCase.cs
├── Infrastructure/
│   ├── Persistence/
│   │   ├── SqlConnectionFactory.cs
│   │   └── Orders/
│   │       └── OrderRepository.cs
│   └── Integrations/                (bila ada integrasi eksternal)
└── Common/                          (komponen lintas feature)
```

Keempat file dalam folder CreateOrder menggunakan namespace `Seller.Api.Features.Orders.CreateOrder` jika root namespace proyek adalah `Seller.Api`. File lain mengikuti foldernya, misalnya `IOrderRepository` memakai `Seller.Api.Features.Orders.Repositories` dan `OrderRepository` memakai `Seller.Api.Infrastructure.Persistence.Orders`. Aturan jumlah tipe per file dan pengecualian nested dijelaskan di [standar kode](#standar-kode).

Buat folder saat ada kode yang membutuhkannya; jangan membuat folder kosong atau domain contoh sebagai bagian wajib bootstrap. `Common` hanya menampung komponen yang benar-benar digunakan lintas feature, bukan tempat memindahkan aturan bisnis milik satu feature. Registrasi dependency awal berada di `Program.cs`, termasuk penghubung interface repository ke implementasinya; tidak diperlukan proyek/assembly atau lapisan DI tambahan untuk struktur ini.

Mulai satu proyek API; folder tidak harus menjadi assembly. Domain tidak bergantung pada ASP.NET, Dapper, atau SQL client. UseCase bergantung pada abstraction repository; Infrastructure mengimplementasikannya. Controller memanggil UseCase; Program.cs menjadi composition root. Jangan memanggil satu controller dari controller lain.

## Alur
Controller → validation → UseCase → domain bila diperlukan → repository abstraction → Dapper/SP.
Gunakan domain behavior untuk invariant bisnis yang nyata; jangan membuat aggregate kosong untuk CRUD sederhana.
Gunakan ExecuteAsync pada UseCase. Tanpa MediatR dan EF Core. SQL Server diakses hanya melalui Dapper dan stored procedure; jangan gunakan SQL inline. Dapper memakai parameter dan CommandType.StoredProcedure; token diteruskan melalui CommandDefinition.

## Tanggung jawab UseCase, Domain dan Repository

- UseCase mengatur proses satu operation: mengambil data, memeriksa akses sesuai kebijakan aplikasi, memanggil perilaku domain bila diperlukan, menyimpan perubahan dan menghasilkan hasil operasi. UseCase tidak memuat detail Dapper, koneksi SQL, atau nama SP.
- Domain menjaga invariant dan perubahan state objek bisnis. Contoh ilustratif: `Order.Cancel()` menolak pembatalan setelah pesanan dikirim. Domain tidak mengambil identitas dari HTTP, mengakses database, atau mengatur response API.
- Repository abstraction menyatakan operasi akses data yang dibutuhkan feature. Implementasinya dalam Infrastructure menjalankan Dapper/SP. Jangan membuat generic repository atau metode yang belum diperlukan.
- Untuk CRUD sederhana tanpa perilaku domain yang nyata, gunakan UseCase dan repository tanpa memaksakan folder Domain. Aturan proses tetap berada di UseCase. Adanya interface repository tidak mewajibkan domain entity.

Keputusan struktur dan alasannya dicatat pada [ADR 0001](adr/0001-feature-folder-structure.md).

## Konteks identitas dan pemeriksaan akses

Pembagian enforcement controller, UseCase, repository/SP dan DTO bersumber dari bagian Pembagian enforcement akses di docs/BE/SECURITY.md.

`ICurrentUser` adalah abstraction lintas feature yang diinjeksikan ke UseCase. Implementasi HTTP memetakan principal tervalidasi; interface tidak mengekspos tipe ASP.NET. Konteks berlaku per request, bukan state global antar-pengguna. Domain tetap tidak bergantung pada HTTP.

Field dan tipe identifier dalam `ICurrentUser`, pemetaan claims, model tenant dan permissions diputuskan sesuai produk saat authentication dirancang. Jangan membuat tenant, role, identitas default atau bypass hanya untuk melengkapi contoh. Rincian enforcement ada di docs/BE/SECURITY.md; alasan keputusan dicatat di [ADR 0003](adr/0003-access-enforcement-and-current-user.md).

## Keputusan terbuka
- Validator: FluentValidation sudah dipilih dan terpasang; dipakai saat feature input dibangun kembali.
- Authentication: self-managed Identity adalah arah produk; implementasi akun/token ditunda. Validasi JWT dan fallback authorization yang ada dipertahankan; sesi, permissions dan tenant masih perlu keputusan.
- API versioning: konvensi /api/v1 siap; library dan kebijakan migrasi dipilih saat dibutuhkan.
- Logging dan audit: kemampuan umum wajib dipetakan sesuai BASELINE-FEATURES.md; detail operasional diputuskan per proyek. Profil file/admin/WIB hanya referensi di docs/BE/reference/. SignalR, caching dan client generation sesuai kebutuhan, bukan otomatis tersedia.
- Paket dan SDK: verifikasi kompatibilitas serta pin versi saat implementasi runtime.

## Transaksi dan performa

### Audit trail

Sediakan kemampuan tracing perubahan data pengguna dan dampak turunannya sesuai cakupan proyek. Pola starter menggunakan SP, bukan trigger, dengan data dan audit atomik: kegagalan audit tidak boleh menghasilkan perubahan bisnis sukses tanpa audit yang diwajibkan. Actor berasal dari server; gunakan waktu dan ID operasi untuk korelasi. SQL langsung di luar SP tidak tercatat otomatis.

Snapshot JSON sebelum/sesudah dan tabel audit pasangan adalah pola referensi yang sudah dibahas. Schema fisik, representasi, cakupan, akses/retensi, proses non-user dan mekanisme propagasi ditetapkan saat adopsi berdasarkan kebutuhan produk. Jangan menyalin contoh Orders sebagai domain proyek. Catatan ADR 0004–0007 menyimpan konteks pola; pembatasan cakupan terbaru ada pada ADR 0012.

Logging terpisah dari transaksi bisnis; lihat [LOGGING.md](LOGGING.md). Kebijakan file harian, WIB, halaman admin dan cleanup pada profil referensi tidak menjadi keharusan setiap proyek.

### Batas transaksi
Tentukan batas transaksi per UseCase, gunakan connection/transaction yang sama untuk operasi atomik. Simpan audit perubahan atomik bila dibutuhkan. Hindari repository yang membuka transaksi terpisah untuk tiap langkah.
Pagination server-side dengan batas ukuran; hindari mengambil BLOB pada list. Pilih kolom eksplisit. Jangan cache data privat tanpa scope dan invalidation yang jelas.

## Penamaan dan perubahan database

- Schema awal menggunakan `dbo`. Nama tabel plural dengan PascalCase, misalnya `dbo.Orders`.
- Stored procedure memakai pola `dbo.{Resources}_{Operation}`, misalnya `dbo.Orders_Create` dan `dbo.Orders_GetById`. Repository memanggil nama SP beserta schema secara eksplisit.
- Simpan script dalam `database/migrations/` dengan nomor urut unik dan deskripsi kebab-case: `0001-create-orders.sql`, `0002-create-orders-procedures.sql`, `0003-alter-orders-create.sql`. Nomor menunjukkan urutan penerapan, bukan versi nama SP.
- Terapkan script berdasarkan nomor dari kecil ke besar; prasyarat schema/tabel harus tersedia sebelum SP yang bergantung padanya. Periksa urutan yang sudah ada sebelum menambah nomor baru.
- Script yang sudah diterapkan tidak diedit, dihapus, atau dinomori ulang. Perubahan tabel maupun SP berikutnya memakai script bernomor baru. Perintah `CREATE OR ALTER` pada SP tidak menjadi alasan mengubah script lama.
- Seller Hub sudah memakai DbUp dan journal dbo.SellerBackendSchemaVersions melalui Seller.Database. Deployment tetap eksplisit; kompatibilitas script historis dijelaskan di bagian adopsi di bawah.
- Untuk setiap perubahan, dokumentasikan prasyarat, dampak data dan kompatibilitas dengan versi API yang masih berjalan. Perubahan parameter/result set SP harus diselaraskan dengan mapping repository dan diuji di database terisolasi.
- Jika penerapan gagal, hentikan urutan berikutnya, periksa perubahan yang sudah terjadi serta status transaksi/riwayat penerapan sebelum menentukan pemulihan. Jangan mengasumsikan seluruh script sudah rollback atau mengulangnya secara buta. Perbaikan pada perubahan yang sudah diterapkan menggunakan script baru; tindakan pemulihan yang berisiko kehilangan data perlu keputusan pengguna.

Contoh Orders hanya ilustrasi penamaan; tidak menetapkan tabel atau domain bawaan. Tidak ada database live yang boleh diubah tanpa otorisasi. Keputusan ini dicatat dalam [ADR 0002](adr/0002-database-naming-and-migrations.md).

## Adopsi Seller Hub dan status aktual

Lihat [ADR 0013](adr/0013-seller-hub-starter-alignment.md). Satu host src/Seller.Api dengan Controllers. Komponen bersama ada di Common, connection factory di Infrastructure/Persistence, dan DI ada di Program.cs. /health adalah liveness infrastruktur di Common/Health; tidak memerlukan domain/UseCase bisnis. OpenAPI hanya Development. Feature bisnis mengikuti Features/{Feature}/{Operation} saat diperlukan, tanpa folder contoh kosong.

Proyek tests/Seller.Api.Tests dipertahankan untuk kompatibilitas solution/scripts; test fondasi HTTP berada di Common dengan namespace mengikuti folder. Proyek baru unit/integration dibuat bila diperlukan, bukan mengganti nama proyek yang sudah ada tanpa kebutuhan.

Tool src/Seller.Database memakai DbUp yang sudah terpasang. Script sumber berada di database/migrations. Script historis 001_SystemChecks_Get.sql dan LogicalName Seller.Commerce.Database.Scripts.001_SystemChecks_Get.sql dipertahankan persis; ini pengecualian migrasi yang mungkin telah diterapkan. Script baru mengikuti nomor 0002 dan seterusnya dengan deskripsi kebab-case, dijalankan dalam urutan numerik. Journal dbo.SellerBackendSchemaVersions tetap sama; migrasi tidak dijalankan otomatis saat startup API.

UserSecretsId API seller-migration-backend-development dan migrator seller-hub-database-development tetap sama. Konfigurasi Authentication:Authority, Authentication:Audience dan ConnectionStrings:Backend tetap diperlukan startup. Jangan mencetak nilainya. SDK dan paket mengikuti global.json/project files; keterbatasan verifikasi dicatat di ALIGNMENT-REVIEW.md.

## Standar kode

- C# file, class, record dan public member: PascalCase; local/parameter: camelCase; private field: _camelCase.
- Interface: I prefix. Metode asynchronous: Async suffix; UseCase: ExecuteAsync.
- Feature dan operation memakai nama bisnis, bukan folder generik Helpers yang menampung semuanya.
- Gunakan nullable reference types, DI constructor, async/await dan CancellationToken pada semua I/O.
- Hindari .Result, .Wait(), async void, service locator dan static mutable state.
- Komentar memakai /* ... */ ketika membantu menjelaskan alasan. Hindari XML summary dan komentar yang mengulang kode.
- Request/response DTO terpisah dari entity. Jangan mengembalikan DataTable atau entity persistence langsung.
- Centralize pesan melalui AppMessages; pertahankan HTTP status yang sesuai.
- Validasi input di batas aplikasi; invariant bisnis tetap ditegakkan dalam domain/UseCase.
- Tangkap exception hanya bila dapat ditangani; middleware menangani exception tak terduga dan mencatat trace ID.
- Redaksi log mengikuti docs/BE/SECURITY.md dan cakupan logging mengikuti docs/BE/LOGGING.md.
- Gunakan konfigurasi bertipe dan validasi konfigurasi penting saat startup.
- Keamanan query mengikuti [Security](SECURITY.md).
- Sertakan path file ketika menjelaskan potongan kode kepada pengguna.
- Pemilihan dan bukti pengujian mengikuti docs/BE/TESTING.md.

### Nama file dan tipe operation

Gunakan nama lengkap yang mencantumkan operation bisnis dan peran tipe, meskipun file sudah berada di folder operation. Nama file mengikuti nama tipe: `{Operation}Request.cs`, `{Operation}Response.cs`, `{Operation}Validator.cs` bila validator dipakai, dan `{Operation}UseCase.cs`.

Contoh ilustratif untuk operation `CreateOrder`:

| File | Nama tipe |
| --- | --- |
| CreateOrderRequest.cs | CreateOrderRequest |
| CreateOrderResponse.cs | CreateOrderResponse |
| CreateOrderValidator.cs | CreateOrderValidator |
| CreateOrderUseCase.cs | CreateOrderUseCase |

Jangan menggunakan nama generik `Request.cs`, `Response.cs`, `Validator.cs`, atau `UseCase.cs` untuk tipe operation. Contoh ini hanya menunjukkan penamaan, bukan menetapkan domain Order atau mewajibkan validator/library tertentu.

### Jumlah tipe per file

Gunakan satu tipe utama per file, dengan nama file mengikuti nama tipe. Tipe nested boleh berada dalam file induknya hanya jika digunakan oleh tipe induk tersebut. Tipe yang digunakan oleh tipe lain ditempatkan dalam file tersendiri; ukuran tipe yang kecil bukan alasan untuk menggabungkan beberapa tipe utama dalam satu file.

### Namespace

Namespace mengikuti root namespace proyek dan susunan folder relatif terhadap direktori proyek. Folder `src` dan nama direktori proyek tidak ditambahkan lagi sebagai segmen namespace.

Contoh: dengan root namespace `Backend.Api`, file `src/Backend.Api/Features/Orders/CreateOrder/CreateOrderRequest.cs` berisi tipe `CreateOrderRequest` dalam namespace `Backend.Api.Features.Orders.CreateOrder`. Request, response, validator dan UseCase dalam folder operation yang sama memakai namespace tersebut. Sesuaikan awalan dengan root namespace proyek ketika starter diadopsi.

### Struktur dan penamaan test

Folder test operation mengikuti `Features/{Feature}/{Operation}/` dalam proyek unit atau integration test sesuai boundary yang diuji. Test domain mengikuti `Features/{Feature}/Domain/`, karena invariant domain dapat dipakai oleh beberapa operation. Buat folder hanya ketika test diperlukan.

Gunakan nama file dan tipe lengkap yang menyebut target pengujian dengan akhiran `Tests`, misalnya `CreateOrderUseCaseTests.cs` berisi `CreateOrderUseCaseTests` dan `CreateOrderEndpointTests.cs` berisi `CreateOrderEndpointTests`.

```text
tests/
├── Backend.UnitTests/
│   └── Features/Orders/
│       ├── CreateOrder/
│       │   └── CreateOrderUseCaseTests.cs
│       └── Domain/
│           └── OrderTests.cs
└── Backend.IntegrationTests/
    └── Features/Orders/CreateOrder/
        └── CreateOrderEndpointTests.cs
```

Namespace mengikuti root namespace proyek test dan folder, misalnya `Backend.UnitTests.Features.Orders.CreateOrder` dan `Backend.IntegrationTests.Features.Orders.CreateOrder`. Nama proyek/feature di atas hanya ilustrasi, bukan kewajiban membuat domain Order.

Nama metode test memakai pola `Method_Kondisi_Hasil`, dengan setiap bagian PascalCase. Contoh test domain sinkron: `Cancel_WhenOrderIsShipped_ThrowsException`. Untuk test asynchronous, pertahankan akhiran `Async`, misalnya `ExecuteAsync_WhenRequestIsValid_CreatesOrderAsync`. Untuk endpoint, bagian pertama dapat memakai HTTP method, misalnya `Post_WhenRequestIsValid_ReturnsCreatedAsync`.

Nama harus menggambarkan perilaku yang benar-benar diperiksa. Konvensi ini tidak memilih framework test, assertion library, atau fixture; ikuti pilihan proyek ketika tersedia.
