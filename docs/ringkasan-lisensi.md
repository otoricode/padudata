# Ringkasan Lisensi PaduData (Versi Sederhana)

**Untuk siapa:** kepala daerah, Walidata, Forum Satu Data, DPMD, atau siapa pun pemangku kebijakan yang perlu memahami status hukum PaduData **tanpa** membaca dokumen lisensi penuh.

**Bukan dokumen hukum.** Ini penjelasan awam. Yang mengikat secara hukum adalah teks resmi berbahasa Inggris di [`LICENSE`](../LICENSE) (kode) dan lisensi CC BY-SA 4.0 (dokumentasi). Terjemahan lengkap tidak resmi ada di [`LICENSE.id.md`](../LICENSE.id.md).

---

## Inti Singkatnya

- **Boleh dipakai siapa saja, gratis, tanpa izin.** Kabupaten/kota mana pun boleh mengunduh, memasang, dan menjalankan PaduData hari ini juga — tidak perlu menghubungi Otoritech, maintainer, atau pihak mana pun.
- **Boleh diubah sesuka hati.** Ganti nama, ganti warna, tambah fitur, sambungkan ke sistem lokal — semua boleh.
- **Ada satu syarat kalau dipakai untuk melayani publik:** kalau versi yang sudah diubah dijalankan sebagai layanan (misalnya diakses lewat web oleh OPD/desa), kode sumber versi itu **wajib dibuka** untuk penggunanya. Ini yang disebut prinsip *public money, public code* — kalau uang APBD membiayai perbaikannya, perbaikan itu harus bisa dipakai balik oleh daerah lain.
- **Tidak ada jaminan.** Perangkat lunak diberikan apa adanya (*as-is*). Otoritech dan kontributor tidak bertanggung jawab atas kerugian dari pemakaiannya — instansi yang menjalankan tetap bertanggung jawab atas keamanan dan operasional instalasinya sendiri, seperti sistem lain pada umumnya.
- **"Tidak ada jaminan" bukan berarti kurang andal — justru sebaliknya.** Lisensi tertutup juga tidak memberi jaminan sungguhan (klaim "jaminan" di kontrak vendor biasanya terbatas dan sulit ditagih), tapi kodenya terkunci — tidak ada yang bisa memeriksa apakah klaim keamanan/kualitasnya benar, baik oleh pemda pembeli maupun pihak luar. Kode PaduData terbuka penuh: bisa diperiksa, diaudit, dan diperbaiki oleh siapa saja — pemda sendiri, akademisi, komunitas pegiat perangkat lunak bebas, bahkan auditor BSSN — bukan cuma dipercaya begitu saja dari klaim satu vendor. Keandalan yang teruji lewat pemeriksaan terbuka oleh banyak pihak lebih kuat daripada janji jaminan kontraktual vendor yang tidak bisa diverifikasi.

## Kenapa Bentuknya Begini? Soal Vendor Lock-in

Masalah lama sistem pemerintah: dibangun vendor tertutup, kodenya tidak bisa diaudit, hanya vendor itu yang tahu isinya. Begitu kontraknya habis atau vendor menaikkan harga, pemda tidak punya pilihan — mau pindah pun harus bangun ulang dari nol, karena kode dan datanya terkunci di sistem vendor lama. Ini **vendor lock-in**: pemda jadi tersandera pada satu penyedia, **bukan pada teknologinya**.

AGPL-3.0 dipilih justru untuk mencegah ini:

- **Kode sumber selalu bisa diaudit** — bukan cuma oleh pembuat aslinya, tapi oleh pemda mana pun yang memakainya, kapan pun, tanpa perlu izin vendor.
- **Pindah vendor tidak perlu bangun ulang.** Karena kode sumbernya wajib terbuka (Pasal 13), pemda bisa mengganti penyedia jasa implementasi kapan saja — vendor baru tinggal melanjutkan dari kode yang sama, bukan mulai dari nol.
- **Tidak ada yang bisa "menutup" ulang kodenya.** Sekali dirilis AGPL-3.0, siapa pun yang mengembangkan dan menjalankannya sebagai layanan tetap terikat kewajiban membuka kodenya — termasuk kalau vendor lain yang mengembangkan lebih lanjut.
- **Investasi satu daerah menjadi milik semua daerah.** Kalau satu kabupaten membiayai perbaikan lewat APBD, perbaikan itu otomatis bisa dipakai kabupaten/kota lain — bukan terkunci sebagai keunggulan kompetitif satu vendor.

