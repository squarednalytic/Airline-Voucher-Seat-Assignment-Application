# Airline Voucher Seat Assignment Application

A full-stack web application for airline crew members to generate random seat vouchers for promotional campaigns. Built with React.js and Laravel.

---

## 📋 Table of Contents
1. [Prerequisites](#prerequisites)
2. [Project Structure](#project-structure)
3. [Quick Start](#quick-start)
4. [Detailed Installation](#detailed-installation)
5. [Environment Configuration](#environment-configuration)
6. [Database Setup](#database-setup)
7. [Running the Application](#running-the-application)
8. [Docker Setup](#docker-setup-optional)
9. [API Documentation](#api-documentation)
10. [Testing](#testing)
11. [Support](#support)

---

## 1. 🚀 Prerequisites

### Backend Requirements
```bash
# PHP 8.2 or higher
php --version  # Should output: PHP 8.2.x

# Composer (Latest version)
composer --version  # Should output: Composer version 2.x

# SQLite3 (usually included with PHP)
sqlite3 --version  # Should output: 3.x.x

# Node.js 18.0 or higher
node --version  # Should output: v18.x or higher

# npm 9.0 or higher
npm --version  # Should output: 9.x or higher

2. 📁 Project Structure
project/
├── backend/                          # Laravel application
│   ├── app/
│   │   ├── Http/
│   │   │   ├── Controllers/
│   │   │   │   └── VoucherController.php
│   │   │   ├── Requests/
│   │   │   │   ├── CheckVoucherRequest.php
│   │   │   │   └── GenerateVoucherRequest.php
│   │   │   └── Resources/
│   │   │       ├── CheckResource.php
│   │   │       └── GenerateResource.php
│   │   ├── Models/
│   │   │   └── Voucher.php
│   │   └── Services/
│   │       └── SeatGeneratorService.php
│   ├── bootstrap/
│   ├── config/
│   │   ├── app.php
│   │   ├── cors.php
│   │   └── database.php
│   ├── database/
│   │   ├── migrations/
│   │   │   └── 2024_01_01_000001_create_vouchers_table.php
│   │   ├── seeds/
│   │   │   └── DatabaseSeeder.php
│   │   └── vouchers.db
│   ├── routes/
│   │   └── api.php
│   ├── tests/
│   │   ├── Feature/
│   │   │   └── VoucherApiTest.php
│   │   └── TestCase.php
│   ├── .env.example
│   ├── artisan
│   ├── composer.json
│   └── Dockerfile
│
├── frontend/                         # React application
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── VoucherForm.jsx
│   │   │   ├── SeatDisplay.jsx
│   │   │   └── ErrorMessage.jsx
│   │   ├── services/
│   │   │   └── api.js
│   │   ├── styles/
│   │   │   └── App.css
│   │   ├── App.jsx
│   │   └── index.js
│   ├── .env.example
│   ├── package.json
│   └── Dockerfile
│
├── docker-compose.yml
└── README.md

3. ⚡ Quick Start
# 1. Clone the repository
git clone <repository-url>
cd project

# 2. Backend setup
cd backend
composer install
cp .env.example .env
php artisan key:generate
touch database/vouchers.db
chmod 664 database/vouchers.db
php artisan migrate
php artisan db:seed  # Optional

# 3. Frontend setup
cd ../frontend
npm install
cp .env.example .env

# 4. Start the application (in two terminals)

# Terminal 1 - Backend
cd backend
php artisan serve

# Terminal 2 - Frontend
cd frontend
npm start

# 5. Access the application
# Frontend: http://localhost:3000
# Backend API: http://localhost:8000

4. 🔧 Detailed Installation

git clone <repository-url>
cd project

# Navigate to backend directory
cd backend

# Install PHP dependencies
composer install

# Create environment file
cp .env.example .env

# Generate application key
php artisan key:generate

# Create SQLite database file
touch database/vouchers.db

# Set proper permissions
chmod 664 database/vouchers.db
chmod -R 775 storage bootstrap/cache

# Create storage link (optional)
php artisan storage.link

Step 3: Frontend Setup (React)

# Navigate to frontend directory
cd ../frontend

# Install Node dependencies
npm install
# or if using yarn
# yarn install

5. ⚙️ Environment Configuration
Backend (.env) - backend/.env

# Application
APP_NAME="Voucher App"
APP_ENV=local
APP_KEY=base64:your-generated-key-here
APP_DEBUG=true
APP_URL=http://localhost:8000

# Database Configuration (SQLite)
DB_CONNECTION=sqlite
DB_DATABASE=/absolute/path/to/your/project/backend/database/vouchers.db
# For relative path:
# DB_DATABASE=database/vouchers.db

# CORS Configuration
FRONTEND_URL=http://localhost:3000

# Logging
LOG_CHANNEL=stack
LOG_LEVEL=debug

Frontend (.env) - frontend/.env
# API Configuration
REACT_APP_API_URL=http://localhost:8000/api
REACT_APP_API_TIMEOUT=30000
REACT_APP_DEBUG=true

6. 🗄️ Database Setup
Create and Configure Database

cd backend

# Method 1: Using Artisan commands
php artisan migrate
php artisan db:seed

# Method 2: Fresh migration with seed
php artisan migrate:fresh --seed

# Method 3: Custom database creation script
cat > setup_database.sh << 'EOF'
#!/bin/bash
touch database/vouchers.db
chmod 664 database/vouchers.db
php artisan migrate
php artisan db:seed
echo "Database setup complete!"
EOF

chmod +x setup_database.sh
./setup_database.sh

Verify Database
php artisan tinker
>>> \App\Models\Voucher::count();
// Should return 0 or number of seeded records
>>> exit

7. 🏃 Running the Application
Method 1: Separate Terminals (Recommended)

Terminal 1 - Backend Server:
cd backend
php artisan serve
# Server running at: http://localhost:8000

Terminal 2 - Frontend Server:
cd frontend
npm start
# or
npm run dev
# Server running at: http://localhost:3000

8. 🐳 Docker Setup (Optional)
Using Docker Compose
docker-compose.yml

version: '3.8'

services:
  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    volumes:
      - ./backend:/var/www/html
      - /var/www/html/vendor
    environment:
      - DB_CONNECTION=sqlite
      - DB_DATABASE=/var/www/html/database/vouchers.db
    networks:
      - voucher-network

  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      - REACT_APP_API_URL=http://localhost:8000
    depends_on:
      - backend
    networks:
      - voucher-network

networks:
  voucher-network:
    driver: bridge
Backend Dockerfile - backend/Dockerfile
FROM php:8.2-fpm

RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    sqlite3 \
    libsqlite3-dev

RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install pdo_mysql pdo_sqlite mbstring exif pcntl bcmath gd

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

COPY . .

RUN composer install --no-interaction

RUN chown -R www-data:www-data /var/www/html/storage \
    && chown -R www-data:www-data /var/www/html/bootstrap/cache

EXPOSE 8000

CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]

Frontend Dockerfile - frontend/Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]

Docker Commands

# Build and start containers
docker-compose up -d

# View logs
docker-compose logs -f

# Run migrations
docker-compose exec backend php artisan migrate

# Run seeds
docker-compose exec backend php artisan db:seed

# Access container shell
docker-compose exec backend bash
docker-compose exec frontend sh

# Stop containers
docker-compose down

# Rebuild and start
docker-compose up -d --build

Using Laravel Sail (Alternative)
cd backend

# Install Sail
composer require laravel/sail --dev

# Start Sail
./vendor/bin/sail up -d

# Run migrations
./vendor/bin/sail artisan migrate

# Access the application
# Backend: http://localhost

9. 📚 API Documentation
Endpoint: Check Voucher Existence
POST /api/check

Request Body:
json

{
    "flightNumber": "GA102",
    "date": "2025-07-12"
}

Response (200 OK):
json

{
    "exists": false
}

Endpoint: Generate Vouchers
text

POST /api/generate

Request Body:
json

{
    "name": "Sarah Johnson",
    "id": "CS98765",
    "flightNumber": "ID202",
    "date": "2025-07-15",
    "aircraft": "Airbus 320"
}

Response (201 Created):
json

{
    "success": true,
    "seats": ["3B", "7C", "14D"]
}

Error Response (409 Conflict):
json

{
    "success": false,
    "message": "Vouchers have already been generated for this flight and date"
}

Validation Error Response (422):
json

{
    "message": "The given data was invalid.",
    "errors": {
        "date": ["Date must be in YYYY-MM-DD format"],
        "aircraft": ["Invalid aircraft type. Must be ATR, Airbus 320, or Boeing 737 Max"]
    }
}

10. 🧪 Testing
Backend Tests

cd backend

# Run all tests
php artisan test

# Run specific test suite
php artisan test --testsuite=Feature
php artisan test --testsuite=Unit

# Run specific test file
php artisan test --filter VoucherApiTest
php artisan test --filter SeatGeneratorServiceTest

# Run with coverage report (requires Xdebug)
php artisan test --coverage

# Run tests in parallel
php artisan test --parallel

Frontend Tests
cd frontend

# Run tests
npm test

# Run tests with coverage
npm test -- --coverage

# Run tests in watch mode
npm test -- --watch

# Run specific test file
npm test -- VoucherForm.test.js

11. 📊 Support
Application Access

    Frontend: http://localhost:3000

    Backend API: http://localhost:8000

    API Check: http://localhost:8000/api/check

    API Generate: http://localhost:8000/api/generate

Logs Location

    Backend Logs: backend/storage/logs/laravel.log

    Frontend Logs: Browser console (F12)

Quick Commands Reference
# Backend
php artisan serve              # Start server
php artisan migrate             # Run migrations
php artisan migrate:fresh       # Reset and run migrations
php artisan db:seed             # Run seeders
php artisan tinker              # Interactive shell
php artisan test                # Run tests
php artisan config:clear        # Clear config cache
php artisan route:list          # List all routes

# Frontend
npm start                       # Start dev server
npm run build                   # Build for production
npm test                        # Run tests
npm run eject                   # Eject from react-scripts

Environment Files Summary

project/
├── backend/.env                # Backend configuration
├── backend/.env.example        # Backend template
├── frontend/.env               # Frontend configuration
└── frontend/.env.example       # Frontend template

📝 License

This project is created for the Test Astronacci International Technical Assessment.







