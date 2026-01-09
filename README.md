# 🎮 Portfolio Project - SI3LN: Arcade Analytics

**Team:** [Hugo Ramos](https://github.com/hugou74130) & [Melissa Sbibih](https://github.com/Schpser)
*This document follows the 5-stage project curriculum structure.*

---

## 📑 Table of Contents

1. **[PART 1: Idea Development](#part-1-idea-development-completed-)** (Stage 1 - Completed) ✅
   - [Team Formation & Roles](#0-team-formation--roles-)
   - [Brainstorming & Idea Evaluation](#1-brainstorming--idea-evaluation-)
   - [Selected MVP: Definition & Refinement](#2-selected-mvp-definition--refinement-)

2. **[PART 2: Project Planning](#part-2-project-planning-in-progress-)** (Stage 2 - In Progress) ▶️
   - [Executive Summary & MVP Blueprint](#3-executive-summary--mvp-blueprint-)
   - [High-Level Timeline & Curriculum Alignment](#4-high-level-timeline--curriculum-alignment-)

3. **[PART 3: Technical Documentation](#part-3-technical-documentation-to-come-)** (Stage 3 - To Come) ⚪
   - [Core Technical Specifications](#5-core-technical-specifications) (Architecture, APIs, SCM/QA)

4. **[PART 4: MVP Development](#part-4-mvp-development-to-come-)** (Stage 4 - To Come) ⚪
   - Development Sprint Logs & Testing

5. **[PART 5: Project Closure](#part-5-project-closure-to-come-)** (Stage 5 - To Come) ⚪
   - Final Report & Demo

---

## PART 1: Idea Development (Completed) ✅

### 0. Team Formation & Roles 👥

| Member | Primary Role | Strengths & Interests | Initial Responsibilities |
|--------|-------------|----------------------|------------------------|
| **Hugo Ramos** | 🎨 Lead Game Developer & Visual Architect | Game logic (C), Visual design, UI/UX sensitivity, Performance optimization | `game_client_c` development, Asset creation/enhancement (`assets_shared/`), Visual consistency across all modules |
| **Melissa Sbibih** | ⚙️ Lead Backend Developer & System Architect | API design (Python), Data modeling, Documentation, Full-stack integration | `backend_api_python` development, `web_dashboard` logic, Technical documentation & planning |

#### 💡 Work Standards & Synergy

**🤝 Collaboration Model:**  
We will adopt a "Feature-Pairing" model. For each main feature (e.g., "Score submission"), Hugo will develop the C client and Melissa the API endpoint, with systematic cross-code reviews.

**💬 Communication:**  
- Daily 15-minute sync sessions (Discord)
- Centralized documentation on Notion
- Task management via Trello (To Do/Doing/Done)

**🎯 Decision-making:**  
- Consensus for creative choices
- Final technical decision by the domain expert (Hugo for the game, Melissa for the API)

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
A. Classic Arcade with Basic Dashboard	-	Jeu simple + dashboard affichant juste le score.	5	2	10	❌ Rejected - Trop basique, ne valorise pas la stack.
B. Multiplayer Arcade with Live Leaderboard	Combine (Game + Real-time)	Jeu où plusieurs clients se connectent pour une partie en temps réel. Dashboard avec classement live.	2	5	10	❌ Rejected - Complexité réseau trop élevée pour le temps alloué.
C. Data-Rich Arcade with Analytics Dashboard (Selected)	Modify & Put to another use	Jeu riche en événements (kill, bonus, dégâts) envoyant des données structurées. Dashboard avec statistiques détaillées, graphiques et insights.	4	5	20	✅ SELECTED
2. Selected MVP: Definition & Refinement 🏗️
🎯 MVP Title: SI3LN: Arcade Analytics - A cohesive gaming ecosystem.

Aspect	Definition
**Problem** | Classic arcade games offer an ephemeral experience. Players have no insight into their performance, trends, or history.
**Solution** | Couple an engaging gaming experience (`game_client_c`) with an analytical dashboard (`web_dashboard`) via a robust API (`backend_api_python`), transforming a gaming session into actionable data.
**Target Audience** | 1. Casual Gamers (25-35 years old). 2. Data-enthusiasts who enjoy tracking their progress.
**Application Type** | Desktop-based Gaming Ecosystem: Heavy client in C (for performance) + responsive web dashboard (for accessibility).
**Why This Idea?** | 1. Perfect alignment with the imposed folder structure. 2. Demonstrates a complete data flow (Client → API → DB → Frontend). 3. Showcases our complementary skills. 4. Scope perfectly manageable by a team of 2.

**✅ MVP SMART Objectives:**

**Specific:** Deliver a game with 1 ship type, 2 enemies, 1 bonus. Dashboard with 3 charts (score over time, event heatmap, success rate).

**Measurable:** The API exposes 3 functional REST endpoints. The C client sends events to POST `/api/game/event`. The dashboard makes at least 2 GET calls.

**Achievable:** Based on known technologies (C, Python/Flask, JS). Pre-existing structure.

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
| C Client Performance | Low | High | Rapid game loop prototyping from week 1. Profiling with Valgrind if needed. |
| Visual Consistency | Medium | Medium | Hugo is responsible for assets. Color/UI style guide defined before dev. |

### 3. Executive Summary & MVP Blueprint 👑

| 🔑 Key Element | Description |
|----------------|-------------|
| **🔭 Vision** | Make gaming performance measurable, visible, and engaging through data |
| **🛠️ Tech Stack** | `C` (Client) \| `Python/Flask` (API) \| `SQLite` (DB) \| `HTML/CSS/JS` (Dashboard) \| `Git/GitHub` (SCM) |
| **💎 Value Proposition** | 1 product, 2 facets: the thrill of arcade gaming + the introspection of analytics |
| **🎯 Expected Impact** | Transform a "high score" into a detailed progression story |

---

### 4. High-Level Timeline & Curriculum Alignment 🗺️

This timeline follows the 5-stage curriculum structure.

| Stage | Estimated Period | Primary Objective | Key Deliverables | Status |
|-------|------------------|-------------------|------------------|--------|
| **Stage 1:** Idea Development | D-1 to D-7 | Define the concept, team, and feasibility | This document (Part 1) | ✅ **COMPLETED** |
| **Stage 2:** Project Planning | D-7 to D-14 | Plan execution and technical architecture | Detailed timeline, API contract, Data model | ▶️ **IN PROGRESS** |
| **Stage 3:** Technical Documentation | Weeks 1-2 | Formalize all technical specifications | Architecture diagrams, User Stories, SCM/QA plans | ⚪ **TO COME** |
| **Stage 4:** MVP Development | Weeks 3-6 | Build, integrate, and test modules | Functional code (Client + API + Dashboard), Tests | ⚪ **TO COME** |
| **Stage 5:** Project Closure | Weeks 7-8 | Finalize, document, and present the project | Final report, Demo video, Optimizations | ⚪ **TO COME** |

#### 📅 Detailed Sprint Plan (Weeks 3-6 - Stage 4)

| Sprint | Focus | Key Activities |
|--------|-------|----------------|
| **🔧 Sprint 1** (Setup) | Environment & Foundation | Dev environment setup, base architecture, API contract validated |
| **🎮 Sprint 2** (Core Gameplay) | Game Engine | C game engine (movement, shooting, basic collisions) |
| **🔌 Sprint 3** (Data Pipeline) | API Integration | Operational Python API endpoints, event sending from C client |
| **📊 Sprint 4** (Dashboard) | Frontend Development | Static web pages, initial charts with mocked data |
| **🔗 Sprint 5** (Integration) | End-to-End Connection | Complete C→API→DB→Dashboard connection, integration tests |
| **✨ Sprint 6** (Polish) | Finalization | Visual improvements (assets), finishing touches, demo preparation |

---

## PART 3: Technical Documentation (To Come)

This section will be developed during Stage 3. Here is the planned structure:

### 5. Core Technical Specifications

#### 5.1. 📖 User Stories & Mockups
*(e.g., "As a player, I want to see my accuracy rate as a chart...")*

#### 5.2. 🏗️ System Architecture Diagram
Overview of components and data flow

#### 5.3. 💾 Database Schema
SQLite ER diagram (tables: `games`, `players`, `events`)

#### 5.4. 🔌 API Specification (Swagger/OpenAPI)
Complete endpoint documentation (`POST /game/event`, `GET /player/stats`)

#### 5.5. 🧪 SCM & QA Strategy
Git workflow (adapted Git Flow), test plan (unit tests for API, integration tests)

---

## PART 4: MVP Development (To Come)

📓 Development journal for Stage 4.

---

## PART 5: Project Closure (To Come)

📋 Final report for Stage 5.

---

*Last updated: January 9, 2026* 🚀