# 🕹️ ARCAD3X — User Manual

**Platform:** ARCAD3X — Fullstack Gaming Platform  
**Game:** SI3LN — Space Invaders III Last Night  
**Version:** 1.0.0  
**Last updated:** March 12, 2026

---

## 📑 Table of Contents

### Part A — Frontend (Web Dashboard)
1. [User Roles & Access Levels](#1-user-roles--access-levels)
2. [Navigation & Layout](#2-navigation--layout)
3. [Home Page](#3-home-page)
4. [Authentication (Login / Sign Up)](#4-authentication-login--sign-up)
5. [Profile Page](#5-profile-page)
6. [Games Page](#6-games-page)
7. [Game Play Page](#7-game-play-page)
8. [Help & Support](#8-help--support)
9. [Settings (Admin Only)](#9-settings-admin-only)
10. [About Page](#10-about-page)
11. [Global Search](#11-global-search)
12. [Internationalization (i18n)](#12-internationalization-i18n)
13. [Mobile Features](#13-mobile-features)

### Part B — Backend API Reference
14. [API Overview](#14-api-overview)
15. [Authentication Endpoints (`/api/auth/`)](#15-authentication-endpoints-apiauth)
16. [Game Endpoints (`/api/game/`)](#16-game-endpoints-apigame)
17. [Profile Endpoints (`/api/game/profile/`)](#17-profile-endpoints-apigameprofile)
18. [Security Features](#18-security-features)

### Part C — Admin Operations
19. [Django Admin Panel (`/admin/`)](#19-django-admin-panel-admin)
20. [Management Commands](#20-management-commands)
21. [Admin-Only API Access](#21-admin-only-api-access)

### Appendix
22. [Complete User Actions Matrix](#22-complete-user-actions-matrix)
23. [Complete API Endpoints Matrix](#23-complete-api-endpoints-matrix)

---

# Part A — Frontend (Web Dashboard)

---

## 1. User Roles & Access Levels

The dashboard uses **role-based access control (RBAC)** with three user levels. UI elements are dynamically shown/hidden via CSS classes (`.admin-only`, `.player-only`, `.guest-only`).

| Role | Description | Visible Sections |
|------|-------------|-----------------|
| **Guest** | Not logged in | Home, Games, Help, About, Login/Sign Up link, Game Play (scores not saved) |
| **Player** | Registered & logged in | All Guest sections + Profile, Logout button, scores saved to API |
| **Admin** | Staff/superuser account | All Player sections + Settings page (platform stats, Django admin link) |

---

## 2. Navigation & Layout

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Side Menu (▶ triangle)** | Click the ▶ play triangle on the left edge | Toggles the `.menu-dropdown` visibility via `classList.toggle('visible')` | A sliding menu appears with navigation items: Profile, Games, Help, Settings (admin), About |
| **Menu Items** | Click any menu item (e.g., 🎮 Games) | Reads `data-page` attribute, calls `app.navigateTo(page)` | Navigates to the corresponding page, menu auto-closes |
| **ARCAD3X Logo (top bar)** | Click the centered "ARCAD3X" title | Navigates to `home` page | Returns to the home page from any location |
| **ARCAD3X Logo (menu header)** | Click the "ARCAD3X" text in the menu | Navigates to `home`, closes menu dropdown | Returns to home page |
| **Page Logos** | Click "ARCAD3X" on sub-pages (profile, help) | Each page header has a `data-page="home"` link | Returns to the home page |
| **Click outside menu** | Click anywhere outside the menu dropdown | Document click listener checks if click is outside menu trigger and dropdown | Menu closes automatically |

---

## 3. Home Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Welcome Banner** | View (automatic) | Displays `Welcome in ARCAD3X` heading with subtitle | Shows the platform name and tagline |
| **SI3LN Game Card** | Click "Play now →" link | Calls `gamesManager.launchGame('si3ln')` | Navigates to the full-screen game play page and loads the game |
| **Leaderboard Card** | View (automatic) | On page load, `loadHomeData()` calls `facade.getLeaderboard(3)` via `GET /api/game/leaderboard?limit=3` | Displays Top 3 players with rank, username, and score (e.g., "#1 PlayerX 1500 pts") |
| **Player Profile Card** | View (automatic) | If authenticated, fetches profile via `facade.getCurrentPlayer()` → `GET /api/game/profile/me` | Shows "Welcome, [username]!" for logged-in users, or "Please log in" for guests |

---

## 4. Authentication (Login / Sign Up)

### 4.1 Login Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Login Link** | Click "🔐 Login / Sign Up" in top bar | `navigateTo('login')` | Displays the login form |
| **Username Field** | Type username | Standard text input, required | Captures username for authentication |
| **Password Field** | Type password | Standard password input, required | Captures password for authentication |
| **Remember Me** | Toggle checkbox | Stores preference (UI only in current version) | Visual toggle (future persistence feature) |
| **Login Button** | Click "Login" | Calls `facade.login(username, password)` → `POST /api/auth/login` with `{"username", "password"}`. Stores JWT in `localStorage`. | On success: redirects to home, shows username in top bar, applies player role UI. On failure: shows "Login failed" alert |
| **Forgot Password** | Click link | Displays alert | Shows "Password recovery feature coming soon!" |
| **Create an Account** | Click link | `navigateTo('create-account')` | Navigates to the sign-up form |

### 4.2 Sign Up Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Email Field** | Type email | Real-time validation via regex `^[^\s@]+@[^\s@]+\.[^\s@]+$` | Green border if valid, red if invalid |
| **Pseudo (Username) Field** | Type username | Validates: min 3 chars, `[A-Za-z0-9_]+` pattern, profanity check against blocklist | Shows ✓ "Pseudo disponible" (green) or ❌ error message (red). Warning: "This username cannot be changed later" |
| **Password Field** | Type password | Live validation against 3 requirements checked independently | Three inline indicators update in real-time: ✗/✓ 8 characters min, ✗/✓ At least 1 number, ✗/✓ At least 1 uppercase |
| **Confirm Password** | Type password again | Compares with password field on every keystroke | Shows ✓ "Les mots de passe correspondent" (green) or ❌ mismatch error (red) |
| **Preferred Language** | Select from dropdown | 12 language options (FR, EN, ES, DE, IT, PT, NL, PL, RU, JA, KO, ZH) | Stores language preference |
| **Accept Terms** | Toggle checkbox | Required to enable the submit button | Submit button becomes clickable only when all validations pass AND terms are accepted |
| **Create My Account** | Click button | Calls `facade.signup({username, email, password})` → `POST /api/auth/register`. JWT stored automatically | On success: redirects to home, user is logged in. On failure: "Registration failed" alert |
| **Login Here** | Click link | `navigateTo('login')` | Navigates back to login form |

### 4.3 Logout

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Logout Button** | Click "Logout" in top bar | `facade.logout()` removes JWT from `localStorage` (keys: `access_token`, `SI3LN_SESSION`, `SI3LN_JWT_TOKEN`) | UI reverts to guest mode: login link reappears, username disappears, profile hidden in menu, guest-only elements shown |

### 4.4 Session Expiry

| Trigger | How It Works | Result |
|---------|--------------|--------|
| JWT token expired | `checkAuth()` decodes JWT payload, checks `exp` claim against `Date.now()`. If `exp * 1000 < Date.now()`, token is cleared | User is silently logged out. Guest UI is restored. User must re-login |

---

## 5. Profile Page

*Accessible only to authenticated users (Player / Admin role).*

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Avatar** | View (automatic) | Loaded from `profile.avatar_url` via API, falls back to default image | Displays the player's profile picture |
| **Display Name & Username** | View (automatic) | Populated from `profile.username` | Shows player name and @username |
| **Statistics Grid** | View (automatic) | Fetches from `GET /api/game/profile/me` | Displays 4 stat cards: Total Score, Games Played, Highest Level, Achievements count |
| **Best Scores** | View (automatic) | Fetches sessions via `facade.getGameSessions(playerId)` → `GET /api/game/sessions?player_id=X`, sorts by score descending, takes top 5 | Shows ranked list: #1 1500pts Level 5, #2 1200pts Level 4, etc. |
| **Bio (About Me)** | View (automatic) | Loaded from `profile.bio` field | Displays user's bio text, or "No description yet..." placeholder |
| **Favorite Games** | View (automatic) | Placeholder section | Shows "Favorites coming soon!" |
| **Edit Profile Button (✎)** | Click | Opens the edit profile modal (`editProfileModal`) | Modal overlay appears with editing options |

### 5.1 Edit Profile Modal

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Background Color Picker** | Click a color swatch | 6 predefined dark colors (`#000000` to `#2a0a0a`). Selected color gets `.selected` class | Profile page background changes to selected color |
| **Profile Picture Upload** | Click "Choose Image", select file | File input accepts `image/*`, validates file size ≤ 5MB client-side. Preview shown immediately via `FileReader.readAsDataURL()` | Avatar preview updates instantly. File stored as `_pendingAvatarFile` for upload on save |
| **Bio Textarea** | Type text | Max 500 characters, live character counter updates on input | Character counter shows "X/500". Text is HTML-escaped server-side (XSS prevention) |
| **Privacy Toggle** | Toggle "Show my scores on profile" | Checkbox bound to `show_scores` field | Controls whether scores are visible on public profile |
| **Favorite Games** | Select from multi-select | Hold Ctrl/Cmd to pick multiple games | Stores favorite game selections (future feature) |
| **Cancel Button** | Click | Hides modal, discards changes | Modal closes, no API calls made |
| **Save Changes** | Click | Sequentially: (1) Uploads avatar if changed via `POST /api/game/profile/me/avatar` (FormData with magic-byte validation, 5MB limit), (2) Sends `PATCH /api/game/profile/me` with `{bio, bg_color, show_scores}` | Profile reloads with updated data. Button shows "Saving…" during operation |
| **Close (×)** | Click the × button | Hides modal | Modal closes |

---

## 6. Games Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **SI3LN Game Card** | Click the card or "▶ PLAY NOW" overlay | `gamesManager.launchGame('si3ln')` | Navigates to full-screen game play page, loads the game in an iframe |
| **Coming Soon Cards (×3)** | View (no interaction) | Static placeholder cards with 🎮 icon | Displays "Coming Soon..." for future games (Space Warriors, Fantasy Quest, etc.) |

---

## 7. Game Play Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Game Loading** | Automatic (1.2s delay) | Shows spinning loader overlay for 1200ms while iframe loads | Loading animation displayed, then game appears |
| **Game Iframe** | Play the game | Pygame/Pygbag game compiled to WebAssembly, embedded in sandboxed `<iframe>` with `allow-scripts allow-same-origin` | Full SI3LN game experience in the browser |
| **Exit Game (⬅)** | Click "Exit Game" | Ends active game session (if any) via `PATCH /api/game/sessions/{id}`, resets iframe to `about:blank`, clears background styling | Returns to Games page |
| **Fullscreen (⛶)** | Click "Fullscreen" | Uses browser Fullscreen API (`requestFullscreen()`) with cross-browser fallbacks (webkit, moz, ms) | Game page fills entire screen. Button text changes to "Exit Fullscreen" (⊡) |
| **Exit Fullscreen** | Click "Exit Fullscreen" or press Esc | Calls `document.exitFullscreen()` | Returns to windowed mode |
| **Game Title** | View (automatic) | Shows "🎮 SI3LN" from `currentGameName` | Displays current game name in the control bar |
| **Previous / Next Game** | Click ◀ or ▶ navigation buttons | Cycles through games array with modulo index. If next game is `comingSoon`, shows alert | Launches previous/next playable game, or shows "[Game Name] - Coming Soon!" alert |
| **Guest Info Banner** | View (automatic, guests only) | Displayed for 8 seconds if user is not authenticated | Shows: "Playing as guest - Your score won't be saved. Login to save your progress!" with a clickable Login link |
| **Game Session (authenticated)** | Automatic on game launch | `facade.createGameSession()` → `POST /api/game/sessions` with `{player_id}` from JWT | Creates a server-side session. On exit, session is ended with score/level via `PATCH /api/game/sessions/{id}`. Player stats auto-update on first completion |
| **Game Background** | Automatic | Sets `backgroundColor` and `backgroundImage` on the game container from game config (`homepageBg: '#000428'`, `homepageImage`) | Game area has themed background matching the game's visual identity |

---

## 8. Help & Support

### 8.1 Help Menu

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Games Tutorials Card** | Click "View Tutorials" | `helpManager.showHelpDetail('games')` | Displays SI3LN tutorial with controls, gameplay rules, scoring table, and tips |
| **Report Player Card** | Click "Submit Report" | `helpManager.showHelpDetail('report')` | Displays player report form |
| **Report Bug Card** | Click "Report Bug" | `helpManager.showHelpDetail('bug')` | Displays bug report form with auto-detected browser info |
| **Support Us Card** | Click "Support Project" | `helpManager.showHelpDetail('support')` | Displays support/donation information page |
| **← Back to Help Menu** | Click back button | `helpManager.showHelpMenu()` | Returns to the 4-card help menu |

### 8.2 Games Tutorial Content

| Information | Details |
|-------------|---------|
| **Controls** | Arrow Keys / WASD — Move ship; Space Bar — Shoot; P — Pause; ESC — Exit to menu |
| **Gameplay** | Destroy alien invaders before they reach the bottom. Use barriers for cover. Watch for UFO bonus |
| **Scoring** | Small aliens: 10 pts, Medium: 20 pts, Large: 30 pts, UFO: 50–300 pts (random), Level completion: 1000 pts |
| **Tips** | Accuracy > speed; use barriers strategically; watch alien patterns; save power-ups; register to save scores |

### 8.3 Report Player Form

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| Player Username | Text input | ✅ | Username of the player to report |
| Reason for Report | Dropdown | ✅ | Options: Offensive Username, Offensive Description, Harassment, Cheating/Hacking, Spam, Other |
| Details | Textarea | ✅ | Detailed description of the incident |
| Evidence | Textarea | ❌ | Screenshots, date/time, other evidence |
| **Submit** | Button | — | Generates and **downloads a `.txt` report file** (formatted with headers, timestamp, platform version, reporter name). Alert prompts user to email it to `support@arcad3x.com` |

### 8.4 Bug Report Form

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| Bug Title | Text input | ✅ | Brief description of the bug |
| Category | Dropdown | ✅ | Options: Gameplay, User Interface, Login/Authentication, Profile, Graphics/Visual, Audio, Performance, Other |
| Description | Textarea | ✅ | What happened vs. expected behavior |
| Steps to Reproduce | Textarea | ❌ | Numbered reproduction steps |
| Browser & OS | Text input (auto-filled) | ❌ | Auto-detected via `navigator.userAgent` parsing (e.g., "Chrome 120 on Windows 10/11") |
| **Submit** | Button | — | Generates and **downloads a `.txt` bug report file** (formatted with timestamp, version, browser info, all fields). Alert thanks the user |

### 8.5 Support Us Page

| Information | Details |
|-------------|---------|
| Ways to Support | Play games & share, provide feedback, report bugs, follow on social media, financial support (coming soon) |
| Payment Options | PayPal / Stripe integration announced as coming soon |
| Credits | Created by Hugex & Schps, © 2026 ARCAD3X |

---

## 9. Settings (Admin Only)

*Only accessible to users whose JWT contains `is_superuser: true` or `is_staff: true`. Non-admin users are redirected to the home page.*

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Platform Statistics** | View (automatic) | Fetches `GET /api/game/stats` | Displays: Total Players, Total Sessions, Highest Score, Average Score |
| **Django Admin Panel Link** | Click "Open Django Admin Panel" | Opens `/admin/` in a new browser tab | Access to Django's built-in admin interface for full database management |

---

## 10. About Page

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **About Content** | View (automatic) | `loadAbout()` renders static HTML with version from `APP_CONFIG.VERSION` | Displays: team credits ("Hugex & Schps"), platform description ("FullStack gaming platform"), current version number |

---

## 11. Global Search

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Search Input** | Type a query (min 2 characters) | `SearchService.debounceSearch()` waits 300ms after last keystroke, then performs unified search across 3 content types | Dropdown appears below the search bar with categorized results |
| **Search Button (🔍)** | Click | Triggers immediate search (no debounce) | Same dropdown results |
| **Enter Key** | Press Enter in search field | Triggers immediate search | Same dropdown results |
| **Escape Key** | Press Escape | Hides results dropdown, blurs input | Search results disappear |
| **Click Outside** | Click anywhere outside search container | Document click listener hides dropdown | Search results disappear |

### 11.1 Search Content Types

| Content Type | Icon | Source | Search Method | Example Result |
|-------------|------|--------|---------------|----------------|
| **Games** | 🎮 | Static catalog (3 games) | Matches title and keywords (e.g., "si3ln", "space", "invaders", "arcade", "shooter", "retro") | "SI3LN - Space Invaders III Last Night" → Click to play |
| **Help Articles** | ❓ | Static catalog (4 articles) | Matches title and keywords (e.g., "tutorial", "controls", "bug", "report", "support") | "SI3LN Game Tutorial" → Opens tutorial section |
| **Players** | 👤 | API call `GET /api/game/players` | Filters by username match (case-insensitive) | "PlayerX — 1500 pts" → Opens profile page |

### 11.2 Search Result Actions

| Result Type | Click Action | Navigation |
|-------------|-------------|------------|
| Game (SI3LN) | `launchGame('si3ln')` | Opens game play page and starts the game |
| Game (Coming Soon) | Navigates to Games page | Shows games catalog |
| Help Article | `showHelpDetail(section)` | Opens specific help section (tutorial, report, bug, support) |
| Player | Navigates to Profile page | Opens the profile page |

### 11.3 Search Performance

| Feature | Value |
|---------|-------|
| Debounce delay | 300ms (configurable via `APP_CONFIG.SEARCH_DEBOUNCE_MS`) |
| Cache duration | 60 seconds |
| Max cache entries | 100 (FIFO eviction) |
| Max results | 20 (configurable via `APP_CONFIG.SEARCH_MAX_RESULTS`) |
| Min query length | 2 characters (configurable via `APP_CONFIG.SEARCH_MIN_LENGTH`) |
| Sorting | Exact title matches first, then partial matches |

---

## 12. Internationalization (i18n)

| UI Element | User Action | How It Works | Result |
|------------|-------------|--------------|--------|
| **Language Switcher (🌐)** | Click the globe icon in the top bar | Toggles a dropdown with available languages | Language dropdown appears |
| **English (🇬🇧)** | Click "English" | `i18n.setLanguage('en')` → loads `/locales/en.json` → updates all `[data-i18n]` elements | All UI text switches to English |
| **Français (🇫🇷)** | Click "Français" | `i18n.setLanguage('fr')` → loads `/locales/fr.json` → updates all `[data-i18n]` elements | All UI text switches to French |
| **Current Language Display** | View (automatic) | Shows current language code (e.g., "EN", "FR") next to the globe icon | Reflects the active language |

### 12.1 Translation System

| Feature | Details |
|---------|---------|
| Mechanism | `data-i18n` attributes on HTML elements mapped to translation keys |
| Placeholder translations | `data-i18n-placeholder` for input placeholders |
| Available languages | English (EN), French (FR) |
| Persistence | Language choice stored in `localStorage` |
| Scope | All static UI text (buttons, labels, headings, placeholders, menu items) |

---

## 13. Mobile Features

*Automatically activated when a mobile device is detected via `navigator.userAgent`.*

| Feature | User Action | How It Works | Result |
|---------|-------------|--------------|--------|
| **Swipe Right** | Swipe right on screen | Touch event listeners track `touchStartX` → `touchEndX`, threshold: 50px | Opens the side navigation menu |
| **Swipe Left** | Swipe left on screen | Same swipe detection mechanism | Closes the side navigation menu |
| **Double Tap** | Double-tap on menu area | Detects two taps within 300ms | Closes the menu |
| **Long Press** | Press and hold on game/help/cards (500ms) | `setTimeout` with 500ms delay on `touchstart`, cleared on `touchend`/`touchmove` | Triggers haptic vibration (50ms) if device supports `navigator.vibrate()` |
| **Pull to Refresh** | Pull down from top of page (80px threshold) | Tracks `touchmove` distance when `scrollY === 0` | Reloads current page data via `app.loadPageData()` with haptic feedback (30ms vibration) |
| **Touch Feedback** | Tap any button, menu item, card, or link | Adds `.touch-active` class on `touchstart`, removes after 150ms on `touchend` | Visual touch feedback (pressed state) on interactive elements |
| **Prevent Double-Tap Zoom** | Tap buttons quickly | `touchend` calls `e.preventDefault()` then `e.target.click()` on buttons | Prevents browser zoom on double-tap while maintaining click functionality |
| **Game Page Scroll Lock** | Touch-scroll on game page | `touchmove` prevented when `scrollY === 0` on game-play-page | Prevents accidental pull-to-refresh during gameplay |
| **Mobile CSS** | Automatic | `mobile-device` class added to `<body>`, dedicated `mobile.css` and `responsive.css` stylesheets | Responsive layout, touch-optimized button sizes, mobile-friendly spacing |

---

# Part B — Backend API Reference

---

## 14. API Overview

| Property | Value |
|----------|-------|
| **Base URL** | `http://localhost:8000/api/` (dev) — `https://yourdomain.com/api/` (prod) |
| **Framework** | Django Ninja (automatic OpenAPI / Swagger docs) |
| **Interactive Docs** | `GET /api/docs` — Swagger UI for testing all endpoints |
| **Authentication** | JWT Bearer tokens — `Authorization: Bearer <token>` |
| **Token Lifetime** | 24 hours (configurable via `JWT_EXPIRATION_HOURS`) |
| **Content Type** | `application/json` (all request/response bodies) |
| **Rate Limiting** | 30 req/60s (auth endpoints), 5 req/60s (password endpoints) |

### 14.1 How to Authenticate

```
# Step 1: Get a token
POST /api/auth/login
Content-Type: application/json
{"username": "myuser", "password": "MyP@ss123"}

# Response:
{"token": "eyJ...", "username": "myuser", "player_id": 1}

# Step 2: Use the token in all subsequent requests
GET /api/game/profile/me
Authorization: Bearer eyJ...
```

### 14.2 Common Error Responses

| Status | Meaning | Example Body |
|--------|---------|-------------|
| `400` | Bad Request (validation failed) | `{"error": "Password must be at least 8 characters long."}` |
| `401` | Unauthorized (missing/invalid/expired token) | `{"error": "Invalid or expired token. Please login again."}` |
| `403` | Forbidden (not your resource) | `{"error": "You can only update your own player."}` |
| `404` | Not Found | `{"detail": "Not Found"}` |
| `409` | Conflict (duplicate) | `{"error": "Email address is already in use."}` |
| `429` | Rate Limited | `{"error": "Too many requests. Please try again later."}` (+ `Retry-After: 60` header) |

---

## 15. Authentication Endpoints (`/api/auth/`)

### 15.1 Register — `POST /api/auth/register`  
**Auth:** Public | **Rate Limit:** 30 req/60s

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `username` | string | ✅ | 3-30 chars, `[A-Za-z0-9_]` only, must be unique |
| `password` | string | ✅ | Min 8 chars, 1 uppercase, 1 number, Django password validators |
| `email` | string | ❌ | Valid email format (`user@domain.tld`) |

**Success (200):**
```json
{"token": "eyJ...", "username": "newplayer", "player_id": 5}
```
**Side effects:** Creates a Django `User` + a `Player` profile linked to it.

---

### 15.2 Login — `POST /api/auth/login`  
**Auth:** Public | **Rate Limit:** 30 req/60s

| Field | Type | Required |
|-------|------|----------|
| `username` | string | ✅ |
| `password` | string | ✅ |

**Success (200):**
```json
{"token": "eyJ...", "username": "existinguser", "player_id": 3}
```
**Failure (401):** `{"error": "Invalid credentials"}`

---

### 15.3 Logout — `POST /api/auth/logout`  
**Auth:** Bearer token required

**Success (200):** `{"message": "Logged out successfully. Token has been invalidated."}`  
**Side effects:** Token is added to the Redis blacklist (TTL: 48h). Cannot be reused.

---

### 15.4 Refresh Token — `POST /api/auth/refresh`  
**Auth:** Bearer token required

**Success (200):**
```json
{"token": "eyJnew...", "username": "myuser", "player_id": 3}
```
**Side effects:** Old token is blacklisted — only the new token is valid (token rotation).

---

### 15.5 Get Current User — `GET /api/auth/me`  
**Auth:** Bearer token required

**Success (200):**
```json
{
  "username": "myuser",
  "email": "user@example.com",
  "player_id": 3,
  "total_score": 4500,
  "games_played": 12,
  "is_staff": false,
  "is_superuser": false,
  "role": "player"
}
```
> The `role` field returns `"admin"` for superusers, `"staff"` for staff, or `"player"` for regular users.

---

### 15.6 Change Password — `POST /api/auth/change-password`  
**Auth:** Bearer token required | **Rate Limit:** 5 req/60s

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `old_password` | string | ✅ | Must match current password |
| `new_password` | string | ✅ | Min 8 chars, must differ from old password, Django validators |

**Success (200):** `{"message": "Password changed successfully"}`  
**Failure (400):** `{"error": "Current password is incorrect"}` or `{"error": "New password must be different from the current password."}`

---

### 15.7 Update Account — `PATCH /api/auth/update-account`  
**Auth:** Bearer token required

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `email` | string | ❌ | Must be unique across all users |
| `first_name` | string | ❌ | |
| `last_name` | string | ❌ | |

**Success (200):**
```json
{
  "message": "Account updated successfully",
  "username": "myuser",
  "email": "new@email.com",
  "first_name": "Hugo",
  "last_name": "Ramos"
}
```

---

## 16. Game Endpoints (`/api/game/`)

### 16.1 Players

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/game/players` | ✅ | List all players |
| `POST` | `/api/game/players` | ❌ | Create a new player (game registration) |
| `GET` | `/api/game/players/{id}` | ✅ | Get player by ID |
| `PUT` | `/api/game/players/{id}` | ✅ | Update player (own player only) |
| `DELETE` | `/api/game/players/{id}` | ✅ | Delete player (own player only) |

**Player Object:**
```json
{
  "id": 1,
  "username": "PlayerX",
  "email": "player@example.com",
  "total_score": 4500,
  "games_played": 12,
  "highest_level": 7,
  "created_at": "2026-01-15T10:30:00Z"
}
```

---

### 16.2 Game Sessions

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/game/sessions` | ✅ | List sessions (filter: `?player_id=X&world_id=Y`) |
| `POST` | `/api/game/sessions` | ✅ | Start a new session (`{player_id, world_id?}`) |
| `GET` | `/api/game/sessions/{id}` | ✅ | Get session by ID |
| `PATCH` | `/api/game/sessions/{id}` | ✅ | End/update session (own sessions only) |
| `DELETE` | `/api/game/sessions/{id}` | ✅ | Delete session (own sessions only) |

**Session Create Payload:**
```json
{"player_id": 1, "world_id": null}
```

**Session Update Payload (on game exit):**
```json
{
  "score": 1500,
  "level_reached": 5,
  "enemies_killed": 42,
  "duration_seconds": 300,
  "completed": true
}
```
> **Auto-behavior on first completion:** Player's `total_score` increments, `games_played` +1, `highest_level` updates if new record. `ended_at` is auto-set.

**Session Object:**
```json
{
  "id": 10,
  "player_id": 1,
  "world_id": null,
  "score": 1500,
  "level_reached": 5,
  "enemies_killed": 42,
  "duration_seconds": 300,
  "completed": true,
  "started_at": "2026-03-12T14:00:00Z",
  "ended_at": "2026-03-12T14:05:00Z"
}
```

---

### 16.3 Leaderboard — `GET /api/game/leaderboard`  
**Auth:** Public

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `world_id` | int | — | Filter by world (optional) |
| `limit` | int | 10 | Number of entries to return |

**Response:**
```json
[
  {"rank": 1, "player_id": 1, "player_username": "PlayerX", "score": 5000, "level_reached": 10, "world_name": null, "created_at": "2026-03-12T14:00:00Z"},
  {"rank": 2, "player_id": 3, "player_username": "PlayerY", "score": 3200, "level_reached": 7, "world_name": null, "created_at": "2026-03-11T09:00:00Z"}
]
```

---

### 16.4 Stats — `GET /api/game/stats`  
**Auth:** Public

**Response:**
```json
{
  "total_players": 150,
  "total_sessions": 1024,
  "total_score": 450000,
  "average_score": 439.45,
  "highest_score": 9500
}
```

---

### 16.5 Worlds

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/game/worlds` | ❌ | List all game worlds/themes |
| `GET` | `/api/game/worlds/{id}` | ❌ | Get world by ID |

**World Types:** Classic Space, Neon City, Mystic Forest, Deep Ocean, Mars Desert

**World Object:**
```json
{"id": 1, "name": "CLASSIC", "description": "Classic Space", "background_color": "#000000", "difficulty_multiplier": 1.0}
```

---

### 16.6 Achievements

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| `GET` | `/api/game/achievements` | ❌ | List all available achievements |
| `GET` | `/api/game/achievements/{id}` | ❌ | Get achievement by ID |
| `GET` | `/api/game/players/{id}/achievements` | ✅ | Get player's unlocked achievements |

**Achievement Rarities:** Common, Rare, Epic, Legendary

**Achievement Object:**
```json
{"id": 1, "name": "First Blood", "description": "Kill your first enemy", "icon": "🎯", "points": 10, "rarity": "COMMON", "requirement_type": "enemies_killed", "requirement_value": 1}
```

**Player Achievement Object:**
```json
{"id": 1, "achievement": {"id": 1, "name": "First Blood", "description": "...", "icon": "🎯", "points": 10, "rarity": "COMMON", "requirement_type": "enemies_killed", "requirement_value": 1}, "earned_at": "2026-03-10T12:00:00Z", "unlocked_at": "2026-03-10T12:00:00Z"}
```

---

## 17. Profile Endpoints (`/api/game/profile/`)

### 17.1 Get My Profile — `GET /api/game/profile/me`  
**Auth:** Bearer token required

**Response:**
```json
{
  "id": 1,
  "username": "PlayerX",
  "email": "player@example.com",
  "total_score": 4500,
  "games_played": 12,
  "highest_level": 7,
  "avatar_url": "http://localhost:8000/media/avatars/player1.png",
  "bio": "I love space shooters!",
  "bg_color": "#000428",
  "show_scores": true,
  "created_at": "2026-01-15T10:30:00Z",
  "updated_at": "2026-03-12T14:00:00Z",
  "achievements_count": 5,
  "recent_achievements": [
    {"id": 1, "name": "First Blood", "icon": "🎯", "points": 10, "rarity": "COMMON", "unlocked_at": "2026-03-10T12:00:00Z"}
  ]
}
```

---

### 17.2 Update My Profile — `PATCH /api/game/profile/me`  
**Auth:** Bearer token required

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `username` | string | ❌ | — |
| `email` | string | ❌ | — |
| `bio` | string | ❌ | Max 500 chars, HTML tags stripped (XSS prevention) |
| `bg_color` | string | ❌ | Must match `^#[0-9a-fA-F]{6}$` |
| `show_scores` | bool | ❌ | — |

**Success:** Returns full profile object (same as GET).

---

### 17.3 Upload Avatar — `POST /api/game/profile/me/avatar`  
**Auth:** Bearer token required | **Content-Type:** `multipart/form-data`

| Field | Type | Required | Validation |
|-------|------|----------|------------|
| `avatar` | file | ✅ | Max 5MB, PNG/JPG/GIF/WebP, magic-byte verified |

**Validation chain:**
1. File must not be empty
2. File size ≤ 5 MB
3. Content-Type must be `image/jpeg`, `image/png`, `image/gif`, or `image/webp`
4. **Magic-byte check** — first 4-8 bytes must match the declared content type (prevents disguised malicious uploads)

**Success (200):**
```json
{"message": "Avatar uploaded successfully", "avatar_url": "http://localhost:8000/media/avatars/player1_abc.png"}
```

---

## 18. Security Features

### 18.1 JWT Pepper Authentication

| Feature | Details |
|---------|---------|
| Algorithm | HS256 |
| Pepper | HMAC-SHA256 applied to `user_id` via `JWT_PEPPER` setting |
| Token payload | `{user_id, username, player_id, peppered_id, is_staff, is_superuser, exp, iat, jti, type}` |
| Unique ID (`jti`) | UUID v4 — ensures each token is distinct even for the same user |
| Expiration | 24 hours from creation (`exp` claim) |

### 18.2 Token Blacklisting

| Feature | Details |
|---------|---------|
| Primary store | Redis (`REDIS_URL` env variable, key: `bl:<token>`) |
| Fallback | In-memory `set` (if Redis unavailable) |
| TTL | 48 hours (covers token lifetime + buffer) |
| Triggers | Logout (`POST /api/auth/logout`), Token refresh (`POST /api/auth/refresh`) |

### 18.3 Rate Limiting

| Bucket | Limit | Window | Endpoints |
|--------|-------|--------|-----------|
| `auth` | 30 requests | 60 seconds | `/api/auth/register`, `/api/auth/login` |
| `password` | 5 requests | 60 seconds | `/api/auth/change-password` |

> Rate limit is keyed by client IP (supports `X-Forwarded-For` for proxy setups). Returns `429 Too Many Requests` with `Retry-After: 60` header.

### 18.4 Security Facade Middleware

The `SecurityFacadeMiddleware` automatically processes all `/api/` responses:

| Feature | Details |
|---------|---------|
| Sensitive field stripping | Removes `password`, `password_hash`, `secret`, `internal_id`, `private_key`, `refresh_token`, `raw_token` from all JSON responses |
| Security headers | `X-Content-Type-Options: nosniff`, `X-Frame-Options: SAMEORIGIN`, `Referrer-Policy: strict-origin-when-cross-origin`, `Permissions-Policy: camera=(), microphone=(), geolocation=()` |

### 18.5 Input Validation Summary

| Input | Validation |
|-------|------------|
| Username | 3-30 chars, `[A-Za-z0-9_]` only |
| Email | RFC-style regex `^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$` |
| Password | Min 8 chars + Django validators + no password reuse |
| Bio | Max 500 chars, HTML tags stripped (`re.sub(r'<[^>]+>', '', bio)`) |
| `bg_color` | Hex color `^#[0-9a-fA-F]{6}$` |
| Avatar | ≤ 5MB, allowed MIME types + magic-byte verification |
| Search query | 2-100 chars, no `<>{}\\` characters |
| `level_reached` | Must be ≥ 1 |
| `score` | Must be ≥ 0 |

---

# Part C — Admin Operations

---

## 19. Django Admin Panel (`/admin/`)

*Access requires `is_staff: true` or `is_superuser: true` on the Django User account.*

The admin panel provides full **CRUD** (Create, Read, Update, Delete) access to all database models via a web interface.

### 19.1 Accessing the Admin Panel

| Method | How |
|--------|-----|
| **From Dashboard** | Settings page → click "Open Django Admin Panel" (opens `/admin/` in new tab) |
| **Direct URL** | Navigate to `http://localhost:8000/admin/` |
| **Login** | Use Django superuser credentials (same as API login username/password) |

### 19.2 Manageable Models

| Model | Admin Class | List Display Columns | Searchable Fields | Filters |
|-------|------------|---------------------|-------------------|---------|
| **User** | `UserAdmin` | username, email, first_name, last_name, is_staff, is_active, date_joined | username, email, first_name, last_name | is_staff, is_superuser, is_active, date_joined |
| **Player** | `PlayerAdmin` | username, email, total_score, games_played, highest_level, created_at | username, email | created_at, highest_level |
| **World** | `WorldAdmin` | name, difficulty_multiplier, background_color | name, description | name, difficulty_multiplier |
| **GameSession** | `GameSessionAdmin` | player, world, score, level_reached, completed, started_at, ended_at | player__username | world, completed, started_at |
| **Achievement** | `AchievementAdmin` | name, points, rarity, requirement_type, requirement_value | name, description | rarity, requirement_type |
| **PlayerAchievement** | `PlayerAchievementAdmin` | player, achievement, unlocked_at | player__username, achievement__name | achievement, unlocked_at |
| **Leaderboard** | `LeaderboardAdmin` | rank, player, score, period, world, updated_at | player__username | period, world, created_at |
| **PowerUp** | `PowerUpAdmin` | name, duration_seconds, rarity | name, description | name, rarity |

### 19.3 User Management (Admin Panel)

The custom `UserAdmin` provides **secure password handling**:

| Feature | Details |
|---------|---------|
| **Password display** | Password hashes are **never shown**. A message reads: "🔒 Password is securely hashed and cannot be displayed." |
| **Password change** | Via a dedicated "Change password" form linked from the user edit page |
| **Create new user** | Admin add form provides `username`, `password1`, `password2` fields |
| **Fieldsets** | User info → Personal info → Permissions (is_active, is_staff, is_superuser, groups) → Important dates |

### 19.4 Admin Actions — What You Can Do

| Action | Model | How | Notes |
|--------|-------|-----|-------|
| **View all players** | Player | Player list → sort/filter/search | Ordered by highest `total_score` |
| **Edit player stats** | Player | Click player → edit total_score, games_played, highest_level | `created_at` and `updated_at` are read-only |
| **View all sessions** | GameSession | Session list → filter by world/completed/date | Ordered by most recent `started_at` |
| **Delete a session** | GameSession | Select session(s) → Action: "Delete selected" | Bulk delete supported |
| **Manage worlds** | World | Add/edit/delete game worlds | 5 predefined types: Classic, Neon, Forest, Ocean, Desert |
| **Create achievements** | Achievement | Add new achievement with name, description, icon, points, rarity, requirement | 4 rarities: Common, Rare, Epic, Legendary |
| **Award achievements** | PlayerAchievement | Create new player-achievement link | `earned_at` and `unlocked_at` are auto-set |
| **Manage leaderboard** | Leaderboard | Edit ranks, scores, periods (Daily/Weekly/Monthly/All Time) | Unique constraint: player + period + world |
| **Configure power-ups** | PowerUp | Add/edit power-up types with duration and rarity | 6 types: Speed, Shield, Double/Triple Fire, Health, Score Multiplier |
| **Promote user to admin** | User | Edit user → check `is_staff` / `is_superuser` | Grants access to admin panel and Settings page |
| **Deactivate user** | User | Edit user → uncheck `is_active` | User can no longer log in (soft-ban) |
| **Reset user password** | User | Edit user → "Change password" link | Secure form, never exposes hash |

---

## 20. Management Commands

Django management commands available via the terminal inside the API container or server.

### 20.1 Delete Superuser — `python manage.py deletesuperuser`

Interactively delete a superuser account.

| Option | Description |
|--------|-------------|
| `python manage.py deletesuperuser` | Lists all superusers, prompts for selection |
| `python manage.py deletesuperuser <username>` | Directly targets a specific superuser |
| `python manage.py deletesuperuser <username> --no-input` | Deletes without confirmation prompt (CI/CD use) |

**Example:**
```bash
$ python manage.py deletesuperuser
Superusers:
  1) admin (id=1)
  2) melissa (id=2)
Enter number or username to delete (empty to abort): 2
✔ Superuser 'melissa' deleted.
```

### 20.2 Standard Django Commands

| Command | Description |
|---------|-------------|
| `python manage.py createsuperuser` | Create a new admin account interactively |
| `python manage.py migrate` | Apply database migrations |
| `python manage.py collectstatic` | Collect static files for Nginx |
| `python manage.py shell` | Open Django interactive shell |
| `python manage.py dbshell` | Open PostgreSQL shell directly |
| `python manage.py showmigrations` | Show migration status |
| `python manage.py flush` | Clear all database data (⚠️ destructive) |

---

## 21. Admin-Only API Access

Admins (`is_superuser: true` or `is_staff: true`) have the same API access as players, but with elevated privileges in the dashboard frontend:

| Feature | Endpoint / Location | Admin Benefit |
|---------|---------------------|--------------|
| **Platform Statistics** | `GET /api/game/stats` (dashboard Settings page) | View total players, sessions, scores, averages |
| **Django Admin Link** | Dashboard Settings → "Open Django Admin Panel" | One-click access to full database management |
| **Settings Page** | Dashboard side menu → ⚙️ Settings | Only visible to admin users (hidden for players/guests) |
| **All API endpoints** | Same as player | Full CRUD on own resources + admin panel for all resources |

> **Note:** The API itself does not have admin-exclusive endpoints — all authenticated endpoints apply ownership restrictions (`You can only update your own player/sessions`). Full admin CRUD is handled through the Django Admin Panel at `/admin/`.

---

# Appendix

---

## 22. Complete User Actions Matrix

| # | Action | Page | Role Required | API Call | Result |
|---|--------|------|--------------|----------|--------|
| 1 | View leaderboard (Top 3) | Home | All | `GET /api/game/leaderboard?limit=3` | Displays top 3 players with rank, name, score |
| 2 | View profile preview | Home | Player+ | `GET /api/game/profile/me` | Shows "Welcome, [username]!" |
| 3 | Click "Play now" | Home | All | — | Launches SI3LN game |
| 4 | Login | Login | Guest | `POST /api/auth/login` | JWT stored, UI updates to player mode |
| 5 | Sign up | Create Account | Guest | `POST /api/auth/register` | Account created, JWT stored, auto-login |
| 6 | Logout | Any (top bar) | Player+ | `POST /api/auth/logout` | Token blacklisted, UI reverts to guest mode |
| 7 | View profile | Profile | Player+ | `GET /api/game/profile/me` | Full profile: avatar, stats, bio, scores |
| 8 | View best scores | Profile | Player+ | `GET /api/game/sessions?player_id=X` | Top 5 scores displayed with level |
| 9 | Edit profile (bio) | Profile Modal | Player+ | `PATCH /api/game/profile/me` | Bio updated (max 500 chars, XSS-safe) |
| 10 | Change background color | Profile Modal | Player+ | `PATCH /api/game/profile/me` | Profile background color changes |
| 11 | Upload avatar | Profile Modal | Player+ | `POST /api/game/profile/me/avatar` | Avatar updated (≤5MB, magic-byte validated) |
| 12 | Toggle score privacy | Profile Modal | Player+ | `PATCH /api/game/profile/me` | Scores hidden/shown on public profile |
| 13 | Browse games | Games | All | — | View SI3LN card + 3 "Coming Soon" cards |
| 14 | Launch SI3LN | Games / Home | All | — | Game loads in fullscreen iframe |
| 15 | Play as guest | Game Play | Guest | — | Game plays, scores NOT saved, info banner shown |
| 16 | Play as player | Game Play | Player+ | `POST /api/game/sessions` | Session created, scores saved on exit |
| 17 | Exit game | Game Play | All | `PATCH /api/game/sessions/{id}` | Returns to Games page, session ended |
| 18 | Toggle fullscreen | Game Play | All | — | Browser fullscreen API toggled |
| 19 | Navigate games (◀▶) | Game Play | All | — | Cycles through game catalog |
| 20 | View game tutorial | Help | All | — | Controls, scoring, tips displayed |
| 21 | Report a player | Help | All | — | Downloads formatted `.txt` report file |
| 22 | Report a bug | Help | All | — | Downloads formatted `.txt` bug report (auto-detects browser) |
| 23 | View support info | Help | All | — | Displays support/donation options |
| 24 | Search (players) | Top Bar | All | `GET /api/game/players` | Matching player results in dropdown |
| 25 | Search (games) | Top Bar | All | Local search | Matching game results in dropdown |
| 26 | Search (help articles) | Top Bar | All | Local search | Matching help article results in dropdown |
| 27 | Switch language (EN/FR) | Top Bar | All | — | All UI text translates immediately |
| 28 | Change password | API | Player+ | `POST /api/auth/change-password` | Password updated (rate limited 5/60s) |
| 29 | Update account info | API | Player+ | `PATCH /api/auth/update-account` | Email/name updated |
| 30 | Refresh token | API | Player+ | `POST /api/auth/refresh` | New JWT issued, old one blacklisted |
| 31 | View platform stats | Settings | Admin | `GET /api/game/stats` | Total players, sessions, scores, averages |
| 32 | Open Django admin | Settings | Admin | — | Opens `/admin/` in new tab |
| 33 | Manage all users | Admin Panel | Admin | `/admin/` UI | Full CRUD on all User accounts |
| 34 | Manage all players | Admin Panel | Admin | `/admin/` UI | Edit scores, levels, games_played |
| 35 | Manage sessions | Admin Panel | Admin | `/admin/` UI | View/delete any game session |
| 36 | Manage worlds | Admin Panel | Admin | `/admin/` UI | Add/edit/delete game worlds |
| 37 | Manage achievements | Admin Panel | Admin | `/admin/` UI | Create/edit achievements & award to players |
| 38 | Manage leaderboard | Admin Panel | Admin | `/admin/` UI | Edit ranks, scores by period |
| 39 | Configure power-ups | Admin Panel | Admin | `/admin/` UI | Set duration, rarity for power-up types |
| 40 | Promote/ban users | Admin Panel | Admin | `/admin/` UI | Toggle is_staff, is_superuser, is_active |
| 41 | Delete superuser | Terminal | Admin | `manage.py deletesuperuser` | Remove superuser account |
| 42 | View about info | About | All | — | Team credits, version number |

---

## 23. Complete API Endpoints Matrix

| # | Method | Endpoint | Auth | Rate Limit | Description |
|---|--------|----------|------|------------|-------------|
| 1 | `POST` | `/api/auth/register` | ❌ | 30/60s | Register new user + player |
| 2 | `POST` | `/api/auth/login` | ❌ | 30/60s | Login, returns JWT |
| 3 | `POST` | `/api/auth/logout` | ✅ | — | Logout, blacklists token |
| 4 | `POST` | `/api/auth/refresh` | ✅ | — | Refresh JWT (token rotation) |
| 5 | `GET` | `/api/auth/me` | ✅ | — | Get current user info + role |
| 6 | `POST` | `/api/auth/change-password` | ✅ | 5/60s | Change password |
| 7 | `PATCH` | `/api/auth/update-account` | ✅ | — | Update email/name |
| 8 | `GET` | `/api/game/players` | ✅ | — | List all players |
| 9 | `POST` | `/api/game/players` | ❌ | — | Create player |
| 10 | `GET` | `/api/game/players/{id}` | ✅ | — | Get player by ID |
| 11 | `PUT` | `/api/game/players/{id}` | ✅ | — | Update own player |
| 12 | `DELETE` | `/api/game/players/{id}` | ✅ | — | Delete own player |
| 13 | `GET` | `/api/game/sessions` | ✅ | — | List sessions (filterable) |
| 14 | `POST` | `/api/game/sessions` | ✅ | — | Start new game session |
| 15 | `GET` | `/api/game/sessions/{id}` | ✅ | — | Get session by ID |
| 16 | `PATCH` | `/api/game/sessions/{id}` | ✅ | — | End/update own session |
| 17 | `DELETE` | `/api/game/sessions/{id}` | ✅ | — | Delete own session |
| 18 | `GET` | `/api/game/leaderboard` | ❌ | — | Get leaderboard |
| 19 | `GET` | `/api/game/stats` | ❌ | — | Get platform statistics |
| 20 | `GET` | `/api/game/worlds` | ❌ | — | List game worlds |
| 21 | `GET` | `/api/game/worlds/{id}` | ❌ | — | Get world by ID |
| 22 | `GET` | `/api/game/achievements` | ❌ | — | List all achievements |
| 23 | `GET` | `/api/game/achievements/{id}` | ❌ | — | Get achievement by ID |
| 24 | `GET` | `/api/game/players/{id}/achievements` | ✅ | — | Get player's achievements |
| 25 | `GET` | `/api/game/profile/me` | ✅ | — | Get own enhanced profile |
| 26 | `PATCH` | `/api/game/profile/me` | ✅ | — | Update own profile |
| 27 | `POST` | `/api/game/profile/me/avatar` | ✅ | — | Upload avatar image |

---

*Document generated for ARCAD3X v1.0.0 — SI3LN Portfolio Project, Stage 4 MVP.*
