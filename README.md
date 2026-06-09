# Fuel Quote System

A full-stack web application for managing fuel delivery quotes. Users register, complete a profile, request fuel quotes with dynamic pricing, and view their order history.

## Tech Stack

- **Backend:** Node.js, Express.js
- **Database:** PostgreSQL (via `pg` connection pool)
- **Auth:** bcrypt password hashing, express-session
- **Testing:** Jest, Supertest — 82% code coverage

## Features

- **User authentication** — Registration with password validation (length, uppercase, lowercase, special character), bcrypt hashing, and session-based login
- **Client profiles** — Create and update delivery address, city, state, and zip
- **Dynamic fuel pricing** — Suggested price per gallon calculated from location (TX vs. non-TX), order volume (>1,000 gal threshold), order history, and a company margin
- **Quote history** — All submitted quotes persisted to PostgreSQL and displayed in a sortable table
- **Input validation** — Server-side and client-side checks across all forms

## Pricing Algorithm

Suggested price is calculated as:

```
margin = basePrice × (locationFactor - historyFactor + volumeFactor + companyProfit)
suggestedPrice = basePrice + margin
totalDue = gallonsRequested × suggestedPrice
```

| Factor | Value |
|---|---|
| Base price | $1.50 / gal |
| Location (TX) | +2% |
| Location (non-TX) | +4% |
| Returning customer | −1% |
| Volume > 1,000 gal | +2% |
| Volume ≤ 1,000 gal | +3% |
| Company profit margin | +10% |

## Database Schema

```
UserCredentials    ClientInformation    FuelHistory
──────────────     ─────────────────    ───────────
id (PK)            id (PK, FK)          id (PK)
username           full_name            user_id (FK → UserCredentials)
password           address1             gallonsRequested
                   address2             deliveryAddress
                   city                 deliveryDate
                   state                suggestedPrice
                   zipcode              totalAmountDue
```

## Getting Started

**Prerequisites:** Node.js, PostgreSQL

```bash
# Install dependencies
npm install

# Start the server (tables are created automatically on first run)
node fuelApp.js
```

App runs at `http://localhost:3000`.

**PostgreSQL defaults** (edit `fuelApp.js` to change):
- Host: `localhost`, Port: `5432`
- User: `postgres`, Password: `password`

> ⚠️ Tables are **dropped and recreated** on every server start. Do not use in production without removing the `DROP TABLE` statements in `createTables()`.

## Running Tests

```bash
npm test
```

Jest + Supertest test suite covering GET/POST routes, password validation, bcrypt hashing verification, session handling, and database error simulation via mocking.

## Project Structure

```
├── fuelApp.js            # Express server, routes, DB logic
├── index.html            # Landing page
├── loginPage.html        # Login form
├── registrationPage.html # Registration form
├── profilePage.html      # Profile display
├── editProfile.html      # Profile edit form
├── fuelQuote.html        # Quote request form + pricing logic
├── fuelQuoteHistory.html # Quote history table
├── test.js               # Jest test suite
└── package.json
```
 
