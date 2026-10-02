# MernEats

MernEats is a full-stack takeaway food ordering application. Customers can
search for restaurants, browse menus, manage a cart, pay through Stripe, and
track their orders. Restaurant owners can manage their restaurant details,
images, cuisines, and menu items.

## Features

- Search restaurants by city or town
- Browse restaurant details, cuisines, menus, prices, and delivery estimates
- Auth0-based user authentication and protected routes
- Restaurant-owner management screens
- Shopping cart and checkout flow
- Stripe Checkout payment processing and webhook-based order updates
- Order tracking with statuses such as `placed`, `paid`, `inProgress`,
  `outForDelivery`, and `delivered`
- Cloudinary image uploads for restaurant images
- Responsive React user interface

## Technology stack

### Frontend

- React 19 and TypeScript
- Vite
- React Router
- TanStack React Query
- Tailwind CSS
- Auth0 React SDK
- React Hook Form and Zod
- Radix UI and Lucide icons

### Backend

- Node.js and Express 5
- TypeScript
- MongoDB with Mongoose
- Auth0 JWT validation
- Stripe Checkout and webhooks
- Cloudinary image storage
- Express Validator

## Project structure

```text
.
├── backend/
│   ├── src/
│   │   ├── controllers/    # Request and business logic
│   │   ├── middleware/     # Authentication and validation
│   │   ├── models/         # Mongoose schemas
│   │   └── routes/         # REST API routes
│   └── package.json
├── frontend/
│   ├── src/
│   │   ├── api/            # API and React Query hooks
│   │   ├── components/     # Reusable UI components
│   │   ├── forms/          # Form sections and validation
│   │   ├── pages/          # Application pages
│   │   └── auth/           # Auth0 integration
│   └── package.json
└── vercel.json              # Frontend/backend deployment routing
```

## Prerequisites

- Node.js and npm
- A MongoDB database
- An Auth0 application and API
- A Cloudinary account
- A Stripe account
- The Stripe CLI for local webhook forwarding

## Getting started

Clone the repository and install dependencies in both applications:

```bash
git clone <repository-url>
cd mern-food-ordering-app

cd backend
npm install

cd ../frontend
npm install
```

### Backend environment variables

Create `backend/.env`:

```env
PORT=7000
MONGODB_CONNECTION_STRING=<your-mongodb-connection-string>

AUTH0_AUDIENCE=<your-auth0-api-audience>
AUTH0_ISSUER_BASE_URL=https://<your-auth0-domain>/

CLOUDINARY_CLOUD_NAME=<your-cloudinary-cloud-name>
CLOUDINARY_API_KEY=<your-cloudinary-api-key>
CLOUDINARY_API_SECRET=<your-cloudinary-api-secret>

STRIPE_API_KEY=<your-stripe-secret-key>
STRIPE_WEBHOOK_SECRET=<your-stripe-webhook-signing-secret>
FRONTEND_URL=http://localhost:5173
```

### Frontend environment variables

Create `frontend/.env`:

```env
VITE_API_BASE_URL=http://localhost:7000
VITE_AUTH0_DOMAIN=<your-auth0-domain>
VITE_AUTH0_CLIENT_ID=<your-auth0-client-id>
VITE_AUTH0_CALLBACK_URL=http://localhost:5173
VITE_AUTH0_AUDIENCE=<your-auth0-api-audience>
```

Configure the matching `http://localhost:5173` callback, logout, and web
origins in Auth0. Never commit environment files or secret values.

## Running locally

Start the backend in one terminal:

```bash
cd backend
npm run dev
```

The backend runs on `http://localhost:7000` by default. The development script
also starts Stripe CLI and forwards webhook events to:

```text
http://localhost:7000/api/order/checkout/webhook
```

If Stripe CLI is not installed or you do not need payment testing, start only
the API with:

```bash
npx nodemon
```

Start the frontend in another terminal:

```bash
cd frontend
npm run dev
```

The Vite development server is normally available at
`http://localhost:5173`.

## Available scripts

### Frontend

| Command | Description |
| --- | --- |
| `npm run dev` | Start the Vite development server |
| `npm run build` | Type-check and build the production frontend |
| `npm run lint` | Run ESLint |
| `npm run preview` | Preview the production build locally |

### Backend

| Command | Description |
| --- | --- |
| `npm run dev` | Start the API with Nodemon and Stripe webhook forwarding |
| `npm run stripe` | Forward Stripe events to the local webhook |
| `npm run build` | Compile TypeScript into `backend/dist` |
| `npm start` | Run the compiled backend |

## API overview

The backend exposes these main route groups:

| Route | Purpose |
| --- | --- |
| `GET /health` | Health check |
| `/api/restaurant` | Public restaurant search and details |
| `/api/my/user` | Authenticated user profile operations |
| `/api/my/restaurant` | Authenticated restaurant management |
| `GET /api/order` | Retrieve the authenticated user's orders |
| `POST /api/order/checkout/create-checkout-session` | Create a Stripe Checkout session |
| `POST /api/order/checkout/webhook` | Receive Stripe payment events |

Authenticated routes require an Auth0 bearer token.

## Deployment

The included `vercel.json` configures separate Vercel services:

- `frontend/` is deployed as the Vite application.
- `backend/` is deployed as the Express service.
- `/api/*` requests are rewritten to the backend service.
- All other requests are routed to the frontend service.

Set the production environment variables in the deployment platform before
deploying. Use the production frontend URL for `FRONTEND_URL`,
`VITE_API_BASE_URL`, and the Auth0 callback configuration.

## Security notes

- Keep `.env` files and API keys out of source control.
- Use Stripe webhook signing secrets to verify webhook events.
- Configure Auth0 audience and issuer values consistently between frontend and
  backend.
- Use separate development and production credentials where possible.
