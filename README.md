# SmartStock: AI-Powered POS & Inventory Management System

**SmartStock** is an enterprise-grade, full-stack Point of Sale (POS) and intelligent inventory management platform built for **Lanka Trading**, a household retail showroom network operating across a **Main Showroom** and **02 dedicated storage warehouses (Store 01 & Store 02)**.

The system transitions traditional manual ledger operations into an automated, real-time, AI-assisted digital ecosystem—eliminating transactional latency, human math errors, and multi-location inventory desynchronization.

## 📌 Client Context: Lanka Trading

Lanka Trading handles high-value, individual-unit household appliances (e.g., refrigerators, washing machines, air conditioners) across three physical nodes:

1. **Main Showroom:** High-traffic retail sales desk and customer hub.

2. **Storage Store 01:** Primary bulk warehouse for large appliance stocks.

3. **Storage Store 02:** Auxiliary storage for high-velocity fast-moving inventory.

Before **SmartStock**, sales staff relied on physical ledgers and phone calls to verify warehouse stock, resulting in negative stock anomalies, prolonged customer queue times, and stockout revenue loss. SmartStock provides a **single source of truth** across all three locations.

## ✨ Key Features

### 🛒 Point of Sale (POS) Terminal

* **Live SKU Autocomplete:** Sub-second instant search for products, bypassing manual ledger scanning.

* **Dynamic Cart Engine:** Automated calculation of subtotals, configurable taxes (e.g., VAT/GST), and promotional discounts.

* **Atomic Checkout:** Guaranteed stock allocation using database transaction locks to prevent double-selling across locations.

### 🏢 Multi-Location Inventory Synchronization

* **Real-Time Stock Matrix:** Live status visibility across Main Showroom, Storage Store 01, and Storage Store 02.

* **Stock Transfer Management:** Audit-trailed inter-warehouse transfer workflows.

* **Negative Stock Prevention:** Automated validation preventing item sales when physical stock is zero.

### 🧠 AI-Powered Demand Forecasting & Business Intelligence

* **30-Day Sales Velocity (**$V$**):** Calculates daily consumption rates per SKU to estimate projected depletion dates.

* **Automated Restock Alerts:** Flags SKUs as `CRITICAL`, `WARNING`, or `HEALTHY` based on sales trajectory.

* **Interactive Financial Dashboard:** Visual revenue aggregations, Cost of Goods Sold (COGS), and profit margin analytics powered by Recharts.

### 🔒 Enterprise Security & Roles

* **Role-Based Access Control (RBAC):** Admin (Store Manager), Cashier, and Warehouse Keeper roles.

* **Authentication:** Secure password hashing with `bcrypt` and session persistence via JSON Web Tokens (JWT).

* **Audit Trails:** Every transaction and stock modification is logged with Staff ID, Timestamp, and Store ID.

## 🛠️ System Architecture & Tech Stack

SmartStock follows a decoupled 3-tier Client-Server Architecture:

```
┌─────────────────────────────────────────────────────────┐
│                    React.js Frontend                    │
│          (Virtual DOM, Tailwind CSS, Recharts)          │
└────────────────────────────┬────────────────────────────┘
                             │ REST API / JSON Payload
┌────────────────────────────▼────────────────────────────┐
│                    Node.js & Express                    │
│            (Business Logic, Auth, AI Math)              │
└────────────────────────────┬────────────────────────────┘
                             │ Prisma ORM
┌────────────────────────────▼────────────────────────────┐
│                  PostgreSQL Database                    │
│           (ACID Transactions, Normalized Schemas)       │
└─────────────────────────────────────────────────────────┘

```

| Layer | Technology | Purpose | 
| ----- | ----- | ----- | 
| **Frontend** | React.js (Vite), Tailwind CSS | Fast UI, state management, zero-reload checkout | 
| **Backend** | Node.js, Express.js | API routing, JWT middleware, business rules | 
| **Database** | PostgreSQL | Relational storage, multi-table ACID guarantees | 
| **ORM** | Prisma ORM | Schema migrations, type-safe queries, `$transaction` API | 
| **Security** | `jsonwebtoken`, `bcryptjs` | Token authentication and cryptographic password hashing | 
| **Charts** | Recharts | Data visualization for sales analytics | 

## 🧮 AI Velocity Formula Logic

SmartStock utilizes a statistical velocity model to determine stock depletion trajectories.

For any given product $i$, the **Daily Sales Velocity (**$V_i$**)** over a 30-day window ($t = 30$) is computed as:

$$
V_i = \frac{U_{i, t}}{30}
$$

Where:

* $U_{i, t}$ is the total units of product $i$ sold across all Lanka Trading locations in the past 30 days.

