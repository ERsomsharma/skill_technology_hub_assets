# Skill Technology Hub - Data Flow Diagrams (DFD)

Source snapshot: `skill_technology_hub` (Django project)
Generated on: 2026-02-23

## DFD Level 0 (Context)

```mermaid
flowchart LR
    visitor[Visitor]
    trainee[Trainee]
    customer[Customer]
    admin[Admin Team]
    gateway[Razorpay Gateway]
    email[Email Provider SMTP]
    verifier[Certificate Verifier]

    system((Skill Technology Hub Platform))

    visitor -->|Browse services/templates/projects/news| system
    trainee -->|Register, login, enroll, submit reviews| system
    customer -->|Request services, pay invoices, review| system
    admin -->|Manage content, users, invoices, support| system
    verifier -->|Token/manual certificate checks| system

    system -->|Order create/verify webhook| gateway
    gateway -->|Payment status/signature| system

    system -->|Transactional emails| email
    email -->|Delivery status| system
```

## DFD Level 1 (Process Decomposition)

```mermaid
flowchart LR
    subgraph External
        E1[Visitor]
        E2[Trainee]
        E3[Customer]
        E4[Admin]
        E5[Razorpay]
        E6[SMTP Provider]
        E7[Certificate Verifier]
    end

    subgraph Core Processes
        P1((P1 Content and Catalog))
        P2((P2 Identity and Access))
        P3((P3 Training, Enrollment, Payments))
        P4((P4 Customer Services and Billing))
        P5((P5 Certificate Issuance and Verification))
        P6((P6 Admin Ops, Notifications, Security))
    end

    subgraph Data Stores
        D1[(D1 CMS Data)]
        D2[(D2 User and Profile Data)]
        D3[(D3 Training and Learning Data)]
        D4[(D4 Payment and Invoice Data)]
        D5[(D5 Certificate Data)]
        D6[(D6 Support, Logs, Notifications)]
    end

    E1 --> P1
    E2 --> P2
    E2 --> P3
    E2 --> P5
    E3 --> P2
    E3 --> P4
    E4 --> P6
    E7 --> P5

    P1 <--> D1
    P2 <--> D2
    P3 <--> D3
    P3 <--> D4
    P4 <--> D4
    P4 <--> D6
    P5 <--> D5
    P5 <--> D3
    P6 <--> D1
    P6 <--> D2
    P6 <--> D3
    P6 <--> D4
    P6 <--> D5
    P6 <--> D6

    P3 <--> E5
    P4 <--> E5
    P2 <--> E6
    P3 <--> E6
    P4 <--> E6
    P6 <--> E6
```

## DFD Level 2 - Training and Payment Subsystem

```mermaid
flowchart TD
    U[Trainee User]
    B[Training Batch Detail]
    EN((Create Enrollment))
    CO((Checkout))
    RP[Razorpay Order API]
    VP((Verify Payment))
    WH((Webhook Handler))
    MY[My Enrollments Page]

    S1[(TrainingBatch, BatchPrice)]
    S2[(Enrollment)]
    S3[(Order)]
    S4[(UserNotification, EmailDeliveryLog)]

    U --> B --> EN
    EN --> S2
    EN --> CO
    CO --> S1
    CO --> S2
    CO --> RP
    RP --> S3
    U --> VP
    VP --> S3
    VP --> S2
    VP --> S4
    RP --> WH
    WH --> S3
    WH --> S2
    WH --> S4
    S2 --> MY
    S3 --> MY
```

## DFD Level 2 - Certificate Verification Subsystem

```mermaid
flowchart TD
    TV[Trainee Certificate View]
    DL[Certificate Download PDF]
    VF[Public Verify Form]
    TK((Verify by Token))
    MC((Verify by Number and Code))
    LEG((Legacy SQLite Fallback))
    RES[Verification Result Page]

    C1[(TraineeCertificate)]
    C2[(Enrollment plus Batch plus User)]
    C3[(Legacy SQLite DB optional)]

    TV --> C1
    TV --> C2
    DL --> C1
    DL --> C2

    VF --> TK
    VF --> MC
    TK --> C1
    MC --> C1
    TK --> LEG
    MC --> LEG
    LEG --> C3
    C1 --> RES
    C2 --> RES
    C3 --> RES
```

## Data Store Mapping

| Store | Main Tables/Models |
|---|---|
| D1 CMS Data | `Page`, `TrustedCompany`, `Post`, `PostCategory`, `TemplateItem`, `TemplateCategory`, `Project`, `ProjectCategory`, `PortfolioItem`, `TeamMember`, `ContactMessage` |
| D2 User and Profile Data | `auth_user`, `UserProfile`, `Role`, `NotificationPreference` |
| D3 Training and Learning Data | `TrainingBatch`, `BatchPrice`, `Enrollment`, `TraineeSession`, `TraineeAttendance`, `TraineeAssignment`, `TraineeAssignmentSubmission`, `TraineeBookmark`, trainee reviews |
| D4 Payment and Invoice Data | `Order`, `Invoice`, `InvoicePayment`, appointment payment flags |
| D5 Certificate Data | `TraineeCertificate` with verification token/code logic |
| D6 Support, Logs, Notifications | `TraineeSupportTicket`, `UserNotification`, `EmailDeliveryLog`, `AdminActivityLog`, `WebsiteErrorLog`, `AdminIPBlock` |

