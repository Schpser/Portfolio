# 🎮 Portfolio Project - SI3LN: Arcade Analytics

**Team:** [Hugo Ramos](https://github.com/hugou74130) & [Melissa Sbibih](https://github.com/Schpser)
*This document follows the 5-stage project curriculum structure.*

---

## 📑 Table of Contents

1. **[PART 1: Idea Development](#part-1-idea-development-completed-)** (Stage 1 - Completed) ✅
   - [Team Formation & Roles](#0-team-formation--roles-)
   - [Brainstorming & Idea Evaluation](#1-brainstorming--idea-evaluation-)
   - [Selected MVP: Definition & Refinement](#2-selected-mvp-definition--refinement-)

2. **[PART 2: Project Planning](#part-2-project-planning-in-progress-)** (Stage 2 - Completed) ✅
   - [Executive Summary & MVP Blueprint](#3-executive-summary--mvp-blueprint-)
   - [High-Level Timeline & Curriculum Alignment](#4-high-level-timeline--curriculum-alignment-)

3. **[PART 3: Technical Documentation](#part-3-technical-documentation-to-come-)** (Stage 3 - Completed) ✅
   - [Core Technical Specifications](#5-core-technical-specifications) (Architecture, APIs, SCM/QA)

4. **[PART 4: MVP Development](#part-4-mvp-development-to-come-)** (Stage 4 - To Come) ⚪
   - Development Sprint Logs & Testing

5. **[PART 5: Project Closure](#part-5-project-closure-to-come-)** (Stage 5 - To Come) ⚪
   - Final Report & Demo

---

## PART 1: Idea Development (Completed) ✅

### 0. Team Formation & Roles 👥

| Member | Primary Role |
|--------|-------------|
| **Hugo Ramos** | 🎨 Full-Stack Game Developer with a focus on visual craftsmanship, gameplay feel, and performance optimization. |
| **Melissa Sbibih** | ⚙️ Full-Stack Game Developer with a focus on system architecture, data flow, and clean documentation. |

#### 💡 Our Core Work Principle: Full Shared Ownership

We are a true pair. We reject the classic frontend/backend split. Our strength is in tackling every challenge together, from the low-level C++ game loop to the Python API logic and the JavaScript dashboard. This ensures deep mutual understanding of the entire codebase, superior code quality through constant review, and resilient problem-solving.

**🤝 Collaboration Model:**  
We will adopt a "Feature-Pairing" model. For each main feature (e.g., "Score submission"), we will collaboratively design, implement, and test it together, ensuring shared understanding and code ownership.

**💬 Communication:**  
- Daily sync sessions (Discord)
- Centralized documentation on Notion

**🎯 Decision-making:**  
- Consensus for creative choices
- Final technical decision by the domain expert (Hugo for the game, Melissa for the API)

**🗂️ Practical Collaboration Framework**

To implement this principle efficiently within the project structure (game_client_c, backend_api_python, web_dashboard), we use a "Feature-Driven Pairing" workflow:

Feature Kickoff: For each new feature (e.g., "Player Score Submission"), we design the solution together, defining:

The gameplay logic (C++)

The data structure and API endpoint (Python)

The dashboard visualization (JS)

Development Cycle: We then work side-by-side, on the same task, whether physically or via screen-sharing:

Pair-Programming: One drives (writes code), the other navigates (reviews, researches, plans next steps). We switch roles frequently.

Split-Research & Merge: We sometimes research specific sub-problems separately (e.g., Hugo checks a graphics library, Melissa checks an API design pattern) then immediately reconvene to integrate the findings.

Validation & Integration: We test and integrate the feature together, ensuring it works seamlessly across all three parts of our stack (Game Client → API → Dashboard).

**🔧 Tools & Rituals for a Unified Workflow**

Daily Co-Working Sessions: Blocked time for synchronized development.

Shared Decision Log: A simple document where we record key technical decisions we made together.

Single Pull Request Policy: Any code merged to the main branch must be reviewed and approved by both members.

#### 🤝 Stakeholders

| Stakeholder | Role | Impact | Involvement |
|------------|------|--------|-------------|
| 📊 **Project Evaluators** | Assessors of technical & methodological mastery | 🔴 Critical | 🟡 Medium (Reviews at each stage) |
| 🎮 **Future Players** | End-users of the game and dashboard | 🔴 High | 🟢 Low (Implicit via our UX choices) |
| 👨‍💻 **Development Team (Us)** | Creators, Maintainers, Testers | 🔴 Critical | 🔴 Very High |

---

### 1. Brainstorming & Idea Evaluation 💡

We used the **SCAMPER framework** to evolve the pre-structured project (SI3LN) and generate value-added ideas.

Idea (Based on SI3LN structure)	SCAMPER Trigger	Description	Feasibility (1-5)	Impact (1-5)	Score	Verdict
| **Idea (Based on SI3LN structure)** | **SCAMPER Trigger** | **Description** | **Feasibility (1-5)** | **Impact (1-5)** | **Score** | **Verdict** |
|-------------------------------------|---------------------|-----------------|----------------------|------------------|-----------|-------------|
| A. Classic Arcade with Basic Dashboard | - | Simple game + dashboard displaying only the score. | 5 | 2 | 10 | ❌ Rejected - Too basic, doesn't showcase the stack. |
| B. Multiplayer Arcade with Live Leaderboard | Combine (Game + Real-time) | Game where multiple clients connect for a real-time session. Dashboard with live leaderboard. | 2 | 5 | 10 | ❌ Rejected - Network complexity too high for the allocated time. |
| C. Data-Rich Arcade with Analytics Dashboard (Selected) | Modify & Put to another use | Event-rich game (kills, bonuses, damage) sending structured data. Dashboard with detailed statistics, charts and insights. | 4 | 5 | 20 | ✅ SELECTED |

### 2. Selected MVP: Definition & Refinement 🏗️

🎯 MVP Title: SI3LN: Arcade Analytics - A cohesive gaming ecosystem.

Aspect	Definition

**Problem** | Classic arcade games offer an ephemeral experience. Players have no insight into their performance, trends, or history.

**Solution** | Coupling an engaging gaming experience (`game_client_c`) with an analytical dashboard (`web_dashboard`) via a robust API (`backend_api_python`), transforming a gaming session into actionable data.

**Target Audience** | 1. Casual Gamers (25-35 years old). 2. Data enthusiasts who enjoy tracking their progress.

**Application Type** | Desktop-based Gaming Ecosystem: Heavy client in C++ (for performance) + responsive web dashboard (for accessibility).

**Why This Idea?** | 1. Perfect alignment with the imposed folder structure. 2. Demonstrates a complete data flow (Client → API → DB → Frontend). 3. Showcases our complementary skills. 4. Scope perfectly manageable by a team of 2.

**✅ MVP SMART Objectives:**

**Specific:** Deliver a game with 1 ship type, 2 enemies, 1 bonus. Dashboard with 3 charts (score over time, event heatmap, success rate).

**Measurable:** The API exposes 3 functional REST endpoints. The C++ client sends events to POST `/api/game/event`. The dashboard makes at least 2 GET calls.

**Achievable:** Based on known technologies (C++, Python/Flask, JS). Pre-existing structure.

**Relevant:** Covers targeted RNCP5 skills: project management, full-stack development, system integration.

**Time-bound:** Development over 8 weeks, according to the timeline below.

**🔭 Project Scope:**

| **In-Scope (MVP Core)** | **Out-of-Scope (V2.0+)** |
|-------------------------|--------------------------|
| Basic 2D game with shooting, collision, score mechanics. | 3D game or real-time multiplayer. |
| Python REST API ingesting scores and events. | Complex authentication system (OAuth). |
| Web dashboard with Chart.js/Plotly visualizations. | Machine Learning for predictive analysis. |
| SQLite database for persistence. | Cloud deployment and advanced CI/CD. |
| Centralized asset management (`assets_shared/`). | Client porting to other platforms. |

**🚨 Risks & Mitigation:**

| **Risk** | **Probability** | **Impact** | **Mitigation Strategy** |
|----------|----------------|-----------|------------------------|
| Client-API Communication | Medium | High | Define an API contract (OpenAPI) from Stage 2. Use simple JSON. |
| Unequal Workload | High | Medium | Mandatory Feature-Pairing. Daily sync points. Shared Trello backlog. |
| C++ Client Performance | Low | High | Rapid game loop prototyping from week 1. Profiling with Valgrind if needed. |
| Visual Consistency | Medium | Medium | Hugo is responsible for assets. Color/UI style guide defined before dev. |

### 3. Executive Summary & MVP Blueprint 👑

| 🔑 Key Element | Description |
|----------------|-------------|
| **🔭 Vision** | Make gaming performance measurable, visible, and engaging through data |
| **🛠️ Tech Stack** | `C++` (Client) \| `Python/Flask` (API) \| `SQLite` (DB) \| `HTML/CSS/JS` (Dashboard) \| `Git/GitHub` (SCM) |
| **💎 Value Proposition** | 1 product, 2 facets: the thrill of arcade gaming + the introspection of analytics |
| **🎯 Expected Impact** | Transform a "high score" into a detailed progression story |

---

### 4. High-Level Timeline & Curriculum Alignment 🗺️

This timeline follows the 5-stage curriculum structure.

| Stage | Estimated Period | Primary Objective | Key Deliverables | Status |
|-------|------------------|-------------------|------------------|--------|
| **Stage 1:** Idea Development | D-1 to D-7 | Define the concept, team, and feasibility | This document (Part 1) | ✅ **COMPLETED** |
| **Stage 2:** Project Planning | D-7 to D-14 | Plan execution and technical architecture | Detailed timeline, API contract, Data model | ✅ **COMPLETED** |
| **Stage 3:** Technical Documentation | Weeks 1-2 | Formalize all technical specifications | Architecture diagrams, User Stories, SCM/QA plans | ✅ **COMPLETED** |
| **Stage 4:** MVP Development | Weeks 3-6 | Build, integrate, and test modules | Functional code (Client + API + Dashboard), Tests | ⚪ **TO COME** |
| **Stage 5:** Project Closure | Weeks 7-8 | Finalize, document, and present the project | Final report, Demo video, Optimizations | ⚪ **TO COME** |

#### 📅 Detailed Sprint Plan (Weeks 3-6 - Stage 4)

| Sprint | Focus | Key Activities |
|--------|-------|----------------|
| **🔧 Sprint 1** (Setup) | Environment & Foundation | Dev environment setup, base architecture, API contract validated |
| **🎮 Sprint 2** (Core Gameplay) | Game Engine | C++ game engine (movement, shooting, basic collisions) |
| **🔌 Sprint 3** (Data Pipeline) | API Integration | Operational Python API endpoints, event sending from C++ client |
| **📊 Sprint 4** (Dashboard) | Frontend Development | Static web pages, initial charts with mocked data |
| **🔗 Sprint 5** (Integration) | End-to-End Connection | Complete C++→API→DB→Dashboard connection, integration tests |
| **✨ Sprint 6** (Polish) | Finalization | Visual improvements (assets), finishing touches, demo preparation |

---

## PART 3: Technical Documentation (Completed) ✅

*Following our collaborative "build and learn" approach, this technical blueprint is being defined in parallel with early development sprints. The following sections outline our current, evolving specifications.*

### 5. Core Technical Specifications

#### 5.1. 📖 User Stories & Mockups

**Must Have (MVP):**
*   As a **guest player**, I want to start a game session immediately without creating an account, so I can try the game with zero friction.
*   As a **registered player**, I want my game progress (lives, active bonuses) to be saved if I disconnect, so I can resume my session later without losing progress.
*   As a **player**, I want to see my final score and a simple leaderboard after each game, so I can track my performance.

**Should Have:**
*   As a **registered player**, I want to customize my profile (avatar, username) from the web dashboard.

**Could Have (V2.0):**
*   As a **player**, I want to unlock and select different spaceship skins after achieving high scores, so that I can personalize my gaming experience.
*   As a **data enthusiast**, I want to export my gameplay statistics as a CSV file from the dashboard, so that I can analyze them with my own tools.

**Won't Have This Time (Future Vision):**
*   As a **competitive player**, I want to challenge a friend in a real-time 1v1 duel mode. *(Complexité réseau hors scope MVP)*
*   As a **streamer**, I want my viewers to see my live stats and leaderboard on an overlay in my stream. *(Intégration OBS/Streaming API hors scope)*

### 🎮 Game Client Screenshots (Python Prototype)
<p align="center">
  <img src="https://github.com/Schpser/Portfolio/blob/main/Pictures/SI3LN_Home.png
" alt="Game Home Screen" width="300">
  <img src="https://github.com/Schpser/Portfolio/blob/main/Pictures/SI3LN_Screens.png
" alt="Game Screens" width="300">
</p>
  <img src=https://github.com/Schpser/Portfolio/blob/main/Pictures/SI3LN_Screens_2.png
    alt="Game Screens 2" width="600">
</p>

#### 5.2. 🏗️ System Architecture Diagram
Project structure and data flow (Client ↔ API ↔ DB ↔ Dashboard)

<p align="center">
  <img src="https://github.com/Schpser/Portfolio/blob/main/Pictures/Architecture_Diagram.png" alt="Architecture Diagram">
</p>

**5.3.1 🔄 Sequence Diagram**

<p align="center">
  <img src="https://github.com/Schpser/Portfolio/blob/main/Pictures/Sequence_Diagram.png" alt="Sequence Diagram">
</p>

**5.3.2 Project Structure**

The following directory tree represents the concrete organization of our codebase, reflecting the system architecture:
```
├── README.md
├── assets_shared
│   ├── backgrounds
│   ├── fonts
│   ├── sounds
│   └── sprites
│       ├── bonuses
│       ├── enemies
│       └── players
├── backend_api_python
│   ├── config.py
│   ├── main.py
│   ├── requirements.txt
│   ├── setup.py
│   ├── src
│   │   ├── api
│   │   ├── entities
│   │   ├── game
│   │   ├── ui
│   │   └── utils
│   ├── tests
│   │   ├── __init__.py
│   │   ├── test_collisions.py
│   │   ├── test_enemy.py
│   │   └── test_player.py
│   └── venv
│       ├── bin
│       ├── include
│       ├── lib
│       ├── lib64 -> lib
│       └── pyvenv.cfg
├── documentation
│   ├── REAC_mapping
│   ├── technical
│   │   ├── __init__.py
│   │   ├── api.md
│   │   ├── architecture.md
│   │   └── game_design.md
│   └── user
├── game_client_c
├── infrastructure
│   ├── ci_cd
│   │   └── github-actions.yml
│   ├── docker
│   │   └── docker-compose.yml
│   └── monitoring
└── web_dashboard
    ├── assets
    ├── public
    │   ├── index.html
    │   ├── script.js
    │   └── style.css
    └── src
```

#### 5.4. 🗄️ ER Diagram

Entity-Relationship Diagram for the SQLite database.

<p align="center">
  <img src="https://github.com/Schpser/Portfolio/blob/main/Pictures/ERD_Diagram.png" alt="ER Diagram">
</p>

#### 5.5. 🧪 SCM & QA Strategy
*   **SCM (Git):** We use a simplified **Git Flow**. The `main` branch is always deployable. All features are developed in `feature/*` branches via **pair programming**, followed by a Pull Request reviewed by both team members before merging.
*   **QA & Testing:** For the **Python API**, we implement unit tests with `pytest` for each endpoint. For the **C++ game client**, we perform manual gameplay testing and validation of core mechanics (collisions, scoring). The dashboard is tested for correct data display.

#### 5.6. 🔌 API Specifications
**Internal API Endpoints (Python/Flask):**

| Endpoint | Method | Description | Request Body (JSON) | Success Response (JSON) |
| :--- | :--- | :--- | :--- | :--- |
| `/api/session/start` | POST | Starts a new game session | `{"player_id": 1}` (or empty for guest) | `{"session_id": "abc123", "player_state": {...}}` |
| `/api/session/{id}/event` | POST | Sends a game event (shot, bonus) | `{"type": "BONUS_COLLECTED", "details": {...}}` | `{"status": "ok"}` |
| `/api/session/{id}/end` | POST | Ends a session & submits score | `{"final_score": 1500}` | `{"leaderboard_position": 25}` |
| `/api/player/profile` | GET | Gets player profile (dashboard) | - | `{"username": "Pseudo", "avatar_url": "...", "unlocked_levels": []}` |

#### 5.7. 🧠 Technical Justifications & Technology Choices
*   **C++ for Game Client:** Chosen for **performance and low-level control** required in real-time arcade games, allowing precise management of the game loop, graphics, and collisions.
*   **Python/Flask for API:** Selected for its **rapid development** speed, simplicity, and rich ecosystem. Ideal for building a robust REST API quickly that handles game data logic and communication.
*   **SQLite Database:** Perfect for the MVP due to its **zero-configuration, serverless nature**. It simplifies deployment and is fully capable of handling the data load for a single-player/leaderboard-focused game.
*   **JWT for Authentication:** Provides a **stateless, scalable** way to manage registered player sessions, securely transmitting player identity between the client, API, and web dashboard.

---

## PART 4: MVP Development (To Come)

📓 Development journal for Stage 4.

---

## PART 5: Project Closure (To Come)

📋 Final report for Stage 5.

---

*Last updated: January 11, 2026* 🚀