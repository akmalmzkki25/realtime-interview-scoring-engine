# realtime-interview-scoring-engine

A voice-first AI interview platform. It runs live interviews over the Gemini Multimodal Live API, builds an evidence-grounded skill portfolio from the transcript, and scores candidates against a job vacancy with a null-safe fit/gap engine and an executive PDF export.

```
.
├── api/    # Backend service (Ruby on Rails 7, PostgreSQL, Redis/Sidekiq, Action Cable)
└── web/    # Frontend web app (React 18 + TypeScript, Vite, Tailwind)
```

The two services run together: the web app talks to the API over REST and WebSocket.

## Features

- **Live voice interview** over Gemini Live (`v1alpha`), with a candidate pre-flight check: microphone VU meter, speaker test and a same-origin network speed test.
- **Evidence-grounded portfolio**: skills mapped to L1–L5 behavioral anchors, each backed by verbatim transcript quotes. LLM output is parsed fence-tolerantly, and regeneration is idempotent and row-locked.
- **Fit/Gap engine**: compares a portfolio with a vacancy, tells an unassessed skill apart from a real gap, and supports assessor overrides with an audit trail.
- **Exports**: executive PDF dossier (Prawn) and JSON.
- **Privacy by design** (Indonesian UU PDP No. 27/2022): request-log filtering, no raw audio persisted, tenant-scoped lookups, no secrets in the repository.
- **Tests**: RSpec for the API (services and request specs), Vitest for the web app.

## Running it locally

Each service has its own setup guide. Run the API first, then the web app pointed at it.

1. **API**: see [`api/README.md`](api/README.md). Rails app; needs Ruby (see `api/.ruby-version`), PostgreSQL, and Redis. It serves on `http://localhost:3001`.
2. **Web**: see [`web/README.md`](web/README.md). Vite app; `npm install`, copy `.env.example` to `.env`, point `VITE_API_BASE_URL` at the API, then `npm run dev`. It serves on `http://localhost:5173`.

Copy `.env.example` to `.env` and fill in your own values (database, `REDIS_URI`, Gemini key) before starting. Nothing in this repository ships with real credentials.

## Documentation

- [`docs/ENGINEERING_REPORT.md`](docs/ENGINEERING_REPORT.md): problem analysis, trade-offs, acceptance criteria, test evidence and known limitations.
- [`docs/superpowers/`](docs/superpowers): design specs and implementation plans for each stage of the revamp.

## Attribution

This project builds on the open codebase [`rakamindev/ai-interview-platform`](https://github.com/rakamindev/ai-interview-platform), which provides the base Rails API (authentication, tenancy and the original schema) and the initial web shell. The original code, and the Rakamin brand identity used in the interface, belong to their respective owners. The upstream repository publishes no license, so refer to it for terms of use.

The work in this repository on top of that base is my own: session lifecycle hardening, the RSpec and Vitest suites, the Gemini integration fixes, the portfolio and fit/gap engine changes, the PDF export, the security and tenant-isolation fixes, and the UI revamp.
