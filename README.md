# 🛡️ Zerophish AI — Triple-Engine Phishing Protection
### The world's simplest guide to understanding how everything works

---

## 📖 What Is Zerophish AI?

Zerophish AI is a **browser extension + web dashboard** that **automatically protects you** from dangerous (phishing) websites the moment you visit them.

**Phishing** = A fake website that pretends to be a real one (like a fake PayPal page) to steal your password, bank details, or personal information.

**Think of Zerophish AI like a security guard that:**
- Checks every website before you can see it
- Warns you if something looks suspicious
- Completely blocks you from dangerous websites
- Shows a safety score in the bottom-right corner of every page

---

## 🗂️ Project Structure — Every File Explained

```
zerophish-ai/
│
├── public/extension/           ← The browser extension files
│   ├── manifest.json           ← Extension ID card (what it can do)
│   ├── background.js           ← The brain — analyzes every website
│   ├── content.js              ← Draws the badge and block screen
│   ├── popup.html              ← The UI you see when you click the extension icon
│   ├── popup.js                ← Logic for the popup
│   └── gmail-content.js        ← Scans Gmail emails
│
├── src/
│   ├── pages/
│   │   ├── Index.tsx           ← Home/landing page
│   │   ├── Demo.tsx            ← Interactive demo page
│   │   ├── Dashboard.tsx       ← Security analytics dashboard
│   │   ├── Settings.tsx        ← App settings
│   │   ├── Auth.tsx            ← Login/signup
│   │   ├── Report.tsx          ← Report a phishing site
│   │   ├── Extension.tsx       ← Extension download & guide
│   │   └── NotFound.tsx        ← 404 page
│   │
│   ├── components/
│   │   ├── Navbar.tsx          ← Top navigation bar
│   │   ├── Footer.tsx          ← Bottom of every page
│   │   ├── ThreatMap.tsx       ← World map showing threat locations
│   │   ├── DetectionCharts.tsx ← Pie/bar charts for threat data
│   │   ├── ThreatDetailModal.tsx ← Popup when you click a threat
│   │   └── EnterpriseFeatures.tsx ← Feature summary cards
│   │
│   └── integrations/supabase/ ← Database connection
│
├── supabase/functions/         ← Cloud AI functions (Edge Functions)
│   ├── analyze-phishing/       ← Cloud AI phishing analysis
│   └── get-threats/            ← Fetches threat history for dashboard
│
└── README.md                   ← This file
```

---

## 🧠 AI Models & Algorithms Used

Zerophish AI uses a **Triple-Engine AI system**. Think of it as 3 experts looking at the same website and voting:

### Engine 1 — NLP (Natural Language Processing)
**What it does:** Reads the URL like a sentence and looks for suspicious words.

**Algorithm:** Rule-based keyword matching + pattern scoring

**How it works (like LKG):**
> Imagine someone shows you a letter. If the letter says "URGENT! Your bank account is SUSPENDED! Click HERE NOW!" — you'd be suspicious, right? That's exactly what the NLP engine does. It looks for scary words like `verify`, `suspend`, `login`, `password`, `urgent`, `confirm`.

**Examples of what it catches:**
- `paypal-account-verify.com` → Has "paypal" + "verify" → Suspicious!
- `secure-login-bank.xyz` → Has "secure" + "login" + bad extension → High risk!

---

### Engine 2 — Visual Pattern Detection
**What it does:** Checks how the domain/URL looks visually.

**Algorithm:** Regex pattern matching for homograph attacks and visual tricks

**How it works (like LKG):**
> Imagine someone writes "Gooogle.com" instead of "Google.com" — hard to notice right? Hackers do this with special characters. `xn--pypl-ppa.com` looks like PayPal in some languages but is completely fake. The visual engine catches these tricks.