Regulasi Satu Data Indonesia dan SPBE juga mendorong interoperabilitas dan efisiensi anggaran ke arah yang sama: lisensi ini adalah jaminan teknis-hukum bahwa arah itu tidak bisa dibelokkan balik ke model tertutup di kemudian hari, siapa pun yang mengembangkannya.

## Dua Lisensi, Dua Bagian

| Bagian | Lisensi | Artinya |
|---|---|---|
| **Kode program** (backend, portal, konektor) | AGPL-3.0-only | Bebas pakai/ubah/sebar; wajib buka kode sumber bila versi modifikasi dijalankan sebagai layanan jaringan publik. |
| **Dokumentasi** (panduan, kebijakan teknis) | CC BY-SA 4.0 | Bebas dipakai/disalin/diubah, wajib mencantumkan atribusi sumber, dan hasil turunannya harus disebarkan dengan lisensi yang sama. |

## Pertanyaan yang Sering Muncul

**Apakah kabupaten kami harus bayar lisensi atau minta izin ke Otoritech?**
Tidak. Tidak ada biaya lisensi, tidak ada proses persetujuan, tidak ada kontrak yang perlu ditandatangani untuk mulai memakai kode sumbernya.

**Apakah kami boleh menyewa vendor lain untuk memasang dan mengelolanya?**
Boleh. Lisensi ini tidak mengikat siapa yang boleh dibayar untuk memasang atau merawat sistem — hanya mengatur bahwa kode sumbernya tetap terbuka.

**Kalau vendor kami menambah fitur khusus daerah, apakah fitur itu wajib dibagikan ke publik?**
Ya, kalau fitur itu bagian dari versi yang dijalankan untuk melayani pengguna (OPD/desa/publik) lewat jaringan. Itu konsekuensi dari AGPL-3.0 Pasal 13 — beda dari lisensi lain (mis. MIT) yang tidak mewajibkan ini. Data spesifik daerah sendiri (kode wilayah, kamus indikator, dsb.) **tidak** wajib dibagikan — itu bukan kode program, dan memang sengaja disimpan terpisah di repo `padudata-seed-<daerah>` milik masing-masing daerah (lihat `CONTRIBUTING.md`).

**Apakah data warga (NIK, dsb.) ikut menjadi terbuka karena lisensi ini?**
Tidak. Lisensi ini hanya mengatur **kode program**, bukan data yang mengalir di dalamnya. Desain sistem sendiri melarang penyimpanan data individu (lihat `docs/handoff-prinsip-sistem.md` Bagian 8) — ini kebijakan privasi terpisah, bukan konsekuensi lisensi.

**Siapa yang bisa dimintai pertanggungjawaban kalau sistem error atau ada insiden keamanan?**
Instansi yang menjalankan instalasinya. Lisensi ini membebaskan pembuat kode (Otoritech dan kontributor) dari jaminan/tanggung jawab hukum atas pemakaian kode sumber terbuka — sama seperti lisensi perangkat lunak bebas lain pada umumnya (Linux, PostgreSQL, dsb. juga begini). Untuk pelaporan kerentanan keamanan, lihat `SECURITY.md`.

## Untuk Vendor/System Integrator Pemerintahan

Vendor **boleh** menjadikan PaduData sebagai bisnis — memasang, mengustomisasi, mengintegrasikan, dan menjual jasa implementasinya ke pemda, termasuk mengenakan biaya untuk itu. Yang **tidak boleh**: menjual PaduData (atau versi modifikasinya) ke pemda sebagai produk tertutup/proprietary. Versi apa pun yang dijalankan untuk melayani pengguna lewat jaringan **wajib tetap open source** — kode sumbernya harus tersedia untuk pemda yang memakainya, sesuai AGPL-3.0 Pasal 13.

Singkatnya: **model bisnisnya jasa (implementasi, kustomisasi, dukungan, hosting), bukan lisensi kode.** Vendor tetap bisa untung dari keahlian dan layanannya, tapi tidak bisa mengunci pemda dengan kode tertutup atau menagih biaya lisensi atas kode yang sejatinya bebas.

## Kalau Butuh Detail Lengkap

- Teks hukum resmi (Inggris, mengikat): [`LICENSE`](../LICENSE)
- Terjemahan lengkap tidak resmi (Indonesia, referensi): [`LICENSE.id.md`](../LICENSE.id.md)
- Keputusan kebijakan & teknis mengikat proyek: [`docs/handoff-prinsip-sistem.md`](handoff-prinsip-sistem.md) Bagian 11
