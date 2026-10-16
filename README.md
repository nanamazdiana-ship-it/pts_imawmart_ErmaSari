# PTS Pemrograman Web - ImawMart (Dashboard Katalog Produk UMKM)

- **Nama Mahasiswa:** [Isi Nama Lengkap Kamu]
- **NPM:** 2440304005
- **Nama Aplikasi:** imawMart


---

## 1. Identitas & Deskripsi Singkat Aplikasi

**ImawMart** adalah aplikasi *Interactive Data Dashboard / Katalog Produk Minimarket UMKM* berbasis web interaktif. Aplikasi ini dikembangkan murni menggunakan **HTML5 semantik, CSS3 responsive (Grid/Flexbox), dan Vanilla JavaScript (ES6+)** tanpa menggunakan framework/library eksternal. 

Sistem ini mengintegrasikan REST API eksternal dari DummyJSON Products API secara asinkron (`async/await`) untuk menampilkan katalog produk secara dinamis. Aplikasi dilengkapi fitur pencarian *real-time*, pengurutan (*sorting*) harga dan nama, validasi formulir minat pemesanan berbasis aksesibilitas (ARIA), serta penanganan *UI State* (*Loading*, *Error State* dengan *Retry Button*, dan *Timestamp* pemuatan data).

---

## 2. Perhitungan Variasi Soal Berdasarkan NIM

Sesuai ketentuan Soal PTS Pemrograman Web 2026, kombinasi tugas ditentukan berdasarkan 3 digit terakhir NIM/NPM:

- **NPM:** 2440304005 (3 Digit Terakhir = **005**)
  * **Digit X = 0:** **Dataset / API:** Products (`https://dummyjson.com/products`) | Tema: Dashboard Produk / UMKM
  * **Digit Y = 0:** **Fitur Utama Wajib:** Search real-time + sorting
  * **Digit Z = 5:** **Validasi Form:** Nama minimal 5 karakter
  * **Perhitungan Nilai S:** S = X + Y + Z = 0 + 0 + 5 = 5
  * **Perhitungan Challenge Individual (C):** C = S mod 4 = 5 mod 4 = 1
  * **Hasil Challenge (C = 1):** Tombol *Retry* ketika *Fetch* gagal + Waktu/timestamp terakhir data berhasil dimuat.

---

## 3. Dataset & REST API Endpoints

Aplikasi mengonsumsi REST API publik dari DummyJSON:

- **Endpoint Utama (GET):** `https://dummyjson.com/products`
- **HTTP Method:** `GET`
- **Expected Status Code:** `200 OK`
- **Request Header:** `Accept: application/json`
- **Format Response:** JSON (Array of Objects)

---

## 4. Fitur Utama & Challenge Individual

1. **REST API Data Fetching:** Pengambilan data secara asinkron menggunakan `async/await` dengan pemeriksaan ketat `response.ok`.
2. **Search Real-Time (Fitur Y):** Penyaringan data produk langsung saat pengguna mengetik (*input event*) tanpa perlu melakukan *request* Fetch ulang ke server.
3. **Sorting Data (Fitur Y):** Pengurutan harga produk (Termurah/Termahal) dan abjad nama produk.
4. **Formulir & Validasi Aksesibel (Fitur Z):** Validasi *client-side* untuk form pengajuan sewa/pesanan dengan aturan wajib **Nama minimal 5 karakter**. Dilengkapi atribut ARIA (`aria-describedby`, `aria-live`, `aria-invalid`) dan pemindahan fokus kursor otomatis (*auto-focus*) ke input error pertama.
5. **UI State & Error Handling (Challenge C = 1):**
   * Tampilan *Loading State* saat data dimuat.
   * Tampilan *Error State* dan **Tombol Retry (Coba Lagi)** ketika koneksi internet terputus atau Fetch gagal.
   * Timestamp waktu/jam terakhir data berhasil dimuat ke dalam antarmuka.

---

## 5. Cara Menjalankan Project

1. Pastikan server lokal **Laragon 5** (Apache) sudah aktif.
2. Simpan direktori repositori ini ke dalam folder root server Laragon:
3. Buka peramban (*browser*) dan akses URL:
  http://localhost/pemweb-PTS/
4. **Pengujian Error State & Retry:** Buka Developer Tools (F12) -> Tab Network -> ubah status koneksi menjadi **Offline** -> Refresh halaman. Klik tombol **Coba Lagi (Retry)** setelah mengembalikan status koneksi menjadi **Online**.

---

## 6. Tangkapan Layar Tampilan (Screenshots)

- **Tampilan Desktop (1024px+):**
  ![Screenshot Desktop](assets/images/Screenshot-Desktop.png)
  *(Katalog tampil dalam bentuk CSS Grid fluid 3-4 kolom lengkap dengan Toolbar Search & Ringkasan Data).*

