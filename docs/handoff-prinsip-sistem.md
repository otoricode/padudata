# Handoff v1.0: Prinsip & Keputusan Desain PaduData
**Integrasi Data OPD & Desa/Kelurahan Tingkat Kabupaten/Kota**

| | |
|---|---|
| Nama produk | **PaduData** — nama kerja, hingga pemeriksaan merek final selesai |
| Implementasi awal | Kabupaten Probolinggo (lihat Bagian 2.1) — desain dokumen ini generik, tidak terikat satu daerah |
| Status | **Final untuk desain sistem** |
| Tanggal | 11 Juli 2026 |
| Audiens | System principal/analis, arsitek sistem, backend engineer, DBA, technical writer |
| Sifat | Semua butir berlabel **[KEPUTUSAN]** bersifat mengikat bagi desain; butir berlabel **[LOKAL]** ditetapkan oleh pemangku setempat sebelum Fase 1 |
| Lisensi target | Perangkat lunak: **AGPL-3.0-only**; dokumentasi: **CC BY-SA 4.0** |

Dokumen ini mencakup: (a) aliran data desa sebagai konsumen produk data terpublikasi, sesuai amanat UU Desa Pasal 86; (b) terminologi tata kelola selaras Perpres 39/2019 (Produsen Data / Walidata / Pembina Data / Forum Satu Data); (c) daftar regulasi dengan status keberlakuan terverifikasi per Juli 2026, termasuk Perpres 132/2022, Perpres 82/2023, dan Permendesa PDT 13/2025; (d) Decision Log atas seluruh pertanyaan terbuka desain (Bagian 14); (e) ketentuan lisensi AGPL-3.0 dan tata kelola open source (Bagian 11).

---

## 1. Tujuan & Kedudukan Dokumen

**PaduData** adalah hub integrasi data statistik sektoral tingkat kabupaten/kota — menjembatani OPD dan desa/kelurahan lewat arsitektur *hub-and-spoke*, dirilis sebagai perangkat lunak bebas (AGPL-3.0) agar dapat diadopsi kabupaten/kota mana pun tanpa mengubah kode inti.

Dokumen ini adalah *source of truth* kebijakan **dan** keputusan teknis tingkat prinsip untuk perancangan PaduData. Tim teknis menurunkannya menjadi artefak desain (skema basis data, spesifikasi OpenAPI, model ancaman, runbook) tanpa perlu mengambil ulang keputusan yang sudah ditetapkan di sini. Jika dalam implementasi ditemukan kontradiksi antara dokumen ini dan regulasi yang lebih tinggi, regulasi menang dan dokumen ini direvisi melalui mekanisme perubahan pada Bagian 15.

## 2. Konteks, Ruang Lingkup, dan Posisi dalam Ekosistem Nasional

Hub PaduData menjembatani dua kelompok sumber:

- **OPD** (Dinkes, Dinsos, Dukcapil, Dinas Pendidikan, dan seterusnya) — sebagai **Produsen Data** sekaligus konsumen data lintas domain.
- **Desa & Kelurahan** — seluruh desa/kelurahan di wilayah layanan, dengan kematangan sistem beragam (TataDesa, OpenSID, atau belum bersistem) — sebagai **Produsen Data** sekaligus konsumen produk data terpublikasi.

Dokumen ini berlaku generik untuk kabupaten/kota mana pun yang mengadopsi PaduData — bukan spesifikasi tertutup untuk satu daerah. Karena perangkat lunaknya dirilis open source (AGPL-3.0) dan berpotensi diadopsi pemda lain, desain **tidak boleh meng-hardcode** hal-hal spesifik satu daerah: jumlah wilayah, nama OPD, kamus indikator, ambang validasi, dan aturan retensi harus berupa **konfigurasi**, bukan kode.

### 2.1 Implementasi Awal: Kabupaten Probolinggo

Prinsip dan keputusan di dokumen ini pertama kali diterapkan di **Kabupaten Probolinggo** — 24 kecamatan, 325 desa, 5 kelurahan (330 titik). Angka dan rujukan instansi spesifik-daerah di beberapa bagian dokumen ini (mis. BPS Kabupaten, Arsitektur SPBE daerah) merujuk pada implementasi awal ini sebagai contoh konkret. Kabupaten/kota lain yang mengadopsi PaduData mengganti angka dan rujukan ini lewat repo seed masing-masing (`padudata-seed-<daerah>`, lihat Bagian 11).

**Posisi terhadap sistem nasional — [KEPUTUSAN], batas yang tidak boleh dilanggar desain:**

