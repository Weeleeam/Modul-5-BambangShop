# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Pada kasus BambangShop ini, saya merasa sebuah trait Subscriber belum wajib. Di Observer pattern, interface dibutuhkan jika ada banyak tipe observer berbeda dengan perilaku update yang berbeda-beda. Sementara di kode ini, observer kita hanya satu bentuk data (name, url) dan satu cara update (HTTP POST ke endpoint subscriber). Jadi satu model struct Subscriber sudah cukup untuk kebutuhan saat ini.

2. Untuk id atau url yang harus unik, Vec saja kurang ideal karena uniquenya harus dicek manual dengan linear scan (O(n)) sebelum insert. Ini rawan duplikasi jika pengecekan terlewat dan makin tidak efisien saat data membesar. Struktur map seperti DashMap lebih cocok karena key memang merepresentasikan keunikan itu sendiri (url), sehingga operasi insert/get/delete rata-rata O(1). Jadi pendekatan map lebih tepat daripada list biasa.

3. Singleton pattern dan DashMap bukan pengganti langsung karena masalahnya berbeda. Singleton hanya mengatur jumlah instance global (satu instance), tetapi tidak otomatis thread-safe untuk operasi baca/tulis bersamaan. Pada Rust server yang menangani banyak request paralel, kita tetap butuh struktur data concurrent (seperti DashMap) agar aman dari race condition. Jadi saya tetap perlu DashMap walaupun repository-nya secara konsep sudah singleton lewat lazy_static.

#### Reflection Publisher-2
1. Menurut saya, pemisahan Service dan Repository dari Model diperlukan untuk menjaga separation of concerns. Model sebaiknya fokus pada representasi data dan aturan domain yang paling inti, Repository fokus pada akses data, sedangkan Service fokus pada alur business logic atau use case. Dengan pemisahan ini, kode jadi lebih mudah dirawat, dites, dan diubah.

2. Jika hanya menggunakan Model, interaksi antar model (Program, Subscriber, Notification) cenderung saling menarik tanggung jawab satu sama lain dan menghasilkan coupling tinggi. Program bisa jadi harus tahu detail penyimpanan subscriber, Subscriber bisa ikut mengatur alur publish, dan Notification bisa ikut memegang keputusan bisnis. Akibatnya kompleksitas meningkat dan perubahan kecil berpotensi memicu perubahan berantai di banyak tempat. Selain itu, testing menjadi lebih sulit karena setiap model membawa terlalu banyak dependensi.

3. Saya sudah mengeksplor Postman dan tool ini sangat membantu untuk menguji endpoint secara cepat tanpa membuat client manual. Dalam tugas ini, Postman membantu saya memverifikasi alur subscribe, unsubscribe, publish, dan memastikan response status/body sudah sesuai. Fitur Postman yang paling berguna untuk group project ke depan:
- Collection dan folder untuk mengelompokkan skenario API per fitur.
- Environment variables untuk ganti base URL/dev-produk tanpa ubah request satu per satu.
- Tests tab untuk assertion otomatis pada status code dan response body.
- Runner untuk menjalankan satu collection sebagai regression test sederhana.
- Export/import collection agar mudah kolaborasi antar anggota tim.

#### Reflection Publisher-3
1. Observer Pattern yang dipakai di tutorial ini adalah Push model. Publisher langsung mengirim data notifikasi ke subscriber melalui HTTP POST (product title, product type, status, dan URL produk sudah dikirim dari publisher). Jadi subscriber tidak perlu mengambil data dulu ke publisher untuk mengetahui event yang terjadi.

2. Jika dibayangkan memakai Pull model pada kasus ini, keuntungannya adalah payload dari publisher bisa lebih ringan, lalu subscriber bebas menentukan kapan dan data apa yang ingin diambil. Ini bisa mengurangi data berlebih pada notifikasi. Namun kekurangannya, jumlah request jaringan menjadi lebih banyak karena setelah menerima event subscriber masih harus memanggil API publisher lagi. Kompleksitas juga naik, jadi perlu endpoint tambahan untuk query detail, mekanisme retry/caching yang lebih baik, dan konsistensi data saat banyak subscriber melakukan pull bersamaan.

3. Jika proses notifikasi tidak memakai multi-threading, pengiriman akan berjalan sinkron satu per satu. Dampaknya, response endpoint publish/create/delete bisa menjadi lebih lambat karena harus menunggu semua request notifikasi selesai. Jika ada satu subscriber lambat atau timeout, seluruh alur bisa ikut tertahan.
