# Dokumentasi Integrasi Backend-Frontend Roasty

## Ringkasan Integrasi

Telah berhasil mengintegrasikan seluruh flow aplikasi Roasty dari halaman login hingga profil dengan backend API. Berikut adalah penjelasan alur dan implementasi:

---

## 1. Alur Aplikasi (Flow)

### ✅ Halaman Login → Beranda
- **File**: `login.html`
- **Fungsi**: Login dengan email dan password
- **API Endpoint**: `POST /login`
- **Data Dikirim**: 
  ```json
  {
    "email": "user@email.com",
    "password": "password"
  }
  ```
- **Hasil**: 
  - Token disimpan ke localStorage
  - User info disimpan ke localStorage
  - Redirect ke `Halaman.beranda.html`
  - Admin user redirect ke `admin_dashboard.html`

---

### ✅ Halaman Beranda → Daftar Produk
- **File**: `Halaman.beranda.html`
- **Fungsi**: Menampilkan halaman utama dengan kategori dan rekomendasi produk
- **Fitur**:
  - Kategori links akan mengarah ke halaman daftar produk
  - Product cards dapat diklik untuk melihat detail produk
  - Verifikasi authentication (jika perlu uncomment)
- **Next Page**: `halaman.daftar.produk.html`

---

### ✅ Halaman Daftar Produk → Detail Produk
- **File**: `halaman.daftar.produk.html`
- **Fungsi**: Menampilkan daftar semua produk dari backend
- **API Endpoint**: `GET /products`
- **Fitur**:
  - Fetch semua produk dari API
  - Menampilkan dinamis product cards dengan data dari backend
  - Harga dan stok dari database
  - Klik pada produk akan menyimpan product ID ke sessionStorage
- **Next Page**: `halaman.detail.produk.html`

---

### ✅ Halaman Detail Produk → Keranjang Belanja
- **File**: `halaman.detail.produk.html`
- **Fungsi**: Menampilkan detail lengkap produk
- **API Endpoint**: `GET /products/{id}`
- **Fitur**:
  - Fetch detail produk berdasarkan ID yang disimpan dari halaman sebelumnya
  - Update harga dinamis
  - Tombol "Add to Cart" menyimpan item ke `localStorage` dengan format:
    ```json
    [
      {
        "id": 1,
        "quantity": 1
      }
    ]
    ```
  - Redirect ke halaman keranjang belanja
- **Next Page**: `halaman.keranjang.belanja.html`

---

### ✅ Halaman Keranjang Belanja → Pembayaran
- **File**: `halaman.keranjang.belanja.html`
- **Fungsi**: Menampilkan keranjang belanja dengan item yang dipilih
- **Fitur**:
  - Fetch detail setiap produk dari API menggunakan product ID
  - Hitung total harga berdasarkan quantity × price
  - Tampilkan ringkasan belanja
  - Tombol checkout mengarah ke halaman pembayaran
- **Next Page**: `halaman.pembayaran.html`

---

### ✅ Halaman Pembayaran
- **File**: `halaman.pembayaran.html`
- **Fungsi**: Proses pembayaran dan pembuatan order
- **API Endpoints**:
  - `GET /me` - Ambil data user yang login
  - `POST /orders` - Buat order baru
- **Data Dikirim**:
  ```json
  {
    "items": [
      {
        "product_id": 1,
        "quantity": 2,
        "price": 100000
      }
    ],
    "total_amount": 200000,
    "payment_method": "midtrans"
  }
  ```
- **Fitur**:
  - Verifikasi token (redirect ke login jika tidak ada)
  - Fetch user data untuk tampilkan info pengguna
  - Calculate total dari cart items
  - Tombol "Bayar Sekarang" membuat order ke backend
  - Clear localStorage cart setelah order berhasil
  - Redirect ke halaman beranda setelah sukses

---

### ✅ Halaman Profil
- **File**: `halaman.profil.html`
- **Fungsi**: Edit profil pengguna
- **API Endpoints**:
  - `GET /profile` - Ambil data profil user
  - `PUT /profile` - Update data profil user
- **Data Dikirim**:
  ```json
  {
    "first_name": "Budi",
    "last_name": "Santoso",
    "birth_date": "1995-12-15",
    "gender": "male"
  }
  ```
- **Fitur**:
  - Load data profil saat halaman dibuka
  - Update form dengan data dari backend
  - Tombol "Simpan Perubahan" mengirim update ke API
  - Validasi token (redirect ke login jika expired)

---

