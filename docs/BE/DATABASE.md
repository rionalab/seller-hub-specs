# Database ownership and deployment

## Script mana yang dipakai?

- **setup-db.bat**: dijalankan developer untuk setup pertama kali atau mengganti koneksi lokal. Ini sekaligus menjalankan migration, bukan hanya menyimpan credential.
- **deploy-db.bat**: dijalankan developer ketika ada migration baru, memakai koneksi yang sudah dikonfigurasi.
- **database.ps1**: script internal yang dipanggil kedua file di atas; tidak perlu dijalankan langsung.
- **Seller.Database**: program .NET yang menjalankan DbUp. Library `dbup-sqlserver` sudah direferensikan di project; tidak perlu install manual atau membuka Visual Studio.

Alur: `.bat` → `database.ps1` → `Seller.Database` → DbUp → SQL Server. API tidak ikut menjalankan migration. Daftar seluruh script, termasuk build/run/test dan script yang tidak aktif, ada di [scripts/README.md](../../../seller-hub-backend/scripts/README.md).

## Memastikan setup berhasil

1. Dari root repository, jalankan `dotnet --version`. Jika muncul SDK-not-found, setup belum bisa berjalan; cocokkan SDK terpasang (`dotnet --list-sdks`) dengan `global.json`. Error ini belum menguji koneksi SQL Server.
2. Jalankan setup dan tunggu pesan `Database migrations completed.`, diikuti `Setup selesai. Koneksi API dan migrator tersimpan di User Secrets, di luar repository.` Pesan terakhir menandakan migration dan penyimpanan kedua konfigurasi selesai untuk target saat itu.
3. Untuk pemeriksaan read-only tambahan, buka database tujuan di SSMS dan lihat tabel `dbo.SellerBackendSchemaVersions`. Nama script yang berhasil diterapkan harus tercatat. Stored procedure dapat dilihat di Programmability → Stored Procedures; refresh daftar setelah deployment.

Database yang sudah ada, file konfigurasi yang tersimpan, atau endpoint `/health` yang sukses saja bukan bukti seluruh migration selesai. `/health` tidak mengakses DB. deploy-db.bat juga bukan tes koneksi read-only: script itu akan menerapkan semua migration pending.

Jika migration gagal, koneksi baru belum disimpan oleh setup. Jika yang gagal justru penyimpanan User Secrets setelah migration sukses, database bisa sudah berubah dan salah satu konfigurasi bisa sudah tersimpan; baca pesan error sebelum mengulang. Jangan membagikan isi User Secrets atau connection string yang mengandung credential.

## Menambahkan migration

Simpan file SQL di `database/migrations/` dengan nomor urut unik dan deskripsi kebab-case, lalu jalankan deploy-db.bat. Periksa nomor seluruh file terlebih dahulu: prefix `002` dan `0002` sama-sama berarti urutan 2, sehingga jangan dipakai untuk dua migration berbeda. Script baru otomatis disertakan saat migrator dibangun. Script yang sudah diterapkan tidak diedit, dihapus atau diganti nama; perubahan SP berikutnya memakai file baru, misalnya dengan `CREATE OR ALTER PROCEDURE`.

Jangan menjalankan file migration manual di SSMS untuk alur normal karena journal DbUp tidak ikut diperbarui. Deploy memilih environment variable `SELLER_BACKEND_DB_CONNECTION` lebih dahulu, baru koneksi tersimpan, dan tidak meminta konfirmasi target.

## Developer baru: setup satu kali

Prerequisite: Windows, .NET SDK sesuai global.json, SQL Server sudah terpasang dan berjalan, dan akun Windows punya akses ke instance tersebut.

Dari root repo di CMD:

```cmd
scripts\setup-db.bat
```

Ikuti pertanyaan berikut:

1. Server: Enter untuk localhost; contoh lain .\SQLEXPRESS atau (localdb)\MSSQLLocalDB.
2. Database: Enter untuk SellerCommerceDb, atau isi nama database development sendiri.
3. Sertifikat: izinkan self-signed hanya untuk development lokal; pilih n jika sertifikat sudah valid.
4. Buat database jika belum ada: memerlukan izin CREATE DATABASE. Pilih n jika database sudah disiapkan.
5. Periksa target server/database, lalu ketik y untuk menjalankan setup.

Wizard menggunakan Windows Authentication, membuat database kosong bila dipilih, menjalankan migrasi DbUp dan menyimpan ConnectionStrings:Backend untuk API serta migrator. Tidak ada tabel bisnis baru di luar script yang sudah tersedia. Setup dapat dijalankan ulang; journal DbUp melewati migrasi yang sudah diterapkan.
Pembatalan tidak mengubah apa pun. Koneksi disimpan hanya setelah migrasi sukses. Jika penyimpanan User Secrets gagal sesudah migrasi, jalankan setup ulang; database yang sudah berhasil dibuat tidak dihapus.

## Pemakaian selanjutnya

```cmd
scripts\deploy-db.bat
```

Tidak perlu set ulang connection string. Jalankan setup-db.bat lagi untuk mengganti server/database lokal.

