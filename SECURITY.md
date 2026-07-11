# Kebijakan Keamanan PaduData

PaduData adalah infrastruktur data pemerintah. Kerentanan di sini berpotensi berdampak pada data instansi publik dan, secara tidak langsung, kepercayaan warga terhadap tata kelola data pemerintah. Kami menanggapi laporan keamanan dengan prioritas tertinggi.

## Melaporkan Kerentanan

**Jangan buka issue publik untuk kerentanan keamanan.** Laporan publik sebelum ada perbaikan memberi waktu bagi pihak jahat untuk mengeksploitasinya di instalasi pemerintah yang sedang berjalan.

Laporkan secara privat melalui:
- **GitHub Security Advisory** (tab "Security" → "Report a vulnerability") pada repositori ini — jalur yang direkomendasikan, terenkripsi dan hanya terlihat maintainer.
- Atau email ke `<alamat-keamanan@diisi-sebelum-rilis>` dengan subjek `[SECURITY] <ringkasan singkat>`.

Sertakan sebisa mungkin:
- Deskripsi kerentanan dan dampak potensial (mis. bypass autentikasi, akses data bisnis pendukung tanpa tiket, injeksi, dsb.).
- Langkah reproduksi atau proof-of-concept.
- Versi/commit yang terpengaruh.
- Saran mitigasi jika ada.

## Yang Bisa Anda Harapkan

| Tahap | Target waktu |
|---|---|
| Konfirmasi penerimaan laporan | ≤ 3 hari kerja |
| Penilaian awal tingkat keparahan | ≤ 7 hari kerja |
| Perbaikan untuk kerentanan kritis | secepat mungkin, dikoordinasikan dengan pelapor |
| Disclosure publik (CVE/advisory) | setelah patch tersedia dan diberi waktu wajar bagi pengadopsi untuk update — dikoordinasikan bersama pelapor, biasanya 30–90 hari |

## Cakupan

Termasuk dalam cakupan:
- Kode di repositori ini (`otoricode/padudata`) dan repositori resmi terkait di bawah organisasi `otoricode`.
- Kerentanan yang memungkinkan: bypass autentikasi/otorisasi, eskalasi hak akses lintas peran (mis. desa membaca data internal OPD, atau sebaliknya — pelanggaran langsung terhadap arsitektur hub-and-spoke di `VISION.md`), akses data bisnis pendukung di luar alur tiket, kebocoran data melalui log/error message, injeksi (SQL/NoSQL/command), SSRF, kerentanan pada dependensi yang digunakan secara signifikan.

Di luar cakupan (laporkan sebagai issue biasa, bukan security advisory):
- Kerentanan pada instalasi pihak ketiga yang telah dimodifikasi dari kode asli.
- Praktik konfigurasi buruk di lingkungan deployment spesifik daerah (`padudata-seed-*`) yang bukan cacat kode inti.
- Bug tanpa implikasi keamanan.

## Untuk Instansi yang Menjalankan PaduData

Jika instansi Anda mengoperasikan instalasi PaduData dan mencurigai insiden keamanan aktif (bukan sekadar kerentanan yang belum dieksploitasi), tangani sebagai **insiden**, bukan laporan bug biasa:
1. Ikuti prosedur tanggap insiden internal instansi Anda terlebih dahulu.
2. Koordinasikan dengan BSSN/CSIRT daerah sesuai jalur resmi.
3. Beri tahu kami secara privat (jalur di atas) agar patch dapat disebarluaskan ke seluruh pengadopsi lain — insiden yang tidak dilaporkan ke hulu berisiko terulang di kabupaten/kota lain yang menjalankan kode yang sama.

## Pengakuan

Dengan izin pelapor, kami mencantumkan nama/afiliasi pada catatan rilis setelah advisory dipublikasikan. Tidak ada program bug bounty berbayar saat ini.