| Sistem nasional | Posisi pangkalan data |
|---|---|
| **SIPD** (Permendagri 70/2019) — wajib digunakan pemda | Pangkalan data adalah **pemasok/sumber** bagi data pembangunan daerah di SIPD (khususnya modul e-Walidata/informasi pembangunan daerah), bukan pengganti dan bukan duplikatnya. Sediakan konektor ekspor. |
| **Portal Satu Data Indonesia** (data.go.id, Permen PPN 17/2020) | Data kelas terbuka disebarluaskan Walidata melalui portal SDI daerah yang tersambung ke Portal SDI nasional. Pangkalan data menjadi backend penyedia dataset + metadata baku. |
| **SPLP — Sistem Penghubung Layanan Pemerintah** (Perpres 95/2018, dioperasionalkan Perpres 82/2023) | Bila kementerian/lembaga pusat meminta data, jalurnya adalah SPLP — bukan API bilateral buatan sendiri. Desain API internal harus siap dibungkus sebagai layanan SPLP tanpa perombakan (stateless, kontrak terdokumentasi, autentikasi standar). |
| **Satu iDesa / platform SID nasional** (Permendesa PDT 13/2025) | Prinsip **"isi sekali, pakai berulang"**: jika suatu data sudah diinput desa di SID (nasional atau lokal), pangkalan data mengambilnya via API/interoperabilitas — dilarang mewajibkan desa input ulang data yang sama. |
| **SIAK/Data kependudukan Dukcapil** (UU 23/2006 jo. UU 24/2013, Permendagri 102/2019) | Pangkalan data **tidak pernah** menyimpan atau mereplikasi data kependudukan. Kebutuhan verifikasi identitas (jika ada, di sisi OPD) diselesaikan lewat mekanisme pemanfaatan data Dukcapil resmi milik OPD terkait, di luar pangkalan data. |

## 3. Arsitektur: Hub-and-Spoke Dua Arah

**Hub-and-spoke, bukan point-to-point** — tetap dipertahankan: OPD dan desa tidak boleh saling integrasi satu-per-satu; semua jalur data melewati pangkalan data.

**[KEPUTUSAN]** Desa tidak diposisikan hanya sebagai pemasok data — desa juga **konsumen produk data terpublikasi**. Ini sejalan dengan **UU 6/2014 Pasal 86** (sebagaimana diubah UU 3/2024): desa *berhak mendapatkan akses informasi* melalui sistem informasi yang dikembangkan pemerintah kabupaten/kota, dan menjaga insentif desa untuk menyetor data. Aliran datanya:

```
                    ┌──────────── konsumen nasional ────────────┐
                    │   SIPD (e-Walidata)   Portal SDI   SPLP   │
                    └───────────────────▲───────────────────────┘
                                        │ (5) ekspor/konektor
   OPD A ─┐                             │
   OPD B ─┤ (1) setor agregat   ┌───────┴────────┐  (4) open data (kelas terbuka)
   OPD C ─┼────────────────────▶│  PANGKALAN     │────────────────▶ Publik
   OPD D ─┘                     │  DATA          │
      ▲                         │  (hub, satu-   │
      │ (2) konsumsi lintas     │   satunya      │
      └── domain sesuai hak ────│   perantara)   │
          akses                 └───────┬────────┘
                                        │ (1) setor: API (desa bersistem)
                                        │     atau unggah template (belum bersistem)
                                        │ (3) baca produk terpublikasi:
                                        │     statistik resmi, status validasi,
                                        ▼     dashboard perbandingan (read-only)
                          Desa & Kelurahan (wilayah layanan)
```

Lima aliran resmi — tidak ada aliran lain:

1. **Ingestion**: OPD dan desa menyetor data statistik agregat ke hub (API atau unggah template).
2. **Konsumsi OPD**: OPD membaca data lintas domain **melalui hub**, sesuai hak akses per domain.
3. **Konsumsi desa**: desa membaca **produk data terpublikasi** — statistik resmi yang sudah ditetapkan, status validasi setorannya sendiri, dan dashboard perbandingan antar desa. Read-only, tidak pernah ke data internal OPD.
4. **Publik**: dataset kelas terbuka disebarluaskan sebagai open data (lihat Bagian 8).
5. **Nasional**: konektor keluar ke SIPD, Portal SDI, dan kesiapan SPLP.

**Aturan keamanan yang berlaku:** peran "desa" tidak punya hak baca/tulis langsung ke data OPD dan sebaliknya; keduanya hanya berinteraksi melalui hub. Hub juga **menerbitkan produk data** yang boleh dibaca desa. Model permission harus membedakan tegas "data internal/mentah setoran" (akses terbatas pemiliknya + Walidata) dari "produk terpublikasi" (akses sesuai kelas data).

## 4. Peran & Terminologi Tata Kelola — Diselaraskan Perpres 39/2019

**[KEPUTUSAN]** Peran OPD/desa memakai nomenklatur baku **Perpres 39/2019**, bukan istilah informal seperti "wali data domain" — istilah ini yang akan dipakai auditor dan forum nasional, dan sistem **wajib memakai istilah baku ini** di skema, UI, dan dokumentasi:

| Peran baku (Perpres 39/2019) | Diemban oleh | Tanggung jawab dalam sistem |
|---|---|---|
| **Produsen Data** | Setiap OPD; setiap pemerintah desa/kelurahan | Menghasilkan dan menyetor data sesuai Standar Data dan jadwal; tetap **pemilik** data domainnya — sistem mencatat produsen asal pada setiap record (kolom `producer_id` wajib, tidak nullable) |
| **Walidata** | Unit pada **Diskominfo** | Mengumpulkan, memeriksa, mengelola, menyebarluaskan data; mengoperasikan pangkalan data; menetapkan & mengawasi standar API; admin teknis tertinggi sistem |
| **Pembina Data statistik** | **BPS Kabupaten/Kota setempat** (implementasi awal: BPS Kabupaten Probolinggo) | Pembinaan Standar Data, metadata baku, dan rekomendasi kegiatan statistik sektoral (UU 16/1997; koordinasikan kamus indikator dengan BPS sebelum ditetapkan) |
| **Pembina Data geospasial** | Badan Informasi Geospasial (melalui simpul jaringan daerah) | Bila kelak ada data geospasial; di Fase 1–3 data geospasial di luar lingkup kecuali kode wilayah |
| **Forum Satu Data tingkat kabupaten** | Dikoordinasikan **Bappeda** (Perpres 39/2019); anggota: Walidata, Produsen Data, BPS | Menetapkan daftar data, kamus indikator, aturan toleransi validasi, dan **memutus konflik data** (Bagian 9); keputusannya direkam di dalam sistem |
| **Pembina desa** | **DPMD** | Jembatan resmi ke seluruh desa/kelurahan wilayah layanan; Diskominfo tidak berwenang langsung ke pemerintah desa; DPMD mengelola onboarding, pelatihan, dan eskalasi non-teknis desa |
| **Kepala Daerah** | Bupati | Menetapkan Peraturan Bupati (dasar hukum sistem, klasifikasi data, retensi, kewajiban setor) dan penengah akhir forum |

Implikasi skema: model kepemilikan mencatat produsen asal untuk setiap catatan; peran aplikasi (RBAC) dipetakan 1:1 ke peran tata kelola di atas ditambah peran teknis (operator desa, approver desa/kades, operator OPD, approver OPD, admin Walidata, anggota Forum, auditor read-only).

## 5. Prinsip & Keputusan Integrasi OPD

1. **Wajib membuka API.** Setiap OPD yang punya layanan/sistem online wajib menyediakan API atau, minimal pada Fase 1, menyetor melalui API pangkalan data (push). Kewajiban dimuat dalam Perbup.
2. **Integrasi satu arah ke hub.** OPD hanya berintegrasi ke pangkalan data; hub menjadi perantara bagi konsumen lain. OPD tidak melayani konsumen di luar hub.
3. **[KEPUTUSAN] Kontrak API konkret** (menerjemahkan referensi arsitektur SPBE — Perpres 95/2018 jo. Perpres 132/2022 — dan siap-SPLP):
   - Gaya: **REST + JSON (UTF-8)**. Spesifikasi ditulis dan dipublikasikan sebagai **OpenAPI 3.1**; spesifikasi adalah kontrak, kode mengikuti spesifikasi (*spec-first*).
   - Versioning: pada path (`/v1/...`). Perubahan breaking = kenaikan major; dua versi major terakhir didukung paralel **minimal 12 bulan** dengan jadwal deprecation tertulis di dokumentasi.
   - Pola ingestion: `POST` batch dataset dengan header **`Idempotency-Key`** wajib (mencegah duplikasi saat retry di jaringan buruk). Ukuran batch maksimum 5.000 record per permintaan.
   - Format galat: **RFC 9457 (Problem Details for HTTP APIs)**, pesan berbahasa Indonesia, kode galat stabil dan terdokumentasi.
   - Waktu: **ISO 8601** dengan offset zona; penyimpanan internal UTC.
   - Kode referensi wajib: **kode wilayah administrasi Kemendagri** (Kepmendagri kode & data wilayah edisi terbaru; kode desa 10 digit) untuk seluruh dimensi wilayah; kamus **kode indikator** kabupaten ditetapkan Forum Satu Data dan dikelola sebagai master data berversi di dalam sistem.
   - Bentuk record statistik (minimum): `(kode_indikator, kode_wilayah, periode, nilai, satuan, status_data[sementara|tetap], producer_id, metadata_ref, submitted_at)` — **append-only**, tidak ada UPDATE/DELETE atas nilai; koreksi = record baru berversi.
   - Metadata: mengikuti struktur metadata baku Satu Data Indonesia (Permen PPN 17/2020 dan pedoman Pembina Data): judul, konsep, definisi, klasifikasi, satuan, ukuran, cakupan, periode, produsen, tanggal rilis.
4. **[KEPUTUSAN] Autentikasi & otorisasi API:**
   - **OAuth 2.0 *client credentials*** (RFC 6749) dengan access token JWT berumur pendek (≤ 15 menit). Satu client credential per instansi (bukan per orang); penerbitan, rotasi (maksimal 12 bulan), dan pencabutan dikelola Walidata dengan proses tertulis.
   - Transport: **TLS 1.2 minimum, TLS 1.3 direkomendasikan**; HSTS; mTLS opsional untuk OPD bervolume tinggi.
   - Otorisasi: **RBAC + scope per domain** (contoh scope: `stat.kesehatan.write`, `stat.sosial.read`). Prinsip *least privilege*: scope write hanya untuk domain milik produsen tersebut.
   - **API key statis tidak dipakai** sebagai mekanisme utama (hanya boleh untuk endpoint publik read-only ber-rate-limit, bila diperlukan).