The **Days Remaining Until Stockout (**$D_i$**)** is calculated as:

$$
D_i = \frac{S_{i, \text{total}}}{V_i}
$$

Where:

* $S_{i, \text{total}} = S_{i, \text{Showroom}} + S_{i, \text{Store01}} + S_{i, \text{Store02}}$ represents total remaining physical stock.

If $D_i \le 7$ days, SmartStock triggers a `CRITICAL` reorder alert on the Manager Dashboard with a recommended replenishment quantity:

$$
Q_{\text{reorder}} = (V_i \times 30) - S_{i, \text{total}}
$$

## 📁 Repository Structure

```
smartstock/
├── backend/
│   ├── config/          # DB connection & environment setups
│   ├── controllers/     # Route logic (auth, pos, inventory, analytics)
│   ├── middleware/      # JWT authentication & error handlers
│   ├── prisma/          # Prisma schema, migrations, and seed scripts
│   │   └── schema.prisma
│   ├── routes/          # API route definitions
│   ├── utils/           # AI mathematical formulas & helper functions
│   ├── index.js         # Express server entry point
│   └── package.json
│
├── frontend/
│   ├── public/          # Static assets & icons
│   ├── src/
│   │   ├── assets/      # Images & design files
│   │   ├── components/  # Reusable UI (Cart, ProductCard, Navbar, Modal)
│   │   ├── context/     # Auth Context & Cart Context state
│   │   ├── pages/       # Dashboard, POS Terminal, Inventory, Reports
│   │   ├── services/    # Axios API client instances
│   │   ├── App.jsx      # React router & layout wrap
│   │   └── main.jsx
│   ├── tailwind.config.js
│   └── package.json
│
└── README.md

```

## 🚀 Quick Start Guide

### Prerequisites

* **Node.js** (v18.x or higher)

* **npm** (v9.x or higher)

* **PostgreSQL** (v14.x or higher) instance running locally or hosted (e.g., Supabase / Railway)

### 1. Backend Setup

1. **Navigate to the backend folder:**

   ```
   cd backend
   
   ```

2. **Install dependencies:**

   ```
   npm install
   
   ```

3. **Configure Environment Variables:**
   Create a `.env` file in the `backend/` directory:

   ```
   PORT=5000
   DATABASE_URL="postgresql://username:password@localhost:5432/smartstock_db?schema=public"
   JWT_SECRET="your_super_secret_jwt_key_here"
   NODE_ENV="development"
   
   ```

4. **Run Database Migrations & Seed Data:**

   ```
   npx prisma migrate dev --name init
   npx prisma db seed
   
   ```

5. **Start the Express Backend Server:**

   ```
   npm run dev
   
   ```

   *The server will start on `http://localhost:5000`.*

### 2. Frontend Setup

1. **Navigate to the frontend folder:**

   ```
   cd ../frontend
   
   ```

2. **Install dependencies:**

   ```
   npm install
   
   ```

3. **Configure Environment Variables:**
   Create a `.env` file in the `frontend/` directory:

   ```
   VITE_API_BASE_URL="http://localhost:5000/api"
   
   ```

4. **Start the Frontend Development Server:**

   ```
   npm run dev
   
   ```

   *Open your browser and navigate to `http://localhost:5173`.*

## 📡 Key API Endpoints

### Authentication

* `POST /api/auth/login` - Authenticate staff & receive JWT token.

* `GET /api/auth/me` - Validate current session.

### POS & Sales

* `GET /api/products/search?q={query}` - Instant SKU/Name lookup.

* `POST /api/sales/checkout` - Atomic transaction processing & invoice creation.

### Inventory (Multi-Store)

* `GET /api/inventory/locations` - Fetch live stock levels for Showroom, Store 01, and Store 02.

* `POST /api/inventory/transfer` - Initiate stock movement between storage nodes.

### Analytics & AI

* `GET /api/analytics/dashboard` - Financial summary (Revenue, COGS, Profit).

* `GET /api/analytics/forecast` - AI sales velocity & critical restock recommendations.

## 🧪 Testing & Performance Results

* **Functional Validation:** API endpoints verified using Postman & Browser DevTools Network Tab.

* **User Acceptance Testing (UAT):** Tested directly against legacy paper ledger workflows at Lanka Trading.

* **Performance Benchmark:**

  * Legacy Search Time: \~90 seconds (manual ledger lookup)

  * SmartStock Search Time: **< 1 second** (90% reduction in processing latency)

## 🎓 Academic Attributions

* **Developer:** Thanzeel Muhaideen

* **Degree Program:** BSc (Hons) in Applied Computing

* **Module:** CN6000 Final Year Project

* **Client Organization:** Lanka Trading (Household Retail Showroom Network)

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.
