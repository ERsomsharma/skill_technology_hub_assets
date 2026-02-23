# Skill Technology Hub - Model Charts (ERD)

Source snapshot: `skill_technology_hub` (Django project)
Generated on: 2026-02-23

## 1) Identity, Access, Notification Models

```mermaid
erDiagram
    AUTH_USER {
        int id PK
        string username
        string email
        bool is_staff
        bool is_superuser
    }

    ROLE {
        int id PK
        string name UK
        bool is_active
    }

    USER_PROFILE {
        int id PK
        int user_id FK
        int role_id FK
        string user_type
        string phone
        string company_name
        bool is_email_verified
        string email_verification_token
    }

    NOTIFICATION_PREFERENCE {
        int id PK
        int user_id FK
        bool in_app_enabled
        bool email_enabled
    }

    USER_NOTIFICATION {
        int id PK
        int user_id FK
        string title
        string notification_type
        bool is_read
        datetime created_at
    }

    EMAIL_DELIVERY_LOG {
        int id PK
        int user_id FK
        string recipient_email
        string category
        string status
        datetime created_at
    }

    AUTH_USER ||--o| USER_PROFILE : has
    ROLE ||--o{ USER_PROFILE : assigned_to
    AUTH_USER ||--o| NOTIFICATION_PREFERENCE : has
    AUTH_USER ||--o{ USER_NOTIFICATION : receives
    AUTH_USER ||--o{ EMAIL_DELIVERY_LOG : owns
```

## 2) Training, Enrollment, Payment, Certificate Models

```mermaid
erDiagram
    TRAINING_BATCH {
        int id PK
        string title
        date start_date
        string status
        bool is_featured
    }

    BATCH_PRICE {
        int id PK
        int batch_id FK
        decimal original_price
        decimal discounted_price
        int max_students
    }

    ENROLLMENT {
        uuid id PK
        int user_id FK
        int batch_id FK
        string status
        string name
        string email
        datetime enrolled_at
        datetime confirmed_at
    }

    ORDER {
        uuid id PK
        uuid enrollment_id FK
        int user_id FK
        string order_id UK
        string invoice_number UK
        decimal amount
        string status
        string payment_id
        datetime paid_at
    }

    TRAINEE_SESSION {
        int id PK
        int batch_id FK
        string title
        datetime scheduled_at
        string status
    }

    TRAINEE_ATTENDANCE {
        int id PK
        int session_id FK
        int trainee_id FK
        string status
    }

    TRAINEE_ASSIGNMENT {
        int id PK
        int batch_id FK
        string title
        date due_date
        string status
    }

    TRAINEE_ASSIGNMENT_SUBMISSION {
        int id PK
        int assignment_id FK
        int trainee_id FK
        string status
        decimal score
        datetime submitted_at
    }

    TRAINEE_CERTIFICATE {
        int id PK
        int trainee_id FK
        uuid enrollment_id FK
        string certificate_number UK
        datetime issued_at
    }

    AUTH_USER {
        int id PK
    }

    TRAINING_BATCH ||--|| BATCH_PRICE : has_pricing
    AUTH_USER ||--o{ ENROLLMENT : creates
    TRAINING_BATCH ||--o{ ENROLLMENT : receives
    ENROLLMENT ||--|| ORDER : has_order
    AUTH_USER ||--o{ ORDER : pays

    TRAINING_BATCH ||--o{ TRAINEE_SESSION : schedules
    TRAINEE_SESSION ||--o{ TRAINEE_ATTENDANCE : records
    AUTH_USER ||--o{ TRAINEE_ATTENDANCE : attends

    TRAINING_BATCH ||--o{ TRAINEE_ASSIGNMENT : includes
    TRAINEE_ASSIGNMENT ||--o{ TRAINEE_ASSIGNMENT_SUBMISSION : collects
    AUTH_USER ||--o{ TRAINEE_ASSIGNMENT_SUBMISSION : submits

    AUTH_USER ||--o{ TRAINEE_CERTIFICATE : awarded_to
    ENROLLMENT ||--|| TRAINEE_CERTIFICATE : certificate_for
```

## 3) Customer Service, Billing, Review Models

```mermaid
erDiagram
    SERVICE_PACKAGE {
        int id PK
        string name
        string tier
        decimal price
        bool is_active
    }

    APPOINTMENT {
        int id PK
        int customer_id FK
        int assigned_to_id FK
        string service_type
        string status
        string title
        datetime scheduled_date
        decimal cost
        bool is_paid
    }

    INVOICE {
        int id PK
        string invoice_number UK
        int customer_id FK
        int appointment_id FK
        decimal total_amount
        string status
        date due_date
        date paid_date
    }

    INVOICE_PAYMENT {
        uuid id PK
        int invoice_id FK
        int user_id FK
        string order_id UK
        string payment_id
        decimal amount
        string status
        datetime paid_at
    }

    REVIEW {
        int id PK
        int reviewer_id FK
        int related_appointment_id FK
        int related_service_id FK
        int moderated_by_id FK
        string reviewer_type
        int rating
        string status
        datetime created_at
    }

    AUTH_USER {
        int id PK
    }

    AUTH_USER ||--o{ APPOINTMENT : requests
    AUTH_USER ||--o{ APPOINTMENT : assigned
    APPOINTMENT ||--o| INVOICE : billed_as
    AUTH_USER ||--o{ INVOICE : billed_to
    INVOICE ||--o{ INVOICE_PAYMENT : paid_by_records
    AUTH_USER ||--o{ INVOICE_PAYMENT : submits

    AUTH_USER ||--o{ REVIEW : writes
    AUTH_USER ||--o{ REVIEW : moderates
    APPOINTMENT ||--o{ REVIEW : reviewed_in
    SERVICE_PACKAGE ||--o{ REVIEW : reviewed_package
```

## 4) Content and Public Catalog Models

```mermaid
erDiagram
    PAGE {
        int id PK
        string title
        string slug UK
        string page_type UK
        bool is_published
    }

    TRUSTED_COMPANY {
        int id PK
        string name
        string url
        bool is_active
        int sort_order
    }

    POST_CATEGORY {
        int id PK
        string name
        string slug UK
    }

    POST {
        int id PK
        int category_id FK
        string title
        string slug UK
        string status
        datetime published_at
    }

    TEMPLATE_CATEGORY {
        int id PK
        string name
        string slug UK
    }

    TEMPLATE_ITEM {
        int id PK
        int category_id FK
        string name
        string slug UK
        string pricing_type
        string status
    }

    TEAM_MEMBER {
        int id PK
        string name
        string slug UK
        string designation
        bool is_active
    }

    PROJECT_CATEGORY {
        int id PK
        string name
        string slug UK
    }

    PROJECT {
        int id PK
        int category_id FK
        string title
        string slug UK
        string status
        bool is_featured
    }

    PROJECT_IMAGE {
        int id PK
        int project_id FK
        string caption
    }

    PORTFOLIO_ITEM {
        int id PK
        int project_id FK
        string title
        string slug UK
        bool is_featured
    }

    POST_CATEGORY ||--o{ POST : classifies
    TEMPLATE_CATEGORY ||--o{ TEMPLATE_ITEM : classifies
    PROJECT_CATEGORY ||--o{ PROJECT : classifies
    PROJECT ||--o{ PROJECT_IMAGE : has_gallery
    PROJECT ||--o{ PORTFOLIO_ITEM : showcased_as
```

