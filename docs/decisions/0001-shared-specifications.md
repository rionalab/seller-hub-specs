# 0001 — Satu sumber kebutuhan untuk BE dan FE

Status: Accepted

Pembaruan 17 November 2026: panduan kerja agent FE dan BE dikonsolidasikan di [Panduan kerja agent](../AGENT-GUIDE.md). AGENTS.md di setiap repo menjadi pintu masuk; standar teknis FE dan BE dipusatkan di docs/FE dan docs/BE. Backend tetap menghasilkan artefak OpenAPI di repo kodenya.

Pengguna menyetujui tiga repository sejajar: seller-hub-specs, seller-hub-backend dan seller-hub-frontend. Specs menyimpan konteks produk, business rules, spesifikasi fitur, acceptance criteria dan progres bersama. BE/FE masing-masing mempertahankan AGENTS, peran/skill dan dokumen teknis sesuai stack.

Dokumen bisnis backend dipindahkan ke specs; path lama menjadi rujukan saja untuk menjaga tautan yang sudah ada. Kontrak API aktif tetap di backend. Agent wajib membaca specs secara eksplisit dan tidak mengandalkan instruksi folder induk. Repository dapat dikloning terpisah, tetapi pekerjaan fitur memerlukan checkout specs; kegagalan akses tidak boleh diganti dengan asumsi bisnis.

Tidak ada submodule, sinkronisasi otomatis, remote baru, commit atau push yang dibuat dalam langkah ini. Root workspace bukan repository. Implementasi bisnis/runtime tidak berubah karena pemisahan dokumen.
