# hospital-management-system-blockchain - README

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Payment Module

This module handles all payment-related functionality for the Hospital Management System, including processing payments for medical products, pharmacy products, lab tests, and doctor appointments.

## Features

### Cart Management
- Add items to cart (medical products, pharmacy products, lab tests)
- View cart contents
- Update item quantities
- Remove items from cart
- Clear cart

### Payment Processing
- Create payment intents using Stripe
- Process payments for different product types
- Handle payment success and failure events
- Maintain payment and order records

### Lab Appointments
- Schedule lab appointments
- View appointment details
- Update appointment information
- Cancel appointments
- Support for home collection

## API Endpoints

### Cart Management

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/payment/cart` | GET | Get user's cart contents |
| `/api/payment/cart/add` | POST | Add item to cart |
| `/api/payment/cart/remove/<item_id>` | DELETE | Remove item from cart |
| `/api/payment/cart/update/<item_id>` | PUT | Update item quantity |
| `/api/payment/cart/clear` | DELETE | Clear all items from cart |

### Payment Processing

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/payment/create-intent` | POST | Create a payment intent |
| `/api/payment/webhook` | POST | Handle Stripe webhook events |

### Lab Appointments

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/payment/lab-appointments` | GET | Get user's lab appointments |
| `/api/payment/lab-appointments` | POST | Schedule a new lab appointment |
| `/api/payment/lab-appointments/<appointment_id>` | GET | Get appointment details |
| `/api/payment/lab-appointments/<appointment_id>` | PUT | Update appointment details |
| `/api/payment/lab-appointments/<appointment_id>/cancel` | POST | Cancel an appointment |

## Models

### Cart and Cart Items
- **Cart**: Represents a user's shopping cart
- **CartItem**: Individual items in the cart with type, quantity, and price

### Payment and Invoices
- **Payment**: Records payment transactions with status and metadata
- **Invoice**: Detailed invoice information for completed payments
- **PaymentMethod**: Saved payment methods for users

### Lab Appointments
- **LabAppointment**: Details of lab test appointments including scheduling, status, and results

## Setup

### Environment Variables
The payment module requires the following environment variables:
- `STRIPE_API_KEY`: Your Stripe secret API key
- `STRIPE_WEBHOOK_SECRET`: Webhook signing secret from Stripe
- `STRIPE_PUBLIC_KEY`: Public key for client-side integration

### Stripe Integration
This module uses Stripe for payment processing. Make sure to:
1. Create a Stripe account
2. Set up webhook endpoints in your Stripe dashboard
3. Configure the proper Stripe API keys in your environment

## Testing

Run the tests for this module with:
```bash
python -m unittest tests/test_payment_endpoints.py
```
