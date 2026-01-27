# 🔐 Setup Firestore Security Rules

## ⚠️ PENTING: Dashboard tidak bisa baca data game_results karena Firestore Rules

Dashboard gagal connect karena Firestore Rules di Firebase Console belum dikonfigurasi untuk mengizinkan pembacaan data.

---

## 📋 Langkah-Langkah Setup

### 1. Buka Firebase Console
- Pergi ke: https://console.firebase.google.com/
- Pilih project **physics-adventure**

### 2. Pergi ke Firestore Database
- Di sidebar kiri, pilih: **Firestore Database**
- Pilih tab: **Rules** (di sebelah "Data")

### 3. Copy-paste Rules Berikut
```firestore
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // ✅ Collection: game_results (Leaderboard Data)
    match /game_results/{document=**} {
      allow read: if true;  // Siapa saja bisa baca (untuk dashboard & leaderboard)
      allow write: if request.auth != null;  // Hanya user terauth yang bisa tulis
      allow delete: if false;  // Tidak boleh delete
    }

    // ✅ Collection: artifacts (User Data & Progress)
    match /artifacts/{appId}/users/{userId}/{rest=**} {
      allow read, write: if request.auth.uid == userId;  // User hanya bisa akses data mereka
    }

    match /artifacts/{appId}/public/{document=**} {
      allow read: if true;  // Public data bisa dibaca siapa saja
      allow write: if false;  // Public data tidak bisa diubah dari client
    }

    // Default: Deny semua akses yang tidak didefinisikan
    match /{document=**} {
      allow read, write: if false;
    }
  }
}
```

### 4. Klik Tombol "Publish"
- Tunggu sampai ada pesan: ✅ "Rules deployed successfully"

### 5. Test Koneksi
- Buka: http://localhost:8080/dashboard.html
- Seharusnya sekarang sudah bisa terhubung dan menampilkan data

---

## 🧪 Testing Checklist

- [ ] Dashboard tidak menampilkan error "Gagal terhubung ke database"
- [ ] Stats (Total Pemain, Top Score, Rata-rata Skor) menampilkan nilai
- [ ] Leaderboard Top 10 menampilkan data pemain
- [ ] Permainan Terbaru menampilkan history game
- [ ] Browser console tidak ada error Firebase

---

## ❓ Troubleshooting

### Masalah: "Gagal terhubung ke database"
**Solusi:**
1. Refresh halaman (Ctrl+F5 untuk hard refresh)
2. Buka browser DevTools (F12) → Console
3. Lihat error message di console
4. Pastikan rules sudah di-publish

### Masalah: "Permission denied" error
**Solusi:**
- Pastikan Anda copy-paste rules dengan benar
- Pastikan tidak ada typo di collection name
- Firestore rules case-sensitive

### Masalah: Data tidak muncul meski tidak ada error
**Solusi:**
1. Pastikan ada data di collection `game_results`:
   - Buka Firebase Console → Firestore Database → Data
   - Lihat apakah ada documents di collection `game_results`
2. Jika tidak ada data:
   - Main game terlebih dahulu sampai selesai
   - Skor akan otomatis tersimpan ke `game_results`
3. Tunggu beberapa detik dan refresh dashboard

---

## 📚 Dokumentasi Firestore Rules
- https://firebase.google.com/docs/firestore/security/get-started
- https://firebase.google.com/docs/firestore/security/rules-conditions

---

**Status:** 📌 Setup diperlukan untuk dashboard berfungsi
**Created:** 2025-01-25
