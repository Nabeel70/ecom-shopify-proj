
<div align="center">

# Shopify Multi‑Vendor Marketplace (MERN + Realtime)

Robust multi‑vendor e‑commerce & marketplace platform: shop onboarding, product & event management, coupons, carts & checkout, order lifecycle, withdrawals, realtime conversations, and comprehensive admin controls. Built with a modular MERN stack and a separate Socket.io service for scalable realtime messaging.

</div>

---

## Table of Contents
1. Overview
2. Features
3. Tech Stack
4. Architecture
5. Project Structure
6. Getting Started
7. Environment Variables
8. API Overview
9. Realtime Flow
10. Security & Hardening
11. Testing (Planned)
12. Screenshots / UI (Placeholders)
13. Roadmap
14. Contributing
15. License
16. Acknowledgements

---

## 1. Overview
This repository contains a production‑oriented marketplace application supporting multiple sellers (shops) and end users. Core goals:
* Clear separation of concerns (controllers, middleware, models, utilities)
* Configurable via environment variables
* Extensible for future microservices (payments, notifications, analytics)
* Reusable UI component library with Redux state management

## 2. Features
### User & Auth
* Email activation workflow (users & sellers)
* JWT auth + (optionally) refresh pattern (extendable)
* Profile management & addresses (default / home / office)
* Password update & protected routes

### Marketplace
* Product CRUD (multi‑image uploads via Multer)
* Event (flash sale) creation & lifecycle (start/end dates)
* Category + best‑seller listings & filtering
* Product detail page: reviews, related items, seller info, stock & sold metrics

### Shopping & Orders
* Cart & wishlist management
* Coupon / discount codes (percentage & fixed, per shop or scoped products)
* Multi‑payment channel scaffolding (Stripe, PayPal, COD)
* Order creation, status progression, refund initiation
* Order tracking & receipt style summary

### Communication & Realtime
* Conversation + message persistence (MongoDB)
* Socket.io rooms for live chat
* Online/offline indication & image messaging

### Seller Operations
* Seller dashboard KPIs (balance, service fee handling, latest orders)
* Withdraw request workflow (admin approval)
* Bank detail management
* Event & coupon management

### Admin Controls
* Global oversight: users, sellers, products, events, orders, withdrawals
* Verification / approval actions
* Deletion & moderation

### Platform
* Centralized error & async handling middleware
* Email notifications (Nodemailer)
* Token utilities (activation, auth)
* Responsive UI (Tailwind + component abstractions)

## 3. Tech Stack
**Frontend:** React, React Router, Redux, Tailwind CSS, (optional Material UI), Axios, React‑Toastify

**Backend:** Node.js, Express, MongoDB (Mongoose), Multer, JSON Web Tokens, Nodemailer

**Realtime:** Socket.io (standalone service)

**Tooling:** Yarn / npm, ESLint (add), Prettier (add), Jest / React Testing Library (planned)

## 4. Architecture
```mermaid
flowchart LR
  subgraph Client[Frontend (React + Redux)]
    PAGES[Pages/Routes]
    CMPTS[Components]
    STORE[(Redux Store)]
  end

  subgraph API[Backend API (Express)]
    MW[Auth & Error Middleware]
    CTRL[Controllers]
    MODS[(Mongoose Models)]
    UTIL[Utils: tokens, mail, coupons]
  end

  subgraph RT[Realtime Service]
    SOCK[Socket.io Server]
  end

  Client <--> API
  Client <-- Realtime Events --> RT
  API <--> DB[(MongoDB)]
  API --> MAIL[(SMTP Provider)]
  API --> PAY[(Payment Providers)]
  API --> RT
```

