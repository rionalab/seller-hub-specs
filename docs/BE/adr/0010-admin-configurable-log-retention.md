# 0010 - Retensi log melalui konfigurasi admin

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Log aplikasi sudah dipilih berupa file harian dengan rotasi WIB. Saat membahas masa simpan, pengguna meminta nilainya dapat diatur pada konfigurasi admin.

## Decision

Admin yang berwenang dapat mengatur lama penyimpanan file log dalam hari melalui konfigurasi admin. Pengaturan tidak berupa konstanta kode. Retensi ini tidak mengubah retensi tabel audit bisnis. Perubahan pengaturan diaudit sesuai aturan perubahan data oleh pengguna.

Nilai awal, rentang valid, opsi tanpa cleanup, jadwal/cutoff penghapusan, waktu penerapan perubahan, policy admin dan lokasi penyimpanan konfigurasi belum diputuskan. Keputusan ini tidak mengotorisasi penghapusan file sekarang.

## Alternatives

Retensi tetap dalam kode tidak dipilih karena pengguna ingin mengubahnya melalui konfigurasi admin.

## Consequences

Implementasi memerlukan kontrol akses, validasi server, penyimpanan konfigurasi dan audit perubahan. Penurunan retensi dapat membuat file lama memenuhi syarat penghapusan; perilaku ini harus disepakati sebelum cleanup dibuat. Log tetap berupa file, terlepas dari penyimpanan pengaturannya.

## Validation

Dokumentasi diselaraskan. Saat runtime tersedia, uji izin, validasi, penyimpanan dan audit konfigurasi; uji cleanup setelah aturan penghapusannya diputuskan. Belum ada runtime atau penghapusan file.

## Keputusan lanjutan

Pengguna menyetujui pengaturan melalui halaman admin aplikasi dengan penyimpanan nilai aktif di database. Akses konfigurasi mengikuti Dapper/SP dan perubahan beserta audit disimpan dalam satu transaksi. Halaman admin tidak menulis appsettings.json atau web.config. Isi log tetap berupa file harian; konfigurasi teknis lokasi/provider tetap dikelola melalui file/environment, dan web.config untuk hosting IIS jika digunakan.

Keputusan ini menutup pilihan lokasi penyimpanan konfigurasi yang sebelumnya terbuka. Schema, policy admin, nilai awal/rentang, cleanup, refresh/cache dan perilaku saat database pengaturan tidak tersedia tetap belum diputuskan.

## Supersedes

Tidak ada; melengkapi keputusan retensi yang terbuka pada ADR 0009.

## Keputusan lanjutan — Validasi dan penonaktifan

Pengguna menyetujui retensi berupa bilangan bulat minimal 1 hari dan opsi Nonaktifkan penghapusan otomatis pada halaman admin. Nol, negatif dan pecahan bukan nilai retensi yang valid; status nonaktif dinyatakan terpisah dari angka retensi. Saat cleanup nonaktif, pencatatan dan rotasi log tetap berjalan. Pengaktifan kembali memerlukan retensi valid dan mengikuti jadwal berikutnya, tanpa cleanup langsung. Perubahan status juga diaudit. Batas maksimum belum ditetapkan.

Kebijakan jadwal, kondisi awal nonaktif dan kegagalan pembacaan konfigurasi yang awalnya terbuka kini mengikuti ADR 0011. Keputusan-keputusan lanjutan ini melengkapi bagian awal catatan, bukan mengubah riwayat keputusan sebelumnya.

## Keputusan lanjutan — Tanpa maksimum bisnis

Pengguna memilih tidak ada batas maksimum masa simpan. Retensi tetap bilangan bulat minimal 1 hari dan tidak dibatasi angka bisnis seperti 365 hari. Implementasi harus memvalidasi representasi tipe secara konsisten tanpa pembulatan/pemotongan atau overflow; tipe fisik belum dipilih. Perhitungan umur file harus aman untuk nilai retensi besar. Keputusan ini menutup batas maksimum yang sebelumnya terbuka, dan tidak mengubah opsi cleanup nonaktif maupun jadwal yang sudah disepakati.
