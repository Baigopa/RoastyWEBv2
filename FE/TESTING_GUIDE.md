# 🧪 Quick Testing Guide - Roasty Frontend

## 📋 Pre-Testing Checklist
- [ ] Backend Laravel sudah running di http://localhost:8000
- [ ] Database sudah diisi dengan products
- [ ] API endpoints sudah accessible
- [ ] Semua file HTML sudah ada di folder FE
- [ ] config.js sudah ter-update dengan API_BASE_URL yang benar

---

## 🚀 Testing Flow

### Phase 1: Authentication (5 menit)
```
1. Buka login.html di browser
2. Masukkan credentials (username/password dari backend)
3. Klik "Masuk" button
4. ✅ Expected: Redirect ke Halaman.beranda.html
5. ✅ Expected: Token tersimpan di localStorage
6. ✅ Expected: User info tersimpan di localStorage
```

**Check in Console:**
```javascript
localStorage.getItem('token')          // Should return token string
localStorage.getItem('user')           // Should return user JSON
```

---

### Phase 2: Home Page Navigation (8 menit)

#### Logo Button
```
✅ Click logo → Tetap di Halaman.beranda.html (atau refresh)
```

#### Search Functionality
```
1. Type "grinder" di search box
2. Press Enter atau click search button
3. ✅ Expected: Redirect ke halaman.daftar.produk.html
4. ✅ Expected: Only "grinder" products shown
5. ✅ Expected: Search query di console: 
       sessionStorage.getItem('searchQuery')  // Should return "grinder"
```

#### Category Navigation
```
1. Click "Gadget Kopi" category link
2. ✅ Expected: Redirect ke halaman.daftar.produk.html
3. ✅ Expected: Only "Gadget Kopi" products shown
4. ✅ Check sessionStorage:
       sessionStorage.getItem('selectedCategory')  // Should return "Gadget Kopi"
```

#### Category Pills
```
1. Click "Mesin Kopi" pill
2. ✅ Expected: Redirect ke halaman.daftar.produk.html
3. ✅ Expected: Only "Mesin Kopi" products shown
```

#### Promo Banner
```
1. Click "Cek Sekarang" button di promo banner
2. ✅ Expected: Redirect ke halaman.daftar.produk.html
```

#### Header Buttons
```
1. Click Cart icon
   ✅ Expected: Redirect ke halaman.keranjang.belanja.html
   
2. Click Notifications icon
   ✅ Expected: Alert "Anda tidak memiliki notifikasi baru"
   
3. Click Mail icon
   ✅ Expected: Alert "Tidak ada pesan baru"
   
4. Click Profile (user avatar area)
   ✅ Expected: Redirect ke halaman.profil.html
```

#### Product Cards
```
1. Click product card di "Kejar Diskon" section
   ✅ Expected: Redirect ke halaman.detail.produk.html
   ✅ Expected: sessionStorage.getItem('selectedProductId') = product ID
   
2. Click product card di "Rekomendasi Untukmu" section
   ✅ Expected: Same behavior as above
```

#### Add to Cart (Recommendation)
```
1. Hover over product card di "Rekomendasi"
2. Click add to cart button (icon)
3. ✅ Expected: Alert "Produk ditambahkan ke keranjang!"
4. ✅ Expected: localStorage.getItem('cart') contains item
```

---

### Phase 3: Product List Page (8 menit)

#### Header Buttons
```
Same as Halaman.beranda.html
```

#### Search Functionality
```
1. Type "v60" di search box
2. Press Enter
3. ✅ Expected: Products filtered to show only "v60" items
4. ✅ Verify search query persisted
```

#### Category Filter (Sidebar)
```
1. Click "Grinder" di sidebar
2. ✅ Expected: Only "Grinder" products shown
3. ✅ Expected: No page reload (instant filter)
```

#### Product Cards Click
```
1. Click any product card
2. ✅ Expected: Redirect ke halaman.detail.produk.html
3. ✅ Expected: Product details loaded correctly
```

