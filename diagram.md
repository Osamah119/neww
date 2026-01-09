# OurEvent Platform - Database Schema

> مخطط قاعدة البيانات الكامل لمنصة فعالياتنا

## Entity Relationship Diagram

```mermaid
erDiagram
    %% ==========================================
    %% USERS & AUTHENTICATION
    %% ==========================================
    
    users {
        uuid id PK
        varchar email UK "not null"
        varchar password_hash "not null"
        varchar name "not null"
        varchar phone
        varchar avatar_url
        enum role "admin|organizer|user"
        boolean is_verified "default false"
        boolean is_active "default true"
        timestamp email_verified_at
        timestamp last_login_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    user_sessions {
        uuid id PK
        uuid user_id FK "not null"
        varchar token UK "not null"
        json device_info
        varchar ip_address
        timestamp expires_at "not null"
        timestamp created_at "not null"
    }

    password_resets {
        uuid id PK
        uuid user_id FK "not null"
        varchar token UK "not null"
        timestamp expires_at "not null"
        timestamp used_at
        timestamp created_at "not null"
    }

    %% ==========================================
    %% ORGANIZERS
    %% ==========================================

    organizers {
        uuid id PK
        uuid user_id FK UK "not null"
        varchar company_name "not null"
        varchar company_name_ar
        varchar email "not null"
        varchar phone
        varchar logo_url
        text description
        text description_ar
        varchar website
        varchar twitter
        varchar instagram
        varchar commercial_register
        varchar tax_number
        varchar bank_name
        varchar bank_iban
        boolean is_verified "default false"
        decimal rating "default 0"
        int total_events "default 0"
        int total_attendees "default 0"
        decimal total_revenue "default 0"
        decimal commission_rate "default 10"
        enum status "pending|active|suspended"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    organizer_applications {
        uuid id PK
        uuid user_id FK "not null"
        varchar company_name "not null"
        text description
        varchar commercial_register
        enum status "pending|approved|rejected"
        text admin_notes
        uuid reviewed_by FK
        timestamp reviewed_at
        timestamp submitted_at "not null"
        timestamp created_at "not null"
    }

    organizer_documents {
        uuid id PK
        uuid organizer_id FK "not null"
        uuid application_id FK
        varchar name "not null"
        varchar file_url "not null"
        varchar file_type
        int file_size
        enum document_type "license|id|commercial|other"
        timestamp created_at "not null"
    }

    %% ==========================================
    %% GEOGRAPHIC & LOCATION
    %% ==========================================

    regions {
        uuid id PK
        varchar name "not null"
        varchar name_ar "not null"
        varchar code UK
        boolean is_active "default true"
        timestamp created_at "not null"
    }

    cities {
        uuid id PK
        uuid region_id FK "not null"
        varchar name "not null"
        varchar name_ar "not null"
        varchar code UK
        decimal latitude
        decimal longitude
        varchar timezone "default Asia/Riyadh"
        boolean is_active "default true"
        int sort_order "default 0"
        timestamp created_at "not null"
    }

    venues {
        uuid id PK
        uuid city_id FK "not null"
        uuid organizer_id FK
        varchar name "not null"
        varchar name_ar "not null"
        text description
        varchar address "not null"
        decimal latitude
        decimal longitude
        int capacity "not null"
        varchar contact_phone
        varchar contact_email
        varchar website
        varchar google_maps_url
        boolean is_verified "default false"
        boolean is_active "default true"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    venue_images {
        uuid id PK
        uuid venue_id FK "not null"
        varchar image_url "not null"
        varchar alt_text
        boolean is_primary "default false"
        int sort_order "default 0"
        timestamp created_at "not null"
    }

    venue_amenities {
        uuid id PK
        uuid venue_id FK "not null"
        varchar name "not null"
        varchar name_ar
        varchar icon
        timestamp created_at "not null"
    }

    %% ==========================================
    %% EVENT TYPES & CATEGORIES
    %% ==========================================

    event_types {
        uuid id PK
        varchar name "not null"
        varchar name_ar "not null"
        varchar slug UK "not null"
        text description
        varchar icon
        varchar color
        boolean is_active "default true"
        int sort_order "default 0"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    event_tags {
        uuid id PK
        varchar name "not null"
        varchar name_ar
        varchar slug UK "not null"
        timestamp created_at "not null"
    }

    %% ==========================================
    %% EVENTS
    %% ==========================================

    events {
        uuid id PK
        uuid organizer_id FK "not null"
        uuid type_id FK "not null"
        uuid venue_id FK
        uuid city_id FK "not null"
        varchar title "not null"
        varchar title_ar
        varchar slug UK "not null"
        text description "not null"
        text short_description
        varchar cover_image_url
        varchar address
        decimal latitude
        decimal longitude
        timestamp date_start "not null"
        timestamp date_end "not null"
        timestamp registration_start
        timestamp registration_end
        int max_attendees
        int current_attendees "default 0"
        boolean is_paid "default false"
        decimal min_price "default 0"
        decimal max_price "default 0"
        text terms_and_conditions
        text refund_policy
        enum status "draft|pending|published|closed|completed|cancelled"
        boolean is_featured "default false"
        timestamp featured_until
        int views_count "default 0"
        int likes_count "default 0"
        timestamp published_at
        timestamp cancelled_at
        text cancellation_reason
        uuid approved_by FK
        timestamp approved_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
        timestamp deleted_at
    }

    event_images {
        uuid id PK
        uuid event_id FK "not null"
        varchar image_url "not null"
        varchar thumbnail_url
        varchar alt_text
        boolean is_cover "default false"
        int sort_order "default 0"
        timestamp created_at "not null"
    }

    event_attachments {
        uuid id PK
        uuid event_id FK "not null"
        varchar name "not null"
        varchar file_url "not null"
        enum file_type "pdf|image|document|other"
        int file_size
        boolean is_public "default true"
        int download_count "default 0"
        timestamp created_at "not null"
    }

    event_tag_mapping {
        uuid id PK
        uuid event_id FK "not null"
        uuid tag_id FK "not null"
        timestamp created_at "not null"
    }

    %% ==========================================
    %% TICKETS
    %% ==========================================

    ticket_tiers {
        uuid id PK
        uuid event_id FK "not null"
        varchar name "not null"
        varchar name_ar
        enum type "vip|standard|free"
        text description
        decimal price "not null"
        decimal original_price
        int quantity "not null"
        int sold "default 0"
        int reserved "default 0"
        int max_per_order "default 10"
        int min_per_order "default 1"
        timestamp sale_start
        timestamp sale_end
        boolean is_active "default true"
        int sort_order "default 0"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    ticket_tier_benefits {
        uuid id PK
        uuid tier_id FK "not null"
        varchar benefit "not null"
        varchar benefit_ar
        varchar icon
        int sort_order "default 0"
        timestamp created_at "not null"
    }

    tickets {
        uuid id PK
        uuid event_id FK "not null"
        uuid tier_id FK "not null"
        uuid user_id FK "not null"
        uuid registration_id FK "not null"
        uuid order_id FK "not null"
        varchar ticket_number UK "not null"
        varchar qr_code UK "not null"
        varchar barcode
        varchar attendee_name
        varchar attendee_email
        varchar attendee_phone
        varchar seat_number
        varchar seat_section
        decimal price "not null"
        decimal discount_amount "default 0"
        enum status "active|used|cancelled|expired|refunded"
        timestamp checked_in_at
        uuid checked_in_by FK
        timestamp cancelled_at
        timestamp refunded_at
        decimal refund_amount
        text notes
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% ORDERS & PAYMENTS
    %% ==========================================

    orders {
        uuid id PK
        uuid user_id FK "not null"
        uuid event_id FK "not null"
        varchar order_number UK "not null"
        decimal subtotal "not null"
        decimal discount_amount "default 0"
        decimal service_fee "default 0"
        decimal tax_amount "default 0"
        decimal total_amount "not null"
        varchar currency "default SAR"
        enum status "pending|processing|completed|cancelled|refunded"
        enum payment_status "pending|paid|failed|refunded"
        varchar payment_method
        varchar payment_gateway
        varchar gateway_transaction_id
        json gateway_response
        varchar billing_name
        varchar billing_email
        varchar billing_phone
        varchar coupon_code
        varchar ip_address
        text notes
        timestamp paid_at
        timestamp cancelled_at
        timestamp refunded_at
        timestamp expires_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    order_items {
        uuid id PK
        uuid order_id FK "not null"
        uuid tier_id FK "not null"
        int quantity "not null"
        decimal unit_price "not null"
        decimal total_price "not null"
        decimal discount_amount "default 0"
        timestamp created_at "not null"
    }

    payments {
        uuid id PK
        uuid order_id FK "not null"
        uuid user_id FK "not null"
        decimal amount "not null"
        varchar currency "default SAR"
        varchar payment_method "not null"
        varchar gateway "not null"
        varchar gateway_transaction_id
        json gateway_response
        varchar card_last_four
        varchar card_brand
        enum status "pending|processing|completed|failed|refunded"
        text failure_reason
        varchar refund_id
        decimal refund_amount
        timestamp refunded_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    refunds {
        uuid id PK
        uuid order_id FK "not null"
        uuid payment_id FK "not null"
        uuid user_id FK "not null"
        uuid processed_by FK
        decimal amount "not null"
        text reason
        enum status "pending|approved|rejected|processed"
        varchar gateway_refund_id
        text admin_notes
        timestamp processed_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% REGISTRATIONS
    %% ==========================================

    registrations {
        uuid id PK
        uuid event_id FK "not null"
        uuid user_id FK "not null"
        uuid order_id FK
        varchar registration_number UK "not null"
        int attendee_count "default 1"
        decimal total_amount "not null"
        enum status "pending|confirmed|cancelled|attended|no_show"
        enum payment_status "pending|completed|failed|refunded"
        enum check_in_status "not_checked|checked_in|checked_out"
        timestamp checked_in_at
        timestamp checked_out_at
        varchar source "default web"
        text notes
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% COUPONS & DISCOUNTS
    %% ==========================================

    coupons {
        uuid id PK
        uuid organizer_id FK
        uuid event_id FK
        varchar code UK "not null"
        varchar name
        text description
        enum discount_type "percentage|fixed"
        decimal discount_value "not null"
        decimal max_discount
        decimal min_order_amount
        int max_uses
        int max_uses_per_user "default 1"
        int used_count "default 0"
        timestamp starts_at
        timestamp expires_at
        boolean is_active "default true"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    coupon_usage {
        uuid id PK
        uuid coupon_id FK "not null"
        uuid user_id FK "not null"
        uuid order_id FK "not null"
        decimal discount_amount "not null"
        timestamp created_at "not null"
    }

    %% ==========================================
    %% USER INTERACTIONS
    %% ==========================================

    favorites {
        uuid id PK
        uuid user_id FK "not null"
        uuid event_id FK "not null"
        timestamp created_at "not null"
    }

    event_views {
        uuid id PK
        uuid event_id FK "not null"
        uuid user_id FK
        varchar session_id
        varchar ip_address
        varchar referrer
        timestamp created_at "not null"
    }

    reviews {
        uuid id PK
        uuid event_id FK "not null"
        uuid user_id FK "not null"
        uuid registration_id FK "not null"
        int rating "not null"
        varchar title
        text comment
        boolean is_verified "default false"
        boolean is_visible "default true"
        text admin_response
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% NOTIFICATIONS
    %% ==========================================

    notifications {
        uuid id PK
        uuid user_id FK "not null"
        varchar type "not null"
        varchar title "not null"
        varchar title_ar
        text message "not null"
        text message_ar
        json data
        varchar action_url
        boolean is_read "default false"
        timestamp read_at
        timestamp created_at "not null"
    }

    notification_preferences {
        uuid id PK
        uuid user_id FK UK "not null"
        boolean email_new_events "default true"
        boolean email_event_reminders "default true"
        boolean email_promotions "default true"
        boolean email_newsletter "default true"
        boolean push_new_events "default true"
        boolean push_event_reminders "default true"
        boolean push_order_updates "default true"
        boolean sms_event_reminders "default false"
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% ADMIN & SYSTEM
    %% ==========================================

    admin_activity_logs {
        uuid id PK
        uuid admin_id FK "not null"
        varchar action "not null"
        varchar entity_type
        uuid entity_id
        json old_values
        json new_values
        varchar ip_address
        timestamp created_at "not null"
    }

    system_settings {
        uuid id PK
        varchar key UK "not null"
        text value
        varchar type "default string"
        varchar group
        text description
        boolean is_public "default false"
        uuid updated_by FK
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    payouts {
        uuid id PK
        uuid organizer_id FK "not null"
        decimal amount "not null"
        varchar currency "default SAR"
        varchar bank_name
        varchar bank_iban
        varchar bank_account_name
        enum status "pending|processing|completed|failed"
        varchar reference_number
        text notes
        uuid processed_by FK
        timestamp processed_at
        timestamp created_at "not null"
        timestamp updated_at "not null"
    }

    %% ==========================================
    %% RELATIONSHIPS
    %% ==========================================

    users ||--o{ user_sessions : "has"
    users ||--o{ password_resets : "has"
    users ||--o| organizers : "can be"
    users ||--o{ organizer_applications : "submits"
    users ||--o{ orders : "places"
    users ||--o{ tickets : "owns"
    users ||--o{ registrations : "makes"
    users ||--o{ favorites : "has"
    users ||--o{ reviews : "writes"
    users ||--o{ notifications : "receives"
    users ||--o| notification_preferences : "has"

    organizers ||--o{ events : "creates"
    organizers ||--o{ organizer_documents : "has"
    organizers ||--o{ venues : "owns"
    organizers ||--o{ coupons : "creates"
    organizers ||--o{ payouts : "receives"

    regions ||--o{ cities : "contains"
    cities ||--o{ venues : "has"
    cities ||--o{ events : "hosts"

    venues ||--o{ venue_images : "has"
    venues ||--o{ venue_amenities : "has"
    venues ||--o{ events : "hosts"

    event_types ||--o{ events : "categorizes"
    event_tags ||--o{ event_tag_mapping : "used in"

    events ||--o{ event_images : "has"
    events ||--o{ event_attachments : "has"
    events ||--o{ event_tag_mapping : "has"
    events ||--o{ ticket_tiers : "offers"
    events ||--o{ orders : "generates"
    events ||--o{ registrations : "has"
    events ||--o{ favorites : "favorited by"
    events ||--o{ event_views : "has"
    events ||--o{ reviews : "receives"
    events ||--o{ coupons : "has"

    ticket_tiers ||--o{ ticket_tier_benefits : "has"
    ticket_tiers ||--o{ tickets : "generates"
    ticket_tiers ||--o{ order_items : "ordered as"

    orders ||--o{ order_items : "contains"
    orders ||--o{ payments : "has"
    orders ||--o{ tickets : "generates"
    orders ||--o{ refunds : "may have"
    orders ||--o| registrations : "creates"
    orders ||--o{ coupon_usage : "may use"

    coupons ||--o{ coupon_usage : "tracked by"
```

