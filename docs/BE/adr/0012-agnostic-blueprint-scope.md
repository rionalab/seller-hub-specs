# 0012 - Cakupan blueprint agnostik lintas proyek

Status: Accepted
Tanggal: 2026-11-10

## Context

Pembahasan audit dan logging menghasilkan pilihan operasional yang terlalu rinci untuk starter lintas proyek. Pengguna menegaskan bahwa fitur umum boleh dibahas, sedangkan detail disesuaikan saat proyek konkret mengadopsi blueprint.

## Decision

- Pertahankan default stack, struktur kode, penamaan dan batas keamanan yang sudah disepakati. Agnostik berarti tidak mengunci domain atau kebutuhan operasional semua proyek.
- Catat kemampuan dasar audit, logging, korelasi, konfigurasi dan pengelolaan masa simpan sebagai kebutuhan yang harus dipetakan saat adopsi. Dokumentasi bukan bukti implementasi.
- Pertahankan default audit melalui stored procedure, bukan trigger, dengan perubahan data dan audit atomik. Schema fisik, representasi snapshot, cakupan proses otomatis, akses dan retensi diputuskan sesuai proyek; pola tabel pasangan dan JSON tetap menjadi referensi.
- Pilihan file harian WIB, retensi melalui admin, jadwal 01.00 WIB, batas hari serta perilaku cleanup disimpan sebagai profil referensi opsional. Adopsinya harus eksplisit; proyek boleh menentukan sink, konfigurasi dan kebijakan operasional yang berbeda.
- Jangan membuat halaman admin atau komponen runtime hanya karena profil tersebut tersedia. Jangan meminta detail operasional yang belum diperlukan untuk melanjutkan perbaikan blueprint.

## Alternatives

Memaksakan satu profil operasional untuk semua proyek membatasi penggunaan ulang. Menghapus keputusan lama menghilangkan konteks; karena itu riwayat dipertahankan dengan penjelasan cakupan baru.

## Consequences

Dokumen utama memuat kemampuan dan batas umum. Proyek yang mengadopsi harus mencatat keputusan yang diperlukan beserta kriteria verifikasinya. Workflow agent tetap dapat diperbaiki tanpa menyelesaikan seluruh desain produk.

## Validation

Periksa keselarasan AGENTS, Architecture, Baseline Features, Logging, Setup, Product template dan TODO. Pastikan profil referensi diberi label opsional. Tidak ada runtime yang dibuat atau diuji oleh keputusan ini.

## Supersedes

Menggantikan penerapan universal detail khusus proyek pada ADR 0004–0011, bukan menghapus riwayat atau prinsip umum audit atomik melalui SP dan pemisahan audit dari log diagnostik. Keputusan struktur dan stack sebelumnya tetap berlaku.