#### Add to Cart
```
1. Click "Tambah ke Keranjang" button
2. ✅ Expected: Alert "Produk berhasil ditambahkan ke keranjang!"
3. ✅ Expected: localStorage.cart updated
4. ✅ Expected: Click again → quantity incremented
```

---

### Phase 4: Product Detail Page (5 menit)

#### Product Information
```
1. Verify product name, price, description displayed
2. ✅ Expected: Data fetched from API (/products/{id})
3. ✅ Expected: Price formatted as "Rp X.XXX.XXX"
```

#### Add to Cart Button
```
1. Click "Tambah ke Keranjang" button
2. ✅ Expected: Alert shown
3. ✅ Expected: Redirect ke halaman.keranjang.belanja.html
4. ✅ Expected: Cart contains the product
```

#### Navigation
```
1. Click logo → Halaman.beranda.html
2. Click cart icon → halaman.keranjang.belanja.html
3. Click profile → halaman.profil.html
```

---

### Phase 5: Shopping Cart (5 menit)

#### Cart Display
```
1. Verify all added products shown
2. ✅ Expected: Correct product names and prices
3. ✅ Expected: Total calculated correctly
4. ✅ Verify data from localStorage.cart
```

#### Empty Cart Check
```
1. Clear localStorage.cart
2. Refresh halaman.keranjang.belanja.html
3. ✅ Expected: "Keranjang kosong" message
4. ✅ Expected: "Lanjutkan Belanja" button shows
5. ✅ Click "Lanjutkan Belanja" → halaman.daftar.produk.html
```

#### Add More Items
```
1. Click "Lanjutkan Belanja"
2. Add more products
3. Return to keranjang
4. ✅ Expected: All items accumulated
5. ✅ Expected: Total updated
```

#### Checkout Button
```
1. Click "Lanjutkan ke Pembayaran"
2. ✅ Expected: Redirect ke halaman.pembayaran.html
3. ✅ Expected: All cart items shown
```

---

### Phase 6: Payment/Checkout (5 menit)

#### Payment Data
```
1. Verify user info loaded (dari /me endpoint)
2. Verify cart items shown
3. Verify total calculated
4. ✅ Expected: All data correct
```

#### Create Order
```
1. Click "Lakukan Pembayaran" button
2. ✅ Expected: POST /orders called
3. ✅ Expected: Order created successfully
4. ✅ Expected: localStorage.cart cleared
5. ✅ Expected: Alert "Pesanan berhasil dibuat!"
6. ✅ Expected: Redirect ke Halaman.beranda.html
```

#### Network Check
```
Open DevTools → Network tab
- POST /orders should show status 201 or 200
- Response should contain order ID and details
```

---

### Phase 7: Profile Page (5 menit)

#### Load Profile
```
1. Navigate to halaman.profil.html
2. ✅ Expected: GET /profile called
3. ✅ Expected: Form fields populated with current user data
4. ✅ Fields: first_name, last_name, birth_date, gender, email, phone
```

#### Edit Profile
```
1. Change any field (e.g., first_name)
2. Click "Simpan Perubahan"
3. ✅ Expected: PUT /profile called
4. ✅ Expected: Success message shown
5. ✅ Expected: Data updated in page
```

#### Navigation
```
1. Click logo/cart/profile buttons
2. ✅ Expected: Navigate correctly
```

---

## 🐛 Debugging Guide

### If Buttons Not Working:

#### 1. Check Console Errors
```javascript
// Open DevTools Console (F12)
// Look for JavaScript errors

// Check if config.js loaded
console.log(CONFIG.API_BASE_URL)

// Check localStorage
console.log(localStorage.getItem('token'))

// Check sessionStorage
console.log(sessionStorage.getItem('selectedProductId'))
```

#### 2. Check Network Requests
```
DevTools → Network tab
- Check if API calls being made
- Verify response status (200, 201, etc)
- Check response body for errors
```

#### 3. Check Selectors
```javascript
// In DevTools Console, test selectors:
document.querySelector('div.flex.items-center.gap-6')  // Should return element
document.querySelector('header a.flex')                // Logo button
document.querySelector('button.bg-primary')            // Search button
```

