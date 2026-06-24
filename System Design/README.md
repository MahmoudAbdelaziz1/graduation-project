# System Design Documentation

This folder contains the complete system design chapter for the Tasleem platform graduation project, presenting the architectural decisions, layer responsibilities, workflow, and evaluation.

---




##  Block Diagram and System Architecture

### Introduction

The Tasleem platform was designed using a modern layered architecture that separates system responsibilities into distinct components. This approach improves:

- **Maintainability** — Isolated components can be modified independently
- **Scalability** — Layers can be scaled separately
- **Security** — Multiple security checkpoints
- **Extensibility** — New features integrate with minimal changes

The architecture follows **RESTful API Design**, **Layered Architecture**, and **Service Layer Pattern** principles.

---

## 1. Architecture Overview

Tasleem is implemented as a **RESTful Backend API** using Laravel Framework with seven independent layers:

| # | Layer | Responsibility |
|---|-------|----------------|
| 1 | **Client Layer** | Mobile apps, web applications, admin dashboards |
| 2 | **API Gateway Layer** | Request routing, authentication, authorization, middleware |
| 3 | **Controller Layer** | Request validation, service delegation, response formatting |
| 4 | **Business Logic Layer** | Core operations: orders, wallet, notifications, AI |
| 5 | **Data Access Layer** | Eloquent ORM, relationship management, query optimization |
| 6 | **Data Storage Layer** | MySQL persistence, file storage (local/S3) |
| 7 | **External Services Layer** | AI engine, payment gateways, push notifications, email, SMS |

---

## 2. Block Diagram Components

### 2.1 Client Layer
| Component | Platform | Functions |
|-----------|----------|-----------|
| Mobile Application | iOS, Android | Browse, buy, rent, wallet, notifications |
| Web Application | Browser | Product management, offers, rentals, payments |
| Admin Dashboard | Browser | Users, products, orders, rentals, statistics, logs |

### 2.2 API Gateway Layer
- **Request Routing** — Directs to appropriate controllers
- **Authentication** — Laravel Sanctum token validation
- **Authorization** — Role and permission verification
- **Middleware** — `auth:sanctum`, `admin`, rate limiting
- **Security** — Protection against unauthorized access

### 2.3 Controller Layer (18 Controllers)
| #  | Controller                     | Extends          | Category           | Description                                                                  |
| -- | ------------------------------ | ---------------- | ------------------ | ---------------------------------------------------------------------------- |
| 1  | **BaseController**             | `Controller`     | Base               | Abstract parent providing `sendResponse()`, `sendError()`, `sendPaginated()` |
| 2  | **AuthController**             | `Controller`     | Authentication     | User registration, login, logout, profile                                    |
| 3  | **UserController**             | `BaseController` | User Management    | User CRUD, user products/orders/rentals                                      |
| 4  | **ProductController**          | `BaseController` | Product Management | Product CRUD, search, filter, boost                                          |
| 5  | **ProductImageController**     | `BaseController` | Product Management | Image upload, URL import, deletion                                           |
| 6  | **CategoryController**         | `BaseController` | Product Management | Category CRUD with image upload                                              |
| 7  | **OrderController**            | `BaseController` | Transaction        | Order CRUD, seller confirm, complete, cancel                                 |
| 8  | **RentalController**           | `BaseController` | Transaction        | Rental CRUD, confirm, complete, cancel                                       |
| 9  | **OfferController**            | `BaseController` | Transaction        | Offer create, accept, reject                                                 |
| 10 | **PaymentController**          | `BaseController` | Payment            | Payment CRUD, status management                                              |
| 11 | **WalletController**           | `BaseController` | Payment            | Wallet balance, top-up                                                       |
| 12 | **CartItemController**         | `BaseController` | Engagement         | Cart CRUD, clear cart                                                        |
| 13 | **WishlistController**         | `BaseController` | Engagement         | Wishlist CRUD, check, clear                                                  |
| 14 | **ReviewController**           | `BaseController` | Engagement         | Review CRUD, rating validation                                               |
| 15 | **NotificationController**     | `BaseController` | System             | Notifications list, mark read                                                |
| 16 | **AiRecommendationController** | `BaseController` | System             | AI recommendation CRUD                                                       |
| 17 | **LogController**              | `BaseController` | Administration     | Audit logs, filtering, statistics                                            |
| 18 | **AdminController**            | `BaseController` | Administration     | Platform statistics, revenue tracking                                        |


### 2.4 Business Logic Layer (4 Services)
| Service | Core Functions |
|---------|---------------|
| **OrderService** | Create orders, calculate fees, manage inventory, complete/cancel |
| **WalletService** | Validate balance, hold funds, release funds, process refunds |
| **Notify** | Generate notifications, deliver via channels, persist records |
| **AIRecommendationService** | Communicate with AI engine, cache recommendations, refresh data |

### 2.5 Data Access Layer (15 Models)
User, Product, Category, ProductImage, Order, Rental, Offer, Payment, Review, CartItem, Wishlist, WalletTransaction, Notification, AiRecommendation, Log

### 2.6 Data Storage Layer
| Storage | Contents |
|---------|----------|
| **MySQL Database** | All relational data: users, products, transactions, logs |
| **File Storage** | Product images, user photos (local or cloud S3) |

### 2.7 External Services Layer
| Service | Provider/Type | Function |
|---------|---------------|----------|
| AI Recommendation Engine | Python/ML Microservice | Personalized product suggestions |
| Payment Gateway | Stripe/PayPal/Bank | Credit card and online payments |
| Push Notifications | Firebase Cloud Messaging | Mobile push alerts |
| Email | SendGrid/Mailgun | Transactional emails |
| SMS | Twilio | Text message notifications |

---

## 3. System Architecture Workflow
