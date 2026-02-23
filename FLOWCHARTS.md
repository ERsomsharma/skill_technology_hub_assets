# Skill Technology Hub - Process Flowcharts

Source snapshot: `skill_technology_hub` (Django project)
Generated on: 2026-02-23

## 1) Registration and Email Verification Flow

```mermaid
flowchart TD
    A[User submits registration form] --> B[Create auth user]
    B --> C[Create or update UserProfile]
    C --> D[Generate verification token plus sent_at]
    D --> E[Send verification email]
    E --> F[User opens verify-email token link]
    F --> G{Token profile found?}
    G -- No --> H[Show invalid or used link]
    G -- Yes --> I{Already verified?}
    I -- Yes --> J[Show already verified]
    I -- No --> K{Link expired > 48h?}
    K -- Yes --> L[Show expired and allow resend]
    K -- No --> M[Set is_email_verified true]
    M --> N[Clear token plus sent_at]
    N --> O[Show success and allow login]
```

## 2) Reverification Flow (Profile or Resend)

```mermaid
flowchart TD
    A[User requests resend or reverification] --> B{Authenticated profile flow?}
    B -- Public resend form --> C[Find UserProfile by email]
    C --> D{Profile exists and unverified?}
    D -- No --> E[Generic safe response or already verified]
    D -- Yes --> F
    B -- Authenticated profile flow --> F[Load current user profile]
    F --> G{Cooldown active < 60s?}
    G -- Yes --> H[Inform wait seconds left]
    G -- No --> I[Generate new token and sent_at]
    I --> J[Send verification email]
    J --> K{Send success?}
    K -- Yes --> L[Show success message]
    K -- No --> M[Show temporary delivery error]
```

## 3) Admin Login OTP Flow

```mermaid
flowchart TD
    A[Admin submits login credentials] --> B{Credentials valid?}
    B -- No --> X[Show login error]
    B -- Yes --> C[Generate 6 digit OTP]
    C --> D[Store OTP plus expiry in session]
    D --> E[Send OTP email]
    E --> F[Open OTP verify page]
    F --> G{OTP session valid and not expired?}
    G -- No --> H[Clear session and ask login again]
    G -- Yes --> I{Entered OTP matches?}
    I -- No --> J[Show invalid OTP]
    I -- Yes --> K[Login admin user]
    K --> L[Clear OTP session]
    L --> M[Redirect admin-zone dashboard]
```

## 4) Trainee Enrollment and Payment Verification Flow

```mermaid
flowchart TD
    A[Trainee opens batch detail] --> B[Click enroll]
    B --> C{Pricing configured and seats available?}
    C -- No --> D[Show error and stop]
    C -- Yes --> E{Existing enrollment pending or confirmed?}
    E -- Confirmed --> F[Redirect my enrollments]
    E -- Pending --> G[Redirect checkout existing]
    E -- No --> H[Create pending Enrollment]
    H --> I[Checkout]
    I --> J{Existing created order fresh?}
    J -- No --> K[Create Razorpay order]
    K --> L[Create Order row]
    J -- Yes --> M[Reuse order]
    L --> N[Client pays on gateway]
    M --> N
    N --> O[POST verify payment]
    O --> P{Signature valid and order payable?}
    P -- No --> Q[Return payment failed]
    P -- Yes --> R[Mark order paid]
    R --> S[Confirm enrollment]
    S --> T[Send payment and enrollment notifications]
    T --> U[Redirect success page]
```

## 5) Invoice Payment Flow (Customer)

```mermaid
flowchart TD
    A[Customer opens invoice checkout] --> B{Invoice payable?}
    B -- No --> C[Redirect invoice detail with reason]
    B -- Yes --> D{Created payment exists and fresh?}
    D -- No --> E[Create Razorpay order]
    E --> F[Create InvoicePayment row]
    D -- Yes --> G[Reuse payment row]
    F --> H[Customer pays]
    G --> H
    H --> I[POST verify invoice payment]
    I --> J{Signature valid and still payable?}
    J -- No --> K[Return failure]
    J -- Yes --> L[Mark invoice payment paid]
    L --> M[Set Invoice status paid and paid_date]
    M --> N[If linked appointment set is_paid true]
    N --> O[Redirect invoice detail success]
```

## 6) Certificate Issue and Public Verification Flow

```mermaid
flowchart TD
    A[Eligibility check for enrollment] --> B{Confirmed enrollment plus paid order?}
    B -- No --> C[Not eligible]
    B -- Yes --> D[Compute attendance and assignment rates]
    D --> E{Meets thresholds?}
    E -- No --> C
    E -- Yes --> F{Certificate already exists?}
    F -- Yes --> G[Reuse existing certificate]
    F -- No --> H[Create TraineeCertificate]
    H --> I[Build certificate number]
    G --> J[Build token plus verification code]
    I --> J
    J --> K[Render trainee certificate and downloadable PDF]

    L[Public verify form] --> M{Token provided?}
    M -- Yes --> N[Resolve certificate from signed token]
    M -- No --> O{Number plus code provided?}
    O -- No --> P[Prompt for both fields]
    O -- Yes --> Q[Resolve certificate by number and derived code]
    N --> R{Found in primary DB?}
    Q --> R
    R -- Yes --> S[Render valid certificate details]
    R -- No --> T[Try legacy SQLite fallback]
    T --> U{Legacy match found?}
    U -- Yes --> S
    U -- No --> V[Show invalid certificate message]
```