---

## 📊 Database Tables Summary

### Core Tables (28 tables)

| Category | Tables | Description |
|----------|--------|-------------|
| **👤 Users & Auth** | `users`, `user_sessions`, `password_resets` | User management and authentication |
| **🏢 Organizers** | `organizers`, `organizer_applications`, `organizer_documents` | Event organizer management |
| **📍 Geographic** | `regions`, `cities`, `venues`, `venue_images`, `venue_amenities` | Location and venue management |
| **🏷️ Event Types** | `event_types`, `event_tags` | Event categorization |
| **🎉 Events** | `events`, `event_images`, `event_attachments`, `event_tag_mapping` | Core event data |
| **🎫 Tickets** | `ticket_tiers`, `ticket_tier_benefits`, `tickets` | Ticketing system |
| **🛒 Orders** | `orders`, `order_items`, `payments`, `refunds` | Order and payment processing |
| **📝 Registrations** | `registrations` | Event registrations |
| **🎁 Coupons** | `coupons`, `coupon_usage` | Discount and promotion system |
| **❤️ User Interactions** | `favorites`, `event_views`, `reviews` | User engagement tracking |
| **🔔 Notifications** | `notifications`, `notification_preferences` | Notification system |
| **⚙️ Admin** | `admin_activity_logs`, `system_settings`, `payouts` | System administration |

