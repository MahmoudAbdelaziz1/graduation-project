# Sequence Diagrams

This folder contains UML sequence diagrams illustrating the dynamic behavior of the Tasleem platform's most critical business workflows. These diagrams show the chronological order of interactions between actors, controllers, services, models, and external systems.

---

## Contents

| File | Scenario | Participants |
|------|----------|--------------|
| [`Scenario_1_User_Login_with_Rate_Limiting.md`](Scenario_1_User_Login_with_Rate_Limiting.md) | User authentication with brute-force protection | Client, AuthController, RateLimiter, User Model, Sanctum |
| [`Scenario_2_Submit_Offer_with_Balance_Check.md`](Scenario_2_Submit_Offer_with_Balance_Check.md) | Buyer submits price offer | Buyer, OfferController, Product, Offer, WalletService, Notify, Seller |
| [`Scenario_3_Accept_Offer_&_Create_Order.md`](Scenario_3_Accept_Offer_&_Create_Order.md) | Seller accepts offer and creates order | Seller, OfferController, Offer, OrderService, WalletService, Order, Payment, Notify, Buyer |
| [`Scenario_4_Complete_Order_&_Release_Funds.md`](Scenario_4_Complete_Order_&_Release_Funds.md) | Admin completes order and releases escrow | Admin, OrderController, Order, Payment, WalletService, User, Notify, Buyer, Seller |
| [`Scenario_5_Cancel_Order_&_Refund.md`](Scenario_5_Cancel_Order_&_Refund.md) | Order cancellation with refund processing | Buyer, OrderController, Order, Payment, WalletService, Notify, Seller |
| [`Scenario_6_Create_Rental_with_Availability_Check.md`](Scenario_6_Create_Rental_with_Availability_Check.md) | Rental request with date conflict prevention | Renter, RentalController, Rental, Product, WalletService, Payment, Notify, Owner |
| [`Scenario_7_Boost_Product_with_Wallet_Deduction.md`](Scenario_7_Boost_Product_with_Wallet_Deduction.md) | Product promotion via wallet payment | Seller, ProductController, Product, WalletService, Notify |
| [`Scenario_8_Create_Review_with_Auto_Rating_Update.md`](Scenario_8_Create_Review_with_Auto_Rating_Update.md) | Product review with automatic rating | Buyer, ReviewController, Review, Product |
 

---

## Scenario Overview

### 🔐 Scenario 1: User Login with Rate Limiting
**Purpose:** Secure authentication with brute-force attack protection

**Flow:**
1. Client submits email and password
2. AuthController validates request format
3. RateLimiter checks attempt count (max 5/minute)
4. If blocked → HTTP 429 response
5. If allowed → verify credentials
6. If invalid → increment counter, return 401
7. If valid → check account status
8. If deactivated → return 403
9. If active → clear counter, generate Sanctum token, return 200

**Business Rules:**
- Maximum 5 failed attempts per 60 seconds
- Only active accounts may access the system
- Tokens are Bearer-type via Laravel Sanctum

---

### 💬 Scenario 2: Submit Offer with Balance Check
**Purpose:** Buyer negotiates price with wallet balance verification

**Flow:**
1. Buyer submits offer (product_id, amount, payment_method)
2. Validate product exists and is for sale
3. Prevent offers on own products
4. For wallet payment: calculate total (amount + delivery_fee)
5. Check wallet balance → reject if insufficient
6. Create offer with `pending` status
7. Send notification to seller

**Business Rules:**
- Cannot offer on own products
- Offers only allowed on `sale` type products
- Wallet payments require sufficient balance

---

### ✅ Scenario 3: Accept Offer & Create Order
**Purpose:** Convert accepted offer to confirmed order with escrow

**Flow:**
1. Seller accepts pending offer
2. Verify seller ownership
3. Re-check buyer wallet balance (fallback to cash if insufficient)
4. Update offer status to `accepted`
5. OrderService creates confirmed order
6. Calculate fees (delivery, tasleem commission)
7. For wallet: hold funds from buyer
8. Create order and payment records
9. Decrement product stock
10. Notify buyer

**Business Rules:**
- Only product owner can accept offers
- Offer can only be accepted once
- Product inventory automatically updated

---

### 💰 Scenario 4: Complete Order & Release Funds
**Purpose:** Finalize transaction and release escrow to seller

**Flow:**
1. Admin initiates completion
2. Verify admin privileges
3. Confirm order status is `confirmed`
4. Verify payment is `pending`
5. Check seller's free sales remaining
6. Calculate payout (full amount if free, minus commission otherwise)
7. WalletService releases funds to seller
8. If free sale: decrement `free_sales_remaining`, set `tasleem_fee = 0`
9. Update payment status to `completed`
10. Update order status to `delivered`
11. Notify both parties

**Business Rules:**
- Only administrators may complete orders
- First 2 sales are commission-free per seller
- Funds only released for confirmed orders

---

### ❌ Scenario 5: Cancel Order & Refund
**Purpose:** Cancel transaction and restore funds/inventory

**Flow:**
1. Buyer or Admin initiates cancellation
2. Verify authorization
3. Check cancellable status (`pending` or `confirmed`)
4. For wallet payments: refund to buyer's wallet
5. For cash payments: update payment status only
6. Update order status to `cancelled`
7. Model Observer automatically restores product quantity
8. Reactivate product if stock becomes available
9. Notify both parties

**Business Rules:**
- Completed/delivered orders cannot be cancelled
- Wallet refunds are automatic
- Inventory restoration is observer-triggered

---

### 🏠 Scenario 6: Create Rental with Availability Check
**Purpose:** Request product rental with date conflict prevention

**Flow:**
1. Renter submits rental request with dates
2. Validate date range (end &gt; start, start &gt; today)
3. Check for conflicting rentals (overlapping dates, non-cancelled status)
4. Calculate totals: days, rent, delivery fee, commission
5. For wallet payment: verify and hold funds
6. Create rental record with `pending` status
7. Create payment record
8. Notify product owner

**Business Rules:**
- Rental periods must not overlap with existing active rentals
- Wallet balance verified before holding
- Product owner receives rental request notification

---

### 🚀 Scenario 7: Boost Product with Wallet Deduction
**Purpose:** Promote product visibility via paid boosting

**Flow:**
1. Seller requests boost with duration (1-30 days)
2. Verify product ownership
3. Calculate cost (`boost_fee_per_day × days`)
4. Deduct from wallet via WalletService
5. If insufficient balance → reject (402)
6. Update product: `is_boosted = true`, set `boost_expires_at`
7. Notify seller of successful activation

**Business Rules:**
- Only product owners may boost
- Maximum 30 days promotion
- Non-refundable boost fee

---

### ⭐ Scenario 8: Create Review with Auto Rating Update
**Purpose:** Submit product review with automatic average recalculation

**Flow:**
1. Buyer submits review (rating 1-5, optional comment)
2. Validate input
3. Check for duplicate review (one per user per product)
4. Create review record
5. Model Observer triggered
6. Calculate new average rating from all reviews
7. Update product's `rate` attribute

**Business Rules:**
- One review per user per product
- Rating must be 1-5 integer
- Average rating updated automatically via observer

---

## Wallet Payment Lifecycle

Three of the scenarios (3, 4, 5, 6) demonstrate the complete wallet escrow mechanism:
  