## 5. Project Structure
```
backend/
  controller/        # Business logic per resource
  model/             # Mongoose schemas
  middleware/        # Auth, error, async wrappers
  db/                # Database connection
  utils/             # Tokens, mailer, helpers
  server.js          # Express bootstrap
frontend/
  src/
    pages/           # Route-level views
    components/      # Reusable UI units
    redux/           # Store, actions, reducers
    routes/          # Guarded route HOCs
    styles/          # Styling utilities
    static/          # Static data
socket/
  index.js           # Socket.io namespace/server
```

## 6. Getting Started
### Prerequisites
* Node.js (LTS recommended)
* MongoDB (local or Atlas)
* SMTP credentials (for email activation)
* (Optional) Stripe & PayPal sandbox keys

### Install
```bash
# Backend
cd backend
npm install

# Frontend
cd ../frontend
npm install

# Socket service
cd ../socket
npm install
```

### Development Run (3 terminals)
```bash
cd backend && npm run dev      # starts API (e.g. http://localhost:8000)
cd frontend && npm start       # starts React (http://localhost:3000)
cd socket && npm start         # starts Socket.io (e.g. http://localhost:4000)
```

### Build Frontend
```bash
cd frontend
npm run build
```

## 7. Environment Variables
Create `.env` files (do not commit secrets):

backend/.env
```
PORT=8000
MONGO_URI=mongodb://localhost:27017/ecommerce
JWT_SECRET=replace_with_secure_random
JWT_EXPIRE=5d
ACTIVATION_SECRET=replace_with_secure_random_activation
REFRESH_TOKEN_SECRET=replace_with_secure_random_refresh
REFRESH_TOKEN_EXPIRE=30d
SMTP_HOST=smtp.example.com
SMTP_PORT=587
SMTP_SERVICE=Gmail
SMTP_MAIL=you@example.com
SMTP_PASSWORD=app_password
CLIENT_URL=http://localhost:3000
STRIPE_SECRET_KEY=sk_test_xxx
STRIPE_PUBLISHABLE_KEY=pk_test_xxx
PAYPAL_CLIENT_ID=your_paypal_id
```

frontend/.env
```
REACT_APP_API_URL=http://localhost:8000
REACT_APP_SOCKET_URL=http://localhost:4000
REACT_APP_STRIPE_PUBLISHABLE_KEY=pk_test_xxx
REACT_APP_PAYPAL_CLIENT_ID=your_paypal_id
```

socket/.env
```
SOCKET_PORT=4000
CORS_ORIGIN=http://localhost:3000
```

## 8. API Overview (Representative)
User / Auth
* POST /api/v1/user/create-user
* POST /api/v1/user/activation
* POST /api/v1/user/login
* GET  /api/v1/user/getuser
* PUT  /api/v1/user/update-user
* GET  /api/v1/user/logout

Shop / Seller
* POST /api/v1/shop/create-shop
* POST /api/v1/shop/activation
* POST /api/v1/shop/login
* GET  /api/v1/shop/getSeller
* GET  /api/v1/shop/getAllSellers

Products
* POST /api/v1/product/create-product
* GET  /api/v1/product/get-all-products
* GET  /api/v1/product/:id
* DELETE /api/v1/product/:id

Events
* POST /api/v1/event/create-event
* GET  /api/v1/event/get-all-events
* DELETE /api/v1/event/:id

Coupons
* POST /api/v1/coupon/create
* GET  /api/v1/coupon/get-all
* DELETE /api/v1/coupon/:id

Orders
* POST /api/v1/order/create-order
* GET  /api/v1/order/get-all-orders
* GET  /api/v1/order/:id
* PUT  /api/v1/order/update-status

Withdrawals
* POST /api/v1/withdraw/create-request
* GET  /api/v1/withdraw/get-all
* PUT  /api/v1/withdraw/update-status

Messaging
* POST /api/v1/conversation/create
* GET  /api/v1/conversation/:id
* POST /api/v1/message/create
* GET  /api/v1/message/:conversationId

> NOTE: Verify and adjust to match the actual implemented routes & naming.