## 2. Penyimpanan Data Client-Side

### localStorage
- **token**: JWT token untuk authentication
- **user**: Info user (JSON string)
- **cart**: Array produk di keranjang (JSON string)

### sessionStorage
- **selectedProductId**: ID produk yang dipilih di halaman daftar produk

---

## 3. Konfigurasi API

**File**: `config.js`

```javascript
const CONFIG = {
    API_BASE_URL: "http://localhost:8000/api",
    assets: "http://localhost:8000/storage"
};
```

### Cara Menggunakan:
```javascript
const API_URL = CONFIG.API_BASE_URL;
// Penggunaan: 
fetch(`${API_URL}/products`)
```

---

## 4. Authentication Flow

### Login
1. User submit form di `login.html`
2. POST ke `/login` dengan email & password
3. Backend return token dan user data
4. Frontend simpan token ke localStorage
5. Redirect berdasarkan role (admin/user)

### Protected Routes
```javascript
const token = localStorage.getItem('token');
const headers = {
    'Authorization': `Bearer ${token}`,
    'Accept': 'application/json'
};
```

### Check Authentication
```javascript
const token = localStorage.getItem('token');
if (!token) {
    window.location.href = 'login.html';
}
```

---

## 5. Error Handling

Setiap API call memiliki error handling:
```javascript
try {
    const res = await fetch(url, options);
    if (!res.ok) throw new Error('Error message');
    const data = await res.json();
    // Process data
} catch (err) {
    alert('Error: ' + err.message);
    console.error(err);
}
```

---

## 6. Backend API Reference

### Public Routes
- `POST /register` - Register user baru
- `POST /login` - Login user
- `GET /products` - Get semua produk
- `GET /products/{id}` - Get detail produk

### Protected Routes (Perlu Token)
- `POST /logout` - Logout
- `GET /me` - Get data user sekarang
- `GET /profile` - Get profil user
- `PUT /profile` - Update profil user
- `GET /orders` - Get order user
- `POST /orders` - Create order baru
- `GET /user-addresses` - Get alamat user
- `POST /user-addresses` - Create alamat baru

### Admin Routes (Perlu Token + Role Admin)
- `POST /products` - Create produk
- `PUT /products/{id}` - Update produk
- `DELETE /products/{id}` - Hapus produk
- `GET /admin/orders` - Get semua orders

---

## 7. Setup & Testing

### Prasyarat
1. Laravel backend running di `http://localhost:8000`
2. Database sudah di-seed dengan produk
3. Frontend di `file://` atau server lokal

### Testing Workflow
1. Buka `login.html`
2. Login dengan akun yang sudah ada
3. Klik kategori atau product untuk navigasi
4. Tambah produk ke keranjang
5. Lanjut ke checkout dan buat order
6. Lihat/edit profil

---

## 8. Browser Compatibility

Menggunakan:
- `localStorage` & `sessionStorage`
- `fetch` API
- Modern JavaScript (ES6+)
- Tailwind CSS

**Kompatibel dengan**: Chrome, Firefox, Safari, Edge (modern versions)

---

## 9. Catatan Penting

⚠️ **Security Notes:**
- Token disimpan di localStorage (tidak ideal untuk production)
- Untuk production: pertimbangkan httpOnly cookies
- HTTPS wajib untuk production

⚠️ **CORS:**
Jika mendapat error CORS, pastikan backend memiliki:
```php
// config/cors.php
'allowed_origins' => ['http://localhost:3000', 'file://']
```

---

## 10. Troubleshooting

### Issue: 404 API Not Found
- Check bahwa backend running di `http://localhost:8000`
- Verify routes di `routes/api.php`

### Issue: 401 Unauthorized
- Token expired atau tidak valid
- Redirect user ke login
- Clear localStorage token

### Issue: CORS Error
- Add CORS middleware ke Laravel
- Check `config.js` API URL

### Issue: Product tidak muncul
- Check database sudah di-seed
- Verify `GET /products` response format
- Check console.error logs

---

## 11. Next Steps

Untuk production:
1. Setup environment variables (API URL)
2. Implement refresh token mechanism
3. Add form validation
4. Add loading states & spinners
5. Implement payment gateway integration (Midtrans)
6. Setup PWA (Progressive Web App)
7. Add error boundary dan error pages
8. Optimize images & assets
9. Add unit tests
10. Setup CI/CD pipeline

---

**Terakhir Updated**: 28 Desember 2025
**Status**: ✅ Semua integrasi selesai dan siap testing
