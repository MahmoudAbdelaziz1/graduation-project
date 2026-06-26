# Class Diagram

```mermaid
classDiagram
    direction TB

    %% ==================== ENTITY CLASSES ====================
    class User {
        +bigint id
        +string name
        +string email
        +string password
        +string phone
        +string national_id
        +string address
        +string city
        +string user_photo
        +enum role
        +enum status
        +decimal wallet_balance
        +tinyint free_sales_remaining
        +products() hasMany
        +orders() hasMany
        +rentals() hasMany
        +reviews() hasMany
        +cartItems() hasMany
        +wishlist() belongsToMany
        +payments() hasMany
        +walletTransactions() hasMany
        +appNotifications() hasMany
        +logs() hasMany
        +recommendations() hasMany
        +isAdmin() bool
        +isActive() bool
    }

    class Product {
        +bigint id
        +string name
        +text description
        +decimal price
        +bigint category_id
        +bigint owner_id
        +integer quantity
        +integer view_count
        +decimal rate
        +integer pay_count
        +enum status
        +enum type
        +boolean is_boosted
        +timestamp boost_expires_at
        +owner() belongsTo
        +category() belongsTo
        +images() hasMany
        +orders() hasMany
        +rentals() hasMany
        +reviews() hasMany
        +cartItems() hasMany
        +wishlistedBy() belongsToMany
        +recommendations() hasMany
        +isAvailable() bool
        +isActivelyBoosted() bool
        +isForSale() bool
        +isForRent() bool
    }

    class Category {
        +bigint category_id
        +string name
        +string photo
        +enum status
        +products() hasMany
        +scopeActive()
    }

    class ProductImage {
        +bigint image_id
        +bigint product_id
        +string image_url
        +string alt_text
        +product() belongsTo
    }

    class Order {
        +bigint order_id
        +bigint user_id
        +bigint product_id
        +integer quantity
        +decimal unit_price
        +decimal total_price
        +decimal tasleem_fee
        +decimal delivery_fee
        +enum status
        +user() belongsTo
        +product() belongsTo
        +seller() hasOneThrough
        +payment() hasOne
        +isShippable() bool
        +isCompleted() bool
    }

    class Rental {
        +bigint rental_id
        +bigint product_id
        +bigint renter_id
        +date start_date
        +date end_date
        +decimal daily_price
        +integer total_days
        +decimal total_price
        +enum payment_method
        +decimal tasleem_fee
        +decimal delivery_fee
        +enum status
        +product() belongsTo
        +renter() belongsTo
        +owner() hasOneThrough
        +payment() hasOne
        +isActive() bool
        +isCompleted() bool
    }

    class Offer {
        +bigint id
        +bigint product_id
        +bigint buyer_id
        +bigint seller_id
        +decimal amount
        +enum payment_method
        +enum status
        +product() belongsTo
        +buyer() belongsTo
        +seller() belongsTo
    }

    class Review {
        +bigint review_id
        +bigint product_id
        +bigint user_id
        +integer rating
        +text comment
        +product() belongsTo
        +user() belongsTo
        +getStarRatingAttribute() array
    }

    class CartItem {
        +bigint cart_item_id
        +bigint user_id
        +bigint product_id
        +integer quantity
        +date rental_start_date
        +date rental_end_date
        +enum item_type
        +user() belongsTo
        +product() belongsTo
        +getSubtotalAttribute() decimal
    }

    class Wishlist {
        +bigint wishlist_id
        +bigint user_id
        +bigint product_id
        +user() belongsTo
        +product() belongsTo
    }

    class Payment {
        +bigint payment_id
        +bigint order_id
        +bigint rental_id
        +bigint user_id
        +decimal amount
        +enum payment_method
        +enum status
        +string transaction_id
        +user() belongsTo
        +order() belongsTo
        +rental() belongsTo
        +isSuccessful() bool
    }

    class WalletTransaction {
        +bigint id
        +bigint user_id
        +enum type
        +decimal amount
        +decimal balance_after
        +string ref_type
        +bigint ref_id
        +string description
        +user() belongsTo
    }

    class Notification {
        +bigint id
        +bigint user_id
        +string type
        +string title
        +text body
        +string ref_type
        +bigint ref_id
        +timestamp read_at
        +user() belongsTo
    }

    class Log {
        +bigint log_id
        +bigint user_id
        +string action_type
        +string action_name
        +string module
        +string entity_type
        +bigint entity_id
        +json old_data
        +json new_data
        +string ip_address
        +string status
        +user() belongsTo
        +getActorNameAttribute() string
    }

    class AiRecommendation {
        +bigint rec_id
        +bigint user_id
        +bigint product_id
        +decimal score
        +enum algorithm_type
        +text reason
        +json metadata
        +timestamp expires_at
        +user() belongsTo
        +product() belongsTo
        +isValid() bool
    }

    %% ==================== SERVICE CLASSES ====================
    class WalletService {
        -User user
        +move(User user, string type, decimal amount, string ref_type, bigint ref_id, string description) WalletTransaction
        +hold(User user, decimal amount) WalletTransaction
        +release(User user, decimal amount) WalletTransaction
        +refund(User user, decimal amount) WalletTransaction
    }

    class OrderService {
        +placeOrder(bigint user_id, bigint product_id, integer quantity, decimal price, string status, string payment_method) Order
        +completeOrder(Order order) Order
        +cancelOrder(Order order) Order
    }

    class Notify {
        +send(bigint user_id, string type, string title, string body, string ref_type, bigint ref_id) Notification
    }

    class AIRecommendationService {
        +getRecommendations(bigint user_id) array
        +saveRecommendation(array data) AiRecommendation
        +refreshRecommendations()
    }

    %% ==================== CONTROLLER CLASSES ====================
    class BaseController {
        #sendResponse(result, message, code) JsonResponse
        #sendError(error, errorMessages, code) JsonResponse
        #sendPaginated(items, data, message) JsonResponse
    }

    class AuthController {
        +register(Request request) JsonResponse
        +login(Request request) JsonResponse
        +logout(Request request) JsonResponse
        +me(Request request) JsonResponse
    }

    class UserController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +products(id) JsonResponse
        +orders(id) JsonResponse
        +rentals(id) JsonResponse
    }

    class ProductController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +similar(id) JsonResponse
        +boost(Request request, id) JsonResponse
    }

    class OrderController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +sellerConfirm(id) JsonResponse
        +complete(id) JsonResponse
        +cancel(id) JsonResponse
    }

    class RentalController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +confirm(id) JsonResponse
        +complete(id) JsonResponse
        +cancel(id) JsonResponse
    }

    class OfferController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +accept(id) JsonResponse
        +reject(id) JsonResponse
    }

    class PaymentController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
    }

    class CartItemController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +clear(user_id) JsonResponse
    }

    class ReviewController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
    }

    class CategoryController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
    }

    class WishlistController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +destroy(id) JsonResponse
        +clear(userId) JsonResponse
        +check(Request request) JsonResponse
    }

    class WalletController {
        +show() JsonResponse
        +topup(Request request) JsonResponse
    }

    class NotificationController {
        +index() JsonResponse
        +markRead(id) JsonResponse
        +markAllRead() JsonResponse
    }

    class AiRecommendationController {
        +index(Request request) JsonResponse
        +store(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
    }

    class LogController {
        +addLog(userId, actionType, actionName, module, entityId, oldData, newData, ipAddress, userAgent, status, message, errorCode) Log
        +index(Request request) JsonResponse
        +show(id) JsonResponse
        +entityLogs(entityType, entityId) JsonResponse
        +userLogs(userId) JsonResponse
        +stats() JsonResponse
    }

    class AdminController {
        +stats() JsonResponse
    }

    class ProductImageController {
        +index(productId) JsonResponse
        +store(Request request) JsonResponse
        +uploadSingle(Request request) JsonResponse
        +show(id) JsonResponse
        +update(Request request, id) JsonResponse
        +destroy(id) JsonResponse
        +destroyMultiple(Request request) JsonResponse
        +addFromUrl(Request request) JsonResponse
    }

    %% ==================== RESOURCE CLASSES ====================
    class UserResource {
        +toArray(request) array
    }

    class ProductResource {
        +toArray(request) array
    }

    class OrderResource {
        +toArray(request) array
    }

    class RentalResource {
        +toArray(request) array
    }

    class OfferResource {
        +toArray(request) array
    }

    class ReviewResource {
        +toArray(request) array
    }

    class PaymentResource {
        +toArray(request) array
    }

    class CartItemResource {
        +toArray(request) array
    }

    class CategoryResource {
        +toArray(request) array
    }

    class ProductImageResource {
        +toArray(request) array
    }

    class WishlistResource {
        +toArray(request) array
    }

    class AiRecommendationResource {
        +toArray(request) array
    }

    class LogResource {
        +toArray(request) array
    }

    %% ==================== INTERFACES ====================
    class Authenticatable {
        <<interface>>
        +getAuthIdentifier()
        +getAuthPassword()
    }

    %% ==================== RELATIONSHIPS ====================

    %% Model-to-Model Relationships
    User "1" --> "many" Product : owns (owner_id)
    User "1" --> "many" Order : places
    User "1" --> "many" Rental : rents (renter_id)
    User "1" --> "many" Review : writes
    User "1" --> "many" CartItem : owns
    User "1" --> "many" Payment : makes
    User "1" --> "many" WalletTransaction : owns
    User "1" --> "many" Notification : receives
    User "1" --> "many" Log : performs
    User "1" --> "many" AiRecommendation : receives
    User "1" --> "many" Offer : makes_as_buyer
    User "1" --> "many" Offer : receives_as_seller
    User "*" --> "*" Product : via Wishlist

    Category "1" o-- "many" Product : contains
    Product "1" *-- "many" ProductImage : has
    Product "1" --> "many" Order : included_in
    Product "1" --> "many" Rental : rented_in
    Product "1" --> "many" Review : receives
    Product "1" --> "many" CartItem : in
    Product "1" --> "many" Offer : offered_on
    Product "1" --> "many" AiRecommendation : recommended

    Order "1" --> "0..1" Payment : paid_by
    Rental "1" --> "0..1" Payment : paid_by

    %% Controller-to-Service Dependencies
    AuthController ..|> BaseController
    UserController ..|> BaseController
    ProductController ..|> BaseController
    OrderController ..|> BaseController
    RentalController ..|> BaseController
    OfferController ..|> BaseController
    PaymentController ..|> BaseController
    CartItemController ..|> BaseController
    ReviewController ..|> BaseController
    CategoryController ..|> BaseController
    WishlistController ..|> BaseController
    WalletController ..|> BaseController
    NotificationController ..|> BaseController
    AiRecommendationController ..|> BaseController
    LogController ..|> BaseController
    AdminController ..|> BaseController
    ProductImageController ..|> BaseController

    %% Controller-to-Model Dependencies
    UserController ..> User
    UserController ..> UserResource
    ProductController ..> Product
    ProductController ..> ProductResource
    OrderController ..> Order
    OrderController ..> OrderResource
    OrderController ..> OrderService
    OrderController ..> WalletService
    OrderController ..> Notify
    RentalController ..> Rental
    RentalController ..> RentalResource
    RentalController ..> WalletService
    RentalController ..> Notify
    OfferController ..> Offer
    OfferController ..> OfferResource
    OfferController ..> OrderService
    OfferController ..> WalletService
    OfferController ..> Notify
    PaymentController ..> Payment
    PaymentController ..> PaymentResource
    CartItemController ..> CartItem
    CartItemController ..> CartItemResource
    ReviewController ..> Review
    ReviewController ..> ReviewResource
    CategoryController ..> Category
    CategoryController ..> CategoryResource
    WishlistController ..> Wishlist
    WishlistController ..> WishlistResource
    WalletController ..> WalletService
    NotificationController ..> Notification
    AiRecommendationController ..> AiRecommendation
    AiRecommendationController ..> AiRecommendationResource
    AiRecommendationController ..> AIRecommendationService
    LogController ..> Log
    LogController ..> LogResource
    AdminController ..> User
    AdminController ..> Product
    AdminController ..> Order
    AdminController ..> Rental
    AdminController ..> WalletTransaction
    ProductImageController ..> ProductImage
    ProductImageController ..> ProductImageResource

    %% Service-to-Model Dependencies
    WalletService ..> WalletTransaction
    WalletService ..> User
    OrderService ..> Order
    OrderService ..> Product
    OrderService ..> User
    OrderService ..> WalletService
    OrderService ..> Notify
    Notify ..> Notification
    Notify ..> User
    AIRecommendationService ..> AiRecommendation

    %% Resource-to-Model Dependencies
    UserResource ..> User
    ProductResource ..> Product
    ProductResource ..> CategoryResource
    ProductResource ..> UserResource
    ProductResource ..> ProductImageResource
    OrderResource ..> Order
    OrderResource ..> UserResource
    OrderResource ..> ProductResource
    OrderResource ..> PaymentResource
    RentalResource ..> Rental
    RentalResource ..> ProductResource
    RentalResource ..> UserResource
    OfferResource ..> Offer
    OfferResource ..> ProductResource
    OfferResource ..> UserResource
    ReviewResource ..> Review
    ReviewResource ..> ProductResource
    ReviewResource ..> UserResource
    PaymentResource ..> Payment
    PaymentResource ..> OrderResource
    PaymentResource ..> RentalResource
    PaymentResource ..> UserResource
    CartItemResource ..> CartItem
    CartItemResource ..> UserResource
    CartItemResource ..> ProductResource
    CategoryResource ..> Category
    ProductImageResource ..> ProductImage
    WishlistResource ..> Wishlist
    WishlistResource ..> UserResource
    WishlistResource ..> ProductResource
    AiRecommendationResource ..> AiRecommendation
    AiRecommendationResource ..> UserResource
    AiRecommendationResource ..> ProductResource
    LogResource ..> Log
    LogResource ..> UserResource

    %% Interface Realization
    User ..|> Authenticatable
```    