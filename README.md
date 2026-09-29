

# Online Grocery Management System (OGMS)

A web-based grocery shopping and store management application developed as a Final Year Project at Virtual University of Pakistan. Customers can browse and search groceries, manage their carts, place orders, and view order status. Administrators manage catalogues, products, inventory, customers, orders, and reports.

## Features

### Customer experience

- Account registration, sign-in, and profile management.
- Product browsing by catalogue and category.
- Product search and filtering by brand, price, and availability.
- Product details, shopping cart, and checkout.
- Cash-on-delivery ordering, order history, and order-status notifications.
- Information pages for FAQs, contact details, and store policies.

### Administration

- Dashboard with store activity and reporting.
- Catalogue, category, and product management.
- Inventory and stock management.
- Product image uploads.
- Customer and order management.
- Reports with charts and Excel/PDF exports.

## Technology stack

| Layer | Technologies |
| --- | --- |
| Frontend | React 19, HTML, CSS, Bootstrap 5, React Router |
| Frontend tooling and API requests | Vite 7, Axios |
| Backend | Node.js, Express, MySQL2 |
| Database | MySQL |
| Authentication | JSON Web Tokens (JWT), bcrypt |
| File uploads | Multer |
| Password-reset email integration | Nodemailer (SMTP configuration required) |
| Reports | Chart.js, SheetJS, jsPDF |

## Architecture

The application follows the three-tier architecture described in the software report:

1. **Presentation layer:** React components and pages provide the customer storefront and administrator interface.
2. **Application layer:** Node.js and Express handle API requests, authentication, uploads, and business logic through routes, controllers, and middleware.
3. **Database layer:** MySQL stores accounts, catalogue data, products, orders, and related records.

The React frontend communicates with the Express API over HTTP. The backend connects to MySQL using MySQL2.

## Project structure

```text
Application/
├── client/                       # React frontend
│   ├── public/                   # Static assets
│   └── src/                      # Pages, components, and styles
├── server/                       # Express backend
│   ├── config/                   # Database connection
│   ├── controllers/              # Request handling
│   ├── middleware/               # Authentication and uploads
│   ├── migrations/               # Database maintenance scripts
│   ├── routes/                   # API routes
│   ├── uploads/                  # Catalogue and product images
│   └── server.js                 # Server entry point
├── DB/
│   └── ogms_db.public.sql        # Schema and public catalogue data
└── README.md
```

## Prerequisites

- Node.js and npm compatible with the project's Vite 7 dependency.
- A MySQL server and access to the MySQL command-line client.
- Git, if cloning the repository from the terminal.

