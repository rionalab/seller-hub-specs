# Frontend Testing

Mulai dari perilaku dan risiko perubahan. package.json saat inspeksi belum memiliki test script atau framework test. Jangan memasang framework tanpa keputusan; inspeksi ulang jika kode berubah. Lint/typecheck/build bukan test perilaku UI.

| Boundary | Bukti yang diperlukan |
| --- | --- |
| Transformasi/validasi murni | Unit test perilaku relevan tanpa server |
| Komponen/form | Interaksi pengguna, loading/error/empty/success, keyboard/fokus sesuai risiko |
| Integrasi API | Method/payload/envelope/error sesuai kontrak; mock hanya membuktikan client terhadap fixture |
| Alur BE–FE | Backend test nyata yang sesuai, identitas test, outcome/data/akses; mock tidak cukup |

Gunakan fixture deterministik tanpa data pelanggan. Test penolakan akses juga memeriksa data tidak muncul/aksi terlarang tidak terjadi. Laporkan framework/boundary, hasil run, skipped dan prasyarat. Jika test belum tersedia, lakukan pemeriksaan yang ada dan tandai celah; jangan menyebut validasi lengkap.

Perubahan dokumentasi cukup diperiksa konsistensi/rujukannya; tidak mewajibkan build runtime. Pilihan nama test menggambarkan aksi, kondisi dan hasil, dengan lokasi dekat feature atau struktur test yang telah dipilih proyek.
