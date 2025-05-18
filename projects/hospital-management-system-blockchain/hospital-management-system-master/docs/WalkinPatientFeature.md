# hospital-management-system-blockchain - WalkinPatientFeature

*This documentation is from the private repository hospital-management-system-blockchain.*

---

# Walk-in Patient Registration System

This document describes the Walk-in Patient Registration feature that allows hospitals to easily onboard patients without requiring them to create an account.

## Overview

The Walk-in Patient Registration system provides a streamlined process for registering patients who arrive at the hospital without a pre-booked appointment. This system:

1. Allows staff to quickly register walk-in patients with minimal information
2. Provides patients with access to their medical information via a unique link
3. Eliminates the need for account creation while maintaining security
4. Enables continuity of care through email-based access

## Key Components

### 1. Patient Booking Form (`/patient-booking-form`)

This form allows hospital staff to register walk-in patients with just basic information:
- Name
- Email address
- Phone number
- Appointment date/time
- Doctor selection (optional)

The form can be accessed:
- Via the sidebar menu under "Walk-in Bookings"
- From the main page "Walk-in Registration" button
- When selecting a specific doctor from the main page

### 2. Temporary Dashboard (`/temp-dashboard`)

After registration, patients receive an email with a secure link containing a unique access token. This link directs them to a temporary dashboard where they can:
- View their appointment details
- Access medical records
- Receive updates on their treatment
- Schedule follow-up appointments

The temporary dashboard provides all essential patient functionality without requiring account creation.

## How It Works

1. **Registration**: Staff uses the Patient Booking Form to register walk-in patients
2. **Notification**: System sends an email to the patient with a secure access link
3. **Access**: Patient uses the link to access their Temporary Dashboard
4. **Treatment**: Patient can view/receive updates about their treatment
5. **Follow-up**: Patient can book follow-up appointments through the same system

## Security

- Each temporary access link contains a unique token tied to the patient's email
- Tokens have expiration dates that can be configured
- No sensitive information is stored in the URL or token itself
- All communication occurs via the patient's verified email address

## Benefits

- **Low Friction**: Minimizes barriers for new patients
- **Security**: Maintains privacy and data security
- **Efficiency**: Streamlines the registration process
- **Continuity**: Provides consistent access to care information
- **Flexibility**: Can be used for one-time visits or converted to permanent accounts

## Access Points

The Walk-in Registration feature can be accessed from:
- Main navigation: "Walk-in Bookings" → "Register Walk-in Patient"
- Main page "Walk-in Registration" button
- Doctor profiles: "Book Appointment" button
