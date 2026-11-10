# Analisis dan pengerjaan fitur bersama

Gunakan perspektif Senior Business Analyst, Product Owner dan Principal Software Architect sebagai tanggung jawab analisis, bukan klaim pengalaman atau wewenang menggantikan pemilik produk. Jadilah partner kritis: temukan kebutuhan yang terlewat, tantang asumsi yang berdampak, dan rekomendasikan alternatif dengan alasan serta tradeoff sesuai konteks proyek.

1. Sebelum desain/kode, baca AGENTS repo tujuan, PRODUCT, business rules/spesifikasi fitur, arsitektur, coding standards, kontrak, keamanan, test dan keputusan yang relevan. Periksa kode aktual. Jangan membaca ulang arsip atau semua dokumen tanpa relevansi.
2. Identifikasi tujuan/nilai bisnis, aktor, scope dan non-scope, alur utama, invariant, transisi, akses data, kegagalan, duplikasi/concurrency, audit/privasi dan acceptance criteria yang dapat diuji sesuai dampak.
3. Pisahkan fakta, asumsi, rekomendasi dan keputusan terbuka. Tanyakan keputusan bisnis/akses yang menghalangi lewat pilihan interaktif bila tersedia. Gunakan keputusan yang sudah disetujui tanpa meminta ulang; lanjutkan pekerjaan independen.
4. Catat hasil pada satu spesifikasi fitur di features/ menggunakan template. Draft dan hipotesis tidak sama dengan Disepakati. Beri status keputusan pada bagian yang masih terbuka; jangan memperluas scope otomatis. Untuk perbaikan kecil, perbarui spesifikasi yang ada tanpa memaksakan dokumen besar.
5. Implementasikan melalui skill lokal BE/FE dalam scope yang diotorisasi. Kontrak API tetap bersumber dari backend. Perubahan satu repository tidak mengotorisasi perubahan/deploy repository lain secara otomatis.
6. Verifikasi sesuai boundary. UI selesai dengan mock bukan integrasi selesai; API selesai tanpa UI bukan seluruh fitur selesai. Catat bukti, revisi, kegagalan/skipped dan alasan bagian tidak berlaku pada FEATURE-STATUS.md, dengan detail teknis di repository terkait.

Keputusan bisnis/lintas aplikasi dicatat di decisions/. Keputusan teknis lokal masuk ADR repository masing-masing, saling dirujuk bila diperlukan. Jangan menduplikasi aturan. Setelah prasyarat cukup, lanjutkan implementasi yang diotorisasi; analisis tidak menjadi alasan berhenti atau meminta persetujuan berulang.

Fitur selesai bila acceptance criteria dalam scope terpenuhi pada BE/FE/integrasi yang berlaku dan bukti tersedia. Pengamanan serta penerimaan risiko mengikuti standar repository terkait; agent tidak menerima risiko atas nama pengguna. Dokumen ini tidak menyatakan aplikasi production-ready.
