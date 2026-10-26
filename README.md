# Neuralix AI

A futuristic AI chatbot website with its own accounts, chat history, Free and Premium plans, and automatic GCash payments. It does not use Claude. Neuralix answers with an open-source AI model that you choose and control.

**What's included**

- Sign up and log in (email and password)
- AI chat that types out live, with image analysis and saved history
- Free plan: unlimited messages, Neuralix Core model, chat history, 10 image uploads per day
- Premium plan: Nova and Ultra models, unlimited images, custom instructions, longer answers
- GCash checkout through PayMongo, with Premium turning on automatically after payment
- Owner console: members, Premium count, monthly GCash revenue, give or remove Premium
- No packages to install. It runs on Node.js 22.13 or newer.

---

## 1. Choose Neuralix's AI brain

Neuralix's knowledge comes from an open-source AI model (for example Qwen, Gemma or gpt-oss). These models were trained on a large part of the internet, so they already know a lot, the same way any chatbot does. Bigger models give smarter answers but need stronger hardware.

**Option A: fully your own (no outside AI company)**

1. Get a computer or cloud server with an NVIDIA GPU (16 GB of GPU memory or more is a good start).
2. Install Ollama from ollama.com.
3. Download the models:
   ```
   ollama pull qwen3:8b
   ollama pull gpt-oss:20b
   ollama pull qwen3-vl:8b
   ```
4. In your settings, use `AI_BASE_URL=http://YOUR-SERVER-IP:11434/v1`.

**Option B: no GPU needed**

Use Ollama Cloud, which runs open models for you. Set `AI_BASE_URL=https://ollama.com/v1` and put your Ollama API key in `AI_API_KEY`. Check which model names your account offers and put them in `MODEL_CORE`, `MODEL_NOVA`, `MODEL_ULTRA` and `MODEL_VISION`.

Any other OpenAI-compatible AI service also works with the same two settings.

## 2. Set up GCash payments (PayMongo)

1. Create a PayMongo account at paymongo.com and complete business verification so you can accept live payments.
2. In the PayMongo dashboard, make sure **GCash** is turned on under payment methods.
3. Copy your **secret key**. Use the `sk_test_` key first, then the `sk_live_` key when you go live. Put it in `PAYMONGO_SECRET_KEY`.
4. Create a **webhook**:
   - URL: `https://YOUR-WEBSITE/api/billing/webhook`
   - Event: `checkout_session.payment.paid`
5. Copy the webhook's **signing secret** into `PAYMONGO_WEBHOOK_SECRET`.

When a member pays, PayMongo tells Neuralix and Premium turns on right away: 30 days for monthly, 365 days for yearly. Paying again extends the current Premium.

## 3. Put it online

**Option 1: Render (easiest)**

1. Upload this folder to a new GitHub repository.
2. On render.com, create a **Web Service** from that repository.
3. Build command: leave empty. Start command: `npm start`.
4. Add a **persistent disk** mounted at `/var/data`. Choose a plan that supports disks, or your members and chats will be erased on every restart.
5. Add the settings from `.env.example` as environment variables, with `DATABASE_PATH=/var/data/neuralix.db` and `APP_URL` set to your Render address or your own domain.

**Option 2: any VPS with Docker**

```
cp .env.example .env        # then edit .env
docker build -t neuralix .
docker run -d --name neuralix -p 3000:3000 --env-file .env -v neuralix-data:/app/data --restart unless-stopped neuralix
```

Put it behind HTTPS (for example Caddy or Nginx with a free certificate). GCash and logins need HTTPS.

## 4. Open the Owner console

Put your email in `ADMIN_EMAILS`, sign up on the website with that email, and an **Owner console** link appears at the bottom of the sidebar. Use it to see members and revenue, and to give Premium to testers or to people who paid you directly.

## Run it on your own computer

```
cp .env.example .env    # set APP_URL=http://localhost:3000 and NODE_ENV=development
npm start
```

Then open http://localhost:3000.

## Good to know

- Free image uploads reset at midnight Philippine time.
- Messages are rate-limited to 20 per minute per member to protect your AI server from abuse.
- Back up the `data` folder (or your persistent disk) regularly. It holds every account, chat and payment.
- Images are shrunk on the member's phone before upload and are not stored; only the count is saved.
- Passwords are stored as salted scrypt hashes. Sessions use secure, HTTP-only cookies.

## Settings

| Setting | What it does |
|---|---|
| `APP_URL` | Your website address, used for GCash return links |
| `DATABASE_PATH` | Where the database file is saved |
| `ADMIN_EMAILS` | Emails that can open the Owner console |
| `AI_BASE_URL`, `AI_API_KEY` | Your AI server and its key |
| `MODEL_CORE`, `MODEL_NOVA`, `MODEL_ULTRA` | Model behind each Neuralix tier |
| `MODEL_VISION` | Model used for messages with images |
| `MAX_TOKENS_*` | Longest answer per tier |
| `FREE_IMAGES_PER_DAY` | Free image uploads per day (default 10) |
| `PRICE_MONTHLY`, `PRICE_ANNUAL` | Premium prices in pesos (default ₱199 and ₱1,910) |
| `PAYMONGO_SECRET_KEY`, `PAYMONGO_WEBHOOK_SECRET` | Your PayMongo keys |
| `PAYMONGO_METHODS` | Payment methods on the checkout page (default `gcash`) |
