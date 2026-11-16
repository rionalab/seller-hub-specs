# 0003 - Pembagian enforcement akses dan konteks pengguna

Status: Accepted
Tanggal: 2026-11-10

## Context

Persyaratan keamanan sebelumnya sudah menyebut policy dan ownership/tenant, tetapi belum menegaskan tanggung jawab tiap bagian aplikasi dan cara UseCase memperoleh identitas. Pengguna menyetujui pembagian controller, UseCase, repository/SP, ICurrentUser dan DTO pada SEC-02.

## Decision

Controller menerapkan authentication dan policy endpoint. UseCase memeriksa izin operasi terhadap objek, menggunakan ICurrentUser dari DI tanpa membaca HttpContext langsung. ICurrentUser merepresentasikan identitas server yang sudah tervalidasi dan tidak bergantung pada tipe ASP.NET dalam interface-nya.

Repository/SP membatasi akses data menggunakan scope ownership/tenant yang diperlukan dan ditentukan server, termasuk saat write. DTO hanya menerima field yang boleh diisi pengguna; actor audit berasal dari server. Domain tetap menjaga invariant bisnis.

Roles, permissions, kebutuhan tenant, endpoint anonymous, detail claims dan identifier belum ditentukan. Keputusan tersebut tetap milik proyek produk; abstraction ini tidak memilih provider authentication atau bearer/cookie.

## Alternatives

Membaca HttpContext langsung di UseCase tidak dipilih agar proses aplikasi tidak bergantung pada transport HTTP. Pemeriksaan akses hanya di controller tidak dipilih karena izin endpoint tidak cukup untuk membuktikan izin atas objek. Input client tidak dijadikan sumber otoritas identitas atau scope.

## Consequences

Tanggung jawab pemeriksaan akses lebih jelas dan konteks pengguna dapat diganti secara terkontrol dalam unit test. Implementasi harus menjaga konsistensi policy, izin objek dan scope data. Test dengan identitas pengganti tidak membuktikan validasi token produksi; pipeline authentication tetap harus diuji pada boundary yang sesuai.

## Validation

Periksa konsistensi ARCHITECTURE.md, SECURITY.md dan PRODUCT.template.md; telusuri skenario akses lintas pemilik/tenant serta input actor/field sensitif. Ini validasi dokumentasi. Implementasi ICurrentUser, policy dan pengujian runtime belum tersedia.

## Supersedes

Tidak ada.
