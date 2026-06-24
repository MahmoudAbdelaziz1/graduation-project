# Tasleem — E-Commerce & Rental Marketplace Platform

## Graduation Project Documentation

**Tasleem** is a full-featured e-commerce and rental marketplace platform built as a RESTful API using Laravel 10 and PHP 8.2. The platform supports product sales, rental services, wallet-based escrow payments, AI-powered recommendations, and comprehensive audit logging.

---

## 📋 Table of Contents

- [Project Overview](#project-overview)
- [System Architecture](#system-architecture)
- [Documentation Structure](#documentation-structure)
- [Technology Stack](#technology-stack)
- [Key Features](#key-features)
- [API Documentation](#api-documentation)
- [Database Design](#database-design)
- [Setup Instructions](#setup-instructions)

---

## Project Overview

Tasleem connects buyers, sellers, and renters through a secure marketplace with the following capabilities:

| Module | Description |
|--------|-------------|
| **Authentication** | Token-based auth with Laravel Sanctum, rate limiting, account status management |
| **Product Management** | Product listings with categories, images, boosting, sale/rental types |
| **Offer System** | Price negotiation with wallet/cash payment options |
| **Order Processing** | Complete order lifecycle with escrow wallet payments |
| **Rental Management** | Date-based rentals with availability checking and conflict prevention |
| **Payment Processing** | Multi-method payments (wallet, cash, credit card, PayPal, bank transfer) |
| **Wallet System** | Internal escrow wallet with top-up, hold, release, refund operations |
| **Reviews & Ratings** | Product reviews with automatic average rating updates |
| **AI Recommendations** | External ML microservice integration for personalized suggestions |
| **Notifications** | Multi-channel notifications (push, email, SMS, in-app) |
| **Audit Logging** | Comprehensive system-wide activity tracking |

---

## System Architecture

The platform follows a **Layered RESTful Architecture** with clear separation of concerns:
┌─────────────────────────────────────────────┐
│        Client Layer   

  Mobile App, Web App, Admin Dashboard
├─────────────────────────────────────────────┤
│  API Gateway    
 Laravel Sanctum, Middleware, Rate Limiting
├─────────────────────────────────────────────┤
│  Controllers   
  18 RESTful Controllers
├─────────────────────────────────────────────┤
│  Services       
 OrderService, WalletService, Notify, AIRecommendationService
├─────────────────────────────────────────────┤
│  Models (ORM)   
15 Eloquent Models with Relationships
├─────────────────────────────────────────────┤
│  Data Storage   
 MySQL Database, File Storage (Local/S3)
├─────────────────────────────────────────────┤
│  External APIs  
 AI Engine, Payment Gateway, Firebase, Email, SMS
└─────────────────────────────────────────────┘


## Documentation Structure

| Folder | Contents |
|--------|----------|
| [`Block Diagram/`](Block%20Diagram/) | System architecture, block diagrams, DFD Level 0/1, data flow analysis |
| [`Class Diagram/`](Class%20Diagram/) | UML class diagrams, entity descriptions, relationship analysis |
| [`Database Design/`](Database%20Design/) | ER diagram, normalization (3NF), indexing strategy, entity descriptions |
| [`Design Patterns/`](Design%20Patterns/) | Applied design patterns with evaluation and SOLID principles assessment |
| [`Sequence Diagrams/`](Sequence%20Diagrams/) | 8 critical business scenario sequence diagrams |
| [`System Design/`](System%20Design/) | Complete system architecture chapter |






## Technology Stack

| Layer | Technology |
|-------|------------|
| **Language** | PHP 8.2 |
| **Framework** | Laravel 10 |
| **Database** | MySQL |
| **ORM** | Eloquent ORM |
| **Authentication** | Laravel Sanctum (Bearer Token) |
| **API Format** | RESTful JSON API |
| **HTTP Client** | Guzzle |
| **Testing** | PestPHP, Faker, Mockery |
| **Development** | Laravel Sail (Docker), Composer, Pint |
| **External Services** | AI Recommendation Engine, Payment Gateway, Firebase FCM, SendGrid/Mailgun, Twilio |

📁 [View detailed technologies →](System%20Design/)

---

## Key Features

### 🔐 Security
- Token-based authentication with Laravel Sanctum
- Rate limiting on authentication endpoints (5 attempts/minute)
- Bcrypt password hashing
- Role-based access control (admin/user)
- Comprehensive audit logging

### 💰 Wallet & Payments
- Internal wallet with escrow mechanism
- Fund reservation (hold) during transactions
- Automatic release to sellers on completion
- Full refund processing on cancellation
- Commission-free first sales for new sellers

### 🤖 AI Integration
- External Python/ML microservice for recommendations
- Multiple algorithms: collaborative, content, hybrid, popularity, location-based
- Cached recommendations with expiration
- Score-based ranking

### 📊 Admin Dashboard
- Platform statistics and revenue tracking
- System logs with filtering and search
- User, order, rental, and payment monitoring

---

## API Documentation

The API follows RESTful conventions with versioning (`/api/v1/`).

### Public Endpoints
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/v1/register` | User registration |
| POST | `/api/v1/login` | User login |
| GET | `/api/v1/products` | List products |
| GET | `/api/v1/products/{id}` | Product details |
| GET | `/api/v1/categories` | List categories |
| GET | `/api/v1/reviews` | List reviews |

### Protected Endpoints (auth:sanctum)
| Resource | Endpoints |
|----------|-----------|
| Users | CRUD + products/orders/rentals |
| Products | CRUD + boost + images |
| Orders | CRUD + seller-confirm + complete + cancel |
| Rentals | CRUD + confirm + complete + cancel |
| Offers | List + create + accept + reject |
| Cart | CRUD + clear |
| Wallet | View + topup |
| Notifications | List + mark read |
| Reviews | CRUD |
| Payments | CRUD |
| Wishlist | CRUD + check |
| AI Recommendations | CRUD |
| Logs | Admin-only statistics and filtering |



---

## Database Design

The database consists of **15 tables** with full referential integrity:

- `users`, `categories`, `products`, `product_images`
- `orders`, `rentals`, `offers`, `payments`
- `reviews`, `cart_items`, `wishlist`
- `wallet_transactions`, `notifications`
- `ai_recommendations`, `logs`

📁 [View detailed database design →](Database%20Design/)

---

## Setup Instructions

```bash
# Clone repository
git clone <repository-url>
cd graduation-project

# Install dependencies
composer install

# Environment setup
cp .env.example .env
php artisan key:generate

# Database setup
php artisan migrate
php artisan db:seed

# Start development server
php artisan serve