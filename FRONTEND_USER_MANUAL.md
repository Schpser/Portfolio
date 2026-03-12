# 🕹️ ARCAD3X — Frontend User Manual

**Platform:** ARCAD3X Web Dashboard (SPA)  
**Game:** SI3LN — Space Invaders III Last Night  
**Version:** 1.0.0  
**Last updated:** March 12, 2026

---

## 📑 Table of Contents

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

## Summary: Complete User Actions Matrix

| # | Action | Page | Role Required | API Call | Result |
|---|--------|------|--------------|----------|--------|
| 1 | View leaderboard (Top 3) | Home | All | `GET /api/game/leaderboard?limit=3` | Displays top 3 players with rank, name, score |
| 2 | View profile preview | Home | Player+ | `GET /api/game/profile/me` | Shows "Welcome, [username]!" |
| 3 | Click "Play now" | Home | All | — | Launches SI3LN game |
| 4 | Login | Login | Guest | `POST /api/auth/login` | JWT stored, UI updates to player mode |
| 5 | Sign up | Create Account | Guest | `POST /api/auth/register` | Account created, JWT stored, auto-login |
| 6 | Logout | Any (top bar) | Player+ | Local only (token cleared) | UI reverts to guest mode |
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
| 28 | View platform stats | Settings | Admin | `GET /api/game/stats` | Total players, sessions, scores, averages |
| 29 | Open Django admin | Settings | Admin | — | Opens `/admin/` in new tab |
| 30 | View about info | About | All | — | Team credits, version number |

---

*Document generated for ARCAD3X v1.0.0 — SI3LN Portfolio Project, Stage 4 MVP.*
