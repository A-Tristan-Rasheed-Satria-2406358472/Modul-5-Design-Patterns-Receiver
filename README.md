# BambangShop Receiver App

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
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment

1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable | type | description |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT | string | Port number that will be listened by this receiver instance. |
    | APP_INSTANCE_ROOT_URL | string | URL address where this receiver instance can be accessed. |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed. |
    | APP_INSTANCE_NAME | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    - Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    - Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    - Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)

- [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
- **STAGE 1: Implement models and repositories**
  - [x] Commit: `Create Notification model struct.`
  - [x] Commit: `Create SubscriberRequest model struct.`
  - [x] Commit: `Create Notification database and Notification repository struct skeleton.`
  - [x] Commit: `Implement add function in Notification repository.`
  - [x] Commit: `Implement list_all_as_string function in Notification repository.`
  - [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
- **STAGE 3: Implement services and controllers**
  - [x] Commit: `Create Notification service struct skeleton.`
  - [x] Commit: `Implement subscribe function in Notification service.`
  - [x] Commit: `Implement subscribe function in Notification controller.`
  - [x] Commit: `Implement unsubscribe function in Notification service.`
  - [x] Commit: `Implement unsubscribe function in Notification controller.`
  - [x] Commit: `Implement receive_notification function in Notification service.`
  - [x] Commit: `Implement receive function in Notification controller.`
  - [x] Commit: `Implement list_messages function in Notification service.`
  - [x] Commit: `Implement list function in Notification controller.`
  - [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections

This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. RwLock itu perlu karena daftar notification dipakai bareng bareng oleh banyak request. Ada request yang cuma baca isi notification, ada juga yang nambah notification baru. Kalau data seperti Vec dipakai dari banyak thread tanpa proteksi, bisa muncul data race dan isi datanya jadi tidak aman. RwLock cocok karena dia ngasih akses baca ke banyak thread sekaligus selama tidak ada yang nulis. Jadi waktu ada beberapa request yang cuma mau lihat daftar notification, semuanya masih bisa jalan bareng tanpa saling nunggu. Kalau pakai Mutex sebenarnya bisa juga buat bikin aksesnya aman, tapi jadinya terlalu ketat. Saat satu thread lagi baca, thread lain yang juga cuma mau baca tetap harus nunggu. Padahal baca data itu tidak mengubah isi Vec. Karena di kasus ini operasi baca bisa terjadi lebih sering daripada operasi tulis, RwLock lebih pas dan lebih efisien. Jadi intinya, kita butuh sinkronisasi karena datanya dipakai bersama, dan RwLock dipilih karena dia lebih enak buat skenario yang pembacaannya banyak.

2. Rust tidak membiarkan kita sembarangan mengubah isi static variable seperti di Java karena Rust sangat ketat soal ownership dan keamanan memory. Global state yang bisa diubah dari mana mana itu rawan banget bikin data race, apalagi kalau programnya berjalan di banyak thread. Rust maunya hal seperti ini kelihatan jelas dan aman dari awal, jadi kalau memang ada data global yang isinya mau diubah, datanya harus dibungkus dulu dalam mekanisme yang aman seperti RwLock atau DashMap. lazy_static dipakai karena isi data seperti Vec dan DashMap itu tidak bisa langsung dibuat sebagai static biasa, soalnya inisialisasinya butuh proses saat runtime. Selain itu, Rust juga tidak mau ngasih akses mutasi langsung ke static biasa karena itu bisa melanggar aturan borrowing yang jadi dasar keamanan Rust. Di Java, mutasi static variable lebih longgar karena pengamanannya banyak dicek saat runtime. Sementara di Rust, aturan amannya dipaksa dari compile time, jadi cara yang dibolehkan itu bukan langsung ubah static variable, tapi simpan data global dalam wadah yang thread safe lalu akses lewat API yang aman.

#### Reflection Subscriber-2

1. Saya melihat bagian lain seperti src/lib.rs, bukan hanya file yang disebut di tutorial. Dari sana saya jadi paham bagaimana APP_CONFIG dan REQWEST_CLIENT disiapkan sebagai global state yang dipakai lintas modul. Saya juga belajar bahwa konfigurasi aplikasi diambil dari environment variable lewat Figment, jadi setiap instance Receiver bisa punya port, root URL, dan nama yang berbeda tanpa perlu mengubah kode programnya.

2. Observer pattern membuat penambahan subscriber jadi mudah karena Publisher tidak perlu tahu detail implementasi tiap Receiver. Publisher hanya menyimpan daftar subscriber berdasarkan product type, lalu saat ada event seperti create, publish, atau delete, Publisher tinggal mengirim notifikasi ke semua subscriber yang relevan. Menambah lebih banyak instance Receiver juga tetap mudah karena cukup jalankan instance baru lalu subscribe ke topic yang diinginkan. Kalau yang ditambah lebih dari satu instance Main app, sistem jadi tidak sesederhana itu, karena tiap Main app akan punya state produk dan subscriber sendiri-sendiri. Artinya perlu sinkronisasi tambahan kalau ingin semua Main app terlihat sebagai satu sistem yang konsisten.

3. Saya sempat memakai dan menambah pengujian lewat Postman collection untuk memastikan flow subscribe, create product, dan list notification berjalan sesuai yang di mau. Menurut saya fitur ini sangat berguna karena testing API jadi lebih cepat, terstruktur, dan mudah diulang. Untuk tugas tutorial maupun Group Project, Postman membantu mengecek endpoint tanpa harus selalu membuat UI atau script manual terlebih dahulu.
