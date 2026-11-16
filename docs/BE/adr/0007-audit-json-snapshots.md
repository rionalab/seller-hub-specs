# 0007 - Snapshot JSON untuk audit perubahan data

Cakupan terkini: lihat [ADR 0012](0012-agnostic-blueprint-scope.md). Detail khusus proyek dalam catatan ini adalah referensi adopsi, bukan kewajiban universal.
Status: Accepted
Tanggal: 2026-11-10

## Context

Pengguna membutuhkan tracing perubahan akibat interaksi pengguna, lalu menyetujui penyimpanan data sebelum/sesudah dalam JSON beserta identitas pengguna, waktu dan ID operasi penghubung.

## Decision

Simpan snapshot JSON sebelum dan sesudah perubahan, identitas pengguna dari server, waktu UTC dan ID operasi yang menghubungkan perubahan dalam satu tindakan. Insert memiliki snapshot sesudah saja; update memiliki keduanya; delete memiliki snapshot sebelum saja. Snapshot merepresentasikan data yang benar-benar diubah, bukan request client. Ikuti aturan secrets dan data sensitif saat memilih field snapshot.

Penulisan melalui SP dan transaksi atomik pada ADR 0004/0005 tetap berlaku. Identitas record serta jenis perubahan harus dapat ditelusuri. Nama/tipe kolom fisik, akses dan retensi belum ditetapkan.

## Alternatives

Menggandakan setiap kolom tabel bisnis dalam tabel audit tidak dipilih sebagai representasi utama; pengguna memilih snapshot JSON. Penyimpanan hanya nilai terbaru tidak cukup untuk menelusuri keadaan sebelumnya.

## Consequences

Snapshot memudahkan perbandingan sebelum/sesudah, tetapi menambah volume penyimpanan. Perubahan schema bisnis perlu diperhitungkan saat membaca snapshot historis. Pemilihan field aman tetap diperlukan; JSON tidak berarti semua data boleh disalin ke audit.

## Validation

Saat runtime tersedia, uji representasi insert/update/delete, kesesuaian dengan perubahan data aktual, keterkaitan satu operasi lintas tabel, serta pengecualian secrets. Saat ini hanya dokumentasi; belum ada pengujian database.

## Supersedes

Tidak ada; melengkapi representasi data yang masih terbuka pada ADR 0006.
