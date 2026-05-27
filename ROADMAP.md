# 🛣️ DapurKu Development Roadmap

12-week MVP Development Plan untuk meluncurkan Platform Ghost Kitchen UMKM Indonesia.

## 📊 Timeline Overview

```
├─ Week 1-2    : Foundation & Setup
├─ Week 3-4    : Authentication & User Management
├─ Week 5-6    : Seller & Product Management
├─ Week 7-8    : Buyer Features & Discovery
├─ Week 9      : Orders & Real-time Updates
├─ Week 10     : Payment Integration
├─ Week 11     : Testing & Bug Fixes
└─ Week 12     : Launch & Monitoring
```

---

## 📅 Phase 1: Foundation & Setup (Week 1-2)

### Week 1: Project Initialization

**Backend**
- [ ] Setup Node.js + Express boilerplate
- [ ] Configure PostgreSQL connection
- [ ] Setup Docker & docker-compose
- [ ] Database migration framework setup
- [ ] Environment configuration (.env)
- [ ] Logging system (Winston/Morgan)
- [ ] Error handling middleware

**Infrastructure**
- [ ] GitHub repository structure
- [ ] CI/CD pipeline setup (GitHub Actions)
- [ ] Code linting & formatting (ESLint, Prettier)
- [ ] Testing framework setup (Jest)

**Documentation**
- [ ] API documentation template (Swagger)
- [ ] Architecture diagrams
- [ ] Database schema diagrams

**Team Kickoff**
- [ ] Setup communication channels (Discord/Slack)
- [ ] Define coding standards
- [ ] Create PR templates
- [ ] Schedule daily standups (30 min)

### Week 2: Database & Core Models

**Database**
- [ ] Create PostgreSQL migrations
- [ ] Define 12 core tables
  - users, sellers, buyers, categories
  - products, product_images, orders, order_items
  - transactions, reviews, verification, audit_logs
- [ ] Setup indexes for performance
- [ ] Create seed data

**Backend Models**
- [ ] User model & repository
- [ ] Seller model & repository
- [ ] Buyer model & repository
- [ ] Product model & repository
- [ ] Order model & repository

**Testing**
- [ ] Unit tests for models
- [ ] Database connection tests
- [ ] Migration tests

---

## 🔐 Phase 2: Authentication & User Management (Week 3-4)

### Week 3: Authentication System

**Backend API - Auth Endpoints**
- [ ] POST /api/auth/register/seller
- [ ] POST /api/auth/register/buyer
- [ ] POST /api/auth/login
- [ ] POST /api/auth/logout
- [ ] POST /api/auth/refresh-token
- [ ] POST /api/auth/forgot-password
- [ ] POST /api/auth/reset-password
- [ ] POST /api/auth/verify-email

**Auth Implementation**
- [ ] JWT token generation & validation
- [ ] Password hashing (bcrypt)
- [ ] Email verification
- [ ] Refresh token mechanism
- [ ] Rate limiting for auth endpoints
- [ ] 2FA setup (optional for MVP)

**Tests**
- [ ] Unit tests for auth service
- [ ] Integration tests for auth endpoints
- [ ] Error handling tests (invalid credentials, etc)

### Week 4: User Profiles & Onboarding

**Backend API - User Endpoints**
- [ ] GET /api/users/me
- [ ] PUT /api/users/profile
- [ ] PUT /api/users/password
- [ ] DELETE /api/users/account
- [ ] GET /api/sellers/:id
- [ ] GET /api/buyers/:id

**Mobile - Auth Screens**
- [ ] Splash screen with auth check
- [ ] Seller registration screen
- [ ] Buyer registration screen
- [ ] Login screen
- [ ] Password recovery screen
- [ ] Email verification screen
- [ ] OTP verification (SMS)

**State Management**
- [ ] Redux setup for auth state
- [ ] Secure token storage (AsyncStorage)
- [ ] User profile state

**Tests**
- [ ] E2E tests for registration flow
- [ ] E2E tests for login flow

---

## 🏪 Phase 3: Seller & Product Management (Week 5-6)