## 6. Prinsip & Keputusan Integrasi Desa

Dua jalur setor ke pangkalan data — bukan ke OPD — dengan **pipeline validasi yang sama** sehingga hasil akhirnya konsisten apa pun jalur masuknya:

| Jalur | Kondisi | Mekanisme |
|---|---|---|
| **API** | Desa sudah punya SID (TataDesa, OpenSID, atau lainnya) | Near real-time, kontrak API dan autentikasi **sama persis** dengan OPD (Bagian 5); client credential diterbitkan atas nama pemerintah desa |
| **Unggah/entri** | Desa belum bersistem | Portal web pangkalan data: unggah template terstandar **atau** entri formulir langsung — satu kali ke hub, bukan ke tiap OPD |

**[KEPUTUSAN] Detail jalur unggah untuk desa belum bersistem:**
- Template **XLSX dan CSV (UTF-8, pemisah koma)**, satu template per domain per periode; versi template tercantum di header file dan divalidasi server — file dengan versi kedaluwarsa ditolak dengan petunjuk unduh versi baru.
- Batas ukuran **25 MB** per unggahan; validasi sinkron dengan laporan kesalahan **per baris, berbahasa Indonesia** (bukan sekadar "gagal").
- Akun: minimal **dua akun per desa** — *operator* (menyiapkan/mengunggah) dan *approver* (kepala desa/sekretaris desa yang mengesahkan setoran). Data berstatus "tersetor" hanya setelah disetujui approver.
- Autentikasi manusia: kata sandi + **MFA TOTP** (aplikasi authenticator); fallback OTP email untuk wilayah tanpa ponsel memadai. Reset akun melalui DPMD/kecamatan dengan verifikasi berjenjang.
- Kecamatan ditetapkan sebagai **titik bantu** unggah bagi desa dengan kendala konektivitas; UI harus bisa dipakai wajar pada koneksi lambat (target: halaman kerja utama < 500 KB transfer awal, berfungsi di Android WebView lama).
- **[KEPUTUSAN] Anti-double-entry (Permendesa PDT 13/2025):** sebelum meminta desa mengisi suatu indikator, sistem mengecek apakah indikator tersebut sudah tersedia dari SID/Satu iDesa melalui jalur API; jika ya, nilai diprefill/diambil otomatis dan desa hanya mengonfirmasi.

**Hak baca desa (aliran 3, Bagian 3):** setiap desa dapat melihat (a) riwayat dan status validasi setorannya sendiri, (b) statistik resmi yang sudah ditetapkan untuk seluruh wilayah kabupaten pada kelas data terbuka/terbatas-desa, (c) dashboard perbandingan indikator antar desa. Desa tidak dapat melihat setoran mentah desa lain yang belum ditetapkan resmi, dan tidak pernah melihat data internal OPD.

## 7. Keterbukaan Vendor

Standar integrasi bersifat **vendor-neutral**. Sistem desa apa pun — TataDesa, OpenSID, atau produk lain — memakai kontrak API, proses onboarding, dan SLA yang **identik**; tidak ada jalur istimewa. **[KEPUTUSAN]** kontrak OpenAPI, dokumentasi integrasi, template unggah, kamus indikator, dan panduan onboarding **dipublikasikan terbuka di repositori publik** (Bagian 11) — bukan didistribusikan eksklusif. Lingkungan **sandbox publik** dengan data sintetis disediakan agar vendor mana pun bisa menguji integrasi tanpa perjanjian khusus.

## 8. Klasifikasi & Privasi Data (Privacy by Design)

Tiga kelas data ditetapkan sebagai berikut:

| Kelas | Definisi | Di pangkalan data? |
|---|---|---|
| **Data statistik (agregat)** | Angka ringkasan per domain per wilayah per periode (mis. jumlah kasus stunting per desa) | **Ya** — inti sistem; wajib disetor rutin |
| **Data bisnis pendukung** | Data di balik angka statistik, **sudah dilepaskan dari NIK/identitas individu** | Ya, **hanya** atas permintaan bertiket untuk pemeriksaan validitas/konsistensi; bukan penyimpanan rutin |
| **Data mentah tingkat individu** (NIK + atribut yang melekat) | Data personal terikat identitas | **Tidak, tanpa pengecualian.** Tetap di sistem OPD/desa masing-masing; tidak pernah direplikasi ke hub |

