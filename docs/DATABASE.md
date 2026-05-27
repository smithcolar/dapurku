# 🗄️ DapurKu Database Schema

Complete PostgreSQL Database Design untuk Platform Ghost Kitchen UMKM Indonesia.

## 📋 Table of Contents

1. [User Management](#user-management)
2. [Seller Management](#seller-management)
3. [Product Management](#product-management)
4. [Order Management](#order-management)
5. [Payment & Transactions](#payment--transactions)
6. [Review & Rating](#review--rating)
7. [Verification](#verification)
8. [Relationships Diagram](#relationships-diagram)

---

## 👥 User Management

### users
Base table untuk semua user (seller, buyer, admin)

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  full_name VARCHAR(255) NOT NULL,
  phone VARCHAR(20) NOT NULL,
  user_type ENUM('seller', 'buyer', 'admin') NOT NULL,
  avatar_url VARCHAR(500),
  bio TEXT,
  is_verified BOOLEAN DEFAULT false,
  email_verified BOOLEAN DEFAULT false,
  email_verified_at TIMESTAMP,
  verification_code VARCHAR(6),
  verification_code_expires_at TIMESTAMP,
  is_active BOOLEAN DEFAULT true,
  last_login_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP,
  
  CONSTRAINT email_format CHECK (email ~ '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}$'),
  CONSTRAINT phone_format CHECK (phone ~ '^[0-9+]{10,}$')
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_user_type ON users(user_type);
CREATE INDEX idx_users_created_at ON users(created_at);
```

---

## 🏪 Seller Management

### sellers
Profile dan informasi seller/penjual

```sql
CREATE TABLE sellers (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
  shop_name VARCHAR(255) NOT NULL,
  shop_slug VARCHAR(255) UNIQUE NOT NULL,
  description TEXT,
  banner_url VARCHAR(500),
  category VARCHAR(100),
  address TEXT NOT NULL,
  city VARCHAR(100) NOT NULL,
  province VARCHAR(100) NOT NULL,
  postal_code VARCHAR(10),
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  service_radius_km INTEGER DEFAULT 5,
  bank_name VARCHAR(100),
  bank_account_number VARCHAR(20),
  bank_account_holder VARCHAR(255),
  rating DECIMAL(3, 2) DEFAULT 0,
  total_orders INTEGER DEFAULT 0,
  total_revenue BIGINT DEFAULT 0,
  is_verified BOOLEAN DEFAULT false,
  verification_status ENUM('pending', 'verified', 'rejected') DEFAULT 'pending',
  verified_at TIMESTAMP,
  operational_status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT rating_range CHECK (rating >= 0 AND rating <= 5)
);

CREATE INDEX idx_sellers_user_id ON sellers(user_id);
CREATE INDEX idx_sellers_city ON sellers(city);
CREATE INDEX idx_sellers_is_verified ON sellers(is_verified);
CREATE INDEX idx_sellers_location ON sellers(latitude, longitude);
CREATE INDEX idx_sellers_created_at ON sellers(created_at);
```

### seller_operating_hours
Jam operasional seller

```sql
CREATE TABLE seller_operating_hours (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  seller_id UUID NOT NULL REFERENCES sellers(id) ON DELETE CASCADE,
  day_of_week INTEGER CHECK (day_of_week >= 0 AND day_of_week <= 6),
  opening_time TIME,
  closing_time TIME,
  is_closed BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(seller_id, day_of_week)
);

CREATE INDEX idx_seller_operating_hours_seller_id ON seller_operating_hours(seller_id);
```

---

## 🏷️ Product Management

### categories
Kategori produk

```sql
CREATE TABLE categories (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name VARCHAR(100) NOT NULL UNIQUE,
  slug VARCHAR(100) UNIQUE NOT NULL,
  description TEXT,
  icon_url VARCHAR(500),
  parent_category_id UUID REFERENCES categories(id),
  is_active BOOLEAN DEFAULT true,
  sort_order INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT name_length CHECK (LENGTH(name) >= 2)
);

CREATE INDEX idx_categories_slug ON categories(slug);
CREATE INDEX idx_categories_parent_id ON categories(parent_category_id);
CREATE INDEX idx_categories_is_active ON categories(is_active);
```

### products
Produk/menu dari seller

```sql
CREATE TABLE products (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  seller_id UUID NOT NULL REFERENCES sellers(id) ON DELETE CASCADE,
  category_id UUID REFERENCES categories(id),
  name VARCHAR(255) NOT NULL,
  description TEXT,
  price BIGINT NOT NULL,
  cost BIGINT,
  stock INTEGER NOT NULL DEFAULT 0,
  preparation_time_minutes INTEGER,
  rating DECIMAL(3, 2) DEFAULT 0,
  total_reviews INTEGER DEFAULT 0,
  total_sold INTEGER DEFAULT 0,
  is_available BOOLEAN DEFAULT true,
  is_featured BOOLEAN DEFAULT false,
  tags TEXT[] DEFAULT ARRAY[]::text[],
  dietary_restrictions TEXT[] DEFAULT ARRAY[]::text[],
  ingredients TEXT[],
  allergens TEXT[],
  sku VARCHAR(100),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP,
  
  CONSTRAINT price_positive CHECK (price > 0),
  CONSTRAINT stock_non_negative CHECK (stock >= 0),
  CONSTRAINT rating_range CHECK (rating >= 0 AND rating <= 5)
);

CREATE INDEX idx_products_seller_id ON products(seller_id);
CREATE INDEX idx_products_category_id ON products(category_id);
CREATE INDEX idx_products_is_available ON products(is_available);
CREATE INDEX idx_products_created_at ON products(created_at);
CREATE INDEX idx_products_search ON products USING GIN(tags);
```

### product_images
Gambar produk

```sql
CREATE TABLE product_images (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  image_url VARCHAR(500) NOT NULL,
  alt_text VARCHAR(255),
  sort_order INTEGER DEFAULT 0,
  is_primary BOOLEAN DEFAULT false,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT only_one_primary UNIQUE (product_id) WHERE is_primary = true
);

CREATE INDEX idx_product_images_product_id ON product_images(product_id);
```

---

## 📦 Order Management

### orders
Pesanan dari pembeli

```sql
CREATE TABLE orders (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  order_number VARCHAR(50) UNIQUE NOT NULL,
  buyer_id UUID NOT NULL REFERENCES users(id),
  seller_id UUID NOT NULL REFERENCES sellers(id),
  status ENUM('pending', 'confirmed', 'preparing', 'ready', 'delivering', 'delivered', 'cancelled') DEFAULT 'pending',
  delivery_address TEXT NOT NULL,
  delivery_latitude DECIMAL(10, 8),
  delivery_longitude DECIMAL(11, 8),
  scheduled_delivery_time TIMESTAMP,
  actual_delivery_time TIMESTAMP,
  subtotal BIGINT NOT NULL,
  tax_amount BIGINT DEFAULT 0,
  delivery_fee BIGINT DEFAULT 0,
  discount_amount BIGINT DEFAULT 0,
  total_amount BIGINT NOT NULL,
  payment_method VARCHAR(50),
  payment_status ENUM('unpaid', 'paid', 'refunded', 'failed') DEFAULT 'unpaid',
  special_requests TEXT,
  rating_given BOOLEAN DEFAULT false,
  cancellation_reason TEXT,
  cancelled_by ENUM('buyer', 'seller'),
  cancelled_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT total_calc CHECK (total_amount >= 0),
  CONSTRAINT subtotal_positive CHECK (subtotal > 0)
);

CREATE INDEX idx_orders_buyer_id ON orders(buyer_id);
CREATE INDEX idx_orders_seller_id ON orders(seller_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created_at ON orders(created_at);
CREATE INDEX idx_orders_order_number ON orders(order_number);
```

### order_items
Item dalam order

```sql
CREATE TABLE order_items (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  order_id UUID NOT NULL REFERENCES orders(id) ON DELETE CASCADE,
  product_id UUID NOT NULL REFERENCES products(id),
  product_name VARCHAR(255) NOT NULL,
  quantity INTEGER NOT NULL,
  unit_price BIGINT NOT NULL,
  total_price BIGINT NOT NULL,
  notes TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT quantity_positive CHECK (quantity > 0),
  CONSTRAINT unit_price_positive CHECK (unit_price > 0)
);

CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);
```

---

## 💳 Payment & Transactions

### transactions
Catatan transaksi pembayaran

```sql
CREATE TABLE transactions (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  transaction_number VARCHAR(50) UNIQUE NOT NULL,
  order_id UUID REFERENCES orders(id),
  buyer_id UUID NOT NULL REFERENCES users(id),
  seller_id UUID NOT NULL REFERENCES sellers(id),
  amount BIGINT NOT NULL,
  payment_method VARCHAR(100),
  payment_method_details JSONB,
  midtrans_transaction_id VARCHAR(100),
  status ENUM('pending', 'completed', 'failed', 'cancelled') DEFAULT 'pending',
  transaction_type ENUM('payment', 'refund', 'settlement') DEFAULT 'payment',
  commission_amount BIGINT DEFAULT 0,
  seller_payout_amount BIGINT DEFAULT 0,
  payout_date TIMESTAMP,
  metadata JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  CONSTRAINT amount_positive CHECK (amount > 0)
);

CREATE INDEX idx_transactions_buyer_id ON transactions(buyer_id);
CREATE INDEX idx_transactions_seller_id ON transactions(seller_id);
CREATE INDEX idx_transactions_status ON transactions(status);
CREATE INDEX idx_transactions_order_id ON transactions(order_id);
CREATE INDEX idx_transactions_created_at ON transactions(created_at);
```

---

## ⭐ Review & Rating

### reviews
Review dan rating dari pembeli

```sql
CREATE TABLE reviews (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  order_id UUID REFERENCES orders(id),
  buyer_id UUID NOT NULL REFERENCES users(id),
  seller_id UUID NOT NULL REFERENCES sellers(id),
  rating INTEGER NOT NULL,
  title VARCHAR(255),
  comment TEXT,
  helpful_count INTEGER DEFAULT 0,
  unhelpful_count INTEGER DEFAULT 0,
  status ENUM('pending', 'approved', 'rejected') DEFAULT 'pending',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP,
  
  CONSTRAINT rating_range CHECK (rating >= 1 AND rating <= 5),
  CONSTRAINT one_review_per_order UNIQUE(order_id, buyer_id)
);

CREATE INDEX idx_reviews_product_id ON reviews(product_id);
CREATE INDEX idx_reviews_buyer_id ON reviews(buyer_id);
CREATE INDEX idx_reviews_created_at ON reviews(created_at);
```

### review_images
Gambar dalam review

```sql
CREATE TABLE review_images (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  review_id UUID NOT NULL REFERENCES reviews(id) ON DELETE CASCADE,
  image_url VARCHAR(500) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_review_images_review_id ON review_images(review_id);
```

---

## ✅ Verification

### seller_verification
Proses verifikasi kebersihan dapur seller

```sql
CREATE TABLE seller_verification (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  seller_id UUID NOT NULL UNIQUE REFERENCES sellers(id) ON DELETE CASCADE,
  kitchen_photo_url VARCHAR(500),
  id_card_photo_url VARCHAR(500),
  certificate_url VARCHAR(500),
  inspection_date TIMESTAMP,
  status ENUM('pending', 'verified', 'rejected', 'expired') DEFAULT 'pending',
  verification_badge VARCHAR(100),
  notes TEXT,
  rejection_reason TEXT,
  verified_by UUID REFERENCES users(id),
  verified_at TIMESTAMP,
  expires_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_seller_verification_seller_id ON seller_verification(seller_id);
CREATE INDEX idx_seller_verification_status ON seller_verification(status);
```

---

## 📊 Additional Tables

### favorites
Produk favorit pembeli

```sql
CREATE TABLE favorites (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  buyer_id UUID NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  product_id UUID NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(buyer_id, product_id)
);

CREATE INDEX idx_favorites_buyer_id ON favorites(buyer_id);
```

### audit_logs
Log aktivitas sistem

```sql
CREATE TABLE audit_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  action VARCHAR(255) NOT NULL,
  entity_type VARCHAR(100),
  entity_id UUID,
  old_values JSONB,
  new_values JSONB,
  ip_address VARCHAR(45),
  user_agent TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_created_at ON audit_logs(created_at);
```

---

## 🔗 Relationships Diagram

```
users (1) ──── (1) sellers
  │                  │
  │                  │
  └─────────────────┘
  
sellers (1) ──── (N) products
         │            │
         │            │
         └────────┬───┘
                  │
                  └──── (N) product_images
                  
sellers (1) ──── (N) orders ──── (N) order_items ──── (1) products

users (1) ──── (N) orders (many buyers)
users (1) ──── (N) reviews (as buyer)

transactions (1) ──── (1) orders
                      (1) sellers
                      (1) users (buyer)

reviews (1) ──── (N) review_images
        (1) ──── (1) products
        (1) ──���─ (1) orders
        (1) ──── (1) sellers

seller_verification (1) ──── (1) sellers
```

---

## 🔑 Key Constraints

### Primary Keys
- All tables use UUID as primary key (uuid_generate_v4())

### Foreign Keys
- Cascade delete for most relationships
- Restrict delete for critical data

### Unique Constraints
- email (users)
- order_number (orders)
- transaction_number (transactions)
- shop_slug (sellers)
- product SKU (products)

### Check Constraints
- Price > 0
- Stock >= 0
- Rating 0-5
- Email format validation
- Phone format validation

---

## 📈 Performance Optimization

### Indexing Strategy

```sql
-- Search & Discovery
CREATE INDEX idx_products_seller_location ON products(seller_id);
CREATE INDEX idx_products_category ON products(category_id);

-- Geolocation
CREATE INDEX idx_sellers_location_gist ON sellers USING GIST(
  ll_to_earth(latitude, longitude)
);

-- Time-series queries
CREATE INDEX idx_orders_created_date ON orders(DATE(created_at));
CREATE INDEX idx_transactions_created_date ON transactions(DATE(created_at));

-- Status tracking
CREATE INDEX idx_orders_status_created ON orders(status, created_at);
```

### Query Optimization

```sql
-- Use EXPLAIN ANALYZE
EXPLAIN ANALYZE
SELECT p.*, s.shop_name, s.rating
FROM products p
JOIN sellers s ON p.seller_id = s.id
WHERE p.category_id = ? AND s.is_verified = true
ORDER BY p.rating DESC
LIMIT 20;

-- Add Materialized View for trending
CREATE MATERIALIZED VIEW trending_products AS
SELECT p.id, p.name, COUNT(oi.id) as order_count
FROM products p
LEFT JOIN order_items oi ON p.id = oi.product_id
WHERE oi.created_at > NOW() - INTERVAL '7 days'
GROUP BY p.id
ORDER BY order_count DESC;

CREATE INDEX idx_trending_products_order_count ON trending_products(order_count);
```

---

## 🔒 Security Considerations

- Passwords never stored in plain text
- Sensitive data encrypted at rest
- GDPR compliance with soft deletes
- Audit logs for regulatory compliance
- Row-level security for multi-tenancy (if needed)

---

## 📝 Migration Strategy

All database changes use version-controlled migrations:

```
migrations/
├── 001_initial_schema.sql
├── 002_add_seller_verification.sql
├── 003_add_audit_logs.sql
└── ...
```

---

**Last Updated**: 27 Mei 2026

**Database Version**: PostgreSQL 12+

**Maintenance**: Regular backups, VACUUM, ANALYZE
