# 0001 — Satu sumber kebutuhan untuk BE dan FE

Status: Accepted

Pengguna menyetujui tiga repository sejajar: seller-hub-specs, seller-hub-backend dan seller-hub-frontend. Specs menyimpan konteks produk, business rules, spesifikasi fitur, acceptance criteria dan progres bersama. BE/FE masing-masing mempertahankan AGENTS, peran/skill dan dokumen teknis sesuai stack.

Dokumen bisnis backend dipindahkan ke specs; path lama menjadi rujukan saja untuk menjaga tautan yang sudah ada. Kontrak API aktif tetap di backend. Agent wajib membaca specs secara eksplisit dan tidak mengandalkan instruksi folder induk. Repository dapat dikloning terpisah, tetapi pekerjaan fitur memerlukan checkout specs; kegagalan akses tidak boleh diganti dengan asumsi bisnis.

Tidak ada submodule, sinkronisasi otomatis, remote baru, commit atau push yang dibuat dalam langkah ini. Root workspace bukan repository. Implementasi bisnis/runtime tidak berubah karena pemisahan dokumen.
