# OurEvent Platform - Database Schema

## Entity Relationship Diagram (PlantUML)

```plantuml
@startuml OurEvent_Database_Schema

!define PRIMARY_KEY(x) <b><color:#b8860b><&key></color> x</b>
!define FOREIGN_KEY(x) <color:#aaaaaa><&key></color> x
!define COLUMN(x) <color:#efefef><&media-record></color> x
!define TABLE(x) entity x << (T,#FFAAAA) >>

skinparam backgroundColor #1a1a2e
skinparam class {
    BackgroundColor #16213e
    BorderColor #0f3460
    ArrowColor #e94560
    FontColor #ffffff
}

' =============================================
' USER & AUTHENTICATION
' =============================================

TABLE(users) {
    PRIMARY_KEY(id): UUID
    --
    COLUMN(email): VARCHAR(255) <<unique, not null>>
    COLUMN(password_hash): VARCHAR(255) <<not null>>
    COLUMN(name): VARCHAR(255) <<not null>>
    COLUMN(phone): VARCHAR(20)
    COLUMN(avatar_url): VARCHAR(500)
    COLUMN(role): ENUM('admin','organizer','user') <<default 'user'>>
    COLUMN(is_verified): BOOLEAN <<default false>>
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(email_verified_at): TIMESTAMP
    COLUMN(last_login_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(user_sessions) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    COLUMN(token): VARCHAR(500) <<unique, not null>>
    COLUMN(device_info): JSON
    COLUMN(ip_address): VARCHAR(45)
    COLUMN(expires_at): TIMESTAMP <<not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(password_resets) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    COLUMN(token): VARCHAR(255) <<unique, not null>>
    COLUMN(expires_at): TIMESTAMP <<not null>>
    COLUMN(used_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' ORGANIZERS
' =============================================

TABLE(organizers) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<unique, not null>>
    COLUMN(company_name): VARCHAR(255) <<not null>>
    COLUMN(company_name_ar): VARCHAR(255)
    COLUMN(email): VARCHAR(255) <<not null>>
    COLUMN(phone): VARCHAR(20)
    COLUMN(logo_url): VARCHAR(500)
    COLUMN(cover_image_url): VARCHAR(500)
    COLUMN(description): TEXT
    COLUMN(description_ar): TEXT
    COLUMN(website): VARCHAR(255)
    COLUMN(twitter): VARCHAR(255)
    COLUMN(instagram): VARCHAR(255)
    COLUMN(linkedin): VARCHAR(255)
    COLUMN(commercial_register): VARCHAR(50)
    COLUMN(tax_number): VARCHAR(50)
    COLUMN(bank_name): VARCHAR(100)
    COLUMN(bank_iban): VARCHAR(34)
    COLUMN(bank_account_name): VARCHAR(255)
    COLUMN(is_verified): BOOLEAN <<default false>>
    COLUMN(verified_at): TIMESTAMP
    COLUMN(rating): DECIMAL(3,2) <<default 0>>
    COLUMN(total_events): INT <<default 0>>
    COLUMN(total_attendees): INT <<default 0>>
    COLUMN(total_revenue): DECIMAL(12,2) <<default 0>>
    COLUMN(commission_rate): DECIMAL(5,2) <<default 10>>
    COLUMN(status): ENUM('pending','active','suspended') <<default 'pending'>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(organizer_applications) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    COLUMN(company_name): VARCHAR(255) <<not null>>
    COLUMN(description): TEXT
    COLUMN(commercial_register): VARCHAR(50)
    COLUMN(status): ENUM('pending','approved','rejected') <<default 'pending'>>
    COLUMN(admin_notes): TEXT
    COLUMN(reviewed_by): UUID
    COLUMN(reviewed_at): TIMESTAMP
    COLUMN(submitted_at): TIMESTAMP <<not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(organizer_documents) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(organizer_id): UUID <<not null>>
    FOREIGN_KEY(application_id): UUID
    COLUMN(name): VARCHAR(255) <<not null>>
    COLUMN(file_url): VARCHAR(500) <<not null>>
    COLUMN(file_type): VARCHAR(50)
    COLUMN(file_size): INT
    COLUMN(document_type): ENUM('license','id','commercial_register','other')
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' GEOGRAPHIC & LOCATION
' =============================================

TABLE(regions) {
    PRIMARY_KEY(id): UUID
    --
    COLUMN(name): VARCHAR(100) <<not null>>
    COLUMN(name_ar): VARCHAR(100) <<not null>>
    COLUMN(code): VARCHAR(10) <<unique>>
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(cities) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(region_id): UUID <<not null>>
    COLUMN(name): VARCHAR(100) <<not null>>
    COLUMN(name_ar): VARCHAR(100) <<not null>>
    COLUMN(code): VARCHAR(10) <<unique>>
    COLUMN(latitude): DECIMAL(10,8)
    COLUMN(longitude): DECIMAL(11,8)
    COLUMN(timezone): VARCHAR(50) <<default 'Asia/Riyadh'>>
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(venues) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(city_id): UUID <<not null>>
    FOREIGN_KEY(organizer_id): UUID
    COLUMN(name): VARCHAR(255) <<not null>>
    COLUMN(name_ar): VARCHAR(255) <<not null>>
    COLUMN(description): TEXT
    COLUMN(description_ar): TEXT
    COLUMN(address): VARCHAR(500) <<not null>>
    COLUMN(address_ar): VARCHAR(500)
    COLUMN(latitude): DECIMAL(10,8)
    COLUMN(longitude): DECIMAL(11,8)
    COLUMN(capacity): INT <<not null>>
    COLUMN(contact_phone): VARCHAR(20)
    COLUMN(contact_email): VARCHAR(255)
    COLUMN(website): VARCHAR(255)
    COLUMN(google_maps_url): VARCHAR(500)
    COLUMN(is_verified): BOOLEAN <<default false>>
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(venue_images) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(venue_id): UUID <<not null>>
    COLUMN(image_url): VARCHAR(500) <<not null>>
    COLUMN(alt_text): VARCHAR(255)
    COLUMN(is_primary): BOOLEAN <<default false>>
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(venue_amenities) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(venue_id): UUID <<not null>>
    COLUMN(name): VARCHAR(100) <<not null>>
    COLUMN(name_ar): VARCHAR(100)
    COLUMN(icon): VARCHAR(50)
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' EVENT TYPES & CATEGORIES
' =============================================

TABLE(event_types) {
    PRIMARY_KEY(id): UUID
    --
    COLUMN(name): VARCHAR(100) <<not null>>
    COLUMN(name_ar): VARCHAR(100) <<not null>>
    COLUMN(slug): VARCHAR(100) <<unique, not null>>
    COLUMN(description): TEXT
    COLUMN(description_ar): TEXT
    COLUMN(icon): VARCHAR(50)
    COLUMN(color): VARCHAR(7)
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(event_tags) {
    PRIMARY_KEY(id): UUID
    --
    COLUMN(name): VARCHAR(50) <<not null>>
    COLUMN(name_ar): VARCHAR(50)
    COLUMN(slug): VARCHAR(50) <<unique, not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' EVENTS
' =============================================

TABLE(events) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(organizer_id): UUID <<not null>>
    FOREIGN_KEY(type_id): UUID <<not null>>
    FOREIGN_KEY(venue_id): UUID
    FOREIGN_KEY(city_id): UUID <<not null>>
    COLUMN(title): VARCHAR(255) <<not null>>
    COLUMN(title_ar): VARCHAR(255)
    COLUMN(slug): VARCHAR(300) <<unique, not null>>
    COLUMN(description): TEXT <<not null>>
    COLUMN(description_ar): TEXT
    COLUMN(short_description): VARCHAR(500)
    COLUMN(short_description_ar): VARCHAR(500)
    COLUMN(cover_image_url): VARCHAR(500)
    COLUMN(address): VARCHAR(500)
    COLUMN(address_ar): VARCHAR(500)
    COLUMN(latitude): DECIMAL(10,8)
    COLUMN(longitude): DECIMAL(11,8)
    COLUMN(date_start): TIMESTAMP <<not null>>
    COLUMN(date_end): TIMESTAMP <<not null>>
    COLUMN(registration_start): TIMESTAMP
    COLUMN(registration_end): TIMESTAMP
    COLUMN(max_attendees): INT
    COLUMN(current_attendees): INT <<default 0>>
    COLUMN(is_paid): BOOLEAN <<default false>>
    COLUMN(min_price): DECIMAL(10,2) <<default 0>>
    COLUMN(max_price): DECIMAL(10,2) <<default 0>>
    COLUMN(terms_and_conditions): TEXT
    COLUMN(terms_and_conditions_ar): TEXT
    COLUMN(refund_policy): TEXT
    COLUMN(refund_policy_ar): TEXT
    COLUMN(status): ENUM('draft','pending','published','closed','completed','cancelled') <<default 'draft'>>
    COLUMN(is_featured): BOOLEAN <<default false>>
    COLUMN(featured_until): TIMESTAMP
    COLUMN(views_count): INT <<default 0>>
    COLUMN(likes_count): INT <<default 0>>
    COLUMN(shares_count): INT <<default 0>>
    COLUMN(published_at): TIMESTAMP
    COLUMN(cancelled_at): TIMESTAMP
    COLUMN(cancellation_reason): TEXT
    COLUMN(admin_notes): TEXT
    COLUMN(approved_by): UUID
    COLUMN(approved_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
    COLUMN(deleted_at): TIMESTAMP
}

TABLE(event_images) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    COLUMN(image_url): VARCHAR(500) <<not null>>
    COLUMN(thumbnail_url): VARCHAR(500)
    COLUMN(alt_text): VARCHAR(255)
    COLUMN(is_cover): BOOLEAN <<default false>>
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(event_attachments) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    COLUMN(name): VARCHAR(255) <<not null>>
    COLUMN(file_url): VARCHAR(500) <<not null>>
    COLUMN(file_type): ENUM('pdf','image','document','other')
    COLUMN(file_size): INT
    COLUMN(is_public): BOOLEAN <<default true>>
    COLUMN(download_count): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(event_tag_mapping) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    FOREIGN_KEY(tag_id): UUID <<not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' TICKETS
' =============================================

TABLE(ticket_tiers) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    COLUMN(name): VARCHAR(100) <<not null>>
    COLUMN(name_ar): VARCHAR(100)
    COLUMN(type): ENUM('vip','standard','free') <<default 'standard'>>
    COLUMN(description): TEXT
    COLUMN(description_ar): TEXT
    COLUMN(price): DECIMAL(10,2) <<not null>>
    COLUMN(original_price): DECIMAL(10,2)
    COLUMN(quantity): INT <<not null>>
    COLUMN(sold): INT <<default 0>>
    COLUMN(reserved): INT <<default 0>>
    COLUMN(max_per_order): INT <<default 10>>
    COLUMN(min_per_order): INT <<default 1>>
    COLUMN(sale_start): TIMESTAMP
    COLUMN(sale_end): TIMESTAMP
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(ticket_tier_benefits) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(tier_id): UUID <<not null>>
    COLUMN(benefit): VARCHAR(255) <<not null>>
    COLUMN(benefit_ar): VARCHAR(255)
    COLUMN(icon): VARCHAR(50)
    COLUMN(sort_order): INT <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(tickets) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    FOREIGN_KEY(tier_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(registration_id): UUID <<not null>>
    FOREIGN_KEY(order_id): UUID <<not null>>
    COLUMN(ticket_number): VARCHAR(20) <<unique, not null>>
    COLUMN(qr_code): VARCHAR(500) <<unique, not null>>
    COLUMN(barcode): VARCHAR(50)
    COLUMN(attendee_name): VARCHAR(255)
    COLUMN(attendee_email): VARCHAR(255)
    COLUMN(attendee_phone): VARCHAR(20)
    COLUMN(seat_number): VARCHAR(20)
    COLUMN(seat_section): VARCHAR(50)
    COLUMN(price): DECIMAL(10,2) <<not null>>
    COLUMN(discount_amount): DECIMAL(10,2) <<default 0>>
    COLUMN(status): ENUM('active','used','cancelled','expired','refunded') <<default 'active'>>
    COLUMN(checked_in_at): TIMESTAMP
    COLUMN(checked_in_by): UUID
    COLUMN(cancelled_at): TIMESTAMP
    COLUMN(refunded_at): TIMESTAMP
    COLUMN(refund_amount): DECIMAL(10,2)
    COLUMN(notes): TEXT
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' ORDERS & PAYMENTS
' =============================================

TABLE(orders) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(event_id): UUID <<not null>>
    COLUMN(order_number): VARCHAR(20) <<unique, not null>>
    COLUMN(subtotal): DECIMAL(12,2) <<not null>>
    COLUMN(discount_amount): DECIMAL(12,2) <<default 0>>
    COLUMN(service_fee): DECIMAL(10,2) <<default 0>>
    COLUMN(tax_amount): DECIMAL(10,2) <<default 0>>
    COLUMN(total_amount): DECIMAL(12,2) <<not null>>
    COLUMN(currency): VARCHAR(3) <<default 'SAR'>>
    COLUMN(status): ENUM('pending','processing','completed','cancelled','refunded','partially_refunded') <<default 'pending'>>
    COLUMN(payment_status): ENUM('pending','paid','failed','refunded','partially_refunded') <<default 'pending'>>
    COLUMN(payment_method): VARCHAR(50)
    COLUMN(payment_gateway): VARCHAR(50)
    COLUMN(gateway_transaction_id): VARCHAR(255)
    COLUMN(gateway_response): JSON
    COLUMN(billing_name): VARCHAR(255)
    COLUMN(billing_email): VARCHAR(255)
    COLUMN(billing_phone): VARCHAR(20)
    COLUMN(coupon_code): VARCHAR(50)
    COLUMN(ip_address): VARCHAR(45)
    COLUMN(user_agent): TEXT
    COLUMN(notes): TEXT
    COLUMN(paid_at): TIMESTAMP
    COLUMN(cancelled_at): TIMESTAMP
    COLUMN(refunded_at): TIMESTAMP
    COLUMN(expires_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(order_items) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(order_id): UUID <<not null>>
    FOREIGN_KEY(tier_id): UUID <<not null>>
    COLUMN(quantity): INT <<not null>>
    COLUMN(unit_price): DECIMAL(10,2) <<not null>>
    COLUMN(total_price): DECIMAL(10,2) <<not null>>
    COLUMN(discount_amount): DECIMAL(10,2) <<default 0>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(payments) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(order_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    COLUMN(amount): DECIMAL(12,2) <<not null>>
    COLUMN(currency): VARCHAR(3) <<default 'SAR'>>
    COLUMN(payment_method): VARCHAR(50) <<not null>>
    COLUMN(gateway): VARCHAR(50) <<not null>>
    COLUMN(gateway_transaction_id): VARCHAR(255)
    COLUMN(gateway_response): JSON
    COLUMN(card_last_four): VARCHAR(4)
    COLUMN(card_brand): VARCHAR(20)
    COLUMN(status): ENUM('pending','processing','completed','failed','refunded') <<default 'pending'>>
    COLUMN(failure_reason): TEXT
    COLUMN(refund_id): VARCHAR(255)
    COLUMN(refund_amount): DECIMAL(12,2)
    COLUMN(refunded_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(refunds) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(order_id): UUID <<not null>>
    FOREIGN_KEY(payment_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(processed_by): UUID
    COLUMN(amount): DECIMAL(12,2) <<not null>>
    COLUMN(reason): TEXT
    COLUMN(status): ENUM('pending','approved','rejected','processed') <<default 'pending'>>
    COLUMN(gateway_refund_id): VARCHAR(255)
    COLUMN(admin_notes): TEXT
    COLUMN(processed_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' REGISTRATIONS
' =============================================

TABLE(registrations) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(order_id): UUID
    COLUMN(registration_number): VARCHAR(20) <<unique, not null>>
    COLUMN(attendee_count): INT <<default 1>>
    COLUMN(total_amount): DECIMAL(12,2) <<not null>>
    COLUMN(status): ENUM('pending','confirmed','cancelled','attended','no_show') <<default 'pending'>>
    COLUMN(payment_status): ENUM('pending','completed','failed','refunded') <<default 'pending'>>
    COLUMN(check_in_status): ENUM('not_checked','checked_in','checked_out') <<default 'not_checked'>>
    COLUMN(checked_in_at): TIMESTAMP
    COLUMN(checked_out_at): TIMESTAMP
    COLUMN(source): VARCHAR(50) <<default 'web'>>
    COLUMN(notes): TEXT
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' COUPONS & DISCOUNTS
' =============================================

TABLE(coupons) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(organizer_id): UUID
    FOREIGN_KEY(event_id): UUID
    COLUMN(code): VARCHAR(50) <<unique, not null>>
    COLUMN(name): VARCHAR(100)
    COLUMN(description): TEXT
    COLUMN(discount_type): ENUM('percentage','fixed') <<not null>>
    COLUMN(discount_value): DECIMAL(10,2) <<not null>>
    COLUMN(max_discount): DECIMAL(10,2)
    COLUMN(min_order_amount): DECIMAL(10,2)
    COLUMN(max_uses): INT
    COLUMN(max_uses_per_user): INT <<default 1>>
    COLUMN(used_count): INT <<default 0>>
    COLUMN(starts_at): TIMESTAMP
    COLUMN(expires_at): TIMESTAMP
    COLUMN(is_active): BOOLEAN <<default true>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(coupon_usage) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(coupon_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(order_id): UUID <<not null>>
    COLUMN(discount_amount): DECIMAL(10,2) <<not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

' =============================================
' USER INTERACTIONS
' =============================================

TABLE(favorites) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(event_id): UUID <<not null>>
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(event_views) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID
    COLUMN(session_id): VARCHAR(100)
    COLUMN(ip_address): VARCHAR(45)
    COLUMN(user_agent): TEXT
    COLUMN(referrer): VARCHAR(500)
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(reviews) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(event_id): UUID <<not null>>
    FOREIGN_KEY(user_id): UUID <<not null>>
    FOREIGN_KEY(registration_id): UUID <<not null>>
    COLUMN(rating): INT <<not null>>
    COLUMN(title): VARCHAR(255)
    COLUMN(comment): TEXT
    COLUMN(is_verified): BOOLEAN <<default false>>
    COLUMN(is_visible): BOOLEAN <<default true>>
    COLUMN(admin_response): TEXT
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' NOTIFICATIONS
' =============================================

TABLE(notifications) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<not null>>
    COLUMN(type): VARCHAR(50) <<not null>>
    COLUMN(title): VARCHAR(255) <<not null>>
    COLUMN(title_ar): VARCHAR(255)
    COLUMN(message): TEXT <<not null>>
    COLUMN(message_ar): TEXT
    COLUMN(data): JSON
    COLUMN(action_url): VARCHAR(500)
    COLUMN(is_read): BOOLEAN <<default false>>
    COLUMN(read_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(notification_preferences) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(user_id): UUID <<unique, not null>>
    COLUMN(email_new_events): BOOLEAN <<default true>>
    COLUMN(email_event_reminders): BOOLEAN <<default true>>
    COLUMN(email_promotions): BOOLEAN <<default true>>
    COLUMN(email_newsletter): BOOLEAN <<default true>>
    COLUMN(push_new_events): BOOLEAN <<default true>>
    COLUMN(push_event_reminders): BOOLEAN <<default true>>
    COLUMN(push_order_updates): BOOLEAN <<default true>>
    COLUMN(sms_event_reminders): BOOLEAN <<default false>>
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' ADMIN & SYSTEM
' =============================================

TABLE(admin_activity_logs) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(admin_id): UUID <<not null>>
    COLUMN(action): VARCHAR(100) <<not null>>
    COLUMN(entity_type): VARCHAR(50)
    COLUMN(entity_id): UUID
    COLUMN(old_values): JSON
    COLUMN(new_values): JSON
    COLUMN(ip_address): VARCHAR(45)
    COLUMN(user_agent): TEXT
    COLUMN(created_at): TIMESTAMP <<not null>>
}

TABLE(system_settings) {
    PRIMARY_KEY(id): UUID
    --
    COLUMN(key): VARCHAR(100) <<unique, not null>>
    COLUMN(value): TEXT
    COLUMN(type): VARCHAR(20) <<default 'string'>>
    COLUMN(group): VARCHAR(50)
    COLUMN(description): TEXT
    COLUMN(is_public): BOOLEAN <<default false>>
    COLUMN(updated_by): UUID
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

TABLE(payouts) {
    PRIMARY_KEY(id): UUID
    --
    FOREIGN_KEY(organizer_id): UUID <<not null>>
    COLUMN(amount): DECIMAL(12,2) <<not null>>
    COLUMN(currency): VARCHAR(3) <<default 'SAR'>>
    COLUMN(bank_name): VARCHAR(100)
    COLUMN(bank_iban): VARCHAR(34)
    COLUMN(bank_account_name): VARCHAR(255)
    COLUMN(status): ENUM('pending','processing','completed','failed') <<default 'pending'>>
    COLUMN(reference_number): VARCHAR(100)
    COLUMN(notes): TEXT
    COLUMN(processed_by): UUID
    COLUMN(processed_at): TIMESTAMP
    COLUMN(created_at): TIMESTAMP <<not null>>
    COLUMN(updated_at): TIMESTAMP <<not null>>
}

' =============================================
' RELATIONSHIPS
' =============================================

' User relationships
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

' Organizer relationships
organizers ||--o{ events : "creates"
organizers ||--o{ organizer_documents : "has"
organizers ||--o{ venues : "owns"
organizers ||--o{ coupons : "creates"
organizers ||--o{ payouts : "receives"

' Geographic relationships
regions ||--o{ cities : "contains"
cities ||--o{ venues : "has"
cities ||--o{ events : "hosts"

' Venue relationships
venues ||--o{ venue_images : "has"
venues ||--o{ venue_amenities : "has"
venues ||--o{ events : "hosts"

' Event relationships
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
event_types ||--o{ events : "categorizes"
event_tags ||--o{ event_tag_mapping : "used in"

' Ticket relationships
ticket_tiers ||--o{ ticket_tier_benefits : "has"
ticket_tiers ||--o{ tickets : "generates"
ticket_tiers ||--o{ order_items : "ordered as"

' Order relationships
orders ||--o{ order_items : "contains"
orders ||--o{ payments : "has"
orders ||--o{ tickets : "generates"
orders ||--o{ refunds : "may have"
orders ||--o| registrations : "creates"
orders ||--o{ coupon_usage : "may use"

' Coupon relationships
coupons ||--o{ coupon_usage : "tracked by"

@enduml
```