Aturan turunan (mengikat untuk skema):
- Tidak boleh ada kolom/field yang menyimpan NIK, nomor KK, nama orang, atau pengenal individu langsung maupun *quasi-identifier* yang memungkinkan penelusuran balik (kombinasi tanggal lahir + alamat, dsb.). Tambahkan **linter skema/CI check** yang menolak migrasi berisi pola kolom semacam ini.
- Permintaan data bisnis pendukung hanya melalui **tiket validasi** di dalam sistem: siapa meminta, dataset mana, alasan (tujuan validasi), siapa menyetujui — semuanya tercatat di audit log. Data yang diterima terkunci pada tiket tersebut dan tidak dapat diekspor ke modul lain.
- **Ambang agregasi minimum (k-anonymity sederhana):** sel statistik dengan nilai populasi sangat kecil berisiko mengidentifikasi individu (mis. "1 penderita penyakit X di dusun Y"). **[KEPUTUSAN]** default k = 5 untuk publikasi kelas terbuka pada domain sensitif (kesehatan, sosial); nilai di bawah ambang ditampilkan sebagai "< 5". Ambang per domain dapat disesuaikan Forum Satu Data.
- **Verifikasi, bukan replikasi:** bila proses bisnis OPD membutuhkan pencocokan NIK, itu dilakukan di sistem OPD melalui kanal pemanfaatan data Dukcapil resmi (Permendagri 102/2019) — bukan fungsi pangkalan data.

**[KEPUTUSAN] Klasifikasi akses 3 tingkat** (diselaraskan UU 14/2008 tentang Keterbukaan Informasi Publik dan pembatasan akses Permen PPN 17/2020), disimpan sebagai atribut wajib setiap dataset:
1. **Terbuka** — dipublikasikan sebagai open data (lisensi data terbuka, format machine-readable CSV/JSON, metadata lengkap).
2. **Terbatas** — hanya peran internal pemerintahan tertentu (OPD lintas domain, desa untuk produk tertentu) — default untuk data yang belum ditetapkan resmi.
3. **Dikecualikan** — data bisnis pendukung dalam tiket validasi; akses hanya pemohon tiket + Walidata + auditor.

**Status hukum PDP yang wajib diketahui perancang (per Juli 2026):** UU 27/2022 berlaku penuh (masa transisi Pasal 74 berakhir Oktober 2024). Peraturan Pemerintah pelaksananya **belum ditetapkan** (RPP telah selesai harmonisasi dan berada dalam proses penetapan Presiden), dan **Badan PDP belum berdiri** — pengawasan sementara oleh Kemkomdigi. Konsekuensi desain: patuhi UU secara langsung (dasar pemrosesan, minimisasi data, pembatasan tujuan, keamanan, pencatatan pemrosesan/ROPA, kesiapan notifikasi kegagalan pelindungan ≤ 3×24 jam kepada subjek dan lembaga), dan sediakan **titik konfigurasi** untuk menyesuaikan retensi/prosedur ketika PP dan Badan PDP beroperasi. Sistem harus mampu menghasilkan **laporan kepatuhan** (record of processing, log permintaan data bisnis) saat diaudit.

## 9. Validasi & Resolusi Konflik Data

Saat dua sumber menyetor angka berbeda untuk domain yang sama:

1. **Tidak ada reject otomatis.** Kedua data diterima dan disimpan apa adanya (append-only, riwayat versi penuh — tidak menimpa).
2. Sistem **menandai (flag)** selisih — tidak memutuskan mana yang benar.
3. Penetapan data resmi dilakukan **oleh manusia melalui Forum Satu Data**, dan **[KEPUTUSAN]** keputusannya **direkam di dalam sistem** dengan jejak audit penuh, bukan di luar sistem tanpa jejak: forum (atau walidata atas mandat forum) menandai satu versi sebagai `official=true` disertai nomor/tanggal keputusan dan dasar penetapan; versi lain tetap tersimpan.

**[KEPUTUSAN] Aturan deteksi selisih:**
- Kunci pembanding: **(kode_indikator, kode_wilayah, periode)**.
- Default: **setiap perbedaan nilai** antar sumber pada kunci yang sama menghasilkan flag. Toleransi (persentase atau absolut) hanya berlaku bila Forum Satu Data menetapkannya untuk domain tertentu; aturan toleransi disimpan sebagai **konfigurasi berversi** di dalam sistem (bukan hardcode), lengkap dengan siapa menetapkan dan kapan berlaku.
- Antrean rekonsiliasi dengan status: `baru → dalam pembahasan → ditetapkan → ditutup`; setiap flag memiliki SLA tindak lanjut internal (default 30 hari kalender ke pembahasan pertama) dan dashboard bagi Walidata dan Forum.
- Produsen yang datanya di-flag mendapat **notifikasi otomatis** dan dapat melihat perbandingan nilai (tanpa membuka data internal pihak lain di luar nilai indikator yang bentrok).

## 10. Keamanan — Standar Minimum yang Mengikat

**[KEPUTUSAN]**