## 9. Realtime Messaging Flow
1. Authenticated client connects to Socket.io with token context
2. Joins conversation room (conversationId)
3. Emits message event (text/image)
4. Server persists message to MongoDB
5. Broadcast to all participants in room
6. (Optional) Trigger notifications/email/webhook

## 10. Security & Hardening
* Use HTTPS & secure cookies in production
* Store secrets outside VCS (vault / CI secrets)
* Validate & sanitize file uploads (Multer + MIME/type checks)
* Input validation (celebrate / joi or zod recommended future enhancement)
* Rate limiting & request throttling (add express-rate-limit)
* Helmet & CORS configuration
* Implement refresh token rotation & revocation lists (future)
* Index critical MongoDB fields (email, shopId, productId)

## 11. Testing (Planned)
Suggested initial coverage:
* Auth lifecycle (signup, activation, login)
* Product CRUD + permissions
* Coupon application logic
* Order creation + status transitions
* Messaging persistence

Add under `backend/tests` with Jest + Supertest; React Testing Library for UI states.

## 12. Screenshots / UI
Place images under `frontend/public/screenshots/` and reference:

| Area | Screenshot |
|------|------------|
| Home | ![Home](frontend/public/screenshots/home.png) |
| Product Detail | ![Product](frontend/public/screenshots/product.png) |
| Cart / Checkout | ![Checkout](frontend/public/screenshots/checkout.png) |
| Seller Dashboard | ![Seller](frontend/public/screenshots/seller.png) |
| Admin Dashboard | ![Admin](frontend/public/screenshots/admin.png) |
| Chat | ![Chat](frontend/public/screenshots/chat.png) |

## 13. Roadmap
* Role-based granular permissions (admin -> moderator -> support)
* Queue-based email & notification system (Bull / Redis)
* Inventory reservations & low-stock alerts
* Analytics & reporting module
* Payment abstraction service (multi-provider failover)
* Docker & docker-compose setup
* CI (GitHub Actions) with lint + test + build
* Load & performance testing suite (k6 / Artillery)

## 14. Contributing
1. Fork the repository
2. Create a feature branch: `git checkout -b feat/your-feature`
3. Commit changes (Conventional Commits preferred)
4. Push & open a Pull Request with context, screenshots, and testing notes
5. Await review & iterate

## 15. License
This project is licensed under the MIT License.

