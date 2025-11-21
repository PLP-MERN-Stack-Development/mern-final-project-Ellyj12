# Swapper

A full-stack marketplace for bartering and free gifting. Swapper lets community members publish listings, filter available items, and negotiate trades or free pickups through a guided swap workflow. The project pairs a React + Vite front end with a Node.js/Express API backed by MongoDB Atlas and Cloudinary for asset storage.

## Live Demo

> **Update this link after deployment:** [https://your-deployment-url.example.com](https://your-deployment-url.example.com)

## Video Walkthrough

> **Update after recording a 5-10 minute demo:** [https://youtu.be/your-demo-link](https://youtu.be/your-demo-link)

## Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Screenshots](#screenshots)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Environment Variables](#environment-variables)
  - [Installation](#installation)
  - [Running Locally](#running-locally)
  - [Testing](#testing)
  - [Building for Production](#building-for-production)
- [API Overview](#api-overview)
- [Database Index Management](#database-index-management)
- [Project Scripts](#project-scripts)
- [Contributing](#contributing)
- [License](#license)

## Features

- **User authentication & profiles** - Secure registration/login with persisted auth state, profile photos, and location metadata.
- **Item marketplace** - Create, browse, and filter listings by category, condition, type (Trade/Free), keywords, and pagination.
- **Swap workflow** - Initiate swap or free-pickup requests, manage pending/incoming trades, accept/decline/cancel/complete swaps, and issue completion codes.
- **Dashboard insights** - Personalized dashboard summarising listings, pending requests, historical swaps, and quick actions.
- **Responsive UI** - Modern React interface built with shadcn/ui components, optimized for desktop and mobile.
- **Automated testing** - Vitest + Testing Library coverage for core client flows and Jest + Supertest suites for API endpoints.

## Architecture

```
├── client/        # React + Vite front end (TypeScript)
│   ├── src/
│   │   ├── features/
│   │   ├── components/
│   │   └── tests/functional/
│   └── vite.config.ts
├── server/        # Express API (ES modules)
│   ├── controllers/
│   ├── models/
│   ├── routes/
│   ├── middlewear/
│   ├── config/db.js
│   └── scripts/syncIndexes.js
└── README.md
```

Back end and front end are developed in separate workspaces but share a common repository root. The API exposes REST endpoints consumed by the React app, while MongoDB Atlas stores user, item, category, and swap documents.

## Tech Stack

| Layer        | Technologies |
|--------------|--------------|
| Front end    | React 19, Vite, TypeScript, Zustand, React Hook Form, shadcn/ui, Tailwind CSS |
| Back end     | Node.js, Express 5, Mongoose 8, MongoDB Atlas, Cloudinary, JWT |
| Testing      | Vitest, Testing Library, Jest, Supertest, MongoDB Memory Server |
| Tooling      | ESLint, Prettier, Vite React Compiler, Nodemon |

## Screenshots

> Replace the image paths below with actual screenshots (commit them to `docs/screenshots/`).

![Home Listings](docs/screenshots/items-grid.png)
![Item Detail & Swap Dialog](docs/screenshots/item-detail.png)
![User Dashboard](docs/screenshots/dashboard.png)

## Getting Started

### Prerequisites

- **Node.js** v18 or newer
- **npm** v9 or newer (bundled with Node)
- **MongoDB Atlas** cluster (or a local MongoDB instance for development)
- **Cloudinary** account for image uploads

### Environment Variables

Create a `.env` file inside `server/` with the following variables:

```ini
MONGO_URI=your-mongodb-atlas-connection-string
PORT=5000
NODE_ENV=development
JWT_SECRET=your-generated-secret
CLIENT_URL=http://localhost:5173
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret
```

If you plan to run the build output, add any additional environment variables required by your hosting platform.

For local testing you can generate a JWT secret with PowerShell:

```powershell
$bytes = New-Object byte[] 32
[Security.Cryptography.RandomNumberGenerator]::Create().GetBytes($bytes)
[Convert]::ToBase64String($bytes)
```

### Installation

```bash
# Clone the repository
git clone https://github.com/Ellyj12/Swapper.git
cd Swapper

# Install front-end dependencies
cd client
npm install

# Install back-end dependencies
cd ../server
npm install
```

### Running Locally

Start the API:

```bash
cd server
npm run dev
```

Start the React app (in a separate terminal):

```bash
cd client
npm run dev
```

Visit [http://localhost:5173](http://localhost:5173) to use the app. The API listens on `http://localhost:5000` by default.

### Testing

Client tests (Vitest + Testing Library):

```bash
cd client
npm run test -- --coverage
```

Server tests (Jest + Supertest):

```bash
cd server
npm run test
```

### Building for Production

Build the front end and serve static assets from `/client/dist` via your hosting solution:

```bash
cd client
npm run build
```

Run the API in production mode:

```bash
cd server
npm start
```

## API Overview

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST   | `/api/auth/register` | Register a new user with optional profile photo |
| POST   | `/api/auth/login` | Authenticate and retrieve JWT credentials |
| GET    | `/api/items` | Browse/filter paginated listings |
| GET    | `/api/items/my-items` | Fetch the authenticated user's listings |
| GET    | `/api/items/:id` | Fetch a single item detail |
| POST   | `/api/items/create` | Create a listing with up to 5 images |
| DELETE | `/api/items/:id` | Delete a listing the user owns |
| GET    | `/api/swaps/my-swaps` | Retrieve swaps the user participates in |
| POST   | `/api/swaps/create` | Start a trade or free pickup request |
| POST   | `/api/swaps/:id/accept` | Accept an incoming swap request |
| POST   | `/api/swaps/:id/decline` | Decline an incoming swap |
| POST   | `/api/swaps/:id/cancel` | Cancel a pending request |
| POST   | `/api/swaps/:id/complete` | Finalize a swap with confirmation codes |
| GET    | `/api/dashboard` | Summary statistics for listings and swaps |
| GET    | `/api/categories` | Retrieve available item categories |

All protected routes expect an `Authorization: Bearer <token>` header from a prior login.

## Database Index Management

Indexes are defined in each Mongoose schema. During development and testing the connection enables `autoIndex`, ensuring schemas remain in sync. 

To force index synchronization (useful after deploying schema changes), run:

```bash
cd server
node scripts/syncIndexes.js
```

The script connects to your MongoDB instance, calls `syncIndexes()` for the User, Item, Swap, and Category models, and then closes the connection.

## Project Scripts

### Client (`/client`)

| Script | Description |
|--------|-------------|
| `npm run dev` | Start the Vite development server |
| `npm run lint` | ESLint checks |
| `npm run test` | Run Vitest unit/functional tests |
| `npm run build` | Create a production build |

### Server (`/server`)

| Script | Description |
|--------|-------------|
| `npm run dev` | Run Express API with nodemon |
| `npm start` | Start API in production mode |
| `npm run test` | Execute Jest test suite |

## Contributing

1. Fork the repository and create a feature branch.
2. Run linting and tests before pushing.
3. Open a pull request describing your changes and screenshots where relevant.

## License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.
