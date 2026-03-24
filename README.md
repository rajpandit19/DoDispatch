div align="center">

# 💌 DoDispatch

### *Say what your heart couldn't.*

**Anonymous messaging platform — built with Node.js, Express & MySQL**

[![Node.js](https://img.shields.io/badge/Node.js-18+-339933?style=flat-square&logo=node.js&logoColor=white)](https://nodejs.org)
[![Express](https://img.shields.io/badge/Express-4.x-000000?style=flat-square&logo=express&logoColor=white)](https://expressjs.com)
[![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat-square&logo=mysql&logoColor=white)](https://mysql.com)
[![JWT](https://img.shields.io/badge/Auth-JWT-FB015B?style=flat-square&logo=jsonwebtokens&logoColor=white)](https://jwt.io)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)

[Features](#-features) · [Quick Start](#-quick-start) · [API Docs](#-api-reference) · [Frontend](#-frontend) · [Deploy](#-deployment)

---

![DoDispatch Banner](https://placehold.co/900x300/0a0a0f/7eb8f7?text=DoDispatch+%E2%80%94+Anonymous+Messaging&font=montserrat)

</div>

---

## 🌟 What is DoDispatch?

DoDispatch lets people send anonymous messages — apologies they never sent, love they were too afraid to confess, gratitude they always meant to express. Messages are delivered in-app, via email, or SMS. The sender's identity is always protected.

---

## ✨ Features

- 🎭 **True Anonymous Messaging** — send without an account; `sender_id` stored as `NULL`
- 🔐 **JWT Authentication** — secure signup, login, email verification, password reset
- 📥 **Inbox** — paginated, filterable, real-time unread count
- 📧 **Email Delivery** — branded HTML emails via Nodemailer / Gmail SMTP
- 💬 **SMS Delivery** — Twilio integration for SMS message delivery
- 🔔 **In-App Notifications** — notification log per user
- 🚫 **Block System** — block users from sending messages
- 😊 **Reactions** — emoji reactions on received messages
- 🛡️ **Security** — Helmet, rate limiting (3 tiers), input validation, bcrypt passwords
- 📊 **Logging** — Winston structured logs

---

## 🏗️ Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js 18+ |
| Framework | Express 4 |
| Database | MySQL 8 + mysql2 |
| Auth | JWT (jsonwebtoken) |
| Password Hashing | bcryptjs (12 rounds) |
| Email | Nodemailer (Gmail SMTP) |
| SMS | Twilio |
| Validation | express-validator |
| Security | helmet, express-rate-limit, cors |
| Logging | Winston |

---

## 📁 Project Structure

```
dodispatch-backend/
├── src/
│   ├── server.js                   # Entry point
│   ├── app.js                      # Express setup, middleware, routes
│   ├── database/
│   │   ├── db.js                   # MySQL connection pool
│   │   ├── migrate.js              # Creates all tables
│   │   └── seed.js                 # Sample data for testing
│   ├── controllers/
│   │   ├── auth.controller.js      # Register, login, verify, reset password
│   │   ├── message.controller.js   # Send, react, delete messages
│   │   ├── inbox.controller.js     # Inbox, notifications, mark read
│   │   └── user.controller.js      # Profile, block/unblock
│   ├── routes/
│   │   ├── auth.routes.js
│   │   ├── message.routes.js
│   │   ├── inbox.routes.js
│   │   └── user.routes.js
│   ├── middleware/
│   │   ├── auth.js                 # JWT required
│   │   ├── optionalAuth.js         # JWT optional (for anonymous sends)
│   │   ├── validate.js             # Input validation errors
│   │   └── errorHandler.js         # Global error handler
│   ├── services/
│   │   ├── mail.service.js         # Email templates + delivery
│   │   ├── sms.service.js          # Twilio SMS
│   │   └── notification.service.js # In-app notifications
│   └── utils/
│       ├── jwt.js                  # Sign / verify token
│       ├── AppError.js             # Operational error class
│       └── logger.js               # Winston logger
├── dodispatch_integrated.html      # Frontend (cinematic ad + send form)
├── .env.example                    # Environment variable template
├── .gitignore
└── package.json
```

---

## 🚀 Quick Start

### Prerequisites
- Node.js 18+
- MySQL 8.0
- Git

### 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/dodispatch.git
cd dodispatch
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment

```bash
cp .env.example .env
```

Open `.env` and fill in your values:

```env
PORT=5000
NODE_ENV=development

# MySQL
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=dodispatch

# JWT
JWT_SECRET=your_super_secret_key_change_this
JWT_EXPIRES_IN=7d

# Email (Gmail SMTP)
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USER=your_email@gmail.com
MAIL_PASS=your_app_password

# Twilio (optional - for SMS)
TWILIO_ACCOUNT_SID=ACxxxxxxxxxxxxxxxx
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_PHONE_NUMBER=+1XXXXXXXXXX

# Frontend URL (for CORS)
FRONTEND_URL=http://localhost:3000
```

### 4. Set up the database

```sql
-- In MySQL:
CREATE DATABASE dodispatch CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 5. Run migrations

```bash
npm run migrate
```

### 6. (Optional) Seed sample data

```bash
npm run seed
# Creates 3 test users with password: Password123!
# → arjun_dev / arjun@example.com
# → priya_k   / priya@example.com
# → rohan_m   / rohan@example.com
```

### 7. Start the server

```bash
npm run dev        # Development (auto-reload)
npm start          # Production
```

### 8. Verify it's running

```bash
curl http://localhost:5000/health
# → { "status": "ok", "service": "DoDispatch API" }
```

---

## 🗃️ Database Schema

```
users                  — accounts, credentials, profile
messages               — anonymous/named messages + delivery status
email_verifications    — email confirmation tokens
password_resets        — password reset tokens
message_reactions      — emoji reactions on messages
notifications          — in-app notification log
blocked_users          — block list (many-to-many)
```

---

## 📡 API Reference

**Base URL:** `http://localhost:5000/api`

All protected routes require:
```
Authorization: Bearer <jwt_token>
```

---

### 🔐 Auth  `/api/auth`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/register` | None | Create account |
| POST | `/login` | None | Login, receive JWT |
| GET | `/verify-email/:token` | None | Verify email address |
| POST | `/forgot-password` | None | Send password reset link |
| POST | `/reset-password` | None | Reset with token |
| GET | `/me` | ✅ Required | Get own user info |

**Register**
```json
POST /api/auth/register
{
  "username": "arjun_dev",
  "email": "arjun@example.com",
  "password": "SecurePass123!",
  "display_name": "Arjun Sharma",
  "phone": "+919876543210"
}
```

**Login**
```json
POST /api/auth/login
{
  "email": "arjun@example.com",
  "password": "SecurePass123!"
}
```
```json
// Response
{
  "success": true,
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": { "id": "...", "username": "arjun_dev", "email": "..." }
}
```

---

### ✉️ Messages  `/api/messages`

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/send` | Optional | Send anonymous/named message |
| GET | `/to/:username` | None | Public profile (send page) |
| POST | `/:message_id/react` | ✅ Required | Add emoji reaction |
| DELETE | `/:message_id` | ✅ Required | Delete from inbox |

**Send Anonymous Message**
```json
POST /api/messages/send
{
  "recipient_username": "priya_k",
  "content": "I've admired your work for a long time.",
  "is_anonymous": true,
  "delivery_method": "in_app"
}
```
> `delivery_method`: `"in_app"` | `"email"` | `"sms"`  
> No `Authorization` header = fully anonymous (sender stored as NULL)

---

### 📥 Inbox  `/api/inbox`  *(Auth Required)*

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/` | Get paginated inbox |
| GET | `/notifications` | Get notifications |
| GET | `/:message_id` | Get single message (auto marks read) |
| PATCH | `/:message_id/read` | Mark one as read |
| PATCH | `/mark-all-read` | Mark all as read |

```bash
GET /api/inbox?page=1&limit=20&filter=unread
```

---

### 👤 Users  `/api/users`  *(Auth Required)*

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/profile` | Get own profile + message count |
| PATCH | `/profile` | Update display name, bio, avatar |
| POST | `/block/:username` | Block a user |
| DELETE | `/block/:username` | Unblock a user |
| DELETE | `/account` | Deactivate account |

---

## 🖥️ Frontend

The repo includes `dodispatch_integrated.html` — a cinematic single-page frontend with:

- 6-scene animated ad (Problem → Breakthrough → Transformation)
- Sign In / Register modal
- Anonymous message send form
- Live inbox drawer with unread count
- Real-time toast notifications

**To connect frontend to backend**, change one line in the HTML:
```js
const API = 'http://localhost:5000/api'; // ← update for production
```

Open `dodispatch_integrated.html` directly in your browser.

---

## 🔒 Security Features

| Feature | Implementation |
|---|---|
| Password hashing | bcryptjs, 12 salt rounds |
| Auth tokens | JWT with configurable expiry |
| HTTP headers | Helmet.js |
| Rate limiting | 200 req/15min global · 20 req/15min auth · 10 msg/min send |
| Input validation | express-validator on all routes |
| Anonymous privacy | `sender_id = NULL` for anonymous messages |
| Block list | Prevents blocked users from sending |
| Email enumeration | Forgot-password always returns 200 |

---

## ☁️ Deployment

### Deploy on Railway (Free)

```bash
# Install Railway CLI
npm install -g @railway/cli

# Login and deploy
railway login
railway init
railway up
```

Add your environment variables in the Railway dashboard.

### Deploy on Render (Free)

1. Push to GitHub
2. Go to [render.com](https://render.com) → New Web Service
3. Connect your GitHub repo
4. Set **Build Command**: `npm install`
5. Set **Start Command**: `npm start`
6. Add all environment variables from `.env.example`

### Deploy on VPS (Ubuntu)

```bash
# Install PM2
npm install -g pm2

# Start with PM2
pm2 start src/server.js --name dodispatch

# Auto-restart on reboot
pm2 startup
pm2 save
```

---

## 🧪 Testing the API

Use [Postman](https://postman.com) or curl:

```bash
# Health check
curl http://localhost:5000/health

# Register
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"testuser","email":"test@example.com","password":"Pass1234!","display_name":"Test User"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"Pass1234!"}'

# Send anonymous message (no token needed)
curl -X POST http://localhost:5000/api/messages/send \
  -H "Content-Type: application/json" \
  -d '{"recipient_username":"testuser","content":"Hello from anonymous!","is_anonymous":true}'
```

---

## 🌍 Environment Variables

| Variable | Description | Required |
|---|---|---|
| `PORT` | Server port (default 5000) | No |
| `NODE_ENV` | `development` / `production` | Yes |
| `DB_HOST` | MySQL host | Yes |
| `DB_PORT` | MySQL port | No |
| `DB_USER` | MySQL username | Yes |
| `DB_PASSWORD` | MySQL password | Yes |
| `DB_NAME` | MySQL database name | Yes |
| `JWT_SECRET` | JWT signing secret (keep strong!) | Yes |
| `JWT_EXPIRES_IN` | Token expiry e.g. `7d` | No |
| `MAIL_HOST` | SMTP host | Yes |
| `MAIL_PORT` | SMTP port | No |
| `MAIL_USER` | SMTP username | Yes |
| `MAIL_PASS` | SMTP password / app password | Yes |
| `TWILIO_ACCOUNT_SID` | Twilio account SID | Optional |
| `TWILIO_AUTH_TOKEN` | Twilio auth token | Optional |
| `TWILIO_PHONE_NUMBER` | Twilio sender number | Optional |
| `FRONTEND_URL` | Frontend URL for CORS | Yes |

---

## 🤝 Contributing

1. Fork the repo
2. Create your feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'Add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**DoDispatch** — *Don't hold it. Send it.*

Made with ❤️ | [Report Bug](../../issues) · [Request Feature](../../issues)

</div>
