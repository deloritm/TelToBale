<div align="center">

<img src="https://img.shields.io/badge/Cloudflare-Workers-F38020?style=for-the-badge&logo=cloudflare&logoColor=white" />
<img src="https://img.shields.io/badge/Telegram-Bot-26A5E4?style=for-the-badge&logo=telegram&logoColor=white" />
<img src="https://img.shields.io/badge/Bale-Bot-23D18B?style=for-the-badge&logoColor=white" />
<img src="https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge" />

<br/><br/>

# ⚡ Bridge — Telegram to Bale

**Automatically forward messages from Telegram channels to Bale, powered by Cloudflare Workers**

> No server. No cost. No complexity — set it up in under 20 minutes.

<br/>

```
📢 Telegram Channel  ──▶  ⚡ Cloudflare Worker  ──▶  📣 Bale Channel
```

<br/>

</div>

---

## ✨ Features

- 🆓 **Completely free** — runs on Cloudflare Workers' free plan
- ⚡ **Serverless** — no VPS or hosting required
- 🔄 **Automatic** — checks and forwards new messages every 5 minutes
- 🎛️ **Admin panel** — manage tokens and channels via a web interface
- 📦 **KV Storage** — stores settings and message offsets
- 🔐 **Secure** — tokens are stored encrypted in KV

---

## 📋 Prerequisites

Have the following ready before you start:

| Item | Description |
|------|-------------|
| 🤖 Telegram Bot | Created via `@BotFather` |
| 🤖 Bale Bot | Created via `@BaleBot` |
| ☁️ Cloudflare Account | Free registration at `cloudflare.com` |

---

## 🚀 Step-by-Step Setup

### Step 1 — Create a Telegram Bot

1. Search for **`@BotFather`** on Telegram and open it
2. Send the `/newbot` command
3. Enter a name and username for your bot (username must end with `_bot`)
4. Copy the token you receive:

```
7123456789:AAHxyz-abcdefghijklmnopqrstuvwxyz
```

> ⚠️ **Warning:** Never share this token with anyone!

5. Add the bot to your source Telegram channels as an **admin** (or at least as a member so it can read messages)

---

### Step 2 — Create a Bale Bot

1. Search for **`@BaleBot`** on Bale and open it
2. Send the `/newbot` command and follow the steps
3. Copy the token you receive
4. Add the bot to your destination Bale channel as an **admin**
5. Find your Bale channel ID (it starts with `-100`)

> 💡 To find your Bale channel ID, check the invite link or use an ID-finder bot

---

### Step 3 — Create a Cloudflare Account