```
MIT License

Copyright (c) 2025 YOUR_NAME

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

Replace `YOUR_NAME` with the appropriate copyright holder.

## 16. Acknowledgements
If portions of this codebase originated from open‑source templates or other repositories, retain original LICENSE files and attribution per their terms. Please ensure compliance with each dependency's license (MIT, ISC, Apache 2.0, etc.).

---

### Disclaimer
API routes, variable names, and some flows are inferred from the existing structure. Adjust where your implementation differs.

---

Happy building! 🚀

Order History: Provide users with an overview of all their previous orders, allowing them to track deliveries and request refunds if necessary. <Br/>

Inbox and Chat: Enable users to communicate with sellers, fostering a seamless exchange of information. <Br/>

Address Management: Allow users to store multiple addresses for efficient checkout, including default, home, and office options. <Br/>

8️⃣ Seller Dashboard 👨🏻‍🔧

Product and Order Management: Provide sellers with an overview of their products and the latest orders, along with the ability to update delivery statuses. <Br/>

Event Creation: Allow sellers to create and manage events, including details such as event name, description, category, dates, and images. <Br/>

Shop Settings: This enables sellers to update their shop information, including images, addresses, phone numbers, and descriptions. <Br/>

Inbox and Communication: Facilitate communication between sellers and users, ensuring a smooth exchange of information. <Br/>

9️⃣ Admin Dashboard 👑

Admin Authentication: Implement secure login functionality for admins. <Br/>

Overview and Analytics: Provide admins with an overview of total earnings, all sellers, all orders, and the latest orders. <Br/>

Seller and User Management: Enable admins to manage sellers and users, including the ability to delete accounts if necessary. <Br/>

Product and Event Management: Allow admins to view all products and events in the database, facilitating efficient management. <Br/>

Withdrawal Management: Provide admins with the ability to verify seller withdrawal requests, update balances, and send email notifications. <Br/>

Image Management: Enable admins to delete images, ensuring data integrity and storage optimization. <Br/>

🚀 With the power of these cutting-edge technologies, the MERN Marketplace delivers a robust and feature-rich multi-vendor website. It ensures a seamless user experience, efficient data management, real-time communication, and secure transactions. Join me in revolutionizing the e-commerce experience by connecting buyers and sellers in a dynamic and user-friendly environment.

Feel free to reach out to me for more information or to explore collaboration opportunities.

#MERNMarketplace #E commerce #React #NodeJS #ExpressJS #MongoDB #SocketIO #TailwindCSS #MaterialUI #Innovation #OnlineShopping #RevolutionizingRetail

#### _**IMPORTANT NOTE**_ - 
This project does not have a mongoDB connection setup. Set up the connection based on the environments below.
- local development: create a config folder (make sure to name it .env) in the config folder, which exports your db.uri connection.
-  (make new folder `uploads`) in the backend.


## File structure
#### `client` - Holds the client application
- #### `public` - This holds all of our static files
- #### `src`
    - #### `assets` - This folder holds assets such as images, docs, and fonts
    - #### `components` - This folder holds all of the different components that will make up our views
      - Admin
      - cart
      - Checkout
      - Events
      - layout
      - Logout
      - Payment
      - Products
      - Profile
      - Route
      - Shop
      - Sign up
      - Wishlist
     - #### `pages` - This folder holds All pages Admin, shop, user
       - Shop
     - #### `redux` - This folder holds all states of the Web app
       - action
       - reducer
     - #### `static` - This folder holds Static file like logo categorie
    - #### `App.js` - This is what renders all of our browser routes and different views
    - #### `index.js` - This is what renders the react app by rendering App.js, should not change
- #### `package.json` - Defines npm behaviors and packages for the client
#### `server` - Holds the server application
- #### `config` - This holds our configuration files, like mongoDB uri
- #### `controller` - These hold all of the callback functions that each route will call
- #### `db` - These hold all of Data Base Connection
- #### `middleware` - These hold all error handle
- #### `models` - This holds all of our data models
- #### `uploads` - Store all image in hear
- #### `utils` - This holds all of our HTTP to URL. jwtToken and sand mail, Token gentrare
- #### `multer.js` - Sand mail login
- #### `server.js` - Defines npm behaviors and packages for the client
- #### `package.json` - Defines npm behaviors like the scripts defined in the next section of the README
#### `socket` - Socket.io is use to chaing feacher
  - .env
  - index.js
  - package.json
#### `.gitignore` - Tells git which files to ignore
#### `README` - This file!

---

💻 How to run the app locally! 🏃

### STEP-1
`git clone https://github.com/SahilMund/Ecommerce-Shopify.git`

### STEP-2
- `cd frontend`
- `yarn install`
- `yarn start`

### STEP-3
- `cd backend`
- `yarn install`
- create folder `uploads`
- create `confilg` folder and a `.env` file
- use your Cradincial in.env file

```
PORT = 8000
DB_URL = ""
JWT_SECRET_KEY = ""
JWT_EXPIRES = 7d
ACTIVATION_SECRET = 
SMPT_HOST = 'smtp.gmail.com'
SMPT_PORT = 465
SMPT_PASSWORD = 
SMPT_MAIL =
STRIPE_API_KEY = 
STRIPE_SECRET_KEY = 
```
- `yarn start`

### STEP-4

- `cd socket`
- `yarn install`
- create a `.env` file
```
PORT = 4000
```
- `yarn start`