- Acuan kepatuhan: **Peraturan BSSN No. 4 Tahun 2021** (pedoman manajemen keamanan informasi SPBE dan standar teknis & prosedur keamanan SPBE) + penilaian mandiri **Indeks KAMI** sebelum go-live tiap fase.
- Enkripsi: **in-transit TLS 1.2+/1.3** di semua jalur (termasuk internal antar layanan); **at-rest AES-256** untuk basis data, backup, dan berkas unggahan.
- Akses berbasis peran (RBAC) sesuai pemetaan Bagian 4; tinjauan hak akses per semester; akun manusia wajib MFA; tidak ada akun bersama.
- **Audit log immutable** (append-only, tervalidasi hash berantai atau WORM storage) untuk: autentikasi, perubahan konfigurasi, setiap permintaan/akses data bisnis pendukung beserta alasannya, dan penetapan data resmi.
- Pemisahan lingkungan dev/staging/prod; **larangan menyalin data produksi** ke non-produksi tanpa anonimisasi; data uji = sintetis.
- **Uji penetrasi** oleh pihak independen sebelum go-live Fase 1 dan selanjutnya tahunan; pemindaian dependensi otomatis di CI; SBOM per rilis.
- Insiden: prosedur tanggap insiden tertulis, koordinasi CSIRT daerah/BSSN; kesiapan notifikasi kegagalan pelindungan data pribadi sesuai UU PDP (meski sistem tidak menyimpan data individu, data bisnis pendukung dalam tiket tetap diperlakukan sebagai berisiko).
- **Hosting & residensi data:** pusat data pemerintah di wilayah Indonesia (Pusat Data Nasional/PDNS atau data center pemda yang memenuhi standar), sesuai arah infrastruktur SPBE. Tidak menggunakan layanan cloud yang menempatkan data produksi di luar yurisdiksi Indonesia.
- Pendaftaran sebagai penyelenggara sistem elektronik lingkup publik mengikuti PP 71/2019 dan ketentuan instansi.

**[KEPUTUSAN] SLA & keberlangsungan:**
- Ketersediaan API ingestion + portal: **99,5% per bulan** (target 24/7; jendela pemeliharaan diumumkan ≥ 3 hari sebelumnya).
- **RPO 24 jam** (backup harian terenkripsi, disimpan terpisah dari sistem produksi), **RTO 4 jam**; **uji restore per kuartal** dengan berita acara.
- Rate limiting per client untuk melindungi dari klien yang rusak/menyerang; ingestion harus tetap berfungsi saat lonjakan musiman (tenggat setor serentak seluruh desa/kelurahan wilayah layanan).

## 11. Lisensi AGPL-3.0 & Tata Kelola Open Source

Perangkat lunak dirilis **AGPL-3.0-only**. Konsekuensi yang mengikat desain dan proses:

- **Pasal 13 AGPL (network use):** siapa pun yang menjalankan versi termodifikasi sebagai layanan jaringan wajib menawarkan kode sumber versi tersebut kepada pengguna. **[KEPUTUSAN]** aplikasi menyediakan endpoint/halaman "Tentang & Kode Sumber" yang menampilkan versi berjalan (git tag/commit) dan tautan repositori — bawaan, tidak bisa dimatikan lewat konfigurasi.
- **Kompatibilitas dependensi:** seluruh dependensi runtime wajib berlisensi kompatibel AGPL-3.0; dilarang dependensi proprietary yang mengikat (termasuk SDK layanan berbayar yang tak tergantikan). Pemeriksaan lisensi otomatis di CI.
- **Repositori publik** sejak awal (bukan "open source setelah jadi"): issue tracker publik, rilis ber-tag **SemVer**, CHANGELOG, dan proses kontribusi terbuka (PR + DCO). Vendor mana pun — termasuk pengembang TataDesa/OpenSID — berkontribusi lewat jalur yang sama; hak merge dipegang tim yang ditunjuk Walidata.
- **Dokumentasi** (arsitektur, panduan admin, panduan integrasi, panduan operator desa) berlisensi **CC BY-SA 4.0**, hidup di repositori yang sama, diperlakukan sebagai artefak rilis (dokumentasi usang = bug).
- **Portabilitas antar pemda:** semua yang spesifik-daerah (kode wilayah, kamus indikator, ambang, branding, integrasi lokal) berupa konfigurasi/seed data; sediakan panduan "deploy untuk kabupaten/kota lain" sejak Fase 2.
- Tidak ada telemetri yang mengirim data keluar instansi; fitur pembaruan/versi bersifat *pull* dan opsional.

## 12. Peta Regulasi → Kewajiban Desain (status diverifikasi per Juli 2026)