1. Go to [cloudflare.com](https://cloudflare.com) and sign up
2. Verify your email address
3. Log in to the dashboard — no domain or paid plan needed

**Free plan limits (more than enough):**

| Service | Free Limit |
|---------|-----------|
| Workers | 100,000 requests / day |
| KV Writes | 1,000 writes / day |
| KV Reads | 10,000,000 reads / day |

---

### Step 4 — Create a KV Namespace

KV is where tokens, channel lists, and message offsets are stored.

1. In the Cloudflare dashboard, click **Workers & Pages**
2. Select **KV** from the sub-menu
3. Click **Create a namespace**
4. Name it exactly `KV` (uppercase)
5. Click **Add**

---

### Step 5 — Create a Worker and Upload the Code

1. In the Cloudflare dashboard, click **Workers & Pages**
2. Click **Create**
3. Select **Create Worker**
4. Give it a name (e.g. `tel-to-bale`) and click **Deploy**
5. Click **Edit Code**
6. Delete all the default code and paste the contents of **`worker.js`**
7. Click **Deploy**

> ⚠️ Make sure the code starts with `export default {` — this confirms it was copied correctly

---

### Step 6 — Bind KV to the Worker

Without this step the Worker won't function!

1. On the Worker page, click the **Settings** tab
2. Find the **Bindings** section
3. Click **Add** and select **KV Namespace**
4. In the **Variable name** field, type: `KV` (exactly, uppercase)
5. Select the KV Namespace you just created
6. Click **Save**

> ℹ️ If the variable name is wrong, the Worker will return a `KV binding missing` error

---

### Step 7 — Log In to the Admin Panel

1. Open your Worker URL:

```
https://your-worker-name.workers.dev/admin
```

2. Enter the default password `admin1234` and log in
3. Under **Tokens**, enter your Telegram and Bale bot tokens and save
4. Under **Destination Channel**, enter your Bale channel ID (e.g. `-1001234567890`)
5. Under **Source Channels**, enter your Telegram channel IDs — one per line
6. Click **Save Channels**

> 🔐 **Important:** Change the default password `admin1234` in the code! First line of `worker.js`:

```javascript
const ADMIN_PASSWORD = "your-new-password";
```

---

### Step 8 — Initial Test

1. In the admin panel, press the **▶ Run** button
2. If everything is correct, the dashboard will show stats
3. Send a test message in your source Telegram channel
4. Press **Run** again — the message should appear in your Bale channel

✅ If the message arrived — congratulations! Bridge is working.

**If you get an error, check:**
- Is the Telegram bot a member of the source channel?
- Is the Bale bot an admin of the destination channel?
- Are the channel IDs entered correctly?

---

### Step 9 — Set Up a Cron Job (Auto-run)

#### Option 1: cron-job.org (Free)

The cron job hits your Worker every 5 minutes to check for new messages.

1. Go to [cron-job.org](https://cron-job.org) and sign up
2. Click **Create cronjob**
3. Enter your Worker URL:

```
https://your-worker-name.workers.dev/cron
```

4. Set the schedule to **Every 5 minutes**
5. Save and enable the cron job

> 📊 That's 288 requests per day — only 0.3% of Cloudflare's free quota

#### Option 2: Cloudflare Cron Triggers ⭐ (Recommended)

No external service needed — triggered directly by Cloudflare:

1. In the Worker dashboard, click the **Settings** tab
2. Find the **Cron Triggers** section
3. Click **Add Cron Trigger**
4. Enter the following expression:

```
*/5 * * * *
```

5. Click **Add Trigger**

> ⭐ This method is more reliable since it invokes the Worker directly without HTTP

---

## ✅ Final Checklist

Check everything off before you go live:

- [ ] Telegram bot created and token saved
- [ ] Telegram bot is a member or admin of source channels
- [ ] Bale bot created and token saved
- [ ] Bale bot is an admin of the destination channel
- [ ] Bale channel ID saved (starts with `-100`)
- [ ] Cloudflare Worker created and code uploaded
- [ ] KV Namespace created and bound to Worker (Variable: `KV`)
- [ ] Tokens and channels entered in the admin panel
- [ ] Initial test passed successfully
- [ ] Cron job configured (cron-job.org or Cloudflare Triggers)
- [ ] Default password `admin1234` changed

---

## 🗂️ Project Structure

```
bridge/
├── worker.js       # Main Cloudflare Worker code
└── README.md       # This file
```

---

## 🛠️ Architecture

```
┌─────────────────┐     poll every 5min     ┌──────────────────────┐
│  Telegram API   │ ◀────────────────────── │  Cloudflare Worker   │
│  (getUpdates)   │                         │                      │
└─────────────────┘                         │  ┌────────────────┐  │
                                            │  │   KV Storage   │  │
┌─────────────────┐     forward messages    │  │ tokens/offsets │  │
│    Bale API     │ ◀────────────────────── │  └────────────────┘  │
│  (sendMessage)  │                         │                      │
└─────────────────┘                         │  ┌────────────────┐  │
                                            │  │  Admin Panel   │  │
┌─────────────────┐     trigger             │  │   /admin       │  │
│  Cron Trigger   │ ──────────────────────▶ │  └────────────────┘  │
│  (*/5 * * * *)  │                         └──────────────────────┘
└─────────────────┘
```

---

## 📄 License

MIT License — free to use, modify, and distribute

---

<div align="center">

Built with ❤️ for Iran

</div>
