# Tempmail Plus API

REST API untuk membuat alamat email sementara, memeriksa inbox, membaca pesan, dan mengelola email menggunakan layanan [tempmail.plus](https://tempmail.plus).

> **Catatan:** Email sementara tidak cocok untuk akun penting atau menerima informasi sensitif. Ketersediaan pesan dan domain bergantung pada layanan sumber.

## Fitur

- Membuat alamat email sementara dengan username acak atau pilihan sendiri.
- Mendapatkan daftar domain yang tersedia.
- Melihat daftar pesan dan membaca isi email.
- Menunggu email baru dengan polling.
- Menghapus satu pesan atau mengosongkan inbox.

## Base URL

Setelah deploy ke Vercel, gunakan URL proyek Anda, misalnya:

```text
https://nama-proyek-anda.vercel.app
```

Endpoint juga tersedia melalui domain proyek saat ini: `https://hex-tempmail.vercel.app`.

## Daftar endpoint

Semua endpoint menerima parameter melalui query string. Contoh di bawah memakai `BASE_URL` sebagai placeholder untuk URL deployment Anda.

| Method | Endpoint | Parameter | Keterangan |
| --- | --- | --- | --- |
| `GET` | `/` | — | Informasi API dan daftar route. |
| `GET` | `/api/generate` | `username` (opsional), `domain` (opsional) | Membuat alamat email. Username tidak valid akan diganti dengan username acak; domain yang tidak tersedia akan kembali ke domain default. |
| `GET` | `/api/domains` | — | Mendapatkan daftar domain yang didukung. |
| `GET` | `/api/inbox` | `email` (wajib), `limit` (opsional, default `20`), `first_id` (opsional), `epin` (opsional) | Mendapatkan daftar pesan di inbox. |
| `GET` | `/api/read` | `email` (wajib), `mail_id` (wajib), `epin` (opsional) | Membaca detail pesan. |
| `GET` | `/api/delete` | `email` (wajib), `mail_id` (wajib), `epin` (opsional) | Menghapus satu pesan. |
| `GET` | `/api/destroy` | `email` (wajib), `first_id` (opsional), `epin` (opsional) | Menghapus/mengosongkan inbox. |
| `GET` | `/api/poll` | `email` (wajib), `timeout` (opsional, detik; default `60`), `interval` (opsional, milidetik; default `3000`), `autoRead` (opsional), `epin` (opsional) | Menunggu email baru. `autoRead=false` mengembalikan ringkasan pesan tanpa membaca detailnya. |

### Contoh penggunaan

Buat email dengan username dan domain tertentu:

```bash
curl "BASE_URL/api/generate?username=contoh123&domain=mailto.plus"
```

Buat email acak:

```bash
curl "BASE_URL/api/generate"
```

Lihat domain yang didukung:

```bash
curl "BASE_URL/api/domains"
```

Periksa inbox:

```bash
curl "BASE_URL/api/inbox?email=contoh123%40mailto.plus&limit=10"
```

Baca pesan (ganti `MAIL_ID` dengan `mail_id` dari hasil inbox):

```bash
curl "BASE_URL/api/read?email=contoh123%40mailto.plus&mail_id=MAIL_ID"
```

Tunggu email baru selama maksimal 30 detik:

```bash
curl "BASE_URL/api/poll?email=contoh123%40mailto.plus&timeout=30&interval=3000"
```

Hapus pesan atau kosongkan inbox:

```bash
curl "BASE_URL/api/delete?email=contoh123%40mailto.plus&mail_id=MAIL_ID"
curl "BASE_URL/api/destroy?email=contoh123%40mailto.plus"
```

Jika alamat email atau parameter berisi karakter khusus, URL-encode nilainya.

## Format respons

Respons berhasil umumnya berupa JSON dengan `status: true`. Contoh hasil generate:

```json
{
  "status": true,
  "email": "contoh123@mailto.plus",
  "username": "contoh123",
  "domain": "mailto.plus"
}
```

Endpoint yang memerlukan parameter wajib akan mengembalikan HTTP `400` jika parameter tersebut tidak diberikan. Kesalahan saat mengambil data dari layanan sumber dapat mengembalikan HTTP `500` beserta pesan pada properti `error`.

## Deploy ke Vercel

1. Fork atau push repository ini ke akun GitHub Anda.
2. Masuk ke [Vercel](https://vercel.com/) dan pilih **Add New → Project**.
3. Import repository `api-tempmail` dari GitHub.
4. Biarkan konfigurasi build mengikuti deteksi otomatis Vercel; proyek ini menggunakan fungsi serverless pada direktori `api/` dan konfigurasi routing di `vercel.json`.
5. Klik **Deploy**.
6. Setelah deployment selesai, buka URL proyek. Tes `https://URL-PROYEK-ANDA/api/domains` atau `https://URL-PROYEK-ANDA/`.

Tidak ada environment variable yang diperlukan pada konfigurasi saat ini. Dependensi `axios` tercantum di `package.json` dan akan dipasang saat deployment.

## Menjalankan lokal

Butuh Node.js dan npm. Dari direktori proyek:

```bash
npm install
npx vercel dev
```

Buka URL lokal yang diberikan Vercel CLI, biasanya `http://localhost:3000`.

## Teknologi

- Node.js (JavaScript)
- Vercel Serverless Functions
- Axios
- [tempmail.plus](https://tempmail.plus) sebagai layanan email sementara

## Lisensi

Belum ada lisensi yang ditentukan di repository ini. Hubungi pemilik repository untuk ketentuan penggunaan.
