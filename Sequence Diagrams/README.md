# Sequence Diagrams & Workflows

## Overview
Sequence diagrams model the runtime behavior of the Tasleem platform, demonstrating how actors, controllers, services, models, and external systems interact to execute business processes.

## Wallet Payment Lifecycle
The platform uses an escrow-based wallet system:
1. **Hold**: Funds reserved upon order/rental creation.
2. **Release**: Funds transferred to seller upon admin completion.
3. **Refund**: Funds returned to buyer upon cancellation.

## Critical Scenarios

### 1. User Login with Rate Limiting
Validates credentials, enforces a 5-attempts-per-minute limit via `RateLimiter`, checks account status, and generates a Sanctum token.

### 2. Submit Offer with Balance Verification
Buyer submits an offer. If `payment_method` is wallet, the system verifies sufficient balance (amount + delivery fee) before creating the offer and notifying the seller.

### 3. Accept Offer & Create Order
Seller accepts an offer. The system re-verifies wallet balance (falling back to cash if insufficient), creates a `confirmed` Order, holds funds, and decrements inventory.

### 4. Complete Order & Release Funds
Admin marks order as delivered. System calculates seller payout (deducting `tasleem_fee` unless free-sales quota applies), releases funds via `WalletService`, and notifies both parties.

### 5. Cancel Order & Process Refund
Buyer/Admin cancels a pending/confirmed order. Wallet funds are automatically refunded. Model Observers trigger inventory restoration.

### 6. Create Rental with Availability Check
Renter requests dates. System checks for overlapping active rentals. If available, calculates fees, holds wallet funds, and notifies the owner.

### 7. Boost Product with Wallet Deduction
Seller pays a fee via wallet to boost product visibility. System updates `is_boosted` and `boost_expires_at` timestamps.

### 8. Create Review with Auto Rating Update
Buyer submits a review. Model Observer automatically recalculates the product's average `rate` and updates the Product record.