The supplied SQL export uses MySQL-specific options, including `utf8mb4_0900_ai_ci`, GTID statements, and binary-log settings. The target MySQL server must support these options; see [Troubleshooting](#troubleshooting) for import errors.

## Local setup

### 1. Get the project

```bash
git clone https://github.com/fahdullah/FYP-OGMS-Online-Grocery-Management-System-.git
cd FYP-OGMS-Online-Grocery-Management-System-
```

If you already have the project, open its `Application` folder instead. The commands below assume the project root contains `client`, `server`, and `DB`.

### 2. Import the database

**The import drops and recreates the included tables. Back up an existing `ogms_db` database before importing.**

Start your MySQL server. From a terminal at the project root, connect with your database account:

```bash
mysql -u root -p
```

Enter your MySQL password when prompted. At the `mysql>` prompt, import the public database:

```sql
SOURCE DB/ogms_db.public.sql;
USE ogms_db;
SHOW TABLES;
EXIT;
```

The export includes statements to create and select `ogms_db`. Check the import output for errors before starting the application. Replace `root` with your own database username if needed.

The public export includes table definitions, catalogues, categories, and products. It omits administrator and customer records, password hashes, carts, orders, and notifications. The original private export is excluded from Git.

### 3. Configure and start the backend

Open a terminal at the project root:

```bash
cd server
npm install
```

Create a file named `.env` inside `server`:

```dotenv
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_mysql_password
DB_NAME=ogms_db
JWT_SECRET=replace_with_a_long_random_secret
```

Use your own database credentials. If your local database account has no password, leave `DB_PASSWORD=` empty. Keep `.env` private; it is excluded from Git.

Generate a random JWT secret with:

```bash
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
```

Paste the generated value into `JWT_SECRET`, then start the server:

```bash
npm run dev
```

Keep this terminal open. Visit [the local API](http://localhost:5000/) to check for the `API is running...` response, and check the terminal for a successful database connection.

### 4. Start the frontend

Open a second terminal at the project root:

```bash
cd client
npm install
npm run dev
```

Open the URL printed by Vite, usually [http://localhost:5173](http://localhost:5173).

### 5. Create accounts

Register a customer account through the application's sign-up form. No customer or administrator login credentials are included in the public database.

For administrator access, provision a record locally in the `admins` table with `full_name`, `email`, a bcrypt-generated `password_hash`, and `is_active = 1`. Use your own password and store only its bcrypt hash. Customer registration does not create administrator accounts.

## Available commands

Run each command from the indicated directory:

| Directory | Command | Purpose |
| --- | --- | --- |
| `server` | `npm run dev` | Start the API with automatic restart |
| `server` | `npm start` | Start the API with Node.js |
| `client` | `npm run dev` | Start the Vite development server |
| `client` | `npm run build` | Build the frontend into `client/dist` |
| `client` | `npm run preview` | Preview the built frontend locally |
| `client` | `npm run lint` | Run the frontend's configured lint checks |

## Configuration notes

- **API address:** Frontend requests currently use `http://localhost:5000`. Changing the backend port or hosting address also requires updating those references in `client/src`.
- **Images:** Keep `server/uploads` with the project. The backend serves these files under `/uploads`; database image paths depend on them.
- **Password-reset email:** `server/controllers/authController.js` contains placeholder Ethereal SMTP credentials. Configure a working mail transport before expecting password-reset emails to be delivered.
- **Hosting:** These instructions cover local development. Publishing the repository on GitHub does not host the frontend, API, or database.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Database connection fails | Confirm the database service is running and `server/.env` matches its credentials. |
| SQL import reports an unknown collation or unsupported syntax | Check the target MySQL version against the export's collation and SQL options. Adapt a separate copy of the export if needed. |
| SQL import reports GTID or binary-log permission errors | The export contains `GTID_PURGED` and `SQL_LOG_BIN` statements. Ask the database administrator to review them, or prepare a local import copy without the replication-specific statements when replication restoration is not required. |
| Product images are missing | Confirm the backend is running and the corresponding files exist in `server/uploads`. |
| Frontend cannot reach the API | Check that the backend is available at `http://localhost:5000` and inspect its terminal output. |
| Port is already in use | Stop the conflicting application or configure another port. Update frontend API references if the backend port changes. |
| Administrator sign-in fails | The public export has no administrator account. Provision one locally with a valid bcrypt password hash. |
| Password-reset email does not arrive | Replace the placeholder SMTP configuration with a working mail transport. |

## Current scope and future enhancements

The project is developed for a single store and a local development environment. Checkout currently uses cash on delivery. Order tracking displays order status within the application.

The software report identifies the following as future enhancements:

- Online payment gateway integration.
- Real-time delivery tracking and advanced logistics.
- A mobile application.
- Multiple-store support and advanced inventory management.
- Customer reviews and feedback.
- Personalized product recommendations.

These enhancements are outside the current implementation.

## Academic background

The project follows the **VU Process Model**, combining structured development phases with iterative review and refinement. The software report covers requirements, use cases, architecture, sequence and class diagrams, database design, interfaces, and test-case specifications.

| Role | Name |
| --- | --- |
| Student developer | Fahad Ullah |
| Student developer | Muhammad Ali Khaliq |
| Project supervisor | Warda Fiaz |

**Institution:** Virtual University of Pakistan
