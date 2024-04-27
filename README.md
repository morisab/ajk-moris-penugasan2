# Penugasan 2 AJK - Ansible

Penugasan 2 AJK, menggunakan Ansible untuk mendeploy backend dan frontend website. <br>
Dikerjakan oleh Mohammad Idris Arif Budiman (5025221114)

## Daftar Isi

- [Objektif](#objektif)
- [Prepare](#prepare)
- [Build](#build)
- [Migrate](#migrate)
- [Deploy](#deploy)
- [Main](#main)
- [Screenshot](#screenshot)

## Objektif

Penugasan ini bertujuan untuk mengotomatiskan proses deployment backend dan frontend website menggunakan Ansible. Dengan melakukan konfigurasi ini, proses deployment dapat dilakukan secara konsisten dan mudah diulang.

## Prepare

Playbook `prepare.yml` bertanggung jawab untuk melakukan persiapan yang diperlukan.<br>
Playbook ini terdiri dari dua roles utama:

1. **Role `nginx-prepare`** bertanggung jawab untuk melakukan persiapan yang diperlukan untuk menjalankan Nginx sebagai web server. Tugas-tugas spesifik yang dilakukan oleh role ini adalah:

   - **Menginstal Nginx**: Ini melibatkan pengunduhan dan instalasi paket Nginx dari repositori.
   - **Mengaktifkan UFW dan mengizinkan akses melalui port 80, 9000, 3000, dan 22**: UFW (Uncomplicated Firewall) adalah firewall yang mudah digunakan. Role ini mengaktifkan UFW dan mengatur aturan untuk mengizinkan lalu lintas melalui port yang diperlukan.
   - **Memulai layanan Nginx**: Setelah instalasi dan konfigurasi selesai, role ini memulai layanan Nginx sehingga web server mulai berjalan.

2. **Role `mysql-prepare`** bertanggung jawab untuk melakukan persiapan yang diperlukan untuk menjalankan MySQL. Tugas-tugas spesifik yang dilakukan oleh role ini adalah:

   - **Menginstal MySQL Server dan MySQL Client**: Ini melibatkan pengunduhan dan instalasi paket MySQL Server dan MySQL Client dari repositori.
   - **Membuat database dan pengguna MySQL**: Role ini membuat database dan pengguna baru di MySQL, dan memberikan hak akses yang sesuai kepada pengguna tersebut pada database.
   - **Mengkonfigurasi akses remote ke MySQL dengan mengubah file konfigurasi MySQL**: Untuk memungkinkan akses remote ke server MySQL, role ini mengubah file konfigurasi MySQL dan mengizinkan akses dari alamat IP atau jaringan tertentu.

## Build

Playbook `build.yml` bertanggung jawab untuk membangun frontend dan backend. <br>
Playbook ini terdiri dari dua roles utama:

1. **Role `backend-build`**: Role ini bertanggung jawab untuk membangun backend. Ini melibatkan penambahan repositori PHP, pembaruan cache apt, instalasi PHP dan paket terkait, penambahan direktori aman untuk Git, kloning repositori backend, perubahan izin dan kepemilikan direktori backend, pengunduhan dan instalasi Composer, instalasi dan pembaruan dependensi Composer, penyalinan dan konfigurasi file env, dan menjalankan composer dump-autoload.

2. **Role `frontend-build`**: Role ini bertanggung jawab untuk membangun frontend. Ini melibatkan instalasi curl, NVM, dan Yarn, pengaturan Node, instalasi npm, kloning repositori frontend, perubahan izin dan kepemilikan direktori frontend, penghapusan package-lock, penggantian URL dalam file config.ts, instalasi dependensi dengan Yarn, dan instalasi PM2.

## Migrate

Playbook `migrate.yml` ini memiliki satu role utama, yaitu **`backend-migrate`**. Melakukan beberapa tindakan yang diperlukan saat melakukan migrasi backend aplikasi. Ini termasuk menghasilkan kunci aplikasi menggunakan perintah php artisan key:generate, menjalankan migrasi basis data dengan perintah php artisan migrate, dan menghasilkan JWT secret menggunakan perintah php artisan jwt:secret. Semua perintah dijalankan di direktori backend yang ditentukan.

## Deploy

Playbook `deploy.yml` bertanggung jawab untuk mendeploy frontend dan backend dari aplikasi. Playbook ini terdiri dari dua bagian utama:

1. **Role `backend-deploy`**: Role ini digunakan untuk mendeploy backend dari aplikasi. Tugas-tugasnya termasuk menyalin template konfigurasi backend ke direktori Nginx, membuat _symlink_ ke konfigurasi yang aktif, dan mengonfigurasi situs backend.

2. **Role `frontend-deploy`**: Role ini digunakan untuk mendeploy frontend dari aplikasi. Tugas-tugasnya termasuk memulai frontend dengan PM2, menyalin template konfigurasi frontend ke direktori Nginx, membuat _symlink_ ke konfigurasi yang aktif, dan mengonfigurasi situs frontend. Setelah kedua bagian aplikasi dideploy, dilakukan pengecekan dengan melakukan permintaan GET ke `localhost:3000` dan menampilkan responsnya.

## Main

Playbook `main.yml` adalah playbook utama yang mengatur alur kerja untuk deployment aplikasi. Playbook ini memanggil playbook lain dalam urutan yang telah ditentukan, memastikan bahwa seluruh proses deployment dilakukan secara teratur dan efisien.

### Langkah-langkah dalam Playbook:

1. **Prepare (`prepare.yml`):** Playbook ini bertanggung jawab untuk melakukan persiapan awal, seperti instalasi dan konfigurasi Nginx sebagai web server dan MySQL sebagai database server.

2. **Build Application (`build.yml`):** Playbook `build.yml` melakukan pembangunan aplikasi. Ini termasuk tugas seperti kloning repositori kode sumber, instalasi dependensi, dan lain-lain.

3. **Migrate Database (`migrate.yml`):** Setelah aplikasi dibangun, playbook `migrate.yml` melakukan migrasi database.

4. **Deploy Application (`deploy.yml`):** Tahap terakhir adalah mendeploy menggunakan playbook `deploy.yml`. Ini melibatkan tugas seperti penyalinan file konfigurasi, pembuatan _symlink_, dan pengecekan bahwa aplikasi frontend berjalan dengan baik.

Dengan menjalankan playbook `main.yml`, seluruh proses deployment aplikasi dapat dilakukan dengan satu perintah, memastikan proses berjalan lancar dan efisien.

# Screenshot

## Tampilan backend

![backend](https://private-user-images.githubusercontent.com/115335252/326147321-126f8d4b-e60e-4ca2-9fc8-b9f26aebc03e.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODQ2MzUsIm5iZiI6MTcxNDE4NDMzNSwicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzIxLTEyNmY4ZDRiLWU2MGUtNGNhMi05ZmM4LWI5ZjI2YWViYzAzZS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjE4NTVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT01YWQwODkyNWU3MTQ1YTcyYWUxZTU5NTAwNDVmZGE5NTE3MWNhYjdjNjNlMzY1YWQ3MDM3Y2Y5MzQzODVjZTg5JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.kk1CZz_YP8lJkiUdeVV-mx7310PBpAx4Paq-w0N41Kk)

## Tampilan frontend

![frontend1](https://private-user-images.githubusercontent.com/115335252/326147328-1c2a661d-b41a-48e9-980d-59f7e7a762ea.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODQ2MzUsIm5iZiI6MTcxNDE4NDMzNSwicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzI4LTFjMmE2NjFkLWI0MWEtNDhlOS05ODBkLTU5ZjdlN2E3NjJlYS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjE4NTVaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT05NWIyMjcwNzU2N2NmOTczMTUzYzk3ZWQ5NjQ0NGI3YjU2MGY0OWFiMzQ0YzAxMTFkYmM5MWFmMTVjNThhMzJhJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.9B1a5L0KWXeYHHG38o5uWzszmBx7a6JUN15ILGrvGII)

### Register

![register1](https://private-user-images.githubusercontent.com/115335252/326147315-7b347999-038b-4449-acd6-639dd04ca1e3.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODUwMTMsIm5iZiI6MTcxNDE4NDcxMywicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzE1LTdiMzQ3OTk5LTAzOGItNDQ0OS1hY2Q2LTYzOWRkMDRjYTFlMy5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjI1MTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT0zZTVjOTM3YmZhNzhjMmY1MDNlMjYxZWFmYjE4ZTE4MmQ0N2Q0MGY0MTYyNjUwMGQ0NTkyYzBjMmQ2ZjE3YjMzJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.plKTqB_PR_5MP9yNCiFasUxE-YGkK5A2nwjdpAONND4)

![register2](https://private-user-images.githubusercontent.com/115335252/326147312-7e03d930-8b4f-4ffb-9aee-ec6ef93b4abd.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODUwMTMsIm5iZiI6MTcxNDE4NDcxMywicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzEyLTdlMDNkOTMwLThiNGYtNGZmYi05YWVlLWVjNmVmOTNiNGFiZC5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjI1MTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1hOTE4YmJjMWVkOGI3NWU2MGIyYzg1MThiNTNmNjAzYjA3ODNmMTQ3ZGNjZjVlYTQ1Mjc5Y2NhZjI5ZWNiY2YyJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.DTLAPl6-vfqPfIoDPsoTWapMAko_fRPWxs5zSpWiIMM)

### Login

![login1](https://private-user-images.githubusercontent.com/115335252/326147325-445db69d-bf26-4011-ba30-dfc0a47c5c63.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODUwMTMsIm5iZiI6MTcxNDE4NDcxMywicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzI1LTQ0NWRiNjlkLWJmMjYtNDAxMS1iYTMwLWRmYzBhNDdjNWM2My5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjI1MTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1jMzQxOWQyMDMzYzdmNmQxMGM1ZmM4Mjc3MTRjZmYxMTYyYmY4ZmJjZjU4MTNmOGM1OGI2MDE4Y2I3MGE4YTMyJlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.2ml71DGAomNLF21ShnbEI584PrFyyChCSgB3Ue3cJQY)

![login2](https://private-user-images.githubusercontent.com/115335252/326147323-3d7bd90b-b931-4db2-8054-1d88f403f3cb.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTQxODUwMTMsIm5iZiI6MTcxNDE4NDcxMywicGF0aCI6Ii8xMTUzMzUyNTIvMzI2MTQ3MzIzLTNkN2JkOTBiLWI5MzEtNGRiMi04MDU0LTFkODhmNDAzZjNjYi5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjQwNDI3JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI0MDQyN1QwMjI1MTNaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT02NTQzZGZhMTY5ODE5ZDAyOWNiZjA0N2U0MTFkYjRjNzg1ZjFiYjMzYjRhYTc1ZWRiZDVmZjM5YmQ4YTE0M2Q1JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCZhY3Rvcl9pZD0wJmtleV9pZD0wJnJlcG9faWQ9MCJ9.u-b4AzcsEuUIp_cgjxbml3K9x8GmpFabWqZtPNlMzoY)