### Week 5: Seller Dashboard & Shop Setup

**Backend API - Seller Endpoints**
- [ ] POST /api/sellers/shop
- [ ] GET /api/sellers/me/shop
- [ ] PUT /api/sellers/me/shop
- [ ] PUT /api/sellers/me/avatar
- [ ] GET /api/sellers/me/stats
- [ ] GET /api/sellers/me/reviews

**Backend API - Verification**
- [ ] GET /api/sellers/me/verification
- [ ] POST /api/sellers/me/verification/submit
- [ ] GET /api/verification/requests (admin only)
- [ ] PUT /api/verification/:id/approve (admin only)
- [ ] PUT /api/verification/:id/reject (admin only)

**Mobile - Seller App**
- [ ] Seller profile setup screen
- [ ] Shop information form
- [ ] Avatar upload
- [ ] Kitchen verification photo upload
- [ ] Bank account setup

**Tests**
- [ ] Seller creation tests
- [ ] Shop update tests
- [ ] Verification flow tests

### Week 6: Product Management

**Backend API - Product Endpoints**
- [ ] POST /api/sellers/me/products
- [ ] GET /api/sellers/me/products
- [ ] GET /api/sellers/me/products/:id
- [ ] PUT /api/sellers/me/products/:id
- [ ] DELETE /api/sellers/me/products/:id
- [ ] POST /api/sellers/me/products/:id/images
- [ ] DELETE /api/sellers/me/products/:id/images/:imageId

**Backend API - Categories**
- [ ] GET /api/categories
- [ ] POST /api/categories (admin only)

**Mobile - Product Management**
- [ ] Add product screen
- [ ] Edit product screen
- [ ] Delete product screen
- [ ] Product image upload (multiple)
- [ ] Product list screen
- [ ] Product visibility toggle

**Tests**
- [ ] Product CRUD tests
- [ ] Image upload tests
- [ ] Category filter tests

---

## 🔍 Phase 4: Buyer Features & Discovery (Week 7-8)

### Week 7: Discovery & Search

**Backend API - Discovery Endpoints**
- [ ] GET /api/products (with filters)
- [ ] GET /api/products/search
- [ ] GET /api/products/:id
- [ ] GET /api/sellers
- [ ] GET /api/sellers/:id
- [ ] GET /api/sellers/:id/products
- [ ] GET /api/categories
- [ ] POST /api/buyers/me/favorites
- [ ] GET /api/buyers/me/favorites

**Filters Implementation**
- [ ] Location-based search (geolocation)
- [ ] Category filter
- [ ] Price range filter
- [ ] Diet type filter (halal, vegan, etc)
- [ ] Rating filter
- [ ] Sorting (newest, most popular, cheapest)

**Search Optimization**
- [ ] Full-text search setup (PostgreSQL)
- [ ] Search suggestions/autocomplete
- [ ] Search history tracking
- [ ] Popular searches caching

**Mobile - Buyer App**
- [ ] Home screen with featured sellers
- [ ] Search & filter screen
- [ ] Product detail screen
- [ ] Seller detail screen
- [ ] Favorites screen
- [ ] Search history

**Tests**
- [ ] Search functionality tests
- [ ] Filter tests
- [ ] Geolocation tests

### Week 8: Pre-order & Subscription

**Backend API - Pre-order**
- [ ] POST /api/products/:id/pre-orders
- [ ] GET /api/products/:id/availability
- [ ] PUT /api/products/:id/availability
- [ ] GET /api/buyers/me/pre-orders
- [ ] POST /api/sellers/me/pre-orders/:id/confirm

**Backend API - Subscription**
- [ ] POST /api/sellers/me/subscriptions
- [ ] GET /api/sellers/me/subscriptions
- [ ] PUT /api/sellers/me/subscriptions/:id
- [ ] DELETE /api/sellers/me/subscriptions/:id
- [ ] GET /api/buyers/me/subscriptions
- [ ] POST /api/buyers/me/subscriptions/:id

**Mobile - Pre-order & Subscription**
- [ ] Pre-order interface
- [ ] Availability calendar
- [ ] Subscription plans
- [ ] Subscribe screen