| Regulasi (status) | Kewajiban konkret bagi desain |
|---|---|
| **Perpres 95/2018** — SPBE (berlaku) | Kerangka umum; aplikasi & infrastruktur mengikuti arsitektur SPBE pemda; keamanan berkoordinasi BSSN |
| **Perpres 132/2022** — Arsitektur SPBE Nasional (berlaku) | Arsitektur pangkalan data harus terdaftar/selaras dengan dokumen Arsitektur SPBE Pemerintah Daerah (domain proses bisnis, data & informasi, aplikasi, infrastruktur, keamanan, layanan) — koordinasikan dengan tim SPBE pemda sebelum desain final |
| **Perpres 82/2023** — Percepatan Transformasi Digital & Keterpaduan Layanan Digital Nasional (berlaku) | Pertukaran data dengan pusat melalui **SPLP**; desain API harus siap dibungkus SPLP; tidak membangun integrasi bilateral ke K/L |
| **Perpres 39/2019** — Satu Data Indonesia (berlaku) | Terminologi & kelembagaan Bagian 4; prinsip Standar Data, Metadata baku, Interoperabilitas, Kode Referensi/Data Induk; penyebarluasan lewat Walidata & Portal SDI |
| **Permen PPN/Bappenas 16, 17, 18 Tahun 2020** (berlaku) | 16/2020: praktik manajemen data SPBE (arsitektur data, data induk, kualitas, basis data); 17/2020: format penyebarluasan & pembatasan akses portal SDI; 18/2020: tata kerja forum — rujukan prosedural Forum Satu Data kabupaten |
| **UU 27/2022** — PDP (berlaku penuh; **PP pelaksana belum terbit, Badan PDP belum berdiri** per Juli 2026) | Bagian 8: minimisasi, pembatasan tujuan, larangan data individu di hub, tiket validasi + audit log, retensi terbatas tujuan, kesiapan notifikasi kebocoran, ROPA; sediakan titik konfigurasi untuk menyesuaikan saat PP terbit |
| **UU 23/2014 Psl 391 & Permendagri 70/2019** — SIPD (berlaku, wajib bagi pemda) | Pangkalan data = pemasok informasi pembangunan daerah ke SIPD; konektor ekspor wajib; jangan menduplikasi fungsi perencanaan/keuangan SIPD |
| **UU 6/2014 jo. UU 3/2024 Psl 86** — Sistem Informasi Desa (berlaku) | Dasar hukum aliran 3 (desa sebagai konsumen); kabupaten wajib mengembangkan SID dan desa berhak mengakses informasi — pangkalan data adalah salah satu wujud kewajiban ini |
| **Permendesa PDT 13/2025** — Pedoman SID / platform Satu iDesa (berlaku, baru) | Prinsip isi-sekali-pakai-berulang (Bagian 6); interoperabilitas data desa dengan sistem pemda dan aplikasi SPBE lain; jangan menciptakan kewajiban input ganda bagi desa |
| **UU 16/1997** — Statistik (berlaku) | Data pangkalan = **statistik sektoral**; pembinaan dan rekomendasi BPS untuk kegiatan statistik sektoral; kamus indikator dikonsultasikan ke BPS |
| **UU 14/2008** — KIP (berlaku) | Dasar klasifikasi terbuka/dikecualikan (Bagian 8); daftar informasi publik |
| **UU 23/2006 jo. UU 24/2013 & Permendagri 102/2019** — Adminduk (berlaku) | Larangan replikasi data kependudukan; verifikasi NIK hanya via kanal Dukcapil resmi milik OPD, di luar hub |
| **PP 71/2019** — PSTE (berlaku) | Kewajiban penyelenggara sistem elektronik lingkup publik; keandalan & pengamanan sistem |
| **Peraturan BSSN 4/2021** (berlaku) | Standar teknis & prosedur keamanan SPBE; Indeks KAMI (Bagian 10) |
| **UU 43/2009** — Kearsipan (berlaku) | Jadwal retensi arsip elektronik; nilai retensi log & data dituangkan dalam Perbup/JRA |

Sistem harus memiliki **halaman/README kepatuhan** yang memetakan fitur ke pasal-pasal kunci di atas — ini akan menjadi bekal audit dan bekal adopsi pemda lain.

## 13. Rencana Rollout Bertahap — dengan Kriteria Keluar

| Fase | Cakupan | Implikasi teknis | Kriteria keluar (exit criteria) |
|---|---|---|---|
| **1 — Pilot** | 2–3 OPD + 5–10 desa bersistem (TataDesa/OpenSID) | Onboarding parsial per-peserta (bukan all-or-nothing); sandbox publik aktif; jalur API lengkap | ≥ 2 domain data mengalir end-to-end (setor → validasi → penetapan → publikasi); ≥ 1 siklus rekonsiliasi konflik selesai di Forum; pentest lulus tanpa temuan kritikal |
| **2 — Perluasan prioritas** | OPD domain prioritas + gelombang desa berikutnya (termasuk jalur unggah) | Onboarding **repeatable & terdokumentasi** (runbook, pelatihan DPMD/kecamatan); jalur unggah template live; konektor SIPD & portal SDI live | ≥ 50% desa aktif menyetor tepat periode; waktu onboarding satu desa ≤ 1 hari kerja; panduan deploy untuk pemda lain terbit |
| **3 — Wajib menyeluruh** | Seluruh OPD + seluruh desa/kelurahan wilayah layanan, didukung Perbup | Skala penuh: uji beban lonjakan setor serentak; dukungan variasi kapasitas peserta; help desk berjenjang (desa → kecamatan/DPMD → Walidata) | ≥ 90% kepatuhan setor per periode; SLA Bagian 10 terpenuhi 3 bulan berturut-turut |

## 14. Decision Log — Pertanyaan Terbuka & Keputusannya

