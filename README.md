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
1. In the Observer design pattern, the Subscriber is often defined as an interface to allow flexibility in implementation. However, in this case, we are dealing with a fairly fixed structure where the behavior of a subscriber is straightforward. Therefore, a single model struct is sufficient to represent a subscriber. The trait would only be necessary if we expect different types of subscribers with varied behavior, but currently, one implementation is enough.

2. Using Vec would not be sufficient in this case because we need fast lookups, insertions, and deletions based on unique keys (url). Vec does not provide efficient key-based access and would require iteration to find an element. On the other hand, DashMap provides thread-safe concurrent access with efficient key-based operations, which is very suitable for a notification system that may handle multiple subscribers in a real-time environment.

3. DashMap is used inside a lazy_static! block, which serves as an implementation of the Singleton pattern in Rust. The SUBSCRIBERS variable becomes a globally accessible instance that is initialized only once — which matches the principles of the Singleton design pattern. However, we still need DashMap specifically because it provides thread-safe concurrent access, which the Singleton pattern alone does not guarantee. In summary, DashMap and the Singleton pattern serve different but complementary purposes and both are needed to ensure safety and global accessibility in this context.

#### Reflection Publisher-2
1. In the traditional MVC pattern, the Model often handles both data access and business logic. However, separating the Service and Repository layers brings better modularity, testability, and maintainability. The Repository is responsible solely for data access—how data is fetched, inserted, or deleted. The Service contains the business logic (how that data should be processed or validated). Keeping these layers separate adheres to the Single Responsibility Principle, making our system easier to debug and extend in the future. If logic lives inside models, they quickly become bloated and hard to manage.

2. If we only rely on the Model layer to handle everything—data access, business logic, and interactions between entities like Program, Subscriber, and Notification—the system will quickly become highly coupled and difficult to maintain. Some consequences include poor separation of concerns, code duplication, and harder testing.

3. Yes, I've explored Postman and found it to be a really helpful tool for testing HTTP endpoints quickly and effectively. In this project (based on the given example), I used Postman to test the new subscribe feature of the Notification system. Some features I want to explore more are collections, JSON body formatting, as well as status and time tracking.

#### Reflection Publisher-3
1. In this tutorial, we use the Push model of the Observer Pattern. The publisher (ProductService) sends notification data directly to all subscribers via an HTTP POST request. The subscribers don't ask or request for updates—they simply receive data when the publisher decides to send it.

2. If we used the Pull model, each subscriber would regularly check (poll) the publisher for updates, instead of receiving them directly. One of the advantages of it is less load on the publisher since it doesn't need to push updates. Subscribers can also decide when and how often to fetch updates. But the drawbacks of this is that we will need a more complex subscriber logic and increased network traffic will occur from constant polling, especially if there are many subscribers.

3. If we removed multithreading, then every subscriber update would block the main server thread. That means, if one subscriber is slow or unresponsive, the entire server would be delayed. Requests to publish or create products would also become much slower and feel laggy.