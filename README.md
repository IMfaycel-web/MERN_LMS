# Mindure

Mindure is a full-stack learning management system for creating, selling, and completing online courses. It provides dedicated student and educator workflows, secure identity management, payment processing, media delivery, progress tracking, ratings, and operational reporting.

## Overview

The application separates the user interface and API into independent services. Students can browse published courses, purchase access, complete lectures, and monitor learning progress. Educators can publish structured course content and review enrollment and revenue data.

User accounts and roles are managed through Clerk, application data is stored in MongoDB, course media is delivered through Cloudinary or embedded video sources, and purchases are processed through Stripe.

## Features

### Students

- Clerk-based registration and authentication
- Public course catalog
- Course details with free lecture previews
- Stripe checkout
- Purchased-course access
- Lecture completion tracking
- Course progress indicators
- Course ratings
- Responsive video playback
- Personal enrollment library

### Educators

- Educator role activation
- Protected educator routes
- Course creation with rich descriptions
- Course chapters and lectures
- Course thumbnails
- YouTube and hosted-video support
- Pricing and percentage discounts
- Published-course management
- Revenue summary
- Enrollment analytics
- Student purchase history

### Platform

- REST API
- MongoDB persistence
- Clerk user synchronization through verified webhooks
- Stripe payment status synchronization
- Cloudinary media storage
- Role-based authorization
- Protected and preview-only course content
- Centralized API error responses
- Configurable cross-origin access
- Responsive user interface

## Tech Stack

| Area | Technologies |
| --- | --- |
| Frontend | React 19, Vite |
| Routing | React Router DOM 7 |
| Styling | Tailwind CSS, PostCSS |
| Authentication | Clerk React, Clerk Express |
| HTTP client | Axios |
| Rich text | Quill |
| Video | React YouTube, Cloudinary |
| Progress display | RC Progress |
| Ratings | React Simple Star Rating |
| Animation | Framer Motion |
| Backend | Node.js, Express 5 |
| Database | MongoDB, Mongoose |
| Payments | Stripe Checkout, Stripe webhooks |
| Media uploads | Cloudinary, Multer |
| Webhooks | Svix |
| Validation | Validator |
| Development tooling | ESLint, Nodemon |

## Installation

### Requirements

- Node.js 18 or later
- npm
- MongoDB database
- Clerk application
- Stripe account
- Cloudinary account

### Install Frontend Dependencies

```bash
cd client
npm install
```

### Install Backend Dependencies

```bash
cd server
npm install
```

## Configuration

Create separate `.env` files inside the `client` and `server` directories.

### Frontend Environment

Create `client/.env`:

```env
VITE_BACKEND_URL=your_backend_origin
VITE_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key

VITE_CLOUDINARY_CLOUD_NAME=your_cloudinary_cloud_name
VITE_CLOUDINARY_UPLOAD_PRESET=your_unsigned_upload_preset
```

Only public, browser-safe values should use the `VITE_` prefix. Never place private API secrets in the frontend environment.

### Backend Environment

Create `server/.env`:

```env
PORT=5000

DATABASE=your_mongodb_connection_string_with_<PASSWORD>_placeholder
DATABASE_PASSWORD=your_mongodb_password

CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
CLERK_SECRET_KEY=your_clerk_secret_key
CLERK_WEBHOOK_SECRET=your_clerk_webhook_secret

STRIPE_SECRET_KEY=your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=your_stripe_webhook_secret
CURRENCY=usd

CLOUDINARY_NAME=your_cloudinary_cloud_name
CLOUDINARY_API_KEY=your_cloudinary_api_key
CLOUDINARY_SECRET_KEY=your_cloudinary_api_secret
```

The backend replaces `<PASSWORD>` in `DATABASE` with `DATABASE_PASSWORD` before connecting to the `lms` database.

### Clerk Webhook

Configure the Clerk webhook to send user events to:

```text
/clerk
```

Enable the following events:

- `user.created`
- `user.updated`
- `user.deleted`

These events synchronize Clerk accounts with MongoDB.

### Stripe Webhook

Configure the Stripe webhook to send payment events to:

```text
/api/stripe
```

Enable:

- `payment_intent.succeeded`
- `payment_intent.payment_failed`

The Stripe webhook must receive the raw request body so that its signature can be verified.

