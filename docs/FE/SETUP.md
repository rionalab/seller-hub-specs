# Setup Frontend

Checkout seller-hub-specs, seller-hub-backend dan seller-hub-frontend sejajar. Baca AGENTS.md masing-masing. Jika docs bersama tidak tersedia, pulihkan checkout tanpa membuat salinan bisnis lokal. Belum ada remote specs yang ditetapkan oleh pekerjaan ini.

Inspeksi Node/pnpm dan kebutuhan versi pada package/lockfile sebelum install. Gunakan lockfile proyek dan konfigurasi contoh; jangan mencetak .env.local. Konfigurasi browser tidak boleh mengandung secrets. Jangan scaffold ulang proyek yang sudah ada.

Script yang tersedia pada package.json: pnpm dev, build, lint, typecheck, format:check dan check. check menggabungkan lint, typecheck dan format:check; belum ada test perilaku. Jangan menjalankan format seluruh proyek untuk perubahan kecil. Keputusan framework test dan detail auth menunggu kebutuhan fitur.

Untuk CI, pilih platform/alat saat dibutuhkan. Cakupan wajib: secrets, dependency, build/lint/typecheck dan pengujian relevan. Catat hasil gagal/skipped; pemeriksaan yang tidak berjalan bukan lulus. Penanganan temuan mengikuti SECURITY.md. Tidak ada pipeline/deployment dibuat oleh dokumen ini.
