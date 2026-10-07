<div align="center">

# TradingBot

**Full-stack crypto investment & signal platform — React frontend, Express API, Supabase persistence, AI-assisted signal generation**

[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react&logoColor=black)](https://react.dev/)
[![Vite](https://img.shields.io/badge/Vite-6-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![Express](https://img.shields.io/badge/Express-4-000000?logo=express&logoColor=white)](https://expressjs.com/)
[![Supabase](https://img.shields.io/badge/Supabase-2-3FCF8E?logo=supabase&logoColor=white)](https://supabase.com/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

</div>

> An elite trading signal platform with real-time Binance, Forex, and Quotex market analysis. A single Express server serves the API and the Vite-built React app, with Supabase as the data layer and an AI-assisted signal generator.

> ⚠️ **Risk notice:** Signals are informational, not financial advice. Never trade capital you cannot afford to lose.

---

## ✨ Features

- **Multi-market coverage** — live pair listings for Binance Futures (`fapi.binance.com`), Forex, and Quotex instruments
- **AI-assisted signal generation** — `/api/signals/generate` produces structured trade signals via the Google GenAI API
- **Token-based access control** — `/api/auth/validate-token` plus full admin CRUD for access tokens (`GET/POST/PUT/DELETE /api/admin/tokens`)
- **Supabase-backed persistence** — tokens and app data stored in Postgres via `@supabase/supabase-js`
- **Rich trading dashboard** — React 19 UI with animated transitions (`motion`), iconography (`lucide-react`), and toast notifications (`sonner`)
- **Unified server** — one Express process serves both the JSON API and the production Vite bundle (SPA fallback included)
- **Type-safe end to end** — strict TypeScript across `server.ts` and the React client, verified with `tsc --noEmit`
- **Modern styling** — Tailwind CSS 4 with `clsx` + `tailwind-merge` for composable class logic

## 🛠️ Tech Stack

| Category      | Technology                                      |
|---------------|-------------------------------------------------|
| Frontend      | React 19, React Router 7, Vite 6                |
| Backend       | Express 4 (run via `tsx`), Node.js 22+          |
| Language      | TypeScript 5.8                                  |
| Database      | Supabase (Postgres) via `@supabase/supabase-js` |
| Market Data   | Binance Futures API, Twelve Data (Forex), axios |
| AI            | Google GenAI SDK (`@google/genai`)              |
| Styling/UI    | Tailwind CSS 4, lucide-react, motion, sonner    |
| Utilities     | date-fns, dotenv, clsx, tailwind-merge          |

## 🏗️ Architecture / How It Works

```
Browser ──▶ Express (server.ts :3000) ──┬──▶ /api/* ──▶ Binance / Twelve Data / Gemini / Supabase
                                        └──▶ /* ──▶ Vite-built SPA (src/App.tsx)
```

1. **Client** — `src/App.tsx` renders the dashboard: token entry, market selectors, signal cards, admin token manager. Supabase client (`src/lib/supabase.ts`) reads `VITE_SUPABASE_URL` / `VITE_SUPABASE_ANON_KEY`.
2. **API layer** — `server.ts` exposes REST endpoints; market routes proxy public exchange data with `axios`, the signal route calls the GenAI model, and admin routes manage access tokens in Supabase.
3. **Serving** — in development, Express mounts Vite middleware for HMR; in production it serves the `dist/` bundle with an SPA fallback (`app.get("*")`).
4. **Secrets** — server-side keys (`SUPABASE_SERVICE_ROLE_KEY`, `BINANCE_API_KEY`, `BINANCE_SECRET_KEY`, `GEMINI_API_KEY`) live in `.env` and are never exposed to the browser.

## 🚀 Getting Started

### Prerequisites

- Node.js 22+
- A Supabase project (URL + anon key)
- API keys: Binance (optional, for private endpoints), Google AI Studio (for signal generation), Twelve Data (for Forex)

### Installation

```bash
npm install
```

### Environment Variables

Create a `.env` file in the project root (see `.env.example` for the full template). **Never commit real keys.**

```bash
# Supabase
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key   # server only

# Binance (server only)
BINANCE_API_KEY=your-binance-api-key
BINANCE_SECRET_KEY=your-binance-secret-key

# AI signal generation (server only)
GEMINI_API_KEY=your-gemini-api-key

# Forex data
VITE_TWELVE_DATA_API_KEY=your-twelve-data-key

# App URL (injected at deploy time)
APP_URL=http://localhost:3000
```

### Run

```bash
# Development — Express + Vite HMR on :3000
npm run dev

# Production build + serve
npm run build
npm start

# Type check
npm run lint
```

Open `http://localhost:3000` in your browser.

## 📁 Project Structure

```
tradingbot/
├── server.ts               # Express API + Vite/static serving
├── src/
│   ├── App.tsx             # Main dashboard application
│   ├── main.tsx            # React entry point
│   ├── index.css           # Tailwind + global styles
│   └── lib/
│       └── supabase.ts     # Supabase client (anon key)
├── index.html              # HTML shell
├── vite.config.ts
├── tsconfig.json
├── package.json
├── .env.example            # Env template (no real secrets)
└── .gitignore
```

## 🔌 API / Usage

Base URL: `http://localhost:3000`

| Method | Endpoint                      | Description                              |
|--------|-------------------------------|------------------------------------------|
| GET    | `/api/market/binance-pairs`   | Tradable `USDT` Binance Futures symbols   |
| GET    | `/api/market/forex-pairs`     | Available Forex pairs                     |
| GET    | `/api/market/quotex-pairs`    | Available Quotex instruments              |
| POST   | `/api/auth/validate-token`    | Validate an access token                  |
| POST   | `/api/signals/generate`       | Generate an AI-assisted trade signal      |
| GET    | `/api/admin/tokens`           | List access tokens (admin)                |
| POST   | `/api/admin/generate-token`   | Create an access token (admin)            |
| PUT    | `/api/admin/tokens/:id`       | Update an access token (admin)            |
| DELETE | `/api/admin/tokens/:id`       | Revoke an access token (admin)            |

Example:

```bash
curl -X POST http://localhost:3000/api/auth/validate-token \
  -H "Content-Type: application/json" \
  -d '{"token":"YOUR_ACCESS_TOKEN"}'
```

## 🗺️ Roadmap

- [ ] Real-time price streaming via WebSocket (replace polling)
- [ ] Backtest view for generated signals against historical data
- [ ] Per-user signal history and performance analytics in Supabase
- [ ] Rate limiting + hardened auth on admin endpoints

## 🤝 Contributing

Fork, branch (`git checkout -b feature/your-feature`), commit clearly, and open a PR. Keep TypeScript strict-clean (`npm run lint` must pass) and never commit secrets — use `.env` locally.

## 📄 License

MIT — see [LICENSE](LICENSE) for details.

## 👤 Author

**Muhammad Waleed (MW Trader)** — Software Engineer building trading automation at [mwtrader.site](https://mwtrader.site)
GitHub: [github.com/mwaleed-pk](https://github.com/mwaleed-pk)
