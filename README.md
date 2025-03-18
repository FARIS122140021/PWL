🏰 DisneyTech Evolution: Migrating from Monolithic to Microservices
🎬 Studi Kasus: The Walt Disney Company
URL: The Walt Disney Company

🎯 Overview
Dalam proyek ini, kita akan melakukan migrasi dari arsitektur monolitik ke microservices berdasarkan studi kasus The Walt Disney Company. Disney mengelola banyak layanan digital, termasuk streaming (Disney+), tiket taman hiburan, merchandise online, dan lainnya. Awalnya, layanan-layanan ini bisa jadi dikembangkan dalam sistem monolitik sebelum mereka beralih ke microservices untuk meningkatkan skalabilitas dan fleksibilitas sistem mereka.

🏗️ Struktur Repository
pgsql
Salin
Edit
disney-microservices/
├── api-gateway/        → Mengelola request ke layanan terkait
├── auth-service/       → Autentikasi pengguna Disney+ & layanan lainnya
├── content-service/    → Manajemen konten streaming & katalog film
├── payment-service/    → Pemrosesan pembayaran langganan & tiket
├── park-service/       → Manajemen reservasi taman hiburan Disney
├── merchandise-service/ → Pengelolaan penjualan merchandise online
├── database-scripts/   → Skrip SQL untuk layanan terpisah
└── README.md           → Dokumentasi proyek
📌 BAB 2 - Planning a Migration
Langkah-langkah perencanaan migrasi:
✅ Identifikasi domain bisnis: Memisahkan layanan berdasarkan kebutuhan spesifik (streaming, tiket, merchandise, dll.).
✅ Menentukan prioritas pemisahan: Memulai dengan layanan yang memiliki traffic tinggi seperti Disney+ dan reservasi tiket.
✅ Membangun API Gateway: Menyediakan satu entry point untuk semua layanan.
✅ Memastikan keamanan & autentikasi: Menggunakan JWT dan OAuth untuk keamanan antar layanan.

🔨 BAB 3 - Splitting The Monolith
Perbandingan struktur API sebelum dan sesudah migrasi:

Sebelumnya (Monolitik)

php
Salin
Edit
Route::post('/register', 'AuthController@register');
Route::get('/movies', 'ContentController@index');
Route::post('/subscribe', 'PaymentController@process');
Route::get('/park/reservations', 'ParkController@show');
Route::post('/merch/order', 'MerchandiseController@order');
Setelah migrasi ke Microservices (API Gateway)

js
Salin
Edit
const express = require('express');
const app = express();
const axios = require('axios');

app.use('/auth', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/content', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/payment', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/park', (req, res) => axios(req).then(response => res.send(response.data)));
app.use('/merch', (req, res) => axios(req).then(response => res.send(response.data)));

app.listen(3000, () => console.log('API Gateway running on port 3000'));
🗄️ BAB 4 - Decomposing The Database
Sebelumnya (Monolitik - Satu Database untuk Semua Layanan):

sql
Salin
Edit
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    password VARCHAR(255)
);

CREATE TABLE subscriptions (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    plan VARCHAR(50),
    status VARCHAR(50)
);
Setelah Migrasi ke Microservices (Database Terpisah per Layanan):

Auth Service Database (Menyimpan data pengguna Disney+)
sql
Salin
Edit
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    password VARCHAR(255)
);
Content Service Database (Manajemen katalog film & serial)
sql
Salin
Edit
CREATE TABLE movies (
    id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255),
    genre VARCHAR(100),
    release_date DATE
);
Payment Service Database (Transaksi langganan Disney+ & reservasi tiket)
sql
Salin
Edit
CREATE TABLE payments (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    amount DECIMAL(10,2),
    status VARCHAR(50)
);
Park Service Database (Data reservasi taman hiburan)
sql
Salin
Edit
CREATE TABLE reservations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT,
    park_id INT,
    date DATE,
    status VARCHAR(50)
);
🚀 Deployment & Scalability
Sebelumnya (Monolitik)
⛔ Semua layanan berjalan dalam satu server besar.
⛔ Jika ada crash, seluruh sistem ikut terdampak.

Sekarang (Microservices)
✅ Masing-masing layanan bisa dideploy secara independen.
✅ Menggunakan Docker untuk menjalankan layanan secara terpisah.
✅ Jika satu layanan gagal, layanan lain tetap berjalan.

Contoh Docker Compose untuk Microservices

yaml
Salin
Edit
version: '3'
services:
  api-gateway:
    build: ./api-gateway
    ports:
      - "3000:3000"
  auth-service:
    build: ./auth-service
    ports:
      - "4000:4000"
  content-service:
    build: ./content-service
    ports:
      - "5000:5000"
  payment-service:
    build: ./payment-service
    ports:
      - "6000:6000"
  park-service:
    build: ./park-service
    ports:
      - "7000:7000"
  merchandise-service:
    build: ./merchandise-service
    ports:
      - "8000:8000"
🎭 Kesimpulan
Dengan migrasi ini, sistem The Walt Disney Company kini lebih modular, fleksibel, dan mudah di-scale. Setiap layanan dapat berkembang secara independen tanpa harus mengganggu sistem lain. Ini menjadikan pengalaman pengguna lebih baik dalam layanan seperti Disney+, pemesanan tiket taman hiburan, hingga pembelian merchandise online.
