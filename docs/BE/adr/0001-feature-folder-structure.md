# 0001 - Struktur folder feature dan infrastructure

Status: Accepted
Tanggal: 2026-11-10

## Context

Starter sudah memilih satu proyek API dengan Vertical Slice, UseCase langsung dan repository abstraction. Lokasi domain, interface repository dan implementasi persistence belum cukup rinci sehingga susunan antar-feature dapat berbeda. Pengguna menyetujui struktur setelah pembahasan tanggung jawab UseCase dan Domain.

## Decision

Controller berada pada Features/{Feature}/; request, response, validator opsional dan UseCase berada pada subfolder {Operation}/ dengan nama lengkap. Domain entities/value objects berada dalam Domain/ hanya saat ada kebutuhan aturan bisnis. Interface repository berada dalam Repositories/ milik feature.

Implementasi repository berada dalam Infrastructure/Persistence/{Feature}/, connection factory dalam Infrastructure/Persistence/, dan integrasi eksternal dalam Infrastructure/Integrations/. Common hanya untuk komponen lintas feature. Program.cs tetap composition root dan tempat registrasi dependency awal. Folder dibuat ketika dibutuhkan, bukan sebagai folder kosong wajib.

UseCase mengatur proses operation; Domain menjaga invariant objek bisnis; repository menangani akses data. CRUD sederhana tidak diwajibkan memiliki domain entity. Struktur ini tidak menambahkan assembly maupun framework.

## Alternatives

Menempatkan controller, domain dan interface repository langsung dalam root feature lebih ringkas, tetapi pengguna memilih subfolder Domain dan Repositories agar tanggung jawab mudah ditemukan. Pemisahan menjadi beberapa assembly tidak diperlukan untuk standar satu proyek API yang sudah berlaku.

## Consequences

Penempatan file menjadi konsisten dan implementasi persistence tetap terpisah dari proses bisnis. Ada tambahan kedalaman folder; biaya ini dibatasi dengan hanya membuat folder yang diperlukan. Keputusan ini tidak menetapkan domain Orders, schema database, validator, atau model authentication.

## Validation

Pohon folder, aturan penempatan dan namespace di [Architecture](../ARCHITECTURE.md) diperiksa terhadap keputusan ini. Tidak ada implementasi runtime untuk dibangun atau diuji pada tahap ini.

## Supersedes

Tidak ada.