## Database Tables Summary

### Core Tables (28 tables)

| Category | Tables | Description |
|----------|--------|-------------|
| **Users & Auth** | `users`, `user_sessions`, `password_resets` | User management and authentication |
| **Organizers** | `organizers`, `organizer_applications`, `organizer_documents` | Event organizer management |
| **Geographic** | `regions`, `cities`, `venues`, `venue_images`, `venue_amenities` | Location and venue management |
| **Event Types** | `event_types`, `event_tags` | Event categorization |
| **Events** | `events`, `event_images`, `event_attachments`, `event_tag_mapping` | Core event data |
| **Tickets** | `ticket_tiers`, `ticket_tier_benefits`, `tickets` | Ticketing system |
| **Orders** | `orders`, `order_items`, `payments`, `refunds` | Order and payment processing |
| **Registrations** | `registrations` | Event registrations |
| **Coupons** | `coupons`, `coupon_usage` | Discount and promotion system |
| **User Interactions** | `favorites`, `event_views`, `reviews` | User engagement tracking |
| **Notifications** | `notifications`, `notification_preferences` | Notification system |
| **Admin** | `admin_activity_logs`, `system_settings`, `payouts` | System administration |

## Key Indexes Recommendations

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

-- Favorites
CREATE UNIQUE INDEX idx_favorites_user_event ON favorites(user_id, event_id);
```

## Notes

- All tables use **UUID** as primary keys for better security and distributed systems support
- **Soft deletes** implemented via `deleted_at` column where applicable
- **Audit columns** (`created_at`, `updated_at`) on all tables
- **Arabic support** with `_ar` suffix columns for bilingual content
- **JSON columns** for flexible data storage (gateway responses, metadata)
- **ENUM types** for status fields to ensure data integrity
