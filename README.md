# Seller Hub Specifications

Satu sumber konteks produk, aturan bisnis, spesifikasi fitur dan acceptance criteria untuk backend dan frontend. Repository ini hanya menyimpan dokumentasi bersama; tidak menampung kode BE/FE.

```text
seller hub/                 (workspace, bukan repository Git)
  seller-hub-specs/         (repository ini)
  seller-hub-backend/       (repository terpisah)
  seller-hub-frontend/      (repository terpisah)
```

- [Product](docs/PRODUCT.md), [Vision](docs/PRODUCT-VISION.md), [Business backlog](docs/BUSINESS-BACKLOG.md).
- [Workflow fitur](docs/FEATURE-WORKFLOW.md), [status lintas BE/FE](docs/FEATURE-STATUS.md), [template spesifikasi](docs/features/FEATURE.template.md).
- [Peta repository dan kontrak](docs/REPOSITORY-MAP.md), [keputusan sumber bersama](docs/decisions/0001-shared-specifications.md).

Clone ketiga repository sebagai folder sejajar dengan nama di atas. Remote specs belum ditetapkan; jangan menebak URL remote atau menganggap sudah terpublikasi. Git specs tidak melacak folder saudara, sehingga BE/FE tidak perlu di-ignore di sini. Masing-masing repository mempertahankan .gitignore sendiri.

Baca AGENTS.md pada repository yang dikerjakan. Instruksi BE/FE secara eksplisit merujuk specs, tanpa memerlukan AGENTS.md di folder induk. Perubahan lintas repository perlu dicatat dan disinkronkan per repository; satu commit tidak otomatis mencakup ketiganya.
