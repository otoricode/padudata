# Visi PaduData

> **Satu pangkalan data terpadu untuk setiap kabupaten/kota di Indonesia — terbuka, taat regulasi, dan berpihak pada yang paling kecil kapasitasnya.**

*Catatan: nama "PaduData" adalah nama kerja hingga pemeriksaan merek final. Isi dokumen ini tidak bergantung pada nama.*

---

## Masalah yang Kami Selesaikan

Di hampir setiap kabupaten/kota di Indonesia, data pembangunan hidup terpencar: setiap OPD memegang angkanya sendiri, setiap desa mengisi format yang berbeda untuk dinas yang berbeda — berkali-kali, untuk data yang sama. Ketika bupati bertanya "berapa angka stunting kita?", jawabannya bisa tiga versi dari tiga dinas, dan tidak ada mekanisme untuk menetapkan mana yang resmi.

Regulasinya sebenarnya sudah lengkap — Satu Data Indonesia, SPBE, UU Pelindungan Data Pribadi, pedoman Sistem Informasi Desa — tetapi perangkat lunak yang menerjemahkannya ke tingkat kabupaten nyaris selalu berupa proyek vendor tertutup: mahal, tidak bisa diaudit, mati ketika kontrak habis, dan harus dibeli ulang oleh setiap daerah.

PaduData adalah jawaban atas keduanya: **hub integrasi data statistik sektoral tingkat kabupaten/kota**, dirancang langsung dari regulasi, dan dirilis sebagai perangkat lunak bebas (AGPL-3.0) agar satu kali dibangun bisa dipakai — dan diperbaiki — oleh semua.

## Visi

Lima tahun dari sekarang, kami ingin melihat dunia di mana:

1. **Desa mengisi data satu kali, dan cukup satu kali.** Tidak ada lagi operator desa yang mengetik angka yang sama ke lima format untuk lima dinas. Data mengalir sekali ke hub, dan hub yang melayani semua yang membutuhkannya.
2. **Setiap angka resmi punya silsilah.** Siapa produsennya, kapan disetor, pernah bentrok dengan angka siapa, dan atas keputusan forum apa ia ditetapkan resmi — semua terekam dan bisa diaudit, oleh auditor maupun oleh warga.
3. **Desa bukan hanya penyetor, tapi juga penerima manfaat.** Setiap desa dapat melihat statistik resmi kabupatennya, posisinya dibanding desa lain, dan status data yang ia setor — karena akses informasi memang hak desa yang dijamin undang-undang.
4. **Tidak ada data pribadi warga di pangkalan data.** Hub ini menyimpan angka tentang wilayah, bukan tentang orang. NIK dan identitas individu tetap di sistem sumbernya — selamanya. Kepercayaan publik adalah prasyarat, bukan fitur.
5. **Kabupaten mana pun bisa memakainya tanpa izin siapa pun.** Deploy dari kode sumber publik, isi konfigurasi daerah sendiri, jalan. Tidak ada lisensi per-kursi, tidak ada vendor lock-in, tidak ada "hubungi sales".

## Prinsip yang Tidak Kami Kompromikan

**Regulasi adalah spesifikasi.** Perpres 39/2019 (Satu Data Indonesia), Perpres 95/2018 jo. 132/2022 (SPBE), Perpres 82/2023 (keterpaduan layanan digital/SPLP), UU 27/2022 (PDP), UU Desa Pasal 86, dan Permendesa PDT 13/2025 bukan lampiran — mereka adalah requirement. Terminologi di kode dan UI memakai istilah baku: Produsen Data, Walidata, Pembina Data, Forum Satu Data.

**Privacy by design, bukan by disclaimer.** Skema basis data secara teknis tidak mampu menyimpan pengenal individu — dijaga oleh pemeriksaan otomatis di CI, bukan oleh janji. Data pendukung untuk validasi hanya masuk lewat tiket beralasan, berumur pendek, dan teraudit.

**Sistem mencatat, manusia memutuskan.** Ketika dua sumber menyetor angka berbeda, sistem tidak menolak dan tidak memilih pemenang — ia menyimpan keduanya, menandai selisihnya, dan menyerahkan penetapan kepada Forum Satu Data, lalu merekam keputusannya. Kedaulatan data tetap di tangan produsen dan forum, bukan di tangan algoritma.

**Berpihak pada kapasitas terkecil.** Kalau sebuah fitur tidak bisa dipakai operator desa dengan ponsel lama dan sinyal satu bar, fitur itu belum selesai. Jalur API untuk yang sudah bersistem dan jalur unggah/formulir untuk yang belum — keduanya warga kelas satu, dengan pipeline validasi yang sama.

**Netral vendor, terbuka total.** Kontrak API, dokumentasi, template, dan sandbox tersedia publik. TataDesa, OpenSID, atau sistem apa pun terhubung lewat pintu yang sama. Tidak ada jalur istimewa.

**Konfigurasi, bukan hardcode.** Semua yang spesifik daerah — kode wilayah, kamus indikator, ambang validasi, branding — adalah data, bukan kode. Repo inti tetap murni; kekhasan daerah hidup di repo seed masing-masing.

**AGPL-3.0 sebagai jaminan, bukan sekadar lisensi.** Siapa pun yang menjalankan versi modifikasi sebagai layanan wajib membuka kodenya. Uang publik yang membiayai perbaikan di satu kabupaten menjadi milik semua kabupaten. *Public money, public code.*

## Apa yang Bukan Visi Kami (Non-Goals)

- **Bukan** basis data kependudukan, dan tidak akan pernah menjadi itu.
- **Bukan** pengganti SIPD, Satu iDesa, atau sistem operasional OPD/desa — kami lapisan integrasi yang memasok mereka, bukan pesaingnya.
- **Bukan** hakim kebenaran data — penetapan data resmi selalu keputusan manusia melalui forum.
- **Bukan** produk satu vendor, satu kabupaten, atau satu periode kepala daerah.

## Ukuran Keberhasilan

- Beban desa turun: jumlah formulir/laporan duplikat yang harus diisi desa per tahun berkurang drastis dan terukur.
- Satu angka resmi: setiap indikator prioritas kabupaten punya tepat satu nilai resmi ber-silsilah per periode.
- Kecepatan onboarding: satu desa atau satu OPD baru terhubung dalam hitungan hari, bukan proyek berbulan-bulan.
- Adopsi lintas daerah: kabupaten/kota lain men-deploy dari repo publik tanpa keterlibatan tim asal — dibuktikan dengan repo seed daerah mereka sendiri.
- Lulus audit: kepatuhan UU PDP, SPBE, dan Satu Data dapat ditunjukkan dari sistem itu sendiri, bukan dari dokumen terpisah.

## Jalan ke Sana

Dimulai dari satu tempat nyata: **Kabupaten Probolinggo** — 24 kecamatan, 325 desa, 5 kelurahan — sebagai implementasi pertama sekaligus pembuktian bahwa prinsip di atas bisa hidup di lapangan, bertahap dari pilot beberapa OPD dan desa, meluas ke domain prioritas, hingga wajib menyeluruh berlandaskan Peraturan Bupati. Setiap pelajaran dari Probolinggo kembali ke repo inti sebagai perbaikan untuk semua.

Kalau berhasil di satu kabupaten dengan 330 titik dan kapasitas teknis yang sangat beragam, ia bisa berhasil di mana saja.

---

*Dokumen ini adalah bintang penunjuk arah, bukan spesifikasi. Rincian keputusan teknis dan kebijakan yang mengikat ada di dokumen handoff proyek. Perubahan visi dilakukan melalui diskusi terbuka di repositori ini.*