**Tests**
- [ ] Pre-order flow tests
- [ ] Availability tests
- [ ] Subscription tests

---

## 📦 Phase 5: Orders & Real-time Updates (Week 9)

### Week 9: Order Management & Real-time

**Backend API - Order Endpoints**
- [ ] POST /api/orders
- [ ] GET /api/orders/:id
- [ ] GET /api/buyers/me/orders
- [ ] GET /api/sellers/me/orders
- [ ] PUT /api/orders/:id/status
- [ ] PUT /api/orders/:id/cancel
- [ ] GET /api/orders/:id/tracking

**Real-time Implementation**
- [ ] WebSocket setup (Socket.io)
- [ ] Order status updates
- [ ] New order notifications
- [ ] Seller notifications
- [ ] Buyer notifications
- [ ] Order chat/messages (optional)

**Backend - Real-time Events**
- [ ] Order created event
- [ ] Order status changed event
- [ ] Order cancelled event
- [ ] Order delivered event

**Mobile - Order Management**
- [ ] Order creation flow (cart → checkout preview)
- [ ] Order confirmation screen
- [ ] Order tracking screen
- [ ] Order history screen
- [ ] Seller order management screen
- [ ] Real-time status notifications
- [ ] Push notifications setup

**Tests**
- [ ] Order creation tests
- [ ] Order status update tests
- [ ] WebSocket connection tests
- [ ] Real-time notification tests

---

## 💳 Phase 6: Payment Integration (Week 10)

### Week 10: Midtrans Payment Gateway

**Backend API - Payment Endpoints**
- [ ] POST /api/orders/:id/payment/initiate
- [ ] POST /api/orders/:id/payment/confirm
- [ ] POST /api/webhooks/midtrans (Midtrans callback)
- [ ] GET /api/transactions
- [ ] GET /api/transactions/:id
- [ ] POST /api/transactions/:id/refund

**Midtrans Integration**
- [ ] Snap integration for checkout
- [ ] Bank transfer support
- [ ] E-wallet support (OVO, DANA, LinkAja, GCash)
- [ ] Installment setup (optional)
- [ ] Webhook handling
- [ ] Payment status synchronization
- [ ] Commission calculation & settlement

**Commission & Settlement**
- [ ] Commission deduction logic (10-15%)
- [ ] Daily settlement calculation
- [ ] Seller payout tracking
- [ ] Settlement history endpoint
- [ ] Bank account verification for payout

**Mobile - Payment Screen**
- [ ] Cart screen
- [ ] Checkout summary
- [ ] Payment method selection
- [ ] Midtrans Snap integration
- [ ] Payment confirmation screen
- [ ] Payment history

**Web Admin - Transactions**
- [ ] Transaction monitoring dashboard
- [ ] Transaction search & filter
- [ ] Refund management
- [ ] Settlement tracking
- [ ] Commission reports

**Tests**
- [ ] Midtrans Snap tests
- [ ] Payment webhook tests
- [ ] Commission calculation tests
- [ ] Settlement tests
- [ ] Refund tests

---

## 🧪 Phase 7: Testing & Quality Assurance (Week 11)

### Week 11: Comprehensive Testing

**Backend Testing**
- [ ] Increase test coverage to 80%+
- [ ] Integration tests for all endpoints
- [ ] Load testing & performance benchmarks
- [ ] Security testing (OWASP top 10)
- [ ] SQL injection & XSS prevention tests
- [ ] Rate limiting tests
- [ ] Error handling tests

**Mobile Testing**
- [ ] Unit tests for components
- [ ] Integration tests for flows
- [ ] UI/UX testing across devices
- [ ] Performance testing
- [ ] Offline functionality tests
- [ ] Push notification tests

**E2E Tests**
- [ ] Complete user flows
- [ ] Registration → Order → Payment
- [ ] Seller flow → Menu → Orders
- [ ] Admin operations

**Bug Fixes & Optimization**
- [ ] Fix critical bugs
- [ ] Performance optimization
- [ ] Database query optimization
- [ ] API response time improvements
- [ ] Mobile app size optimization

