# PaduData

**Pangkalan data terpadu untuk integrasi data statistik OPD dan desa/kelurahan tingkat kabupaten/kota.**

[![Lisensi: AGPL-3.0](https://img.shields.io/badge/Lisensi-AGPL--3.0-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-pilot--fase--1-orange.svg)](#status-proyek)

> Nama "PaduData" adalah nama kerja hingga pemeriksaan merek final selesai. Lihat [`VISION.md`](VISION.md) untuk arah proyek dan [`docs/handoff-prinsip-sistem.md`](docs/handoff-prinsip-sistem.md) untuk keputusan kebijakan & teknis yang mengikat.

> Repositori ini dikelola oleh **[Otoritech](https://otoritech.com)**. Kode dan dokumentasi bebas digunakan apa adanya (*as-is*) sesuai lisensi masing-masing — **tidak perlu menghubungi Otoritech atau siapa pun** untuk memakainya. Lihat [Lisensi](#lisensi), [`LICENSE.id.md`](LICENSE.id.md) (terjemahan), dan [`docs/ringkasan-lisensi.md`](docs/ringkasan-lisensi.md) (versi sederhana untuk pemangku kebijakan).

---

## Apa Ini?

Setiap kabupaten/kota di Indonesia punya masalah yang sama: data pembangunan terpencar di puluhan OPD dan ratusan desa, sering diminta berulang dengan format berbeda-beda, tanpa satu sumber kebenaran yang jelas. PaduData adalah **hub integrasi statistik sektoral** — bukan basis data kependudukan, bukan pengganti SIPD atau Satu iDesa — yang menjembatani OPD dan desa/kelurahan sesuai arsitektur *hub-and-spoke*, dibangun langsung dari kerangka Satu Data Indonesia, SPBE, dan UU Pelindungan Data Pribadi.

Implementasi pertama: **Kabupaten Probolinggo** (24 kecamatan, 325 desa, 5 kelurahan). Dirancang sejak awal agar kabupaten/kota lain bisa men-deploy dari kode sumber yang sama.

Baca dulu:
- **[`VISION.md`](VISION.md)** — kenapa proyek ini ada dan prinsip yang tidak dikompromikan.
- **[`docs/handoff-prinsip-sistem.md`](docs/handoff-prinsip-sistem.md)** — keputusan kebijakan, regulasi acuan, dan spesifikasi teknis tingkat prinsip. Ini rujukan wajib sebelum menyentuh kode.
- **[`GOVERNANCE.md`](GOVERNANCE.md)** — siapa memutuskan apa.
- **[`CONTRIBUTING.md`](CONTRIBUTING.md)** — cara berkontribusi.
- **[`SECURITY.md`](SECURITY.md)** — cara melaporkan kerentanan.

## Arsitektur Singkat

```
   OPD (Produsen Data) ──┐                      ┌──▶ SIPD / Portal SDI / SPLP
                          ▼                      │
                    PANGKALAN DATA (hub) ─────────┤
                          ▲                      │
   Desa/Kelurahan ────────┘                      └──▶ Open Data publik
   (Produsen Data,        via API (SID bersistem)
    juga konsumen         atau unggah template
    produk terpublikasi)  (belum bersistem)
```

Prinsip kunci: **tidak ada integrasi langsung OPD↔desa.** Semua jalur data melewati hub. Data statistik agregat wajib disetor; data bisnis pendukung hanya diminta bertiket untuk validasi; data individu (NIK dst.) **tidak pernah** disimpan di sini. Rincian penuh ada di handoff, Bagian 3, 7, dan 8.

## Tumpukan Teknis (ringkas)

| Lapisan | Pilihan | Rujukan |
|---|---|---|
| Kontrak API | REST + JSON, OpenAPI 3.1, spec-first | handoff §5 |
| Autentikasi antar sistem | OAuth 2.0 client credentials + JWT | handoff §5 |
| Autentikasi manusia (desa) | Password + MFA TOTP | handoff §6 |
| Format galat | RFC 9457 Problem Details | handoff §5 |
| Enkripsi | TLS 1.2+/1.3 in-transit, AES-256 at-rest | handoff §10 |
| Keamanan | Peraturan BSSN No. 4/2021, Indeks KAMI | handoff §10 |
| Lisensi kode | AGPL-3.0-only | `LICENSE` |
| Lisensi dokumentasi | CC BY-SA 4.0 | `docs/LICENSE` |

Spesifikasi API hidup di [`api/openapi.yaml`](api/openapi.yaml) sebagai sumber kebenaran — kode mengikuti spesifikasi, bukan sebaliknya.

## Struktur Repositori

```
padudata/                      (repo ini — inti, netral-daerah)
├── api/openapi.yaml            kontrak API (sumber kebenaran)
├── docs/                       dokumentasi (CC BY-SA 4.0)
├── templates/                  template unggah desa (XLSX/CSV berversi)
├── deploy/                     panduan & manifest deployment generik
├── src/                        kode sumber backend + portal
└── VISION.md, GOVERNANCE.md, CONTRIBUTING.md, SECURITY.md, LICENSE

padudata-seed-probolinggo/      (repo terpisah — spesifik daerah)
├── wilayah.json                 kode wilayah Kemendagri Kab. Probolinggo
├── indikator.json               kamus indikator hasil Forum Satu Data
├── ambang-validasi.json         aturan toleransi selisih per domain
└── branding/                    logo, nama instansi, kontak
```

Kabupaten/kota lain membuat `padudata-seed-<nama-daerah>` sendiri — repo inti **tidak pernah** memuat data spesifik satu daerah. Lihat handoff §11 untuk alasan pemisahan ini.

## Status Proyek

Fase 1 (pilot) — 2–3 OPD dan 5–10 desa bersistem di Kabupaten Probolinggo. Lihat [milestone](../../milestones) dan [issues berlabel `fase-1`](../../issues?q=label%3Afase-1) untuk progres.

Rencana fase lengkap ada di handoff §13.

## Menjalankan Secara Lokal

> Bagian ini akan diisi begitu kerangka backend pertama tersedia. Sampai saat itu, mulai dari `api/openapi.yaml` untuk memahami kontrak yang sedang dibangun.

```bash
git clone https://github.com/otoricode/padudata.git
cd padudata
# instruksi setup menyusul
```

## Lisensi

Kode: [GNU Affero General Public License v3.0 (AGPL-3.0-only)](LICENSE) — teks resmi berbahasa Inggris, mengikat secara hukum. Terjemahan tidak resmi tersedia di [`LICENSE.id.md`](LICENSE.id.md); versi sederhana untuk pemangku kebijakan (non-teknis) ada di [`docs/ringkasan-lisensi.md`](docs/ringkasan-lisensi.md). Siapa pun yang menjalankan versi modifikasi sebagai layanan jaringan wajib menyediakan kode sumbernya. Lihat halaman "Tentang & Kode Sumber" pada setiap instalasi.

Dokumentasi: [CC BY-SA 4.0](docs/LICENSE).

**Bebas dipakai, tanpa izin.** Kode dan dokumentasi di repositori ini boleh dipakai, disalin, dan dimodifikasi apa adanya (*as-is*, tanpa jaminan) oleh siapa pun sesuai ketentuan lisensi di atas — tidak perlu menghubungi Otoritech, maintainer, atau pihak mana pun untuk meminta izin.

## Kontak & Tata Kelola

Repositori (kode & rilis) dikelola oleh **[Otoritech](https://otoritech.com)**. Tata kelola *data* (bukan kode) dikoordinasikan terpisah oleh Walidata (Diskominfo Kabupaten Probolinggo) dan Forum Satu Data Kabupaten Probolinggo untuk implementasi awal. Lihat [`GOVERNANCE.md`](GOVERNANCE.md) untuk struktur lengkap, termasuk cara kabupaten/kota lain bergabung sebagai co-maintainer.
