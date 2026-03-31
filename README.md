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
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or `trait` in Rust) in this BambangShop case, or a single Model `struct` is enough?

Since we implement the Observer Design Pattern, it's better to define `Subscriber` as an interface (or `trait`). We are developing an online shop, thus it is possible that we want to add another `Subscriber` (maybe Email, SMS, etc.) that would receive the notification. Furthermore, by making `Subscriber` an interface, we keep the Open-Closed Principle in SOLID so the maintenance will be easier. Additionally, using a trait also increases flexibility because each `Subscriber` can have its own implementation without changing the existing code. This makes the system more scalable as new notification methods can be added without modifying the core logic.


2. `id` in `Program` and `url` in `Subscriber` is intended to be unique. Explain based on your understanding, is using `Vec` (list) sufficient or using `DashMap` (map/dictionary) like we currently use is necessary for this case? 

In my opinion, using `DashMap` maximizes the object structure. Since `Program` and `Subscriber` have unique attributes, we can make use of them as keys. Using `DashMap` also minimizes the searching complexity from O(N) (if we use `Vec`) to O(1). This way, our program can handle many `Subscribers` and `Program` without getting heavy as the number of objects increases. Moreover, using a map structure also helps prevent duplicate entries because each key must be unique. This ensures data consistency, which is important since both id and url are intended to be unique identifiers.


3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (`SUBSCRIBERS`) static variable, we used the `DashMap` external library for thread safe `HashMap`. Explain based on your understanding of design patterns, do we still need `DashMap` or we can implement Singleton pattern instead? 

We still need `DashMap`. In this case, `DashMap` is used for data access in a multi-threaded environment. We need it to avoid race conditions. Meanwhile, Singleton is just ensuring there is only one instance of a database and accessing it globally. Additionally, Singleton does not inherently provide thread safety. Even if we use Singleton, we still need a thread-safe data structure like `DashMap` to safely handle concurrent access. Therefore, both concepts serve different purposes and cannot replace each other.

#### Reflection Publisher-2
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model? 

This separation is necessary to keep the Single Responsibility Principle in SOLID. Repository will store and interact with data, while Service is going to handle the business logic. Commonly, the flow is that a Service will call a function in the Repository, and let the Repository do something with the data. If we combine those two in one file, it will become too complex and hard to maintain because one component handles too many responsibilities. Additionally, separating them also improves testability. We can test the Service logic without depending on the data layer, and vice versa. This makes debugging easier and the code more modular.


2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model? 

If we only use the Model, the code will be tightly coupled. There will be so many functions with many responsibilities. Each function will depend on other functions, which makes it hard to modify one function without affecting another function. The code will be very long and difficult to test.Moreover, interactions between models like Program, Subscriber, and Notification will become messy because each model might directly call each other’s logic. This can create circular dependencies and make the overall system harder to understand and maintain as it grows.


3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects. 

Postman is very easy to understand and use. Since I started using it in PBP, it has been a functional tool that helps verify our code's functionality. The simulation it provides is also very straightforward. Additionally, Postman helps me test API endpoints without needing to build the frontend first. Some features that I find helpful are Collections for organizing requests, Environment variables for managing different configurations, and the ability to save and reuse requests. I am also interested in its automated testing feature because it can help ensure that our API works correctly as the project grows.

#### Reflection Publisher-3

1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use? 

This tutorial uses the Push model. The Publisher (BambangShop) actively triggers sending data to Subscribers through the `update()` method immediately after an event (such as creating a new product) occurs.


2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull) 

The advantage of using Pull is that Subscribers have full control over when they want to retrieve data, so they will not be overwhelmed by receiving too many notifications at once. The disadvantage is that notifications are not real-time. Subscribers have to continuously check (poll) the server, which can waste network resources if there are no data updates.


3. Explain what will happen to the program if we decide to not use multi-threading in the notification process. 
If we do not use multi-threading, the program will run synchronously (sequentially). If one subscriber has a slow internet connection or its server is down, the Main App will stop and wait (hang) until the request is completed before continuing to send notifications to the next subscriber. Using multi-threading ensures that the notification process does not block the main application performance.