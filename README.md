# Penugasan Ansible

Penugasan Ansible untuk mendeploy backend dan frontend website.

## Daftar Isi

- [Objektif](#objektif)
- [Prepare](#prepare)
- [Build](#build)
- [Migrate](#migrate)
- [Deploy](#deploy)
- [Main](#main)
- [Dokumentasi](#dokumentasi)

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

1. **Role `backend-deploy`**: Role ini digunakan untuk mendeploy backend dari aplikasi. Tugas-tugasnya termasuk menyalin template konfigurasi backend ke direktori Nginx, membuat *symlink* ke konfigurasi yang aktif, dan mengonfigurasi situs backend.

2. **Role `frontend-deploy`**: Role ini digunakan untuk mendeploy frontend dari aplikasi. Tugas-tugasnya termasuk memulai frontend dengan PM2, menyalin template konfigurasi frontend ke direktori Nginx, membuat *symlink* ke konfigurasi yang aktif, dan mengonfigurasi situs frontend. Setelah kedua bagian aplikasi dideploy, dilakukan pengecekan dengan melakukan permintaan GET ke `localhost:3000` dan menampilkan responsnya.

## Main

Playbook `main.yml` adalah playbook utama yang mengatur alur kerja untuk deployment aplikasi. Playbook ini memanggil playbook lain dalam urutan yang telah ditentukan, memastikan bahwa seluruh proses deployment dilakukan secara teratur dan efisien.

### Langkah-langkah dalam Playbook:

1. **Prepare  (`prepare.yml`):** Playbook ini bertanggung jawab untuk melakukan persiapan awal, seperti instalasi dan konfigurasi Nginx sebagai web server dan MySQL sebagai database server.

2. **Build Application (`build.yml`):** Playbook `build.yml` melakukan pembangunan aplikasi. Ini termasuk tugas seperti kloning repositori kode sumber, instalasi dependensi, dan lain-lain.

3. **Migrate Database (`migrate.yml`):** Setelah aplikasi dibangun, playbook `migrate.yml` melakukan migrasi database.

4. **Deploy Application (`deploy.yml`):** Tahap terakhir adalah mendeploy menggunakan playbook `deploy.yml`. Ini melibatkan tugas seperti penyalinan file konfigurasi, pembuatan *symlink*, dan pengecekan bahwa aplikasi frontend berjalan dengan baik.

Dengan menjalankan playbook `main.yml`, seluruh proses deployment aplikasi dapat dilakukan dengan satu perintah, memastikan proses berjalan lancar dan efisien.

# Dokumentasi