Jika setup atau deploy gagal, wrapper `.bat` menunggu tombol ditekan agar pesan error tetap terlihat saat dibuka melalui double-click. Kode keluar kegagalan tetap dipertahankan. Untuk otomasi gunakan `scripts\deploy-db.bat --no-pause` (setup juga menerima `--no-pause`, tetapi wizard tetap interaktif). Proses sukses tidak memakai jeda.
API berjalan melalui scripts\run.bat; konfigurasi Authentication:Authority dan Authentication:Audience tetap wajib dan terpisah dari setup DB.

## Lokasi konfigurasi

- API project: src/Seller.Api; UserSecretsId seller-migration-backend-development.
- Migrator: src/Seller.Database; UserSecretsId seller-hub-database-development.
- User Secrets tersimpan per akun Windows di %APPDATA%\Microsoft\UserSecrets\<UserSecretsId>\secrets.json, bukan dalam repo. User Secrets bukan penyimpanan terenkripsi untuk production.
- Setup hanya memperbarui ConnectionStrings:Backend; nilai JWT dan secret lain dipertahankan.
- deploy-db.bat memakai SELLER_BACKEND_DB_CONNECTION jika environment variable itu tersedia; jika tidak, memakai User Secrets migrator. Hapus override dari sesi CMD dengan set "SELLER_BACKEND_DB_CONNECTION=" jika ingin kembali ke koneksi tersimpan.

Wizard hanya untuk SQL lokal dengan Windows Authentication. API dan migrator memakai identitas Windows developer yang sama pada jalur lokal ini.

## Server bersama, SQL Authentication, CI dan production

Gunakan identitas terpisah: runtime cukup EXECUTE pada SP yang diperlukan; migrator memiliki hak deployment. Jangan memakai akun migrator untuk API pada environment bersama/production. Gunakan sertifikat valid dan secret manager.

Untuk development lanjutan, simpan dua connection string sesuai identitas masing-masing:

```cmd
dotnet user-secrets set "ConnectionStrings:Backend" "<runtime-connection-string>" --project src/Seller.Api
dotnet user-secrets set "ConnectionStrings:Backend" "<migration-connection-string>" --project src/Seller.Database
scripts\deploy-db.bat
```

Untuk CI gunakan SELLER_BACKEND_DB_CONNECTION dari secret manager dan migrator secara eksplisit. Database harus sudah tersedia; opsi --ensure-database hanya dijalankan jika diminta. API tidak pernah membuat database atau menjalankan migrasi.

## Troubleshooting

| Masalah | Tindakan |
| --- | --- |
| dotnet tidak ditemukan / SDK tidak cocok | Install SDK sesuai global.json dan buka CMD baru. |
| Tidak bisa terhubung | Periksa service SQL Server dan nama instance; SQLEXPRESS berbeda dari localhost default instance. |
| Windows login ditolak | Berikan akses akun Windows developer pada SQL instance/database. |
| CREATE DATABASE ditolak | Minta administrator membuat database kosong, lalu ulang setup dan pilih n saat ditanya create. |
| Sertifikat tidak dipercaya | Untuk lokal saja pilih trust di wizard; pada server bersama perbaiki sertifikat. |
| Migrasi gagal | Periksa izin deploy dan script pending; konfigurasi koneksi lama tidak ditimpa. Sebagian script sebelumnya mungkin sudah commit karena transaction per script. |
| API gagal karena Authentication | Isi issuer/audience sesuai README; setup DB tidak membuat identity provider. |

## Migration ownership

Scripts are embedded from database/migrations/*.sql and journaled in dbo.SellerBackendSchemaVersions, transaction per script. Add a new numbered migration after deployment; do not edit deployed scripts.

001_SystemChecks_Get.sql creates dbo.SystemChecks_Get with no input parameters and no table dependency. It returns one row: Status nvarchar(20), CheckedAtUtc datetime2, generated by SYSUTCDATETIME(). It does not modify data.
Example: EXEC dbo.SystemChecks_Get;
There is no production database change in this bootstrap. Migration execution and live repository mapping still need an isolated SQL Server test.

## Verification

scripts\test.bat menjalankan tests/Tooling/DatabaseScripts.Tests.ps1: sukses, pembatalan, migration failure, penolakan server nonlokal/database sistem, saved-secret fallback, environment override dan missing config. Dotnet/SQL/penyimpanan secrets diganti stub pada pengujian script; ini bukan bukti migrasi berhasil di SQL Server asli.


## Kompatibilitas penyelarasan starter

Sumber SQL kini berada di database/migrations. Isi dan LogicalName script 001_SystemChecks_Get.sql tetap sama; journal dbo.SellerBackendSchemaVersions tidak diganti. SP ini merupakan artefak historis, bukan fitur endpoint aktif. Script baru memakai 0002 dan seterusnya sesuai konvensi starter; comparer numerik menjaga urutannya dengan prefix lama 001. Tidak ada DROP atau migrasi live dilakukan saat penyelarasan.
