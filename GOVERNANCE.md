# Tata Kelola PaduData

Dokumen ini menjelaskan **siapa memutuskan apa** dalam proyek PaduData — baik sebagai proyek perangkat lunak open source maupun sebagai sistem yang melayani tata kelola data pemerintah. Dua tata kelola ini berbeda dan disengaja dipisahkan:

- **Tata kelola perangkat lunak** — siapa berhak menerima/menolak kontribusi kode, merilis versi, mengubah arsitektur. Diatur dokumen ini.
- **Tata kelola data & kebijakan** — apa yang wajib disetor, bagaimana konflik data diputus, apa yang boleh dipublikasikan. Diatur oleh Forum Satu Data Kabupaten/Kota sesuai Perpres 39/2019, dituangkan sebagai *keputusan yang direkam di dalam sistem* (lihat handoff §9), bukan oleh maintainer kode.

Seorang maintainer kode **tidak berwenang** mengubah kebijakan data atas nama sendiri; ia hanya mengimplementasikan keputusan Forum Satu Data ke dalam sistem sebagai konfigurasi.

---

## 1. Peran dalam Proyek Perangkat Lunak

| Peran | Wewenang | Dipegang oleh |
|---|---|---|
| **Walidata (Instansi Pengampu)** | Otoritas tertinggi atas arah teknis repo inti; menunjuk/mencabut Core Maintainer; pemegang keputusan akhir bila konsensus maintainer gagal | Unit teknis Diskominfo Kabupaten Probolinggo (instansi pengampu awal; dapat berpindah/melebar seiring adopsi multi-daerah — lihat §5) |
| **Core Maintainer** | Hak merge ke `main`; menyetujui/menolak PR; menetapkan roadmap rilis; menjaga kepatuhan AGPL-3.0 dan kesesuaian dengan handoff kebijakan | Ditunjuk Walidata; minimal 2 orang aktif agar tidak ada single point of failure |
| **Maintainer Domain** | Hak review/merge terbatas pada area tertentu (mis. modul validasi, portal desa, konektor SIPD) | Kontributor tepercaya, diangkat Core Maintainer setelah kontribusi konsisten |
| **Kontributor** | Mengajukan PR/issue; tidak punya hak merge | Siapa pun — pemerintah daerah lain, individu, organisasi masyarakat sipil, vendor SID |
| **Pengadopsi (Adopter)** | Menjalankan instalasi sendiri; dapat mengusulkan kebutuhan lewat issue; tidak otomatis punya hak merge di repo inti | Kabupaten/kota lain yang men-deploy PaduData |

Tidak ada kepemilikan individu atas repositori. Akses admin GitHub organisasi `otoricode` dipegang minimal dua Core Maintainer untuk menghindari terkuncinya proyek pada satu orang.

## 2. Proses Keputusan Teknis

- **Perubahan kecil** (perbaikan bug, dokumentasi, penambahan test): 1 persetujuan Core/Maintainer Domain, tanpa proses RFC.
- **Perubahan sedang** (fitur baru, perubahan non-breaking pada API): 1 persetujuan Core Maintainer + review terbuka minimal 3 hari kerja.
- **Perubahan besar / breaking** (perubahan skema data, versi API baru, perubahan model keamanan, perubahan lisensi dependensi): wajib **RFC** — dokumen usulan di `docs/rfc/`, dibuka untuk komentar publik minimal 14 hari kalender, diputuskan oleh konsensus Core Maintainer. Jika konsensus gagal, Walidata memutus.
- **Perubahan yang menyentuh kebijakan data** (klasifikasi data, ambang validasi default, retensi): tidak diputuskan lewat RFC teknis — dirujuk ke Forum Satu Data, hasilnya baru diimplementasikan sebagai PR yang mengutip nomor keputusan forum.

Semua keputusan RFC dan keputusan Forum Satu Data yang berdampak ke sistem dicatat di `docs/decision-log/` agar dapat dilacak — sejalan dengan prinsip "sistem mencatat, manusia memutuskan" di `VISION.md`.

## 3. Rilis

- Versi mengikuti **SemVer** (`MAJOR.MINOR.PATCH`).
- `MAJOR`: perubahan breaking pada kontrak API atau skema data.
- `MINOR`: fitur baru yang kompatibel mundur.
- `PATCH`: perbaikan bug, keamanan, dokumentasi.
- Setiap rilis `MAJOR`/`MINOR` disertai `CHANGELOG.md` dan, bila relevan, catatan migrasi.
- Dua versi `MAJOR` terakhir didukung paralel minimal 12 bulan (handoff §5) — konsisten untuk konsumen API OPD/desa yang kapasitas upgrade-nya terbatas.

## 4. Keamanan & Kerentanan

Laporan kerentanan **tidak** melalui issue publik — lihat [`SECURITY.md`](SECURITY.md). Core Maintainer dan Walidata memutuskan jadwal disclosure bersama.

## 5. Tata Kelola Multi-Daerah (Adopsi oleh Kabupaten/Kota Lain)

PaduData dirancang portabel sejak awal (lihat `padudata-seed-<daerah>`, handoff §11). Ketika kabupaten/kota lain mengadopsi:

1. Mereka **tidak** perlu izin untuk fork/deploy — AGPL-3.0 mengizinkan ini secara hukum.
2. Bila mereka ingin berkontribusi balik ke repo inti (bukan hanya seed mereka sendiri), jalurnya sama seperti kontributor lain: PR + review.
3. Setelah tiga atau lebih instansi pemerintah daerah menjadi pengadopsi aktif, proyek mengevaluasi pembentukan **Komite Pengarah Multi-Daerah** — perwakilan Walidata dari tiap daerah pengadopsi, bertemu berkala, dengan wewenang mengusulkan (bukan memutus sepihak) perubahan arah roadmap. Keputusan akhir teknis tetap di Core Maintainer sampai struktur ini disepakati lewat RFC.
4. Tujuan jangka panjang: kepemilikan proyek tidak boleh terkunci selamanya pada satu instansi pengampu awal — ini konsisten dengan semangat *public money, public code* di `VISION.md`.

## 6. Kode Etik

Seluruh interaksi di repositori ini — issue, PR, diskusi — tunduk pada [`CODE_OF_CONDUCT.md`]. Pelanggaran ditangani Core Maintainer; kasus yang melibatkan Core Maintainer sendiri ditangani Walidata.

## 7. Mengubah Dokumen Ini

Perubahan pada `GOVERNANCE.md` sendiri memerlukan RFC dan persetujuan seluruh Core Maintainer aktif (bukan mayoritas sederhana) — dokumen tata kelola tidak boleh diubah oleh sebagian kecil pihak yang kebetulan sedang paling aktif.
