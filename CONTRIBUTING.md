# Berkontribusi ke PaduData

Terima kasih sudah mempertimbangkan untuk berkontribusi. Proyek ini melayani infrastruktur data pemerintah, jadi beberapa aturan di sini lebih ketat dari proyek open source pada umumnya — bukan untuk mempersulit, tapi karena kesalahan di sini bisa berdampak ke data publik dan privasi warga.

Sebelum mulai, baca:
1. [`VISION.md`](VISION.md) — arah dan prinsip proyek.
2. [`docs/handoff-prinsip-sistem.md`](docs/handoff-prinsip-sistem.md) — keputusan kebijakan & teknis yang mengikat. Kontribusi yang bertentangan dengan handoff akan ditolak, bukan didiskusikan ulang di PR — diskusikan dulu lewat RFC (lihat `GOVERNANCE.md`).
3. [`GOVERNANCE.md`](GOVERNANCE.md) — siapa berhak memutuskan apa.

## Lisensi Kontribusi: AGPL-3.0 + DCO

Proyek ini berlisensi **AGPL-3.0-only**. Kami memakai **Developer Certificate of Origin (DCO)**, bukan CLA — lebih ringan bagi kontributor, cukup menandatangani commit.

Setiap commit wajib memuat baris `Signed-off-by`:

```bash
git commit -s -m "Pesan commit Anda"
```

Ini menyatakan bahwa Anda berhak mengirimkan kontribusi tersebut di bawah AGPL-3.0 (lihat teks lengkap sertifikasi di [developercertificate.org](https://developercertificate.org/)). PR tanpa sign-off akan ditolak otomatis oleh CI.

**Dependensi baru wajib berlisensi kompatibel AGPL-3.0.** Pemeriksaan lisensi berjalan otomatis di CI; dependensi proprietary atau dengan lisensi yang mengikat akan gagal build. Jika ragu, tanyakan di issue sebelum menulis kode.

## Jenis Kontribusi yang Diterima

- **Kode**: backend, portal, konektor (SIPD, Portal SDI, SPLP), perbaikan bug.
- **Spesifikasi API**: perubahan `api/openapi.yaml` — ini *spec-first*, artinya perubahan kontrak diajukan dan disetujui sebelum implementasi kode mengikutinya.
- **Dokumentasi**: `docs/` (berlisensi CC BY-SA 4.0) — panduan admin, panduan integrasi vendor SID, panduan operator desa.
- **Template & konfigurasi generik**: template unggah, skema validasi — asalkan tidak spesifik satu daerah (lihat batasan di bawah).
- **Terjemahan/penyederhanaan bahasa**: terutama untuk materi yang dipakai operator desa dengan latar teknis beragam.

## Yang **Bukan** untuk Repo Ini

Data spesifik satu daerah (kode wilayah, kamus indikator, ambang validasi, branding instansi) **tidak masuk repo inti** — itu tempatnya di `padudata-seed-<daerah>` masing-masing (lihat `README.md`). PR yang menambahkan data semacam ini ke repo inti akan diminta dipindah.

## Proses Pull Request

1. **Untuk perubahan besar, buka issue atau RFC dulu** (lihat `GOVERNANCE.md` §2 untuk kapan RFC wajib). Jangan langsung menulis ratusan baris kode untuk fitur yang belum didiskusikan — berisiko ditolak setelah kerja besar.
2. Fork, buat branch dari `main` dengan nama deskriptif (`fix/validasi-nik-null`, `feat/konektor-sipd`).
3. Tulis test untuk logika baru. PR tanpa test untuk logika bisnis (bukan UI kosmetik) akan diminta melengkapi.
4. Pastikan CI hijau: lint, test, pemeriksaan lisensi, dan — untuk perubahan skema — pemeriksaan larangan kolom PII (lihat di bawah).
5. Isi deskripsi PR: apa yang berubah, kenapa, dan (jika relevan) bagian handoff/RFC mana yang menjadi dasar.
6. Tunggu review Core Maintainer atau Maintainer Domain terkait.

## Batasan Teknis yang Tidak Bisa Dinegosiasikan di Level PR

Ini bukan gaya penulisan kode, ini pagar kebijakan (handoff §8) yang ditegakkan CI, bukan diskresi reviewer:

- **Dilarang** kolom/field yang menyimpan NIK, NKK, nama individu, atau *quasi-identifier* yang bisa menelusuri balik ke individu, di skema basis data pangkalan data. Linter skema akan menolak migrasi yang mengandung pola ini.
- **Dilarang** endpoint yang mengekspos data bisnis pendukung tanpa melalui alur tiket validasi beralasan.
- Setiap record statistik bersifat **append-only** — tidak ada `UPDATE`/`DELETE` atas nilai; koreksi selalu berupa record baru berversi.
- Endpoint "Tentang & Kode Sumber" (kewajiban AGPL Pasal 13) tidak boleh dihapus atau disembunyikan lewat konfigurasi.

## Setup Pengembangan Lokal

> Diisi begitu kerangka awal backend tersedia. Sementara ini, spesifikasi di `api/openapi.yaml` adalah titik awal paling akurat untuk memahami kontrak yang berlaku.

## Standar Kode

- Format & lint dijalankan otomatis di CI; jalankan `<perintah lint>` secara lokal sebelum push (diisi begitu tooling ditetapkan).
- Pesan commit: awali dengan tipe (`feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`) mengikuti [Conventional Commits](https://www.conventionalcommits.org/) — memudahkan penyusunan `CHANGELOG.md` otomatis.
- Bahasa kode/nama variabel: Inggris. Bahasa dokumentasi pengguna (panduan operator desa, pesan galat pengguna): Indonesia (lihat handoff §5 — pesan galat API wajib berbahasa Indonesia).

## Melaporkan Bug vs Kerentanan Keamanan

Bug biasa → buka issue publik dengan template yang tersedia.
Kerentanan keamanan → **jangan** buka issue publik. Ikuti [`SECURITY.md`](SECURITY.md).

## Kode Etik

Kontribusi di sini tunduk pada `CODE_OF_CONDUCT.md`. Kami ingin ruang ini ramah bagi kontributor dari latar belakang pemerintah maupun bukan, teknis maupun bukan — banyak pemangku kepentingan proyek ini (operator desa, staf OPD) bukan software engineer.