**Documentation**
- [ ] API documentation complete
- [ ] Code documentation
- [ ] Deployment guide
- [ ] Troubleshooting guide
- [ ] FAQ

---

## 🚀 Phase 8: Launch & Monitoring (Week 12)

### Week 12: Pre-launch & Launch

**Pre-launch Checklist**
- [ ] Security audit completed
- [ ] Performance testing passed
- [ ] Data backup strategy implemented
- [ ] Monitoring & alerting setup
- [ ] Incident response plan ready
- [ ] Customer support setup

**Deployment**
- [ ] Production database setup
- [ ] Environment configuration
- [ ] SSL certificates setup
- [ ] CDN setup for static assets
- [ ] Monitoring dashboard (Datadog/New Relic)
- [ ] Log aggregation (ELK/Splunk)

**Launch Activities**
- [ ] Beta user testing
- [ ] Social media announcement
- [ ] PR & media coverage
- [ ] Launch day monitoring
- [ ] Support team on standby

**Post-Launch**
- [ ] Monitor system performance
- [ ] Track user feedback
- [ ] Fix critical issues
- [ ] Analytics tracking
- [ ] User acquisition tracking

**Monitoring & Alerts**
- [ ] Server uptime monitoring
- [ ] API response time alerts
- [ ] Error rate alerts
- [ ] Database performance monitoring
- [ ] Payment transaction monitoring

---

## 🎯 Success Metrics

### Week 12 Goals (MVP)

| Metric | Target |
|--------|--------|
| Active Sellers | 50+ |
| Active Buyers | 500+ |
| Daily Transactions | 100+ |
| App Downloads | 1,000+ |
| System Uptime | 99.5%+ |
| API Response Time | <500ms |
| Test Coverage | 80%+ |

---

## 📝 Post-Launch Roadmap (Future)

### Phase 9: Enhancement (Week 13-16)

- [ ] Admin dashboard enhancement
- [ ] Advanced analytics
- [ ] Seller verification photo AI recognition
- [ ] Recommendation engine
- [ ] In-app messaging
- [ ] Seller rating system improvements

### Phase 10: Expansion (Week 17-20)

- [ ] Multi-city expansion
- [ ] iOS app launch
- [ ] Seller verification badge system
- [ ] Premium seller features
- [ ] Marketing tools for sellers
- [ ] API for 3rd party integrations

### Phase 11: Scale (Week 21+)

- [ ] Series A fundraising preparation
- [ ] International expansion
- [ ] B2B features (corporate catering)
- [ ] Logistics optimization
- [ ] Additional payment methods
- [ ] Seller training program

---

## 💡 Dependencies & Risks

### Critical Dependencies

- ✅ PostgreSQL database availability
- ✅ Midtrans API access
- ✅ Geolocation services
- ✅ Push notification service (Firebase)
- ✅ Cloud hosting (AWS/Digital Ocean)

### Potential Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|-----------|
| Payment gateway delays | Medium | High | Early integration, sandbox testing |
| Database performance | Low | High | Indexing, query optimization |
| Team resource shortage | Low | High | Clear sprint planning |
| Scope creep | High | Medium | Strict MVP definition |
| Security vulnerabilities | Medium | Critical | Regular audits, penetration testing |

---

## 📊 Resource Allocation

```
Backend Development:     40%
Mobile Development:      35%
Infrastructure/DevOps:   15%
Testing/QA:              10%
```

---

## 🤝 Team Communication

**Daily**
- 09:00 - Standup meeting (30 min)
- Async updates in Discord

**Weekly**
- Wednesday 14:00 - Sprint Planning
- Friday 16:00 - Demo & Retrospective

**Monthly**
- Stakeholder update call
- Business metrics review

---

## 📞 Contact & Support

- **Project Lead**: smithcolar
- **GitHub Issues**: For bugs & features
- **Discord**: For daily communication
- **Email**: smithcolar@github.com

---

**Last Updated**: 27 Mei 2026

**Status**: 🔵 In Planning (Ready to Start Development)