- **Tampilan Mobile (320px - 375px):**
  ![Screenshot Mobile](assets/images/screenshot-mobile.png)
  *(Layout bertumpuk vertikal 1 kolom secara responsif, bebas dari horizontal scrollbar).*

---


## 8. Endpoint REST API

| Keterangan | Isi |
|---|---|
| Layanan | DummyJSON (https://dummyjson.com) |
| Endpoint | `GET https://dummyjson.com/products?limit=0` |
| Method | GET (hanya membaca data, tanpa login atau API key) |
| Parameter | `limit=0` artinya ambil semua produk. Tanpa parameter ini, API hanya mengirim 30 produk pertama |
| Format respons | JSON |
| Dipanggil dari | fungsi `loadData()` di `js/app.js`, memakai `fetch(API_URL)`. Alamatnya disimpan di `js/config.js` sebagai `API_URL` |

Bentuk respons (dipotong):

```json
{
  "products": [
    {
      "id": 1,
      "title": "Essence Mascara Lash Princess",
      "category": "beauty",
      "price": 9.99,
      "rating": 4.94,
      "stock": 99,
      "brand": "Essence",
      "thumbnail": "https://cdn.dummyjson.com/..."
    }
  ],
  "total": 194,
  "skip": 0,
  "limit": 194
}
```

Field yang dipakai aplikasi: `title`, `category`, `brand`, `price`, `rating`, `stock`, dan `thumbnail`. Status respons yang ditangani: 200 (berhasil), 404 (alamat salah), dan 500 (server bermasalah), di `loadData()` dan `describeError()`.

## 9. Jawaban 10 Pertanyaan Penjelasan Kode

**1. Alur sejak halaman dibuka sampai data tampil.**
Browser memuat `index.html`, lalu script `config.js`, `logic.js`, dan `app.js` (atribut `defer`, jadi dijalankan setelah HTML selesai dibaca). `app.js` memanggil `init()`. `init()` menjalankan `fillIdentity()` untuk mengisi tahun di footer, memasang event listener, lalu memanggil `loadData()`. `loadData()` memanggil `showLoading()`, melakukan `fetch(API_URL)`, memeriksa `response.ok`, mengubah isi respons dengan `response.json()`, menyimpan `data.products` ke `state.products`, lalu memanggil `showSuccess()`, `renderLastLoaded()`, `renderStats()`, `renderCategoryOptions()`, dan `renderProducts()`. `renderProducts()` memanggil `createProductCard()` untuk setiap produk sehingga kartu tampil.

**2. Mengapa `response.ok` perlu diperiksa setelah `fetch()`.**
`fetch()` hanya menolak (reject) jika permintaan tidak sampai ke server, misalnya internet putus. Jika server menjawab 404 atau 500, `fetch()` tetap dianggap berhasil. Pada aplikasi ini, jika `API_URL` salah ketik menjadi `/product`, server menjawab 404 dengan isi JSON berupa pesan error. Tanpa pemeriksaan `response.ok`, kode lanjut membaca `data.products` yang tidak ada, dan hasilnya halaman kosong tanpa penjelasan. Dengan `if (!response.ok) throw ...`, kesalahan ditangkap `catch` dan `showError()` menampilkan pesan "Alamat API tidak ditemukan (HTTP 404)".

**3. Dari data JSON menjadi elemen DOM.**
Contoh satu objek: `{ "id": 1, "title": "Essence Mascara Lash Princess", "price": 9.99, "category": "beauty", "stock": 99, ... }`. Fungsi `createProductCard(product)` membuat `<li>` dengan `document.createElement`, lalu mengisi `<img>` (`src` dari `thumbnail`, `alt` dari `title`), `<h3>` (isi `title`), badge kategori (`prettyCategory("beauty")` menjadi "Beauty"), harga (`formatPrice(9.99)` menjadi "$9.99"), rating, dan stok. Teks diisi dengan `textContent`, bukan `innerHTML`, agar aman dari penyisipan HTML. Semua kartu dikumpulkan di `DocumentFragment`, lalu ditempel ke `<ul id="product-list">`.

**4. Internet terputus, endpoint salah, atau HTTP 404/500.**
Semuanya ditangkap oleh `try...catch` pada `loadData()`, lalu `showError(error)` menampilkan kotak error dengan pesan dari `describeError()`:
- Internet terputus: `fetch()` melempar `TypeError`, pesannya "Tidak dapat terhubung ke server".
- Endpoint salah / HTTP 404: `response.ok` bernilai `false`, error membawa `status = 404`, pesannya "Alamat API tidak ditemukan".
- HTTP 500: pesannya "Server API sedang bermasalah".
- JSON rusak: `response.json()` melempar `SyntaxError`, ada pesan tersendiri.

Dalam semua kasus indikator loading disembunyikan dan tombol "Coba lagi" muncul. Tombol itu memanggil `loadData()` lagi.

**5. Cara kerja fitur Y (search real-time + sorting).**
Event `input` pada `#search-input` memanggil `handleSearchInput`, yang menyimpan teks ke `state.keyword` lalu memanggil `renderProducts()`. Event `change` pada `#sort-select` memanggil `handleSortChange`, yang menyimpan `state.sortMode` lalu memanggil `renderProducts()`. `renderProducts()` menjalankan `searchProducts()` (memakai `filter()` dan `includes()`), lalu `sortProducts()` (memakai `sort()` pada salinan array), kemudian menggambar ulang daftar dan teks "Menampilkan X dari Y produk".

**6. Cara kerja validasi Z (nama minimal 5 karakter) dan alasannya.**
Form memakai atribut `novalidate` agar validasi dikerjakan JavaScript. Saat submit, `handleFormSubmit()` memanggil `event.preventDefault()`, lalu `validateNamaField()`, yang memanggil `validateNama(value, 5)`: nilai di-`trim()`, kosong menghasilkan "Nama wajib diisi", kurang dari 5 karakter menghasilkan pesan jumlah karakter saat ini. Jika tidak valid, pesan ditampilkan dengan `aria-invalid="true"` dan fokus dipindah ke input. Jika valid, pesan sukses muncul. Input tidak valid tidak boleh diproses karena data yang salah atau kosong dapat merusak data tujuan, menghasilkan catatan yang tidak berguna, dan menjadi celah penyalahgunaan. Validasi di sisi klien juga memberi umpan balik cepat kepada pengguna.

**7. localStorage.**
Project ini tidak memakai localStorage. Skenario yang relevan tanpa menambah fitur baru: menyimpan `state.keyword` dan `state.sortMode` ke localStorage setiap kali pengguna mencari atau mengurutkan (di dalam `handleSearchInput` dan `handleSortChange`), lalu membacanya di `init()` sebelum `loadData()`. Dengan begitu, setelah halaman dimuat ulang atau dibuka lagi, pencarian dan urutan terakhir pengguna tetap terpasang. Data diubah saat pengguna mengubah input dan dibaca sekali saat halaman dibuka.

**8. Satu fungsi penting (`computeStats`, 10 baris).**

```js
function computeStats(products) {
  if (products.length === 0) return null;
  const totalPrice = products.reduce((sum, product) => sum + product.price, 0);
  const mostExpensive = products.reduce((max, product) => (product.price > max.price ? product : max));
  const categories = new Set(products.map((product) => product.category));
  return {
    averagePrice: totalPrice / products.length,
    mostExpensive: mostExpensive,
    categoryCount: categories.size,
  };
}
```

- **Input**: array produk dari API.
- **Proses**: `reduce` pertama menjumlahkan semua harga; `reduce` kedua membandingkan harga untuk mencari produk termahal; `map` mengambil kategori setiap produk dan `Set` membuang duplikat sehingga `size` adalah jumlah kategori.
- **Output**: objek berisi rata-rata harga, objek produk termahal, dan jumlah kategori (atau `null` jika data kosong). `renderStats()` lalu menampilkannya ke kartu ringkasan.

**9. Bug / error paling sulit.**
Bug tersulit adalah nama saya di sidebar tetap tertulis "NAMA LENGKAP" walaupun sudah saya ganti. Penyebabnya, nama diambil JavaScript dari `config.js` yang lama, sedangkan folder project saya belum ditimpa dengan file terbaru, jadi `app.js` terus menimpa teks di HTML dengan isi lama. Saya menemukannya dengan memeriksa isi `config.js` di folder saya dan membandingkannya dengan versi terbaru. Saya memperbaikinya dengan menulis nama langsung di `index.html` (tidak lagi lewat JavaScript), menimpa semua file dengan versi terbaru, lalu menekan Ctrl+F5 untuk membuang cache browser.

**10. Dua perbaikan jika diberi satu minggu lagi.**
1. **Pagination atau filter kategori.** Saat ini 194 kartu digambar sekaligus. Dengan pagination, DOM lebih ringan dan halaman lebih cepat di perangkat murah; filter kategori membantu pengguna menemukan produk lebih cepat.
2. **Debounce pada pencarian dan cache data di localStorage.** Debounce mengurangi render ulang berulang saat mengetik cepat. Cache membuat data tetap tampil (dengan penanda "data tersimpan") saat internet terputus.

