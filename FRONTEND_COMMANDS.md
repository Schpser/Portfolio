# ARCAD3X Dashboard — Frontend Commands & Actions Reference

> Complete reference of every user action available in the ARCAD3X web dashboard, mapped to the underlying JS function, API call, and expected result.

---

## 📑 Table of Contents

- [ARCAD3X Dashboard — Frontend Commands \& Actions Reference](#arcad3x-dashboard--frontend-commands--actions-reference)
  - [📑 Table of Contents](#-table-of-contents)
  - [1. Navigation](#1-navigation)
  - [2. Authentication](#2-authentication)
    - [2.1 Login](#21-login)
    - [2.2 Sign Up](#22-sign-up)
    - [2.3 Logout](#23-logout)
    - [2.4 Auto Auth Check](#24-auto-auth-check)
  - [3. Profile Management](#3-profile-management)
  - [4. Games](#4-games)
    - [4.1 Games Catalog](#41-games-catalog)
    - [4.2 Game Play](#42-game-play)
  - [5. Help \& Support](#5-help--support)
  - [6. Search](#6-search)
  - [7. Language / i18n](#7-language--i18n)
  - [8. Admin (Settings)](#8-admin-settings)
  - [9. Mobile Gestures](#9-mobile-gestures)
  - [10. Home Page Data Loading](#10-home-page-data-loading)
  - [11. Role-Based UI Visibility](#11-role-based-ui-visibility)

---

## 1. Navigation

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 1.1 | Click ▶ play triangle | `.menu-trigger` | `initMenuHandlers()` | — | Opens/closes the side navigation menu dropdown |
| 1.2 | Click menu item | `.menu-item[data-page]` | `navigateTo(pageName)` | — | Hides all pages, shows the target page, loads page-specific data |
| 1.3 | Click ARCAD3X logo (menu) | `.menu-logo` | `navigateTo('home')` | — | Navigates to the home page and closes the menu |
| 1.4 | Click ARCAD3X title (top bar) | `.top-bar-title a[data-page]` | `navigateTo('home')` | — | Navigates to the home page |
| 1.5 | Click page logo (on sub-pages) | `.page-logo[data-page]` | `navigateTo('home')` | — | Returns to the home page from any sub-page |
| 1.6 | Click outside menu | `document` click | Event listener | — | Closes the menu dropdown if open |

---

## 2. Authentication

### 2.1 Login

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 2.1.1 | Click "Login / Sign Up" | `#topLoginLink` | `navigateTo('login')` | — | Shows the login page |
| 2.1.2 | Submit login form | `#loginForm` submit | `AuthManager.handleLogin()` | `POST /api/auth/login` `{"username", "password"}` | On success: JWT stored in localStorage, UI updates to show username, redirects to home. On failure: alert "Login failed" |
| 2.1.3 | Click "Create an account" | `#signupLink` | `navigateTo('create-account')` | — | Shows the signup/registration page |
| 2.1.4 | Click "Forgot password?" | `#forgotPasswordLink` | Alert | — | Shows "Password recovery feature coming soon!" |

### 2.2 Sign Up

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 2.2.1 | Type username | `#signupPseudo` | `AuthManager.validatePseudo(input)` | — | Live validation: ≥3 chars, alphanumeric + underscore only, profanity check. Shows ✓/❌ feedback |
| 2.2.2 | Type email | `#signupEmail` | `AuthManager.validateEmail(input)` | — | Live validation: standard email format. Input border turns green/red |
| 2.2.3 | Type password | `#signupPassword` | `AuthManager.validatePassword(input)` | — | Live validation with 3 requirement indicators: ≥8 chars, ≥1 number, ≥1 uppercase. Each shows ✓/✗ |
| 2.2.4 | Type confirm password | `#signupConfirmPassword` | `AuthManager.matchPasswords(pwd, confirm)` | — | Live check: shows "passwords match" ✓ or "don't match" ❌ |
| 2.2.5 | Check "Accept terms" | `#acceptTerms` | `AuthManager.validateForm()` | — | Enables/disables the submit button based on all validations passing |
| 2.2.6 | Submit signup form | `#signupForm` submit | `AuthManager.handleSignup()` | `POST /api/auth/register` `{"username", "email", "password"}` | On success: JWT stored, redirects to home, auth UI updated. On failure: alert "Registration failed" |
| 2.2.7 | Click "Already have an account?" | `#loginRedirect` | `navigateTo('login')` | — | Switches to the login page |

### 2.3 Logout

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 2.3.1 | Click "Logout" button | `#logoutBtn` | `AppManager.logout()` → `facade.logout()` | — (client-side only) | Removes JWT from localStorage (all 3 keys), updates UI to guest state, navigates to home |

### 2.4 Auto Auth Check

| # | Trigger | JS Function | API Call | Result |
|---|---------|-------------|----------|--------|
| 2.4.1 | Page load | `AppManager.checkAuth()` | — | Parses JWT `exp` claim. If expired: clears tokens, shows guest UI. If valid: shows logged-in UI with username |
| 2.4.2 | Any API returns 401 | `APIClient._handleExpiredToken()` | — | Clears all token keys from localStorage, updates auth UI to guest state (runs once per page load via `_tokenCleared` flag) |

---

## 3. Profile Management

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 3.1 | Navigate to Profile | Menu item `data-page="profile"` | `ProfileManager.loadProfile()` | `GET /api/game/profile/me` | Loads and displays: avatar, username, bio, stats (total score, games played, highest level, achievements count), top 5 best scores |
| 3.2 | View best scores | `#bestScoresList` | `ProfileManager.loadBestScores(playerId)` | `GET /api/game/sessions?player_id={id}` | Fetches all sessions, sorts by score descending, displays top 5 with rank/score/level |
| 3.3 | Click "Edit Profile" | `#editProfileBtn` | `ProfileManager.openEditModal()` | — | Opens the edit modal, pre-fills current bio/color/toggle values |
| 3.4 | Select background color | `.color-option` buttons | Click handler in `openEditModal()` | — | Highlights the selected color option (6 dark-theme colors available) |
| 3.5 | Choose profile picture | `#avatarUpload` file input | `openEditModal()` → FileReader | — | Client-side: validates ≤5MB, shows preview. File stored in `_pendingAvatarFile` for upload on save |
| 3.6 | Edit bio text | `#editBio` textarea | `oninput` → char counter | — | Updates character counter (max 500 chars) |
| 3.7 | Toggle "Show scores" | `#showScoresToggle` | — | — | Toggles privacy setting (saved on "Save Changes") |
| 3.8 | Click "Save Changes" | `#saveProfileBtn` | `ProfileManager.saveProfileChanges()` | 1. `POST /api/game/profile/me/avatar` (if new file) 2. `PATCH /api/game/profile/me` `{"bio", "bg_color", "show_scores"}` | Uploads avatar if changed, updates profile fields, closes modal, reloads profile page |
| 3.9 | Click "Cancel" / ✕ | `.cancel-btn` / `.modal-close` | — | — | Closes the edit modal without saving |
| 3.10 | Guest visits profile | — | `ProfileManager.showPublicProfile('guest')` | — | Shows "Guest" as name, "User not logged in" as bio, hides edit button, shows "Login to see scores" |

---

## 4. Games

### 4.1 Games Catalog

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 4.1.1 | Navigate to Games page | Menu item `data-page="games"` | `navigateTo('games')` | — | Shows the games grid: SI3LN card (playable) + 3 "Coming Soon" cards |
| 4.1.2 | Click SI3LN game card | `#si3lnGameCard` | `GamesManager.launchGame('si3ln')` | — | Navigates to game play page (see 4.2) |
| 4.1.3 | Click "Coming Soon" card | `.game-card.coming-soon` | — | — | No action (cards are non-interactive) |
| 4.1.4 | Click "Play now →" (home) | `#playGameLink` | `GamesManager.launchGame('si3ln')` | — | Launches SI3LN directly from the home page |

### 4.2 Game Play

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 4.2.1 | Game launches | `#game-play-page` | `GamesManager.launchGame(gameId)` | `POST /api/game/sessions` `{"player_id"}` (if logged in) | Navigates to fullscreen game page, applies game background color/image, shows loading overlay (1.2s), creates game session via API, loads game iframe |
| 4.2.2 | Guest plays | — | `GamesManager.showGuestInfo()` | — | Shows a banner: "Playing as guest — Your score won't be saved. Login to save your progress!" (auto-hides after 8s) |
| 4.2.3 | Click "Fullscreen" | `#fullscreenBtn` | `GamesManager.toggleFullscreen()` | — | Enters browser fullscreen mode (via Fullscreen API). Button icon changes to ⊡ "Exit Fullscreen" |
| 4.2.4 | Click "Exit Fullscreen" | `#fullscreenBtn` | `GamesManager.toggleFullscreen()` | — | Exits browser fullscreen mode. Button icon reverts to ⛶ "Fullscreen" |
| 4.2.5 | Press F11 or Esc | Browser | `fullscreenchange` event | — | UI auto-detects fullscreen state change and updates button accordingly |
| 4.2.6 | Click "◀ Previous" | `#prevGameBtn` | `GamesManager.navigateGame('prev')` | — | Cycles to the previous game in the catalog (alerts "Coming Soon!" for unavailable games) |
| 4.2.7 | Click "Next ▶" | `#nextGameBtn` | `GamesManager.navigateGame('next')` | — | Cycles to the next game in the catalog |
| 4.2.8 | Click "⬅ Exit Game" | `#exitGameBtn` | Event handler | `PATCH /api/game/sessions/{id}` (if session exists) | Ends the game session (score 0, level 1), resets iframe to `about:blank`, clears background, navigates back to Games page |

---

## 5. Help & Support

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 5.1 | Navigate to Help | Menu item `data-page="help"` | `navigateTo('help')` | — | Shows the help menu with 4 cards: Tutorials, Report Player, Report Bug, Support Us |
| 5.2 | Click "View Tutorials" | `.help-btn[data-help="games"]` | `HelpManager.showHelpDetail('games')` | — | Shows SI3LN tutorial: controls (Arrow/WASD, Space, P, Esc), gameplay tips, scoring table (Small: 10pts, Medium: 20pts, Large: 30pts, UFO: 50-300pts, Level bonus: 1000pts) |
| 5.3 | Click "Submit Report" | `.help-btn[data-help="report"]` | `HelpManager.showHelpDetail('report')` | — | Shows the Report Player form: username, reason (dropdown: offensive username/description, harassment, cheating, spam, other), details, evidence |
| 5.4 | Submit player report | `#reportPlayerForm` submit | `HelpManager._downloadReportFile(form)` | — | Generates a formatted `.txt` file with timestamp, reporter name, reason, details, evidence. Downloads as `report-{username}-{timestamp}.txt`. Alert instructs to email to support@arcad3x.com |
| 5.5 | Click "Report Bug" | `.help-btn[data-help="bug"]` | `HelpManager.showHelpDetail('bug')` | — | Shows Bug Report form: title, category (gameplay/UI/login/profile/graphics/audio/performance/other), description, steps to reproduce, browser info (auto-detected) |
| 5.6 | Submit bug report | `#bugReportForm` submit | `HelpManager._downloadBugReportFile(form)` | — | Generates a formatted `.txt` file with all fields + auto-detected browser/OS info. Downloads as `bug-report-{timestamp}.txt` |
| 5.7 | Click "Support Project" | `.help-btn[data-help="support"]` | `HelpManager.showHelpDetail('support')` | — | Shows support page: ways to help (play, feedback, social media), payment notice (coming soon), team credits |
| 5.8 | Click "← Back to Help Menu" | `#helpBackBtn` | `HelpManager.showHelpMenu()` | — | Returns to the 4-card help menu |

---

## 6. Search

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 6.1 | Type in search bar (≥2 chars) | `#globalSearch` | `SearchService.debounceSearch(query, callback)` | `GET /api/game/players` (for player search) | After 300ms debounce: searches across games catalog, help articles (local), and players (API). Shows results dropdown with icons (🎮 game, ❓ help, 👤 player) |
| 6.2 | Click search button 🔍 | `.search-button` | `SearchService.search(query)` | `GET /api/game/players` | Immediate search (no debounce). Same results as 6.1 |
| 6.3 | Press Enter in search | `#globalSearch` keydown | `.search-button.click()` | Same as 6.2 | Triggers search button click |
| 6.4 | Press Escape in search | `#globalSearch` keydown | `_hideSearchResults()` | — | Closes results dropdown, blurs search input |
| 6.5 | Click a game result | `.search-result-item[data-type="game"]` | `GamesManager.launchGame(id)` | — | Launches the game (if playable) or navigates to Games page |
| 6.6 | Click a help result | `.search-result-item[data-type="help"]` | `navigateTo('help')` → `HelpManager.showHelpDetail(section)` | — | Navigates to help page and opens the specific help section |
| 6.7 | Click a player result | `.search-result-item[data-type="player"]` | `navigateTo('profile')` | — | Navigates to the profile page |
| 6.8 | Click outside search | `document` click | `_hideSearchResults()` | — | Closes the search results dropdown |

**Search internals:**
- Results are cached for 60 seconds (max 100 entries)
- Max 20 results returned
- Minimum query length: 2 characters
- Games and help articles are searched locally (no API call)
- Players are searched via API (`GET /api/game/players`, filtered client-side by username match)

---

## 7. Language / i18n

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 7.1 | Click language button | `#languageBtn` | Click handler | — | Opens language dropdown (🇬🇧 English, 🇫🇷 Français) |
| 7.2 | Select a language | `.language-option[data-lang]` | `window.i18n.setLanguage(lang)` | — | Updates all elements with `data-i18n` attributes to the selected language. Saves choice to `localStorage('language')`. Updates display code (EN/FR) in top bar |
| 7.3 | Click outside dropdown | `document` click | — | — | Closes the language dropdown |

**i18n coverage:** Navigation labels, top bar, home page, login/signup forms, profile page, games page, help page, all button labels, placeholder texts, validation messages.

---

## 8. Admin (Settings)

| # | User Action | UI Element | JS Function | API Call | Result |
|---|-------------|-----------|-------------|----------|--------|
| 8.1 | Navigate to Settings | Menu item `data-page="settings"` (visible only to admins) | `AppManager.loadSettings()` | `GET /api/game/stats` | **Admin only** — If user is not admin, redirects to home. Otherwise loads: link to Django admin panel (`/admin/`), platform statistics (total players, sessions, highest score, average score) |
| 8.2 | Click "Open Django Admin Panel" | Link to `/admin/` | — | — | Opens Django admin in a new tab (full model CRUD for Players, Sessions, Worlds, Achievements) |

**Role detection:** Parsed from JWT payload (`is_superuser` or `is_staff` → admin role). The `.admin-only` CSS class controls visibility of the Settings menu item.

---

## 9. Mobile Gestures

| # | User Action | Gesture | JS Function | Result |
|---|-------------|---------|-------------|--------|
| 9.1 | Swipe right | Touch: left → right (≥50px) | `MobileManager.onSwipeRight()` | Opens the side navigation menu |
| 9.2 | Swipe left | Touch: right → left (≥50px) | `MobileManager.onSwipeLeft()` | Closes the side navigation menu |
| 9.3 | Double-tap on menu | Two taps within 300ms | `MobileManager.onDoubleTap(e)` | Closes the menu if it's open |
| 9.4 | Long press on card | Touch held ≥500ms on `.game-card`, `.help-card`, `.card` | `MobileManager.onLongPress(element)` | Triggers haptic vibration (50ms) if device supports `navigator.vibrate` |
| 9.5 | Tap any interactive element | Touch on buttons, links, cards | `addTouchFeedback()` | Adds `.touch-active` CSS class for 150ms visual feedback |
| 9.6 | Pull to refresh | — | `initPullToRefresh()` | Refreshes the current page data |

**Mobile detection:** User-Agent regex check for Android, iPhone, iPad, etc. Adds `mobile-device` class to `<body>` for CSS targeting.

---

## 10. Home Page Data Loading

| # | Trigger | JS Function | API Call | Result |
|---|---------|-------------|----------|--------|
| 10.1 | Page load (always) | `AppManager.loadHomeData()` | `GET /api/game/leaderboard?limit=3` | Loads and displays top 3 leaderboard entries (rank, username, score) in `#leaderboard` div |
| 10.2 | Page load (if logged in) | `AppManager.loadHomeData()` | `GET /api/game/profile/me` | Shows "Welcome, {username}!" in `#profile` div |
| 10.3 | Page load (if guest) | — | — | Shows "Please log in" in `#profile` div |

---

## 11. Role-Based UI Visibility

The dashboard dynamically shows/hides elements based on the user's JWT role:

| CSS Class | Guest | Player | Admin | Example Elements |
|-----------|-------|--------|-------|-----------------|
| `.guest-only` | ✅ Visible | ❌ Hidden | ❌ Hidden | "Login / Sign Up" link, guest info banner |
| `.player-only` / `.auth-required` | ❌ Hidden | ✅ Visible | ✅ Visible | Profile menu item, edit profile button |
| `.admin-only` | ❌ Hidden | ❌ Hidden | ✅ Visible | Settings menu item |

Applied by: `AuthManager.applyRoleUI(role)` — called on login, logout, and page load.

---

*Document generated from ARCAD3X web_dashboard source code — March 2026*
