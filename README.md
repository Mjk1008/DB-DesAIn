# Uranus Billboard (DB) — Full Ad Format Documentation

> Written for someone who has never seen the ads visually.
> This document explains every element, interaction, and mechanic across all ads in this repository.

---

## Table of Contents

1. [What Is the Digital Billboard (DB)?](#1-what-is-the-digital-billboard-db)
2. [How It Lives on a Web Page](#2-how-it-lives-on-a-web-page)
3. [Project Structure — All Clients & Campaigns](#3-project-structure--all-clients--campaigns)
4. [Common Building Blocks Used in Every Ad](#4-common-building-blocks-used-in-every-ad)
5. [Asset Types](#5-asset-types)
6. [The Tracking System (tags.js)](#6-the-tracking-system-tagsjs)
7. [Click Handling & Redirect](#7-click-handling--redirect)
8. [Ad Type 1 — Standard Animated DB](#8-ad-type-1--standard-animated-db)
9. [Ad Type 2 — Scroll-Reactive DB](#9-ad-type-2--scroll-reactive-db)
10. [Ad Type 3 — Video Background DB](#10-ad-type-3--video-background-db)
11. [Ad Type 4 — Fortune Wheel DB](#11-ad-type-4--fortune-wheel-db)
12. [Ad Type 5 — Interactive Slider DB](#12-ad-type-5--interactive-slider-db)
13. [Ad Type 6 — Live Data / Chart DB](#13-ad-type-6--live-data--chart-db)
14. [Ad Type 7 — Playable / Game Ads](#14-ad-type-7--playable--game-ads)
15. [Ad Type 8 — Template DB](#15-ad-type-8--template-db)
16. [Ad Type 9 — RBT (Ring Back Tone) Ads](#16-ad-type-9--rbt-ring-back-tone-ads)
17. [Animation Patterns & CSS Classes](#17-animation-patterns--css-classes)
18. [Full Client & Campaign List](#18-full-client--campaign-list)

---

## 1. What Is the Digital Billboard (DB)?

The **Digital Billboard** (called **DB** internally) is a high-impact web advertising format created and produced by **Uranus Agency** and distributed through the **Yektanet** ad network.

Unlike a simple image banner, the DB is a **fully coded, animated, and interactive HTML ad** that runs inside an `<iframe>` embedded on publisher websites (news sites, blogs, apps across Iran).

**Key characteristics:**
- Height: **exactly 150px** (some older versions: 100px)
- Width: **100% responsive** — stretches to fill the full browser width
- Position: **sticky at the bottom** of the browser screen — it stays visible as users scroll
- Format: A self-contained **HTML file** with its own CSS, JavaScript, and all assets (images, video, audio)
- Language: **Persian (Farsi)**, right-to-left (`dir="rtl"`)

---

## 2. How It Lives on a Web Page

The DB lives inside an `<iframe>` tag on the publisher's page. The publisher's page is the **parent** and the ad HTML is the **child** (inside the iframe).

```
Publisher website (parent page)
└── <iframe style="height:150px; width:100%; position:sticky; bottom:0;">
    └── Ad HTML (index.html) — the Digital Billboard
        ├── style.css
        ├── script.js
        ├── tags.js
        └── assets/ (images, video, audio)
```

The iframe is fixed to the bottom of the screen with `position: fixed; bottom: 0`. Because the ad is only 150px tall but spans the full page width, it creates a "shelf" at the bottom — a permanent, visible strip that the user always sees while scrolling.

---

## 3. Project Structure — All Clients & Campaigns

The repository is organized as:

```
/[client-name]/[campaign-name]/sticky-150px/index.html
```

Each folder contains a **single self-contained ad** with all its assets. There are **40+ client brands** and **80+ individual campaigns** in this repository.

---

## 4. Common Building Blocks Used in Every Ad

Every ad, regardless of type, is assembled from the same set of **visual layers**. Think of the ad like a layered cake — each layer is an HTML element positioned on top of the previous one using CSS `position: fixed`.

### The Visual Layers (from back to front):

| Layer Name | HTML Class | What It Is |
|---|---|---|
| **Background** | `.bg` | Full-width background. Can be a PNG image, JPG, SVG, or MP4 video. Sets the color/mood of the ad. |
| **Brand Logo** | `.logo` | The brand's logo image (PNG or SVG). Usually positioned top-right or top-left. Clicking it fires a tracking event. |
| **Overline** | `.overline` | A short brand tagline or product name, rendered as a pre-designed image (SVG or PNG). Usually sits above the CTA button. |
| **CTA Button** | `.cta` | "Call to Action" — the main clickable button. Designed as an image (SVG/PNG). Always has a **pulsing heartbeat animation** (grows and shrinks repeatedly) to attract the eye. Clicking it opens the advertiser's landing page. |
| **Products** | `.products` or `.product` | One or more product images. These are the items being advertised — phones, food, financial cards, etc. |
| **Intro** | `.intro` | An animated element that **enters from outside the screen** at the beginning of the ad. For example, in the Digikala Black Friday ad, a small delivery truck drives in from the right side. |
| **Star / Sparkle** | `.star` | Small star or sparkle decorative images that blink on/off to add visual energy. |
| **Yektanet Logo** | `img[src*="yektanet-logo"]` | A tiny (14×14px) Yektanet branding icon shown in the corner — required by the ad network for all ads. |

---

## 5. Asset Types

Each ad folder contains the following types of files:

### Images
| Extension | Use |
|---|---|
| `.png` | Main visual assets: backgrounds, characters, products, decorative elements |
| `.svg` | Scalable assets: logos, CTA buttons, taglines (overlines), icons |
| `.gif` | Animated images: used for animated percentages, bouncing elements, sparkles |
| `.jpg` / `.jpeg` | Photo backgrounds (used in DaricGold live ads) |
| `.webp` | Optimized web images |

### Video
| Extension | Use |
|---|---|
| `.mp4` | Background videos that loop silently behind the ad content (used in Snapppay, Taline) |

### Audio
| Extension | Use |
|---|---|
| `.m4a` | Sound effects — for example, in the Digikala Black Friday ad, clicking the intro truck plays a car horn sound |
| `.mp3` | Background music or sound effects (used in RBT ads) |

### Code & Data
| Extension | Use |
|---|---|
| `.html` | The ad itself — the main entry point |
| `.css` | All styling and positioning |
| `.js` | All animation logic, interactivity, and tracking |
| `.json` | Lottie animation data files (JSON-based vector animations) — used in Bluebank |
| `chart.umd.min.js` | Chart.js library for price charts — used in live data ads |
| `gsap.min.js` | GSAP animation library (local copy, used in playable ads) |

---

## 6. The Tracking System (tags.js)

Every ad (except a few very simple ones) includes a file called **`tags.js`**. This is the analytics brain of the ad.

### What it does:
`tags.js` defines a function called `fire_tag()` that sends messages from inside the ad iframe to the Yektanet platform using:
```js
window.parent.postMessage({ type: 'yn::event', event_type: '...' }, '*')
```

This allows Yektanet to measure exactly how users interact with every ad — without needing any third-party SDK.

### Events that are tracked automatically:

| Event Name | When It Fires |
|---|---|
| `TIMER_0_SECOND` | Immediately when the ad script loads |
| `DOM_CONTENT_LOADED` | When the HTML structure is ready |
| `WINDOW_LOADED` | When all images and assets finish loading |
| `TIMER_5_SECOND` | 5 seconds after ad loads |
| `TIMER_10_SECOND` | 10 seconds after ad loads |
| `TIMER_15_SECOND` | 15 seconds after ad loads |
| `TIMER_60_SECOND` | 60 seconds after ad loads |

### Events fired by the ad's script:

| Event Name | When It Fires |
|---|---|
| `VISIT_SLIDE01` | User sees the first animation phase (intro) |
| `VISIT_SLIDE02` | User sees the second phase (main content) |
| `VISIT_SLIDE03` | User sees the third phase (product + CTA) |
| `VISIT_SLIDE04–06` | Additional slides in more complex ads |
| `CLICK1` | User clicks on the intro element |
| `CLICK2` | User clicks on the CTA button |
| `CLICK3` | User clicks on the logo |
| `CLICK4` | User clicks on text or numeric elements |
| `CLICK5` | User clicks on products section |
| `CLICK6` | Additional click zones |
| `LOOP` | Ad has completed one full animation cycle and is restarting |

---

## 7. Click Handling & Redirect

When Yektanet serves the ad inside an iframe, it appends a URL parameter:
```
index.html?click_url=https://advertiser-website.com
```

Every ad has this JavaScript snippet:
```js
function getParameterByName(name) { ... }
click_url = getParameterByName('click_url');
if (click_url) {
  document.addEventListener('click', function () {
    open(click_url);
  });
}
```

**What this means:** Any click anywhere on the ad opens the advertiser's website in a new tab. The `click_url` is dynamically injected by Yektanet — the ad code itself never hardcodes the destination URL.

---

## 8. Ad Type 1 — Standard Animated DB

**Examples:** Digikala Black Friday, Bank Sepah, Technolife, Khanoumi, SnapPay, Tage, Calin, Nescafe, Taline, Tapsi, and most other campaigns.

This is the **core DB format** — used in the majority of campaigns.

### How it works (3-act structure):

#### Act 1 — The Intro (0–11 seconds)
An animated element **enters the screen from outside** (usually from the right or left edge). It moves across the screen using GSAP animation and then exits off the opposite side.

**Example — Digikala Black Friday:**
- A miniature delivery truck enters from the right side of the screen
- The truck has spinning wheels (CSS `@keyframes rotate` animation)
- The headlights pulse with a blinking animation (`@keyframes pulse`)
- Clicking the truck plays a **car horn sound** (`boogh.m4a`)
- After 11 seconds, the truck exits off the left side → Act 2 begins

#### Act 2 — The Main Message (11–16 seconds)
The background **scales up from the bottom** (`transform: scaleY(0)` → `scaleY(1)`).
The brand's main visual elements animate in:
- Banner image fades in
- Logo drops down from above
- The main offer (e.g. "17% discount") slides up into view
- A percentage badge bounces in with a spring animation (`ease: 'back.out'`)

#### Act 3 — The Product Showcase (16+ seconds)
The offer numbers exit. Products slide in from the left. The CTA button slides up from below, and the logo repositions to the corner. The ad is now in its "final state" showing:
- Products floating gently up and down (`.float` animation)
- CTA pulsing rhythmically (`.tapesh` animation)
- Stars twinkling on/off near the CTA

#### Looping
After ~63 seconds total, the ad calls `location.reload()` — resetting itself and starting Act 1 again. This fires the `LOOP` tracking event before reload.

#### Between Act 2 and Act 3 (Toggle)
Every 11 seconds, the ad **toggles** between showing products and showing the discount offer, keeping the ad fresh for long-session users.

---

## 9. Ad Type 2 — Scroll-Reactive DB

**Examples:** OKCS "Hafte-haye Milyardi" (v5), Taline Birthday

This is one of the most technically sophisticated DB formats.

### How it works:

The parent page (publisher website) continuously sends scroll position data into the ad iframe using `postMessage`:

```js
// Message from parent page to ad iframe:
{
  type: 'yn-window-scroll',
  scrolled: 45,          // percent scrolled (0–100)
  scrolledInPx: 1200,    // pixels scrolled from top
  heightOfPage: 5000     // total page height in pixels
}
```

The ad listens for this message:
```js
window.addEventListener('message', (e) => {
  if (e.data.type !== 'yn-window-scroll') return;
  handleScroll(e.data.scrolled, e.data.scrolledInPx, e.data.heightOfPage);
});
```

### What the user experiences:
- The ad displays **multiple products** (product1.png, product2.png, product3.png, product4.png...)
- As the user scrolls **down the page**, the product image in the ad **automatically changes** — it transitions to the next product with a smooth opacity animation
- When the user scrolls back up, the product changes back
- The effect is that the ad feels **synchronized with the page** — like the ad "knows" where you are in the article

### Why this is powerful:
- The ad never feels static — it's always changing in response to natural user behavior
- No click needed — the interaction happens passively just by scrolling

---

## 10. Ad Type 3 — Video Background DB

**Examples:** Snapppay Black Friday 3, Taline "Bazar 24 Saate"

### What's in it:
- A **full-width MP4 video** plays silently in a loop as the background (`autoplay muted loop playsinline`)
- On top of the video sits the brand's visual content (logo, overline, CTA, products)
- A **glass/overlay layer** (`div.glass`) with a semi-transparent tinted color sits between the video and the content to ensure text readability

### Special: Curtain Animation (Snapppay)
The Snapppay Black Friday ad adds a **theatrical curtain effect**:
- At the start, two curtain images (left and right) cover the video
- The curtains slide apart like a stage curtain opening, revealing the video behind them
- Products then animate in from below

### Sound Toggle (Snapppay)
A **volume/unmute icon** (`.unmute`, `.volume`) is visible. The user can click it to enable audio on the background video (browsers mute autoplay videos by default).

---

## 11. Ad Type 4 — Fortune Wheel DB

**Example:** MelliGold "Tala Dar Charkheshe" (Gold in the Spin)

### What the user sees:
A **roulette-style spinning wheel** (prize wheel) takes center stage. Around the wheel, **16 LED lights** blink in alternating patterns (odd-numbered lights blink on while even-numbered are dim, then they swap, creating a fairground light effect).

### Animation Sequence:

1. **Wheel enters:** The wheel container animates from off-screen to the center of the ad
2. **Background reveals:** The full background scales in behind it
3. **Wheel moves left:** The spinner shifts to the left side, making room for the header and CTA
4. **Header drops down:** A header/title bar slides down from above
5. **Overline & CTA fade in:** The CTA button appears with a pulsing animation
6. **Wheel spins fast:** The inner part of the wheel spins continuously (blur effect during fast spin)
7. **After 10 seconds — Finale:**
   - Background, header, and overline fade out
   - Wheel moves back to center
   - The wheel **decelerates** with a random stop angle (simulating a real spin result)
   - After stopping, the wheel exits upward off-screen
   - `location.reload()` → the whole sequence restarts

### The LED Lights:
The 16 light images are positioned in a **circle** around the wheel using CSS `transform: rotate()`. The JavaScript alternates between odd and even lights every 500ms — this creates the classic casino/fairground chasing-light effect.

---

## 12. Ad Type 5 — Interactive Slider DB

**Example:** Bluebank "Vam Topol" (Hefty Loan)

### What the user sees:

This is a **real financial calculator** inside an ad. The ad asks: "What is your account balance?" and shows how much loan you qualify for.

### How it works:

#### Phase 1 — Intro Slide (first 4 seconds):
- An intro banner slides up showing the tagline: "هر دقیقه یک وام تپل" (A hefty loan every minute)
- A **Lottie animation** (JSON-based vector card animation) plays — showing an animated bank card appearing

#### Phase 2 — Calculator (after 4 seconds):
The intro banner slides down, revealing the calculator:

**The Slider:**
- An HTML `<input type="range">` slider with min=0, max=5,000,000,000 (5 billion Rials)
- The user **drags the slider** to set their account balance
- As they drag: the "Account Balance" value updates in real time (formatted in Persian numbers)
- The "Loan Amount" display shows **10× the balance** (the actual loan offer)

**The Color System:**
The ad randomly picks one of 5 color themes on load:
1. Blue gradient (`#4472c4 → #2c5aa0`)
2. Purple gradient (`#A220BB → #C945E3`)
3. Green gradient (`#05AA7F → #5DCDB7`)
4. Gold gradient (`#F8AA00 → #DAB100`)
5. Red gradient (`#FA616C → #D52935`)

Each color has a matching Lottie card animation (`json1.json` through `json5.json`).

### Why this is interactive:
- The user actively participates — they set their own input and see personalized output
- This creates a sense of **personal relevance** ("this loan is calculated for me")
- The slider interaction prevents the click URL from firing (stopPropagation) so users can drag without accidentally navigating away

---

## 13. Ad Type 6 — Live Data / Chart DB

**Examples:** DaricGold "LiveAds", MelliGold "LiveAds", Talasea "LiveAds", Tabdeal "Bitcoin LiveAds"

### What the user sees:
A **real-time price chart** of gold or cryptocurrency, pulled from live APIs, displayed directly inside the 150px ad.

### Components:

**Price Display:**
- Large number showing the current price (e.g. "قیمت لحظه‌ای ۱ گرم طلای ۱۸ عیار" = Live price of 1g of 18-karat gold)
- A **green pulsing dot** (● live indicator) next to the price to signal real-time data

**Time Filter (Segmented Control):**
The user can click between 3 time periods:
- **امروز** (Today) — 24-hour chart
- **هفته گذشته** (Last Week)
- **ماه گذشته** (Last Month)

Clicking these buttons fetches different data and updates the chart.

**Chart:**
- A `<canvas>` element rendered by **Chart.js** (`chart.umd.min.js`)
- Line chart showing price movement over the selected time period
- The chart updates dynamically when time period changes

### Why this is powerful:
- The user gets genuinely useful, real-time financial information directly in the ad
- No need to click to a website to see current gold or crypto prices
- Creates a reason to look at the ad — it's a live data tool, not just a promotion

---

## 14. Ad Type 7 — Playable / Game Ads

These are full mini-games embedded in the ad. The most complex ad format in the repository.

---

### 14a. Alibaba — Chrome Dino Game

**File:** `alibaba/chrome-dino/index.html`

A recreation of the famous Chrome browser offline dinosaur game — but **branded for Alibaba** (Iranian travel booking platform).

#### What the user sees:
- A city background scrolls from right to left
- A character (the "dino" equivalent, branded for Alibaba) stands on a track
- Obstacles approach from the right
- A score display: "امتیاز شما: 0" (Your Score: 0)
- A start screen button with Alibaba branding

#### How to play:
- **Tap / click** anywhere → the character **jumps** over incoming obstacles
- Each obstacle avoided = score increases
- If the character hits an obstacle → **Game Over screen** appears
- Game Over screen shows: score breakdown, a reset button, and a **CTA button** to visit Alibaba
- Clicking the reset button → game restarts from the beginning

#### Technical Implementation:
- The game runs on an **HTML `<canvas>` element** (`id="gameCanvas"`)
- GSAP is used for UI animations (intro, game over screen)
- The city background scrolls using CSS animation
- A gate/arch decorative element sits in the background
- Unmute icon allows users to enable game sound effects

#### Why it works:
- Users spend significantly more time interacting with a playable ad than a static one
- The game creates positive brand association through fun
- Game Over state is the moment of highest intent — that's when the CTA is most prominently shown

---

### 14b. Tabdeal — Candy Crush Style Matching Game

**File:** `tabdeal/candycrush-playableads/index.html`

A cryptocurrency-themed match game built for **Tabdeal** (Iranian crypto exchange).

#### Screens:

**Start Dialog:**
- Shows Tabdeal logo
- Headline: "ثبت‌نام کن جایزه‌تو بگیر!" (Register and get your prize!)
- Offer: "۵۰ میلیون بیبی دوج" (50 million Baby Doge coins) for signing up
- A **"شروع بازی"** (Start Game) button
- A close (X) button to dismiss

**Game Screen:**
- A **grid of cryptocurrency token icons** (Bitcoin, Ethereum, Dogecoin, etc.)
- **Timer:** 20-second countdown
- **Score display:** current score
- **Shuffle button:** randomizes the board
- A **helping hand animation** appears after inactivity to show users how to move pieces
- Instructions: "ارزهای دیجیتال مشابه رو کنار هم بذار!" (Put similar cryptocurrencies next to each other!)
- At the bottom: Tabdeal logo + "خرید و فروش امن و آسان" (Safe and easy buying/selling)

**End Dialog:**
- Shows final score
- Headline: "50 میلیون بیبی دوج بابت ثبت‌نام"
- **"دریافت جایزه"** (Receive Prize) button → this is the CTA that opens Tabdeal registration

#### Interaction:
- Users **drag** cryptocurrency tokens using GSAP's `Draggable` plugin
- Matching 3+ identical tokens in a row = they disappear and score increases
- The 20-second timer adds urgency
- When time runs out → End Dialog appears with the prize CTA

---

## 15. Ad Type 8 — Template DB

**Example:** Taline "Bazar 24 Saate" (24-Hour Market)

This is the most forward-looking format — it contains **`<meta>` template field definitions** in the HTML `<head>`:

```html
<meta content="تیتر اصلی" name="field-text1" />
<meta content="ساب‌تیتر" name="field-text2" />
<meta content="متن CTA" name="field-text4" />
<meta content="تصویر لوگو" name="field-image1" />
<meta content="تصویر محصول" name="field-image2" />
<meta content="ویدیو بکگراند" name="field-video1" />
<meta content="بکگراند لوگو" name="field-color1" />
<meta content="رنگ شیشه‌ی روی ویدیو" name="field-color2" />
<meta content="رنگ تیتر" name="field-color3" />
...through field-color9
```

### What this means:
This ad was designed as a **reusable template**. Instead of hardcoded assets, it defines:
- 2 text fields (headline + subtitle)
- 1 CTA text field
- 2 image slots (logo + product image)
- 1 video slot (background video)
- 9 color slots (for full theme customization)

The ad includes a `<video>` background, a glass overlay layer (semi-transparent tint), text elements (`<h1>`, `<h2>`), a `<button>`, and a product image — all driven by the template fields.

### Why this matters:
This is the beginning of an automated ad production system. Instead of a designer building each ad from scratch, a form could be filled in (brand colors, copy, assets) and the template generates the ad automatically.

---

## 16. Ad Type 9 — RBT (Ring Back Tone) Ads

**Location:** `RBT/pishvaz/` in sizes: `300x100`, `300x250`, `sticky-150px`

These are ads for a **ringtone/caller-tune subscription service** (پیشواز — "Pishvaz" in Farsi means a ringtone that callers hear while waiting).

### What makes them different:
- Available in **multiple sizes** (not just 150px sticky): 300×100px and 300×250px as well
- Assets come in **two color variants**: white and yellow (e.g. `22768-white.svg`, `22768-yellow.svg`)
- Include actual **MP3 audio files** (the actual ringtone being advertised, e.g. `22768.mp3`)
- A "play box" with 3 slots showing music-related icons

### How it works:
- The ad displays an album/song selection interface
- Three boxes show available ringtones
- Clicking plays a preview of the ringtone via JavaScript
- A gift icon and CTA button invite users to subscribe

---

## 17. Animation Patterns & CSS Classes

These CSS classes and animation patterns appear consistently across all ads:

| Class | Effect | CSS Animation |
|---|---|---|
| `.tapesh` | CTA button pulsing (heartbeat) | `scale: 1 → 1.2 → 1`, repeating every 0.75s |
| `.float` | Products gently bobbing up and down | `translateY(0 → 5px → 0)`, repeating every 2s |
| `.pulse` | Elements fading in/out | `opacity: 1 → 0.5 → 1`, repeating every 0.7s |
| `.rotate` | Wheels/spinners spinning | `rotate: 0 → 360deg`, repeating continuously |

### GSAP is used for all entrance/exit animations:
- `gsap.to(element, { right: '105%', duration: 11 })` — intro exits left
- `gsap.to(element, { opacity: 1, duration: 1 })` — fade-in
- `gsap.to(element, { top: '10px', ease: 'back.out(1.2)' })` — spring bounce-in
- `gsap.timeline()` — chains multiple animations in sequence

### All elements use `position: fixed`:
Because the ad is a 150px iframe, every element inside uses `position: fixed` (not absolute) to position relative to the iframe viewport. Elements start off-screen (e.g. `top: -100px`, `left: -100%`) and animate into visible positions.

---

## 18. Full Client & Campaign List

| Client | Campaign | Format Type |
|---|---|---|
| Alibaba | Chrome Dino | Playable Game |
| Appstar | — | Standard Animated |
| Auto Heidari | — | Standard Animated |
| Azkivam | 30 Bahman, Etebar 100 Millioni, Vam 100 Millioni | Standard Animated |
| Back To School | — | Standard Animated |
| Bank Sepah | Baran Omid (v1, v2), Omid Zarin | Standard Animated |
| Bank Tejarat | Momtaz, Sabte Hesabe Vekalati | Standard Animated |
| Bankino | Khat | Standard Animated |
| Bitpin | MilliGold, Pin-Jam-kon-Bitcoin-Bebar, Ticket | Standard Animated |
| Bluebank | Black Friday, Vam Topol | Interactive Slider + Lottie |
| Butan | Black Friday | Standard Animated |
| Calin | Combo, Mazeha | Standard Animated |
| DaricGold | LiveAds | Live Data / Chart |
| Delshad | Black Friday | Standard Animated |
| Digikala | Back to School, Black Friday, Black Friday 2, Wepod | Standard Animated |
| Dookhtebartar | Amoozesh | Standard Animated |
| Elanza | Black Friday | Standard Animated |
| Fotouhi | Demo | Standard Animated |
| GSM | Black Friday, Shajarian | Standard Animated |
| Invi | Tala | Standard Animated |
| Irancell | Bamdad 1, Bamdad 2, Bamdad 3 | Standard Animated |
| Khanoumi | Black Friday, Haraje Payane Sal, Pink Box | Standard Animated |
| Khodro45 | Foroosh Dar Chand Saat, Yek On Forookhte Shod | Standard Animated |
| Khosravani | Banner Templates (multiple sizes), Chatbox | Multi-size Templates |
| Lux | Hezar Jayezeh | Standard Animated |
| MelliGold | Az Bazar Jolo Bezan, LiveAds, Tala Dar Charkheshe, Tarkib Barandeh | Fortune Wheel + Live Data |
| Mofid | Ayar | Standard Animated |
| MyBaby | Az Rooze Avval | Standard Animated |
| Nescafe | Taame Jadid | Standard Animated |
| Okala | Kharid Online | Standard Animated |
| OKCS | Hafte-haye Milyardi (v1–v5), Jashn 12 Salegi | Scroll-Reactive |
| Palaz | Az In Roo be Oon Roo | Standard Animated |
| RBT | Pishvaz | RBT / Audio |
| RubyGold | Black Friday | Standard Animated |
| Saraf | Ethereum | Standard Animated |
| Sarayeirani | Bedoone Pishpardakhtesh Khoobe | Standard Animated |
| Sepidar System | CRM | Standard Animated |
| Shahre Farsh | Black Friday, Mother Day | Standard Animated |
| Snappmarket | Az Jam Tekoon Nemikhoram (Man, Woman) | Standard Animated |
| Snapppay | Black Friday, Black Friday 2, Black Friday 3 | Video Background |
| Snappshop | Black Friday | Standard Animated |
| Tabdeal | Bitcoin LiveAds, Black Friday, Candy Crush Playable | Live Data + Playable Game |
| Tage | Rahe Hale Pakizegi (1, 2, 3) | Standard Animated |
| Talasea | LiveAds, Noghresea & Talasea | Live Data |
| Taline | Bazar 24 Saate, Birthday, Black Friday (4 cities) | Template + Standard |
| Tapsi | Hamsafar | Standard Animated |
| TapsiGarage | Digipay | Standard Animated |
| Technolife | Black Friday, Ersalesh Ba Technolife, Technopay | Standard Animated |
| Uranus (Self-promo) | Yalda | Standard Animated |
| Vezarat Eghtesad | Bime Shakhse Sales | Standard Animated |
| Wallex | Silver Live | Standard Animated |

---

## Summary — What Makes This Ad Format Special

1. **High visibility:** Sticky bottom position means users always see it while scrolling — unlike banner ads that scroll away
2. **Full-width impact:** Responsive width uses the entire browser canvas
3. **Rich animation:** GSAP-powered animations with intro, main content, and product phases — far beyond static banners
4. **Interactivity spectrum:** From passive animated ads to fully playable games, the format supports any level of user interaction
5. **Scroll sync:** Ads can respond to the user's exact scroll position — creating a seamless experience between the article and the ad
6. **Live data:** Ads can display real-time gold and crypto prices, making them genuinely useful rather than just promotional
7. **Precise analytics:** `tags.js` tracks every meaningful interaction — slide views, time milestones, click zones — giving advertisers complete visibility into campaign performance
8. **Self-contained:** Each ad is 100% self-contained (no external dependencies except GSAP CDN) — reliable delivery on any publisher site

---

*Documentation written by analyzing the full source code of the Uranus-Billboard repository.*
*All ads are produced by Uranus Agency and distributed through the Yektanet ad network.*
