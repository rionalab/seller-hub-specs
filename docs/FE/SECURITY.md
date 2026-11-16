# Frontend Security

Persyaratan ini belum membuktikan implementasi. Periksa bagian yang terdampak perubahan, bukan otomatis audit penuh aplikasi.

| Perubahan | Fokus pemeriksaan |
| --- | --- |
| UI/form/data API | Output aman, batas input, error tanpa data privat, scope data sesuai akses BE |
| HTML/URL/link/file | Hindari HTML mentah dari input; validasi tujuan URL dan konten, bukan sekadar tampilan |
| Sesi/login/route guard | Model sesi yang disepakati, tidak ada bypass/token contoh; UI guard bukan authorization BE |
| Cache/storage/log | Hindari menyimpan/mencatat secrets/data privat; scope cache dan pembersihan saat identitas berubah |
| Konfigurasi/dependency/build | Semua konfigurasi yang dibundel ke browser bersifat publik; tidak menaruh secret di VITE_*; review paket dan output |

Kontrak cookie/token, CSRF, CORS dan redirect ditentukan bersama BE ketika alurnya dibangun. Jangan memilih penyimpanan token secara diam-diam. Tidak ada kredensial atau informasi pelanggan dalam fixture, screenshot atau log.

Aturan penanganan temuan mengikuti keputusan lintas proyek yang sudah disepakati pada bagian Penanganan temuan di [Security backend](../BE/SECURITY.md): Critical/High wajib diperbaiki dan diverifikasi sebelum selesai/siap digabung; Medium/Low diperbaiki atau diterima eksplisit pemilik proyek dengan alasan/tindak lanjut. Agent tidak menerima risiko sendiri. Catat lokasi, bukti, dampak, perbaikan, status dan batas verifikasi; build lulus bukan penutupan temuan.
