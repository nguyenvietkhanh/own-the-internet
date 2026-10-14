# OWN THE INTERNET — v3 source code

An English-language 10,000-tile website with five fixed price zones (0.1 / 1 / 3 / 10 / 30 SOL), email sign-in, a unique receiving address for each order, SQLite storage and on-chain Solana Devnet payment checks.

## Verified locally

- `node --test tests/*.test.js`: 4 tests passed, including HTTP email sign-in, order creation, tile contention and permission checks.
- Syntax checks passed for frontend and backend.
- **No live Devnet transaction has been sent or confirmed.** The end-to-end payment integration is **unverified**.

## Important limits

- **DEVNET ONLY. This release cannot accept mainnet SOL.** The payment verifier and address generation have not received a security audit.
- The deposit private keys are encrypted at rest for devnet testing. There is NO implemented secure treasury sweeping / recovery service. Never place real-value SOL in these wallets.
- Uploading user media, resale/outbid/refunds, secure hosted authentication sessions, multi-region deployments, e-mail rate limits by account, hardened operational monitoring and legal policies are NOT production complete.
- This app is not hosted. Node must remain running and SQLite needs durable storage. Netlify Drop is not supported.
- Never publish secrets or copy private keys into support chats.

## Local test (Node.js 22+)

1. Create a `.env` or export settings from `.env.example` into the environment (Node does not load .env automatically). Set `DEVNET_ENCRYPTION_KEY` to a random 32-byte hex string.
2. Set `NODE_ENV=development`, `ALLOW_LOCAL_TEST_LOGIN=true`, `ENABLE_DEVNET_PAYMENTS=true` and start with `node server.js`. The local OTP is displayed in the browser for testing ONLY.
3. Open `http://localhost:3000`.
4. For an internet-connected deployment, configure verified sending domain and `RESEND_API_KEY`, `MAIL_FROM`. Use `ALLOW_LOCAL_TEST_LOGIN=false` and `NODE_ENV=production`.
5. Devnet-only SOL transfers can be tested by funding a test wallet using an official Solana Devnet faucet; no real assets are involved.

## Checkout flow

1. The user signs in by email OTP.
2. The server locks an available tile for 15 minutes.
3. The server generates a distinct Solana Ed25519 public payment address for that invoice, storing its devnet-only private key encrypted with AES-256-GCM.
4. The user transfers the invoice's exact Devnet SOL amount to that address using an external wallet (no wallet connection or signing access required in the web app).
5. The watcher periodically requests finalized Solana transactions, validates the destination, amount, transaction success and time, and assigns the tile in a database transaction.
6. Payments at unexpected amounts or after expiry go into a reconciliation queue rather than automatically assigning a tile.

## Required before real-money deployment

Professional security review of payment and wallet key management; hardened scalable wallet custodial / sweeping architecture; reconciliation and refunds; trustworthy production email with anti-abuse; database migration and production backups; image moderation; terms/refunds; privacy controls; documented operational incident handling; real Devnet end-to-end tests and concurrency/load tests. Mainnet would require a separate audited deployment, not an environment flag in this release.