## Running the Application

### Start the Backend

```bash
cd server
npm run server
```

The API listens on port `5000` unless `PORT` is changed.

For production:

```bash
npm start
```

### Start the Frontend

In another terminal:

```bash
cd client
npm run dev
```

Vite serves the frontend on port `5173` by default.

Both services must be running for local development.

## Usage

### Student Workflow

1. Create an account or sign in through Clerk.
2. Browse published courses.
3. Open a course to review its description, curriculum, educator, price, and rating.
4. Watch lectures marked as free previews.
5. Purchase the course through Stripe Checkout.
6. Return to the enrollment library after successful payment.
7. Watch enrolled course lectures.
8. Mark lectures as complete.
9. Review course progress.
10. Submit or update a course rating.

### Educator Workflow

1. Sign in through Clerk.
2. Activate the educator role.
3. Open the educator interface.
4. Create a course with a title, description, price, discount, thumbnail, chapters, and lectures.
5. Publish the course.
6. Review created courses.
7. Monitor completed purchases and total earnings.
8. Review enrolled students and purchase dates.

## API

### Health

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| `GET` | `/` | Public | Confirm that the API is running |

### Course Endpoints

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| `GET` | `/api/courses/all` | Public | List published courses |
| `GET` | `/api/courses/:id` | Public | Return course details with preview restrictions |

### User Endpoints

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| `GET` | `/api/user/profile` | Authenticated | Return the current user |
| `GET` | `/api/user/enrolled-courses` | Authenticated | Return purchased courses |
| `POST` | `/api/user/purchase` | Authenticated | Create a Stripe Checkout session |
| `POST` | `/api/user/update-course-progress` | Authenticated | Mark a lecture as completed |
| `POST` | `/api/user/get-course-progress` | Authenticated | Return course progress |
| `POST` | `/api/user/add-rating` | Enrolled student | Add or update a course rating |

### Educator Endpoints

| Method | Endpoint | Access | Purpose |
| --- | --- | --- | --- |
| `GET` | `/api/educator/update-role` | Authenticated | Assign the educator role |
| `POST` | `/api/educator/add-course` | Educator | Create and publish a course |
| `GET` | `/api/educator/courses` | Educator | Return educator-owned courses |
| `GET` | `/api/educator/dashboard` | Educator | Return earnings, courses, and enrollment data |
| `GET` | `/api/educator/enrolled-students` | Educator | Return students and purchase dates |

### Webhook Endpoints

| Method | Endpoint | Provider | Purpose |
| --- | --- | --- | --- |
| `POST` | `/clerk` | Clerk | Synchronize user records |
| `POST` | `/api/stripe` | Stripe | Synchronize payment and enrollment status |

## Available Scripts

### Frontend

```bash
npm run dev
```

Starts the Vite development server.

```bash
npm run build
```

Creates the production frontend bundle.

```bash
npm run lint
```

Runs ESLint.

```bash
npm run preview
```

Serves the production build locally.

### Backend

```bash
npm run server
```

Starts the API with Nodemon.

```bash
npm start
```

Starts the API with Node.js.

## Deployment

Deploy the frontend and backend as separate services.

### Frontend

```bash
cd client
npm install
npm run build
```

Deploy the generated `dist` directory and configure the public environment variables before building.

### Backend

```bash
cd server
npm install
npm start
```

Configure all server-side secrets through the deployment platform.

Production deployments should:

- Use a secured MongoDB deployment
- Replace every example secret
- Restrict allowed CORS origins
- Use HTTPS
- Verify Clerk and Stripe webhook signatures
- Keep Cloudinary and Stripe secrets server-side
- Avoid committing `.env` files
- Monitor failed payments and webhook deliveries
- Validate media upload types and sizes
- Use production Clerk and Stripe environments consistently

## Contributing

Create a focused branch and keep frontend, backend, database, payment, and authentication changes within their existing responsibilities.

Before submitting changes:

- Install dependencies in both applications
- Run the frontend linter
- Create a production frontend build
- Verify backend startup
- Test authentication and educator authorization
- Test course publishing and previews
- Test Stripe checkout and webhook handling
- Test progress updates and ratings
- Avoid committing credentials or local environment files
- Keep changes focused and clearly documented
