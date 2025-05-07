# Absensi-PT-Abdi-Putra-Pratama
Absensi Untuk Karyawan PT Abdi Putra Pratama

Website ini adalah aplikasi absensi sederhana berbasis PHP dan MySQL. Cocok digunakan untuk keperluan sekolah, kantor, atau organisasi kecil.

---

## 🎯 Fitur
- Login pengguna dan admin
- Absen masuk dengan pencatatan waktu
- Halaman admin untuk melihat seluruh data absensi

---

## 🗂️ Struktur File

```
absensi/
├── admin.php            # Halaman admin
├── index.php            # Form absensi (user)
├── koneksi.php          # Koneksi ke database
├── login.php            # Form login
├── logout.php           # Logout session
├── proses_login.php     # Proses autentikasi
├── simpan_absen.php     # Simpan data absensi
```

---

## ⚙️ Instalasi Lokal

### 1. Clone Repository
```bash
git clone https://github.com/username/website-absensi.git
cd website-absensi
```

### 2. Jalankan XAMPP / Laragon
- Tempatkan folder ini di direktori `htdocs/` (untuk XAMPP)
- Jalankan Apache dan MySQL

### 3. Buat Database di phpMyAdmin
```sql
CREATE DATABASE absensi;

USE absensi;

CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  username VARCHAR(50),
  password VARCHAR(255),
  role ENUM('admin', 'user')
);

CREATE TABLE absensi (
  id INT AUTO_INCREMENT PRIMARY KEY,
  user_id INT,
  nama VARCHAR(100),
  waktu_masuk DATETIME,
  waktu_pulang DATETIME,
  FOREIGN KEY (user_id) REFERENCES users(id)
);
```

### 4. Tambahkan User Manual
```sql
INSERT INTO users (username, password, role) VALUES ('admin', 'admin123', 'admin');
```

### 5. Akses Website
Buka browser dan kunjungi:
```
http://localhost/website-absensi/login.php
```

---

## 🚀 Deployment (Opsional)
Jika ingin di-hosting secara online:

### Opsi 1: Upload ke Hosting Gratis
- Gunakan [000webhost.com](https://000webhost.com) atau [InfinityFree.net](https://infinityfree.net)
- Upload file ZIP, ekstrak, dan sesuaikan koneksi database

### Opsi 2: Deploy dari GitHub
- Gunakan platform seperti [Render](https://render.com) atau [Railway](https://railway.app)
- Hubungkan repository ini
- Siapkan environment PHP dan koneksi database online

---

## 📄 Lisensi
Proyek ini open-source, bebas digunakan dan dimodifikasi untuk keperluan pribadi atau organisasi.

---

## 🤝 Kontribusi
Pull request sangat dipersilakan. Untuk perubahan besar, buka issue terlebih dahulu.

---

Jika kamu butuh bantuan tambahan (export Excel, fitur absen pulang, dsb.), silakan buka issue atau hubungi developer.