---

## 🔑 Key Indexes

```sql
-- Users
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_users_is_active ON users(is_active);

-- Events
CREATE INDEX idx_events_organizer ON events(organizer_id);
CREATE INDEX idx_events_city ON events(city_id);
CREATE INDEX idx_events_type ON events(type_id);
CREATE INDEX idx_events_status ON events(status);
CREATE INDEX idx_events_date_start ON events(date_start);
CREATE INDEX idx_events_is_featured ON events(is_featured);
CREATE INDEX idx_events_slug ON events(slug);

-- Tickets
CREATE INDEX idx_tickets_event ON tickets(event_id);
CREATE INDEX idx_tickets_user ON tickets(user_id);
CREATE INDEX idx_tickets_status ON tickets(status);
CREATE INDEX idx_tickets_number ON tickets(ticket_number);
CREATE INDEX idx_tickets_qr ON tickets(qr_code);

-- Orders
CREATE INDEX idx_orders_user ON orders(user_id);
CREATE INDEX idx_orders_event ON orders(event_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_number ON orders(order_number);

-- Registrations
CREATE INDEX idx_registrations_event ON registrations(event_id);
CREATE INDEX idx_registrations_user ON registrations(user_id);

-- Favorites (Unique constraint)
CREATE UNIQUE INDEX idx_favorites_user_event ON favorites(user_id, event_id);
```

---

## 📋 Data Types Reference

| Type | Usage |
|------|-------|
| `UUID` | Primary keys, foreign keys |
| `VARCHAR(n)` | Short text fields |
| `TEXT` | Long text content |
| `DECIMAL(p,s)` | Money, ratings, coordinates |
| `INT` | Counts, quantities |
| `BOOLEAN` | Flags, toggles |
| `TIMESTAMP` | Dates, times |
| `JSON` | Flexible structured data |
| `ENUM` | Status fields, fixed options |

---

## 🔒 Security Notes

- **Passwords**: Stored as bcrypt hashes, never plain text
- **Tokens**: Session tokens use secure random generation
- **UUIDs**: Used for all IDs to prevent enumeration attacks
- **Soft Deletes**: `deleted_at` column preserves data integrity
- **Audit Trail**: `admin_activity_logs` tracks all admin actions

---

## 🌍 Internationalization

All user-facing text fields have Arabic counterparts:
- `name` / `name_ar`
- `description` / `description_ar`
- `title` / `title_ar`
- `message` / `message_ar`