#### 4. Verify Button Elements Exist
```javascript
// Check specific button
const cartBtn = document.querySelector('div.flex.items-center.gap-6 button');
console.log(cartBtn);  // Should log button element, not null

// Check all buttons in container
const headerActions = document.querySelector('div.flex.items-center.gap-6');
const buttons = headerActions?.querySelectorAll('button');
console.log(buttons.length);  // Should be >= 3
```

#### 5. Test Manual Navigation
```javascript
// If button not responding, test redirect manually:
window.location.href = 'halaman.detail.produk.html';

// Or set session data manually:
sessionStorage.setItem('selectedProductId', '1');
window.location.href = 'halaman.detail.produk.html';
```

---

## 📊 Expected Test Results

### ✅ Pass Criteria:
- [ ] All 7 pages load without JS errors
- [ ] All navigation buttons work correctly
- [ ] Search functionality filters products
- [ ] Category filtering works
- [ ] Add to cart updates localStorage
- [ ] Checkout creates order via API
- [ ] Profile loads and updates
- [ ] Authentication persists across pages
- [ ] Empty cart handled gracefully
- [ ] Total calculations correct

### ⚠️ Known Limitations:
- Payment gateway (Midtrans) integration not implemented
- Product image URLs from API (not local)
- No real-time inventory updates
- No order status tracking

---

## 🔍 Console Commands for Testing

```javascript
// 1. Simulate logout
localStorage.removeItem('token');
localStorage.removeItem('user');
window.location.href = 'login.html';

// 2. Check cart contents
JSON.parse(localStorage.getItem('cart'));

// 3. Add product to cart programmatically
const cart = JSON.parse(localStorage.getItem('cart') || '[]');
cart.push({id: 1, quantity: 2});
localStorage.setItem('cart', JSON.stringify(cart));

// 4. Set session product
sessionStorage.setItem('selectedProductId', '1');

// 5. View all session data
Object.keys(sessionStorage).forEach(key => {
    console.log(`${key}: ${sessionStorage.getItem(key)}`);
});

// 6. Clear all session data
sessionStorage.clear();

// 7. Test API call
fetch('http://localhost:8000/api/products', {
    headers: {
        'Authorization': `Bearer ${localStorage.getItem('token')}`
    }
}).then(r => r.json()).then(d => console.log(d));

// 8. List all event listeners attached
// Note: Requires Chrome extension or developer tools
```

---

## 📱 Responsive Testing

### Desktop (1920px)
```
✅ All buttons visible
✅ Full layout working
✅ Navigation clear
```

### Tablet (768px)
```
✅ Mobile search visible
✅ Menu adjusted
✅ Layout responsive
```

### Mobile (375px)
```
✅ Mobile search functionality
✅ Buttons accessible
✅ Touch-friendly sizes
```

---

## 🎯 Quick Test Checklist (10 minutes)

1. **Login** (1 min)
   - [ ] Login works → Halaman.beranda.html

2. **Search** (1 min)
   - [ ] Search works → filters shown

3. **Category** (1 min)
   - [ ] Category click → filters applied

4. **Product Detail** (2 min)
   - [ ] Click product → loads details
   - [ ] Add to cart → localStorage updated

5. **Cart** (2 min)
   - [ ] Items shown correctly
   - [ ] Checkout button visible

6. **Checkout** (2 min)
   - [ ] Payment page loads
   - [ ] Create order works
   - [ ] Redirect to home

7. **Profile** (1 min)
   - [ ] Profile loads
   - [ ] Can edit and save

---

## 📞 Troubleshooting Contacts

If testing fails, check:
1. Backend API running? `http://localhost:8000/api`
2. Database has products? Check `/products` endpoint
3. config.js correct? `API_BASE_URL` value
4. Browser cache cleared? Ctrl+Shift+Del
5. Console errors? Check all tabs in DevTools
6. Token valid? Check `/me` endpoint with token

---

**Status:** Ready for Testing  
**Date:** 2025