| Pertanyaan terbuka | Keputusan | Di bagian |
|---|---|---|
| Format kontrak API | REST/JSON, OpenAPI 3.1, versioning path, idempotency, RFC 9457, kode wilayah Kemendagri, record append-only | 5 |
| Autentikasi & otorisasi | OAuth 2.0 client credentials + JWT pendek, TLS 1.2+, RBAC + scope per domain; desa belum bersistem: akun operator+approver dengan MFA TOTP via portal | 5, 6 |
| Template unggah desa | XLSX/CSV UTF-8 berversi, 25 MB, validasi per-baris berbahasa Indonesia, approval kades, titik bantu kecamatan | 6 |
| Ambang flag selisih | Default: selisih apa pun di-flag pada kunci (indikator, wilayah, periode); toleransi per domain via konfigurasi berversi milik Forum Satu Data | 9 |
| Retensi data bisnis pendukung | Dihapus permanen **90 hari** setelah tiket validasi ditutup; log permintaan & alasan disimpan **5 tahun** | 8, 10 |
| SLA & audit log | 99,5%/bulan, RPO 24 jam, RTO 4 jam, uji restore kuartalan; audit log immutable, online 2 tahun + arsip 5 tahun (final via JRA di Perbup) | 10 |
| Rencana keamanan siber | Peraturan BSSN 4/2021 + Indeks KAMI, TLS/AES-256, pentest tahunan, SBOM, hosting di Indonesia | 10 |
| Aliran data desa | Desa menjadi konsumen produk terpublikasi (read-only), dasar UU Desa Psl 86; keamanan desa↔OPD tetap terisolasi via hub | 3, 6 |
| Terminologi tata kelola | Diselaraskan Perpres 39/2019: Produsen Data / Walidata / Pembina Data / Forum Satu Data | 4 |
| Lisensi & tata kelola open source | AGPL-3.0-only, endpoint kode sumber, CI pemeriksa lisensi, dokumentasi CC BY-SA 4.0, konfigurasi-bukan-hardcode | 11 |

## 15. Keputusan Tersisa yang Bersifat Lokal **[LOKAL]**

Hanya hal yang memang tidak bisa diputuskan dokumen teknis — semuanya wajib final sebelum go-live Fase 1:

1. Penetapan **Perbup** (kewajiban setor, klasifikasi & retensi/JRA, penunjukan Walidata & forum) — draf substansi teknisnya sudah tersedia dari dokumen ini.
2. Pemilihan **2–3 OPD dan domain indikator pilot** oleh Forum Satu Data (rekomendasi: pilih domain dengan wali kebijakan jelas dan data sudah rutin, mis. stunting/kesehatan + sosial).
3. Penetapan **kamus indikator** perdana (dikonsultasikan ke BPS Kabupaten).
4. Penunjukan personel: admin Walidata, PIC per OPD, jadwal pelatihan DPMD/kecamatan.
5. Pilihan lokasi hosting final (PDNS vs DC pemda) sesuai ketersediaan anggaran dan kapasitas — persyaratan minimalnya sudah ditetapkan di Bagian 10.

Perubahan atas keputusan mana pun dalam dokumen ini dilakukan tertulis, diberi nomor versi, dan disinkronkan ke materi presentasi kebijakan agar tidak terjadi drift.

## 16. Referensi Wajib Baca untuk System Principal/Analis

Urutan baca yang disarankan sebelum menyusun desain rinci:

1. **Perpres 39/2019** + **Permen PPN 16, 17, 18/2020** — kelembagaan, prinsip data, metadata, portal (jantung tata kelola sistem ini).
2. **Perpres 95/2018** + **Perpres 132/2022** + dokumen **Arsitektur SPBE Pemerintah Daerah setempat** (implementasi awal: Kab. Probolinggo — minta ke tim SPBE Diskominfo) — agar desain langsung masuk ke arsitektur resmi daerah.
3. **Perpres 82/2023** — memahami SPLP dan arah keterpaduan nasional.
4. **UU 27/2022** (teks lengkap, khususnya bab dasar pemrosesan, hak subjek, kewajiban pengendali, notifikasi) — dan pantau penetapan PP pelaksana serta pembentukan Badan PDP.
5. **Permendagri 70/2019** — pahami modul SIPD yang akan menjadi tujuan ekspor.
6. **UU 6/2014 jo. UU 3/2024 Pasal 86** + **Permendesa PDT 13/2025** — hak informasi desa dan platform Satu iDesa.
7. **Peraturan BSSN 4/2021** + instrumen **Indeks KAMI** — baseline keamanan.
8. **UU 16/1997** dan pedoman statistik sektoral BPS — sebelum menetapkan kamus indikator.
9. Teks lisensi **AGPL-3.0** (khususnya Pasal 13) — sebelum memilih dependensi.

---

*Disusun berdasarkan materi kebijakan yang telah direview serta verifikasi status regulasi per 11 Juli 2026. Regulasi turunan UU PDP (PP pelaksana, Perpres Badan PDP) sedang dalam proses penetapan — pemegang dokumen wajib memantau penerbitannya dan merevisi Bagian 8 bila terbit.*
