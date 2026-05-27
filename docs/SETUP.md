# 🛠️ DapurKu Setup & Installation Guide

Complete guide untuk setup development environment untuk DapurKu.

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Quick Start (5 minutes)](#quick-start-5-minutes)
3. [Backend Setup](#backend-setup)
4. [Mobile App Setup](#mobile-app-setup)
5. [Web Admin Setup](#web-admin-setup)
6. [Database Setup](#database-setup)
7. [Environment Configuration](#environment-configuration)
8. [Useful Commands](#useful-commands)
9. [Troubleshooting](#troubleshooting)

---

## 📦 Prerequisites

### System Requirements

| Requirement | Version | Download |
|-------------|---------|----------|
| Node.js | 16.x atau lebih | [nodejs.org](https://nodejs.org) |
| PostgreSQL | 12+ | [postgresql.org](https://www.postgresql.org/download) |
| Git | 2.30+ | [git-scm.com](https://git-scm.com) |
| npm | 7.x+ | Included with Node.js |
| Docker (optional) | 20.10+ | [docker.com](https://www.docker.com) |

### Check Installed Versions

```bash
node --version
npm --version
git --version
psql --version
```

### Development Tools (Optional but Recommended)

- **IDE**: Visual Studio Code, WebStorm, atau IntelliJ
- **API Client**: Postman, Insomnia, atau Thunder Client
- **Database GUI**: pgAdmin, DBeaver, atau DataGrip
- **Version Control**: GitHub Desktop atau GitKraken

---

## 🚀 Quick Start (5 minutes)

### 1. Clone Repository

```bash
git clone https://github.com/smithcolar/dapurku.git
cd dapurku
```

### 2. Install Dependencies

```bash
# Install root dependencies (if any)
npm install

# Backend
cd backend && npm install && cd ..

# Mobile
cd mobile && npm install && cd ..

# Web Admin
cd web-admin && npm install && cd ..
```

### 3. Setup Database

```bash
# Buat database PostgreSQL
createdb dapurku_dev

# Setup backend environment
cd backend
cp .env.example .env

# Run migrations
npm run migrate

# Start backend
npm run dev
```

### 4. Start Development Servers

```bash
# Terminal 1: Backend (port 3000)
cd backend && npm run dev

# Terminal 2: Mobile (Expo)
cd mobile && npx expo start

# Terminal 3: Web Admin (port 3001)
cd web-admin && npm start
```

Done! 🎉 Sekarang Anda bisa:
- **API**: http://localhost:3000
- **Mobile**: Scan QR code dengan Expo app
- **Admin**: http://localhost:3001

---

## 💻 Backend Setup

### Step 1: Navigate to Backend

```bash
cd backend
```

### Step 2: Install Dependencies

```bash
npm install
```

### Step 3: Environment Configuration

```bash
cp .env.example .env
```

Edit `.env` file Anda:

```env
# Application
NODE_ENV=development
APP_PORT=3000
APP_NAME=DapurKu

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=dapurku_dev
DB_USER=postgres
DB_PASSWORD=your_password

# JWT
JWT_SECRET=your-secret-key-here
JWT_EXPIRATION=24h
JWT_REFRESH_SECRET=your-refresh-secret-key
JWT_REFRESH_EXPIRATION=7d

# Midtrans Payment Gateway
MIDTRANS_SERVER_KEY=your_midtrans_server_key
MIDTRANS_CLIENT_KEY=your_midtrans_client_key
MIDTRANS_ENVIRONMENT=sandbox

# File Upload
AWS_S3_BUCKET=dapurku-uploads
AWS_S3_REGION=us-east-1
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret

# Email Service
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password

# Logging
LOG_LEVEL=debug
LOG_FILE=logs/app.log

# CORS
CORS_ORIGIN=http://localhost:3001,http://localhost:19000

# Geolocation
GEOLOCATION_API_KEY=your_geolocation_api_key
```

### Step 4: Setup Database

#### Option A: Using PostgreSQL CLI

```bash
# Create database
createdb dapurku_dev

# Create user (if needed)
createuser dapurku_user

# Connect to database
psql dapurku_dev

# Grant privileges
GRANT ALL PRIVILEGES ON DATABASE dapurku_dev TO dapurku_user;
```

#### Option B: Using Docker Compose (Recommended)

```bash
# Start PostgreSQL in Docker
docker-compose up -d postgres

# Wait for PostgreSQL to start (10-15 seconds)
sleep 15

# Run migrations
npm run migrate
```

#### Option C: Using PgAdmin

1. Open http://localhost:5050 (if running pgAdmin)
2. Create new database: `dapurku_dev`
3. Configure connection string di `.env`

### Step 5: Run Database Migrations

```bash
# Create migration files
npm run migrate:create

# Run all migrations
npm run migrate

# Run specific migration
npm run migrate --name=001_initial_schema

# Rollback last migration
npm run migrate:rollback

# Seed database with sample data
npm run seed
```

### Step 6: Start Development Server

```bash
# Development mode (dengan auto-reload)
npm run dev

# Production mode
npm run build
npm start

# Run with debugging
npm run debug
```

Server akan berjalan di: **http://localhost:3000**

### Step 7: Verify Backend

```bash
# Test API health
curl http://localhost:3000/api/health

# Expected response
{
  "status": "ok",
  "uptime": 1234,
  "timestamp": "2026-05-27T10:00:00Z"
}
```

---

## 📱 Mobile App Setup

### Step 1: Navigate to Mobile Directory

```bash
cd mobile
```

### Step 2: Install Dependencies

```bash
npm install

# Install Expo CLI globally (if not already)
npm install -g expo-cli
```

### Step 3: Environment Configuration

```bash
cp .env.example .env
```

Edit `.env`:

```env
API_BASE_URL=http://localhost:3000/api
MIDTRANS_CLIENT_KEY=your_midtrans_client_key
MIDTRANS_ENVIRONMENT=sandbox
FIREBASE_PROJECT_ID=your_firebase_project
FIREBASE_MESSAGING_SENDER_ID=your_sender_id
```

### Step 4: Install Expo App

**Android:**
- Download "Expo Go" dari Google Play Store

**iOS:**
- Download "Expo Go" dari App Store

### Step 5: Start Development Server

```bash
# Start Expo development server
npx expo start

# Pilih option:
# - Press 'a' untuk Android emulator
# - Press 'i' untuk iOS simulator
# - Scan QR code dengan Expo app di smartphone Anda
```

### Step 6: Running on Physical Device

```bash
# Start Expo
npx expo start

# Scan QR code dengan Expo Go app di smartphone
```

### Step 7: Build APK (Android)

```bash
# Build APK
eas build --platform android

# Build AAB untuk Google Play
eas build --platform android --release-channel production
```

### Step 8: Build IPA (iOS)

```bash
# Requires Mac
eas build --platform ios

# Build untuk App Store
eas build --platform ios --release-channel production
```

---

## 🌐 Web Admin Setup

### Step 1: Navigate to Web Admin Directory

```bash
cd web-admin
```

### Step 2: Install Dependencies

```bash
npm install
```

### Step 3: Environment Configuration

```bash
cp .env.example .env
```

Edit `.env`:

```env
REACT_APP_API_BASE_URL=http://localhost:3000/api
REACT_APP_ENVIRONMENT=development
```

### Step 4: Start Development Server

```bash
npm start
```

Server akan berjalan di: **http://localhost:3001**

### Step 5: Build for Production

```bash
npm run build

# Test production build locally
npm install -g serve
serve -s build -l 3001
```

---

## 🗄️ Database Setup

### PostgreSQL Installation

#### macOS (Homebrew)

```bash
brew install postgresql@14
brew services start postgresql@14
createdb dapurku_dev
```

#### Ubuntu/Debian

```bash
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
sudo service postgresql start

# Create database
sudo -u postgres createdb dapurku_dev
```

#### Windows

1. Download PostgreSQL installer dari [postgresql.org](https://www.postgresql.org/download/windows)
2. Run installer, remember superuser password
3. Add PostgreSQL bin directory ke PATH
4. Buka Command Prompt:

```bash
createdb dapurku_dev
```

### Using Docker (Recommended)

```bash
# Create docker-compose.yml di project root
docker-compose up -d

# Verify PostgreSQL is running
docker-compose ps

# Connect to database
docker exec -it dapurku-postgres psql -U dapurku_user -d dapurku_dev
```

### Verify Database Connection

```bash
# Using psql
psql -h localhost -U postgres -d dapurku_dev

# Run simple query
SELECT NOW();

# Exit
\q
```

---

## ⚙️ Environment Configuration

### Backend .env.example

```env
# === APPLICATION ===
NODE_ENV=development
APP_PORT=3000
APP_NAME=DapurKu API

# === DATABASE ===
DB_HOST=localhost
DB_PORT=5432
DB_NAME=dapurku_dev
DB_USER=postgres
DB_PASSWORD=postgres

# === JWT ===
JWT_SECRET=your_jwt_secret_key_12345
JWT_EXPIRATION=24h
JWT_REFRESH_SECRET=your_refresh_secret_key_12345
JWT_REFRESH_EXPIRATION=7d

# === MIDTRANS ===
MIDTRANS_SERVER_KEY=your_midtrans_server_key
MIDTRANS_CLIENT_KEY=your_midtrans_client_key
MIDTRANS_ENVIRONMENT=sandbox

# === AWS S3 (untuk file upload) ===
AWS_S3_BUCKET=dapurku-dev
AWS_S3_REGION=ap-southeast-1
AWS_ACCESS_KEY_ID=your_aws_access_key
AWS_SECRET_ACCESS_KEY=your_aws_secret_key

# === EMAIL ===
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_specific_password

# === LOGGING ===
LOG_LEVEL=debug
LOG_FILE=./logs/app.log

# === CORS ===
CORS_ORIGIN=http://localhost:3001,http://localhost:19000,http://localhost:8081

# === GEOLOCATION ===
GOOGLE_MAPS_API_KEY=your_google_maps_api_key
```

### Mobile .env.example

```env
API_BASE_URL=http://192.168.x.x:3000/api
MIDTRANS_CLIENT_KEY=your_midtrans_client_key
MIDTRANS_ENVIRONMENT=sandbox
FIREBASE_PROJECT_ID=your_firebase_project_id
```

---

## 📝 Useful Commands

### Backend Commands

```bash
# Development
npm run dev              # Start development server
npm run debug           # Start with debugging
npm run build           # Build for production
npm start               # Start production server

# Database
npm run migrate         # Run migrations
npm run migrate:create  # Create new migration
npm run seed            # Seed database
npm run migrate:rollback # Rollback migrations

# Testing
npm run test            # Run all tests
npm run test:watch      # Run tests in watch mode
npm run test:coverage   # Run with coverage report

# Code Quality
npm run lint            # Run ESLint
npm run lint:fix        # Fix ESLint issues
npm run format          # Format code with Prettier
```

### Mobile Commands

```bash
npx expo start          # Start Expo dev server
npx expo build:android  # Build APK
npx expo build:ios      # Build IPA (macOS only)
npm test                # Run tests
npm run lint            # Run ESLint
```

### Web Admin Commands

```bash
npm start               # Start development server
npm run build           # Build for production
npm run test            # Run tests
npm run lint            # Run ESLint
npm run eject           # Eject from Create React App (irreversible!)
```

### Docker Commands

```bash
docker-compose up       # Start all services
docker-compose down     # Stop all services
docker-compose ps       # View running services
docker-compose logs -f backend  # View backend logs
docker-compose exec postgres psql -U dapurku_user -d dapurku_dev
```

---

## 🐛 Troubleshooting

### Backend Issues

#### Port 3000 already in use

```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>

# Or use different port
PORT=3001 npm run dev
```

#### Database connection error

```bash
# Check PostgreSQL is running
psql -h localhost -U postgres -d dapurku_dev

# Verify connection string di .env
# Format: postgresql://user:password@localhost:5432/database_name
```

#### Migration failed

```bash
# Check migration status
npm run migrate:status

# Rollback last migration
npm run migrate:rollback

# See migration history
psql dapurku_dev -c "SELECT * FROM schema_migrations;"
```

### Mobile Issues

#### Expo connection timeout

```bash
# Restart Expo
npx expo start --clear

# Use tunnel connection
npx expo start --tunnel

# Use LAN connection
npx expo start --lan
```

#### Module not found error

```bash
# Clear cache
npm cache clean --force
rm -rf node_modules
npm install

# Restart Expo
npx expo start --clear
```

### Web Admin Issues

#### Port 3001 already in use

```bash
# Use different port
PORT=3002 npm start
```

#### Build fails

```bash
# Clear npm cache
npm cache clean --force

# Remove node_modules
rm -rf node_modules

# Reinstall
npm install

# Build again
npm run build
```

### Database Issues

#### PostgreSQL service not running

```bash
# macOS
brew services start postgresql@14

# Ubuntu
sudo service postgresql start

# Windows
net start postgresql-x64-14
```

#### Cannot connect to database

```bash
# Check if PostgreSQL is listening
sudo netstat -tulpn | grep 5432

# Restart PostgreSQL
sudo service postgresql restart

# Check logs
tail -f /var/log/postgresql/postgresql.log
```

---

## 📞 Getting Help

- **Documentation**: See [docs/](../docs/) folder
- **GitHub Issues**: Report bugs di [issues](https://github.com/smithcolar/dapurku/issues)
- **Discussions**: Ask questions di [discussions](https://github.com/smithcolar/dapurku/discussions)
- **Email**: smithcolar@github.com

---

## 🔄 Next Steps

1. ✅ Setup semua komponen selesai
2. 📚 Baca [API Documentation](./API.md)
3. 🗄️ Baca [Database Schema](./DATABASE.md)
4. 🚀 Ikuti [CONTRIBUTING.md](../CONTRIBUTING.md) untuk development workflow
5. 📝 Check [ROADMAP.md](../ROADMAP.md) untuk prioritas fitur

---

**Last Updated**: 27 Mei 2026

**Tested on**: macOS 12+, Ubuntu 20.04+, Windows 10+