**Examples of what it catches:**
- `xn--pypl-ppa.com` → Punycode attack → BLOCKED
- `g00gle.com` (with zeros instead of O's) → SUSPICIOUS
- `rnicrosoft.com` (rn looks like m) → SUSPICIOUS

---

### Engine 3 — Network Heuristics
**What it does:** Checks the domain's technical properties.

**Algorithm:** Statistical heuristic scoring with weighted risk factors

**How it works (like LKG):**
> Imagine getting a letter with no return address, from a P.O. Box in another country, delivered at 2am. Even without reading it, you'd be suspicious. Similarly, certain domain properties are red flags:
> - New/cheap domain extensions (`.tk`, `.ml`, `.xyz`) = suspicious
> - Website using an IP address instead of a name (like `192.168.1.1/login`) = suspicious
> - Too many sub-parts: `secure.login.verify.paypal.hack.com` = suspicious

---

### Combined Risk Score Formula
```
Risk Score = (NLP Score × 0.65) + (Visual Score × 0.35) + (Network Score × 0.50)
Risk Score 0-27  → SAFE   (green badge)
Risk Score 28-59 → WARNING (yellow badge)
Risk Score 60+   → BLOCKED (red full-screen stop page)
```

---

## 💻 Programming Languages Used

| Language | Used For |
|---|---|
| **TypeScript** | Frontend app (React pages, components) |
| **JavaScript** | Browser extension (background, content, popup) |
| **HTML** | Extension popup UI |
| **CSS** | Styling (popup UI, badge, block overlay) |
| **SQL** | Supabase database queries |
| **Deno/TypeScript** | Supabase Edge Functions (cloud AI) |

---

## 🔌 API Integrations

| API / Service | Purpose | Free/Paid |
|---|---|---|
| **Supabase** | Database, authentication, cloud functions | Free tier |
| **Supabase Edge Functions** | Cloud AI phishing analysis | Free tier |
| **OTX AlienVault** (optional) | Check URLs against known threat intelligence | Free API key |
| **URLScan.io** (optional) | Visual screenshot analysis of URLs | Free API key |
| **Google Fonts** | Typography (Inter font) | Free |

---

## 🌐 How Each Module Works

### 📌 background.js — The Brain
This is the most important file. It runs silently in the background all the time.

**Step-by-step flow:**
1. You open any website in your browser
2. `background.js` wakes up the moment the page finishes loading
3. It runs the **local heuristic engine** (takes less than 1 millisecond — instant!)
4. It saves the analysis result to `chrome.storage.local`
5. `content.js` picks up the result and shows the badge on screen
6. If risk score ≥ 60, it shows the BLOCKED screen

**Key functions:**
- `localAnalyze(url)` — The instant local AI engine
- `TRUSTED_DOMAINS` — Set of safe sites that are never analyzed (Google, GitHub, etc.)
- `chrome.tabs.onUpdated` — Fires whenever you navigate to a new page

---

### 📌 content.js — The Display Layer
This file runs **inside every webpage** you visit (injected by the extension).

**What it draws:**

1. **Risk Badge** (bottom-right corner, always visible):
   - Dark glass-morphism card
   - Animated circular SVG ring showing 0–100 risk score
   - Three progress bars: NLP / Visual / Network scores
   - Color: 🟢 Green (safe) / 🟡 Yellow (suspicious) / 🔴 Red (blocked)

2. **Block Overlay** (full-screen, only for high-risk sites):
   - Red glowing screen
   - Risk score pill
   - Threat reason in bold
   - "Go Back to Safety" and "Proceed Anyway" buttons

**How it knows what to show:**
- Listens to `chrome.storage.local` for changes
- When `background.js` saves the analysis → badge appears automatically

---

### 📌 popup.html + popup.js — The Extension Popup
The UI you see when you **click the Zerophish AI icon** in your toolbar.

**Features:**
- 🔄 Auto-Scan toggle (on/off)
- 📊 Stats: Threats Blocked + Sites Scanned
- 🔍 "Scan Current Page" button → Shows result **instantly** (uses stored result, never re-scans, so the score is always consistent)
- 📊 "View Dashboard" → Opens `http://localhost:8080/dashboard`

**Why was the score different before?**
The old version called the cloud API every time you clicked Scan. The cloud AI uses different random sampling each call, giving different numbers. Now it **always reads the stored local result** — same number every time.

---

## 🎮 How the Demo Page Works

### Tab 1: URL / Email Analysis
**Flow:**
1. You type a URL (like `https://paypal-verify.xyz`) or paste an email text
2. Click "Analyze"
3. The system sends to **Supabase Edge Function** `analyze-phishing`
4. Cloud AI returns: threat level, risk score, reason, NLP/Visual/Network scores
5. Results appear with color-coded cards

**Example:**
- Input: `http://192.168.1.1/bank/login`
- Output: 🚨 BLOCKED — "IP address as hostname; login keyword detected" — Risk: 88/100

---

### Tab 2: Document Scanner (Docs)
**Flow:**
1. You upload a PDF, Word, or text file
2. The system extracts the text content from the file
3. Sends the text to the phishing analysis engine 
4. Looks for phishing language patterns in the document
5. Returns threat level + highlighted suspicious phrases

**Example:**
- Upload: A suspicious email saved as PDF
- Content: "Dear Customer, Your Netflix account is suspended..."
- Output: ⚠️ SUSPICIOUS — "Urgency language + service impersonation"

---

### Tab 3: QR Code Scanner
**Flow:**
1. You upload an image containing a QR code
2. The `jsQR` JavaScript library decodes the QR code to extract the hidden URL
3. The extracted URL is then analyzed by the phishing engine
4. Shows the decoded URL + threat level

**Example:**
- Upload a QR code image from a suspicious poster
- QR decodes to: `http://track-your-package.xyz/verify`
- Output: 🚨 BLOCKED — "Suspicious TLD + tracking keywords"

**Library used:** `jsQR` (open source, runs in browser, no server needed)

---

### Tab 4: Multi-Language Detection
**Flow:**
1. Paste text in ANY language (English, Hindi, Tamil, Spanish, French, Arabic)
2. The AI detects which language it is
3. Analyzes for phishing patterns in that language
4. Returns threat verdict + what was found

**Example:**
- Input (Hindi): "तत्काल! आपका बैंक खाता निलंबित..."
- Output: 🚨 HIGH RISK — Urgency + bank impersonation in Hindi

**Why this matters:** Phishing attacks happen in every language. This catches non-English scams that other tools miss.

---

## 📊 Dashboard — All Features Explained

### 1. Threats Blocked (Red card — top left)
**What it shows:** How many websites were completely blocked by Zerophish (risk score ≥ 60).
**How it updates:** Every time `background.js` blocks a site, it increments the counter in chrome.storage. Dashboard reads this every 15 seconds.
**Example:** If you visited 10 sites and 3 were phishing → shows "3"

### 2. Warnings Issued (Yellow card — second)
**What it shows:** How many websites got a yellow warning (risk score 28–59). These are suspicious but not definitively harmful.
**How it updates:** Same as above — auto-incremented per detection.
**Example:** Sites with suspicious-looking URLs but no confirmed phishing signals → shows warning count

### 3. Total Analyzed (Blue card — third)
**What it shows:** Every single website you've visited while the extension was active.
**Sub-info:** "X safe • Y blocked • Z warned" breakdown shown below the number
**How it updates:** Every tab load increments this counter.

### 4. Avg Response Time (Clock card — fourth)
**What it shows:** How fast the AI analyses websites.
**Value:** Always "< 1ms" because we use local heuristics, not cloud API waits.

### 5. Global Threat Map
**What it shows:** A world map with dots marking where phishing threats were detected geographically.
**How it works:** When a threat is detected, its domain is geo-located (country mapped from TLD/known host). Red dots = blocked sites. Yellow = warnings.
**Note:** Pinpoints are approximate based on domain registration country.

### 6. Recent Detections (Live feed)
**What it shows:** A live feed of every website analyzed, newest first.
**Each entry shows:**
- Red/Yellow/Green badge (threat level)
- The URL (truncated for readability)
- Time since detection ("2m ago", "1h ago")
- The reason for the threat decision
- A confidence bar (% AI confidence)
- **Click any row** → Opens a detailed modal with full analysis breakdown

**How it auto-updates:** The dashboard polls for new data every 15 seconds automatically.

### 7. Threat Distribution (Bar chart on right)
**What it shows:** Percentage breakdown of High Risk vs Suspicious vs Safe detections.
**How bars fill:** Calculated as `(category count / total count) × 100%`.
**Updates:** Every 15 seconds with latest data.

### 8. Protection Score
**What it shows:** What percentage of visited sites were detected as risky.
**Formula:** `((blocked + warned) / total) × 100`
**Example:** If 10 sites visited, 3 blocked, 1 warned → Score = 40%

---

## ⚙️ Settings Page — All Features Explained

### Domain Whitelist
**What it is:** A list of websites you trust 100%. Zerophish will NEVER scan or block these.
**How to use:** Add your company website, favourite trusted sites.
**Example:** Add `mycompany.com` → Zerophish skips scanning it completely.
**Why useful:** Prevents false positives for sites you know are safe.

### Policies
**What it is:** Rules for how strict Zerophish should be.
**Settings include:**
- **Block threshold:** Change when sites get blocked (default: risk score ≥ 60)
- **Auto-scan:** Turn on/off automatic scanning of new tabs
- **Notification style:** Silent / Banner / Full notification
**Example:** Set threshold to 40 → Zerophish blocks even medium-risk sites.

### Alerts
**What it is:** How you get notified about threats.
**Options:**
- **Browser notification:** Popup notification in top-right of Chrome
- **Email alerts:** Get an email when a high-risk site is blocked
- **Dashboard update:** All detections logged to dashboard automatically
**Example:** Enable email alerts → Receive "Zerophish blocked paypal-fake.xyz" in your inbox.

### API Integrations
**What it is:** Connect Zerophish to external threat databases for enhanced detection.
**Available integrations:**
- **OTX AlienVault:** World's largest open threat intelligence database. Checks URLs against millions of known phishing domains.
  - Get free API key at: `otx.alienvault.com`
- **URLScan.io:** Takes a screenshot of suspicious websites for visual analysis.
  - Get free API key at: `urlscan.io`
- **VirusTotal** (optional): Checks URLs against 70+ antivirus engines.
**How to add:** Paste your API key in the field → Click Save → Extension uses it automatically.

---

## 📋 Report Page — Purpose & How It Works

### What is the Report Page?
The Report page lets you **manually report a phishing website** so other users can be protected from it too.

### Step-by-Step: How to Report a Phishing Site

**Step 1:** Go to the Report page (`/report`)

**Step 2:** Enter the suspicious URL
- Example: `http://fake-amazon-deals.xyz`

**Step 3:** Select why it's suspicious:
- Fake login page
- Impersonates a brand
- Asks for personal information
- Suspicious email link
- Other

**Step 4:** (Optional) Paste the email/message content that contained the link

**Step 5:** Click "Submit Report"

**What happens next:**
1. Your report is saved to the shared Supabase database
2. Our AI analyzes the URL with the cloud engine
3. If confirmed phishing → URL is added to detection blocklist
4. Dashboard shows "+1 Threat Reported" in your stats
5. Other Zerophish users are automatically protected from this URL

### Track Phishing — What It Means
The "Track Phishing" feature in the dashboard shows you the history of all threats found during your browsing sessions:
- Which sites were blocked (and when)
- How many times a particular domain was flagged
- Whether a site you reported was confirmed as phishing
- Your personal protection statistics over time

**Why is this useful?**
- You can see patterns (e.g., phishing attacks spike on Mondays)
- You can prove to your organization that threats are being blocked
- You can export reports for security audits

---

## 🔒 Privacy & Security

| What We Collect | What We Don't Collect |
|---|---|
| URL threat verdicts (anonymized) | Your passwords |
| Detection timestamps | Your browsing history |
| Threat categories | Personal information |
| User-submitted reports | Credit card data |

**All analysis happens locally in your browser first.** Only if you use manual scan or the Demo page does data leave your device (sent to our Supabase edge function over HTTPS).

---

## 🚀 How to Run This Project Locally

```bash
# 1. Install dependencies
npm install

# 2. Start the development server
npm run dev

# 3. Open in browser
# → http://localhost:8080

# 4. Load the browser extension
# → Chrome: go to chrome://extensions
# → Enable "Developer mode" 
# → Click "Load unpacked"
# → Select the dist/extension folder
```

---

## 📦 Technology Stack Summary

| Category | Technology |
|---|---|
| Frontend Framework | React 18 + TypeScript |
| Build Tool | Vite |
| UI Components | shadcn/ui |
| Styling | Tailwind CSS |
| Database | Supabase (PostgreSQL) |
| Authentication | Supabase Auth |
| Cloud Functions | Supabase Edge Functions (Deno) |
| Browser Extension | Chrome Manifest V3 |
| QR Code Decoding | jsQR library |
| Charts | Recharts |
| Maps | Leaflet.js / React-Leaflet |
| Icons | Lucide React |
| Fonts | Google Fonts (Inter) |
| Package Manager | npm |

---

*Built with ❤️ by Zerophish AI Team — Protecting the internet, one click at a time.*
