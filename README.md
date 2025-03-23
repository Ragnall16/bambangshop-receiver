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
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create SubscriberRequest model struct.`
    -   [x] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [x] Commit: `Implement add function in Notification repository.`
    -   [x] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Commit: `Implement receive_notification function in Notification service.`
    -   [x] Commit: `Implement receive function in Notification controller.`
    -   [x] Commit: `Implement list_messages function in Notification service.`
    -   [x] Commit: `Implement list function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1
1. In this tutorial, we use `RwLock<Vec<Notification>>` to synchronize access to the list of notifications. The reason `RwLock` is necessary is that multiple parts of the program might need to read or modify notifications concurrently. Using `RwLock` allows multiple readers to access the notification list simultaneously while ensuring that only one writer can modify it at a time. If we used a `Mutex<Vec<Notification>>` instead, it would block all access to the vector whenever a thread locks it, even for read operations. This would create unnecessary contention because reading notifications does not require exclusive access—only writes do. With `RwLock`, we allow multiple threads to read at the same time, improving performance when multiple subscribers or components access notifications. However, when a write operation occurs, `RwLock` ensures that only one thread can modify the data at a time, preventing race conditions.

2. In Java, static variables are shared across all instances of a class, and they can be modified freely using static methods. However, Rust enforces stricter safety rules to prevent data races and unsafe memory access. Unlike Java, Rust ensures that static variables must be immutable or synchronized to prevent multiple threads from modifying them unpredictably. In this tutorial, we use `lazy_static!` to define `NOTIFICATIONS` as a `RwLock<Vec<Notification>>`, ensuring that modifications are thread-safe. Without this, Rust would not allow direct mutation of a static variable because it cannot guarantee that multiple threads won’t modify it at the same time, leading to undefined behavior. This strict memory safety model is what makes Rust safe for concurrent programming without the need for a garbage collector.

#### Reflection Subscriber-2
1. In this tutorial, I followed the provided steps and did not explore additional parts of the code, such as `src/lib.rs`. The main reason for this is that the tutorial already provides a structured approach to implementing the necessary functionality, covering models, repositories, services, and controllers. Since the instructions clearly outline what needs to be implemented, I focused on correctly following them to ensure that my implementation was accurate and functional. Additionally, modifying files outside the tutorial scope could introduce unexpected issues that might affect the stability of the application. However, if I needed a deeper understanding of the overall project structure or encountered specific challenges, I would consider exploring `src/lib.rs` and other files to gain additional insights into how the application is designed.

2. The Observer pattern simplifies adding new subscribers because the publisher (Main app) does not need to be aware of each individual subscriber in advance. Subscribers can dynamically subscribe to notifications for specific product types, and the publisher simply pushes updates whenever an event occurs. This means new subscribers can be added at any time without modifying the publisher’s code, making the system highly scalable.<br> However, adding multiple instances of the Main app introduces additional complexity. Each Main app instance would need a way to coordinate subscriptions to avoid duplicate or inconsistent notifications. If multiple instances operate independently without shared state management, different subscribers might receive different updates, leading to inconsistencies. A potential solution to this issue would be to use a shared database or message broker to synchronize notification events across all instances of the Main app. This would ensure that all subscribers receive consistent updates regardless of which Main app instance handles the notification.

3. I have modified the given Postman collection to include multiple product types for the Create New Product request. This allowed me to test the notification system across three different ports (`8001`, `8002`, and `8003`) to ensure that each subscriber instance received notifications only for the correct product type. By setting up different product types and running multiple instances of the receiver, I was able to confirm that each notification was correctly sent to the intended subscriber. This modification helped in verifying that the Observer pattern implementation was correctly filtering and dispatching notifications. Enhancing the Postman collection in this way was useful because it provided a more thorough way to test real-world scenarios where multiple subscribers exist.