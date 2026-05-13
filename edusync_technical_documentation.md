# 🎓 EduSync Collaborative Learning Platform — Complete Technical Documentation

> **Purpose:** Viva preparation, interviews, and README reference.
> **Stack:** React + TypeScript (Vite) · Node.js + Express · MongoDB + Mongoose · JWT

---

## 📌 1. PROJECT OVERVIEW

### Project Name
**EduSync — Collaborative Learning Platform**

### Problem Statement
Traditional online learning is **isolated and passive**. Students study alone, have no real-time peers, no gamified motivation, and no shared resource library. There is no central collaborative hub where students can compete in quizzes, share study materials, join live study rooms, and track personal progress.

### Objective
Build a **full-stack, gamified collaborative learning platform** where students can:
- Join virtual study rooms and collaborate
- Take competitive quizzes
- Share and discover study resources (PDFs, notes, videos, slides, code)
- Track XP, levels, badges, and streaks
- View performance analytics
- Compete on a leaderboard

### Key Features

| Feature | Description |
|---|---|
| 🔐 Authentication | Secure JWT-based register/login with role system |
| 🏠 Study Rooms | Create/join virtual study rooms with drag-and-drop boards |
| ⚡ Quiz Arena | Competitive timed quizzes with XP rewards |
| 📚 Resource Library | Upload/download/like PDFs, notes, videos, slides, code |
| 🏆 Leaderboard | XP-ranked global leaderboard |
| 📊 Analytics | Weekly stats, study hours chart, quiz performance, contributions |
| 👤 Profile | Avatar, XP meter, badge, streak, personalised stats |
| 🎉 Gamification | XP system, levels, badges, streaks, confetti animations |

### Target Users
- **Students** preparing for exams or university courses
- **Tutors** sharing resources and managing study rooms
- **Study groups** wanting real-time collaborative tools

### Real-World Use Case
A group of engineering students studying Data Structures can:
1. Create a "DSA Study Room"
2. Share PDF notes via the Resource Library
3. Quiz each other in the Quiz Arena
4. View who's on top of the leaderboard
5. Track their weekly study hours in Analytics

---

## 📌 2. TECH STACK IDENTIFICATION

### Frontend
| Technology | Version | Why Used |
|---|---|---|
| **React** | 18.3.1 | Component-based UI, virtual DOM, massive ecosystem |
| **TypeScript** | (via Vite) | Type safety prevents runtime bugs, better IDE support |
| **Vite** | 6.3.5 | Blazing-fast HMR dev server, native ESM, faster than CRA |
| **React Router v7** | 7.13.0 | Client-side routing, nested routes, `createBrowserRouter` |
| **Motion (Framer)** | 12.23.24 | Smooth animations: page transitions, sidebar, XP bar |
| **Recharts** | 2.15.2 | Declarative chart library for analytics (bar, line, pie) |
| **Lucide React** | 0.487.0 | Clean, consistent icon set |
| **Radix UI** | Various | Headless, accessible component primitives (dialog, select, tabs, etc.) |
| **TailwindCSS** | 4.1.12 | Utility-first CSS (used sparingly; custom CSS is primary) |
| **React DnD** | 16.0.1 | Drag-and-drop in the Collaborative Study Room whiteboard |
| **Sonner** | 2.0.3 | Beautiful toast notification system |
| **date-fns** | 3.6.0 | Date formatting for calendar and activity feeds |
| **React Hook Form** | 7.55.0 | Performant form state management |
| **MUI (Material UI)** | 7.3.5 | Additional UI components support |

### Backend
| Technology | Version | Why Used |
|---|---|---|
| **Node.js** | (LTS) | JavaScript runtime; same language as frontend, fast I/O |
| **Express v5** | 5.2.1 | Minimal, flexible web framework; easy route/middleware setup |
| **Mongoose** | 9.3.3 | ODM for MongoDB; Schema definition, validation, methods |
| **jsonwebtoken** | 9.0.3 | JWT generation and verification for stateless auth |
| **bcrypt** | 6.0.0 | Password hashing with salt rounds (cost factor 12) |
| **multer** | 2.1.1 | Multipart file upload (resource files) |
| **dotenv** | 17.3.1 | Environment variable management |
| **cors** | 2.8.6 | Cross-Origin Resource Sharing for frontend-backend communication |
| **nodemon** | 3.1.14 | Auto-restart server on file changes during development |
| **concurrently** | 9.2.1 | Run frontend + backend with single `npm run dev` command |

### Database
| Technology | Why Used |
|---|---|
| **MongoDB** (local) | Schema-flexible NoSQL → fits evolving educational data models. Documents map naturally to JS objects |
| **Mongoose ODM** | Provides schemas, validation, middleware hooks, and relationship references |

### Why NOT alternatives?
- **PostgreSQL**: Good but rigid schema requires migrations for every new field; MongoDB's flexibility suits a startup-like project
- **MySQL**: Same rigidity issue; also misses the JSON-document-native querying
- **Firebase**: Vendor lock-in, limited server-side control, harder to run complex aggregation queries
- **Next.js**: Overkill for this project size; Vite + React gives more control with less abstractions
- **Redux**: Auth state is simple enough for React Context + localStorage; Redux would add unnecessary boilerplate

---

## 📌 3. PROJECT ARCHITECTURE

### Architecture Pattern
- **Client-Server REST API Architecture**
- Frontend: Single-Page Application (SPA) with React
- Backend: RESTful API with Express
- Pattern: **MVC** *(Model-View-Controller)* on the backend
  - **Model** → Mongoose schemas (`/server/models/`)
  - **View** → JSON responses (API endpoints)
  - **Controller** → Business logic (`/server/controllers/`)

### High-Level Architecture Diagram

```
┌───────────────────────────────────────────────────────────────┐
│                  BROWSER (React + Vite SPA)                   │
│  ┌──────────┐  ┌───────────┐  ┌──────────┐  ┌─────────────┐ │
│  │  Pages   │  │Components │  │ Services │  │ AuthContext │ │
│  │(JS Views)│  │(UI Parts) │  │(API calls│  │(Global State│ │
│  └────┬─────┘  └─────┬─────┘  └────┬─────┘  └─────────────┘ │
│       └──────────────┴─────────────┘                          │
│                        ↕ HTTP / Fetch API                     │
└───────────────────────────────────────────────────────────────┘
                         ↕ Port 5000
┌───────────────────────────────────────────────────────────────┐
│                 EXPRESS SERVER (Node.js)                       │
│  ┌──────────────────────────────────────────────────────────┐ │
│  │  Middleware: CORS → Logger → Auth → upload (multer)      │ │
│  └──────────────────────────────────────────────────────────┘ │
│  ┌──────────┐ ┌────────────┐ ┌──────────┐ ┌──────────────┐  │
│  │  Routes  │→│ Controllers│→│  Models  │→│   MongoDB    │  │
│  │/api/auth │ │authCtrl    │ │User.js   │ │  (Mongoose)  │  │
│  │/api/users│ │userCtrl    │ │StudyRoom │ │  localhost   │  │
│  │/api/rooms│ │roomCtrl    │ │Resource  │ │  :27017      │  │
│  │/api/res  │ │resourceCtrl│ │Activity  │ │  /edusync    │  │
│  │/api/quiz │ │quizCtrl    │ │Achievement│ │              │  │
│  │/api/anlyt│ │analyticsCtrl│ │QuizQn    │ │              │  │
│  └──────────┘ └────────────┘ └──────────┘ └──────────────┘  │
└───────────────────────────────────────────────────────────────┘
```

### Request-Response Lifecycle

1. **User action** in browser (e.g., click "Upload Resource")
2. **React component** (Resources.tsx) calls **service function** (resourceService.upload)
3. **Service layer** (api.ts - apiFetch) attaches `Authorization: Bearer <token>` header and sends HTTP request to backend
4. **Express router** matches the route (`POST /api/resources/upload`)
5. **authMiddleware** intercepts → validates JWT → attaches `req.user` → calls `next()`
6. **multer middleware** handles file stream → saves file to `/uploads/` → attaches `req.file`
7. **Controller function** (uploadResource) reads `req.body` + `req.file` → creates MongoDB document
8. **MongoDB** persists the document
9. **Controller** returns JSON response with the new resource
10. **React component** updates state → UI re-renders with new resource card

---

## 📌 4. FRONTEND ANALYSIS

### Complete Folder Structure

```
src/
├── main.tsx                  # Entry point: mounts <App /> to #root
├── app/
│   ├── App.tsx               # Root component: wraps AuthProvider + RouterProvider
│   ├── routes.tsx            # All client-side routes with createBrowserRouter
│   ├── context/
│   │   └── AuthContext.tsx   # Global auth state (user, token, login, logout, register)
│   ├── services/             # API call layer (abstracts all fetch calls)
│   │   ├── api.ts            # Base apiFetch wrapper with auto-auth header injection
│   │   ├── authService.ts    # register(), login()
│   │   ├── userService.ts    # getAll(), getMe(), update()
│   │   ├── studyRoomService.ts # getAll(), getById(), create(), update(), remove()
│   │   ├── resourceService.ts  # getAll(), getById(), create(), upload(), like(), remove()
│   │   ├── analyticsService.ts # getWeeklyStats(), getStudyHours(), quiz perf, contributions
│   │   ├── activityService.ts  # getAll(), create()
│   │   └── achievementService.ts # getAll()
│   ├── pages/                # Full-page view components (one per route)
│   │   ├── Auth.tsx           # Login + Register page (50KB — full form logic + UI)
│   │   ├── Dashboard.tsx      # Home overview: stats, activity feed, quick links
│   │   ├── StudyRooms.tsx     # Browse/create/join study rooms
│   │   ├── CollaborativeRoom.tsx # Live room: chat, whiteboard, members
│   │   ├── QuizArena.tsx      # Timer-based quiz with subject selector + score
│   │   ├── Resources.tsx      # File upload, filter, like, delete resources
│   │   ├── Leaderboard.tsx    # Ranked user list with XP + badges
│   │   ├── Analytics.tsx      # Charts: weekly stats, study hours, quiz perf, contributions
│   │   ├── Profile.tsx        # User profile: avatar, stats, achievements, settings
│   │   ├── CalendarPage.tsx   # Study calendar (planned sessions)
│   │   ├── Quiz.tsx           # Standalone quiz component
│   │   └── StudyBoard.tsx     # Study board (drag-and-drop tasks)
│   ├── components/           # Reusable UI components
│   │   ├── Layout.tsx         # Shell: Sidebar + TopBar + main content <Outlet />
│   │   ├── Sidebar.tsx        # Collapsible nav sidebar with user profile + logout
│   │   ├── ActivityFeed.tsx   # Recent activity log (scrollable feed)
│   │   ├── AnimatedTag.tsx    # Pill tag with animation (e.g., subject tags)
│   │   ├── AvatarStack.tsx    # Overlapping user avatars in study rooms
│   │   ├── Confetti.tsx       # Canvas confetti animation (quiz win celebration)
│   │   ├── FloatingCard.tsx   # Glassmorphism floating card wrapper
│   │   ├── FloatingShapes.tsx # Background animated shapes/blobs
│   │   ├── GlassCard.tsx      # Glass-effect container (backdrop-filter blur)
│   │   ├── GradientButton.tsx # Gradient CTA button with hover effects
│   │   ├── HeroCarousel.tsx   # Slide carousel for landing/dashboard hero
│   │   ├── LeaderboardWidget.tsx # Compact leaderboard preview widget
│   │   ├── QuizTimerWidget.tsx   # Countdown timer for quiz sessions
│   │   └── XPMeter.tsx        # Animated XP progress bar
│   └── data/
│       └── mockData           # Fallback/seed data for development
├── styles/
│   ├── index.css              # Global reset + root CSS variables import
│   ├── theme.css              # Design tokens: colors, gradients, glassmorphism utils
│   ├── fonts.css              # Google Fonts: Orbitron, Inter, Outfit, Space Grotesk
│   ├── responsive.css         # Breakpoint media queries for mobile/tablet/desktop
│   └── tailwind.css           # Tailwind base import (minimal usage)
└── imports/                   # (asset imports, additional configs)
```

### Routing System
```tsx
// routes.tsx uses React Router v7 createBrowserRouter
/ → Dashboard (index)
/study-rooms → StudyRooms
/study-rooms/:id → CollaborativeRoom (dynamic segment)
/quiz-arena → QuizArena
/resources → Resources
/leaderboard → Leaderboard
/analytics → Analytics
/profile → Profile
```
All routes are **child routes** of the `Layout` component, which provides the persistent Sidebar + TopBar shell. The `<Outlet />` from React Router renders the active child page inside the layout.

**Auth guard:** `Layout.tsx` checks `isAuthenticated` from `AuthContext`. If `false`, it renders the `<Auth />` login/register screen instead of the app — effectively a **route-level auth guard**.

### State Management
- **No Redux / Zustand** — deliberately lightweight
- **Global state:** `AuthContext.tsx` (React Context API)
  - Stores: `user`, `token`, `isAuthenticated`, `isLoading`
  - Methods: `login()`, `register()`, `logout()`, `updateUser()`
  - Persisted to `localStorage` (keys: `edusync_token`, `edusync_user`)
- **Local state:** `useState` hooks in each page component (e.g., resource list, filter, modal open/close)

### API Integration — How Frontend Talks to Backend

```
[User Action] → [Page Component] → [Service Function] → [apiFetch()] → [HTTP Request]
                                                              ↓
                                           Auto-injects Authorization header
                                           from localStorage token
```

- `api.ts` exports `apiFetch<T>()` — a generic typed wrapper over native `fetch`
- Automatically reads token from `localStorage` and adds `Authorization: Bearer <token>`
- Handles 401 → auto-logout and redirect to `/`
- All service files (authService, resourceService, etc.) use `apiFetch` under the hood

### UI/UX Design Logic
- **Dark theme** (#0F172A deep navy background) — reduces eye strain for long study sessions
- **Glassmorphism** — `backdrop-filter: blur(20px)` on sidebar, topbar, and cards
- **Gradient accents** — blue-purple (#2F80ED → #7B61FF) for brand identity
- **Micro-animations** via Motion (Framer) — page fade-in/out, sidebar slide, XP bar fill
- **Orbitron font** for headings (futuristic feel), Inter/Space Grotesk for body text
- **Responsive** — hamburger menu + mobile bottom nav bar for phones; collapsible sidebar for desktop

---

## 📌 5. BACKEND ANALYSIS

### Complete Folder Structure

```
server/
├── server.js           # Entry point: Express app setup, middleware, route mounting
├── .env                # Environment variables (MONGO_URI, PORT, JWT_SECRET)
├── config/
│   └── db.js           # MongoDB connection via Mongoose
├── middleware/
│   ├── authMiddleware.js # JWT token validation → attaches req.user
│   ├── errorHandler.js   # Global error catcher (4-arg Express handler)
│   ├── logger.js         # Request logger: [timestamp] METHOD /path
│   └── upload.js         # Multer config: disk storage, file filter, 50MB limit
├── models/             # Mongoose schemas (data shape + validation)
│   ├── User.js
│   ├── StudyRoom.js
│   ├── Resource.js
│   ├── Activity.js
│   ├── Achievement.js
│   └── QuizQuestion.js
├── controllers/        # Business logic for each route group
│   ├── authController.js
│   ├── userController.js
│   ├── studyRoomController.js
│   ├── resourceController.js
│   ├── activityController.js
│   ├── achievementController.js
│   ├── quizController.js
│   └── analyticsController.js
├── routes/             # Route definitions (URL → controller mapping)
│   ├── authRoutes.js
│   ├── userRoutes.js
│   ├── studyRoomRoutes.js
│   ├── resourceRoutes.js
│   ├── activityRoutes.js
│   ├── achievementRoutes.js
│   ├── quizRoutes.js
│   └── analyticsRoutes.js
├── uploads/            # Disk storage for uploaded files (served as static)
└── scripts/            # Seed scripts (e.g., seed quiz questions, initial data)
```

### Server Setup (server.js)
```
1. Import Express, CORS, dotenv, connectDB, middleware
2. dotenv.config() → load .env
3. connectDB() → connect to MongoDB
4. express() → create app instance
5. Middleware pipeline: CORS → JSON parser → logger → static /uploads
6. Mount all route modules under /api/*
7. 404 catch-all handler
8. Global error handler (must be last)
9. app.listen(PORT) → port 5000
```

### All API Endpoints

#### 🔐 Auth (`/api/auth`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| POST | `/api/auth/register` | ❌ | Register new user, returns JWT + user object |
| POST | `/api/auth/login` | ❌ | Login, returns JWT + user object |

#### 👤 Users (`/api/users`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/users` | ❌ | Get all users sorted by XP (leaderboard data) |
| GET | `/api/users/me` | ✅ | Get currently logged-in user's profile |
| GET | `/api/users/:id` | ❌ | Get specific user by ID |
| PUT | `/api/users/:id` | ✅ | Update user (self or admin only) |
| POST | `/api/users` | ❌ | Create user (admin/seed use) |

#### 🏠 Study Rooms (`/api/study-rooms`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/study-rooms` | ❌ | Get all rooms (sorted: active first, then by members) |
| GET | `/api/study-rooms/:id` | ❌ | Get specific room |
| POST | `/api/study-rooms` | ✅ | Create new study room |
| PUT | `/api/study-rooms/:id` | ✅ | Update room details |
| DELETE | `/api/study-rooms/:id` | ✅ | Delete room |

#### 📚 Resources (`/api/resources`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/resources` | ❌ | Get all resources (filter by `?type=PDF&subject=DSA`) |
| GET | `/api/resources/:id` | ❌ | Get single resource |
| POST | `/api/resources` | ✅ | Create resource (JSON body, no file) |
| POST | `/api/resources/upload` | ✅ | Upload file + metadata (multipart/form-data) |
| PUT | `/api/resources/:id/like` | ✅ | Toggle like on resource |
| DELETE | `/api/resources/:id` | ✅ | Delete resource |

#### 📊 Analytics (`/api/analytics`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/analytics/weekly-stats` | ✅ | User's weekly summary (XP, quizzes, streak, rank) |
| GET | `/api/analytics/study-hours` | ❌ | Daily study hours data (chart data) |
| GET | `/api/analytics/quiz-performance` | ❌ | Subject-wise quiz scores |
| GET | `/api/analytics/contributions` | ❌ | Resource type distribution (pie chart data) |

#### 🎯 Quiz (`/api/quiz`)
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/quiz` | ❌ | Get all quiz questions |
| GET | `/api/quiz/subject/:subject` | ❌ | Get questions by subject (case-insensitive) |

#### 🏆 Achievements + Activities
| Method | Endpoint | Auth | Description |
|---|---|---|---|
| GET | `/api/achievements` | ❌ | Get all achievements |
| GET | `/api/activities` | ❌ | Get recent activity feed |
| POST | `/api/activities` | ✅ | Log a new activity |

### Authentication & Authorization
- **Stateless JWT Authentication**
- On login/register → server signs a JWT with `{ id, role }` payload, `7d` expiry
- Client stores JWT in `localStorage`
- Protected routes require `Authorization: Bearer <token>` header
- `authMiddleware.js` verifies token → attaches `req.user = { id, role }` to every protected request
- **Role-based:** `updateUser` checks `req.user.id === req.params.id || req.user.role === "admin"`

### Error Handling
- **Try-catch + next(err)** pattern in every controller
- `errorHandler.js` (global Express error handler) catches all errors:
  - Returns `statusCode` (default 500) and `message`
  - In development mode: also includes `stack` trace in response
- Validation errors from Mongoose automatically bubble up through `next(err)`

---

## 📌 6. DATABASE & MODELS

### Database
- **MongoDB** — local instance at `mongodb://localhost:27017/edusync`
- **Schema Design:** Document-oriented, 6 collections

### Collection Schemas

#### 📄 User Schema (`users`)
```
name          String (required, trimmed)
username      String (unique, auto-generated from name)
email         String (required, unique, lowercase)
password      String (required, min 6 chars, bcrypt hashed)
avatar        String (emoji, default "😊")
color         String (hex color, default "#2F80ED")
role          Enum ["student", "tutor", "admin"] (default "student")
level         Number (default 1)
xp            Number (default 0)
totalXP       Number (default 1000)
rank          Number (default 99)
badge         String (default "Newcomer")
badgeColor    String
streak        Number (days streak, default 0)
quizWins      Number (default 0)
resourcesShared Number (default 0)
joinedRooms   Number (default 0)
createdAt/updatedAt (timestamps)
```
**Pre-save hooks:**
1. Hash password with bcrypt (salt rounds = 12) if modified
2. Auto-generate `@username` from name if not provided

**Instance method:**
- `comparePassword(candidate)` — supports both bcrypt and legacy plain-text (auto-upgrades)

#### 🏠 StudyRoom Schema (`studyrooms`)
```
name          String (required)
subject       String (required)
members       Number (default 0)
maxMembers    Number (default 20)
resources     Number
hasQuiz       Boolean
tags          [String]
gradient      String (CSS gradient)
icon          String (emoji)
description   String
activeNow     Boolean
memberAvatars [{ initials, color }] (sub-document, no _id)
createdBy     ObjectId → ref "User"
```

#### 📚 Resource Schema (`resources`)
```
title         String (required)
type          Enum ["PDF", "Notes", "Video", "Code", "Slides"]
subject       String (required)
uploader      String (display name)
uploaderColor String
uploaderInitials String
uploaderRef   ObjectId → ref "User"
downloads     Number (default 0)
likes         Number (default 0)
likedBy       [ObjectId] → ref "User" (for toggle logic)
tags          [String]
uploadedAt    String
size          String (e.g., "2.3 MB")
fileUrl       String (public URL)
fileType      String (MIME type)
```

#### 📋 Activity Schema (`activities`)
```
user          String
userInitials  String
userColor     String
userRef       ObjectId → ref "User"
action        String (e.g., "uploaded")
target        String (e.g., "DSA Notes PDF")
time          String
type          Enum ["upload","quiz","join","solve","achievement","streak"]
```

#### 🏅 Achievement Schema (`achievements`)
```
name          String (required)
description   String (required)
icon          String (emoji)
color         String
unlocked      Boolean (default false)
rarity        Enum ["common","rare","epic","legendary"]
userId        ObjectId → ref "User"
```

#### ❓ QuizQuestion Schema (`quizquestions`)
```
question      String (required)
options       [String] (required, min 2)
correct       Number (index of correct option)
subject       String (required)
```

### Relationships Between Collections
```
User    ←(createdBy)─── StudyRoom
User    ←(uploaderRef)── Resource
User    ←(likedBy)────── Resource (many-to-many via array)
User    ←(userRef)────── Activity
User    ←(userId)─────── Achievement
```

### CRUD Operations Flow
- **Create:** `Model.create(data)` or `new Model(data).save()`
- **Read:** `Model.find()`, `Model.findById()`, `Model.findOne()` + `.lean()` for performance
- **Update:** `Model.findByIdAndUpdate(id, data, { new: true, runValidators: true })`
- **Delete:** `Model.findByIdAndDelete(id)`

---

## 📌 7. MIDDLEWARE & API CONNECTIVITY

### Middleware Stack (in order)

```
Request arrives at Express
        ↓
1. CORS middleware           — allows requests from http://localhost:5173
        ↓
2. express.json()            — parses JSON request bodies
        ↓
3. logger middleware         — logs [timestamp] METHOD /url to console
        ↓
4. express.static("/uploads") — serves uploaded files publicly
        ↓
5. Router matching          — routes request to correct route file
        ↓
6. authMiddleware (optional) — validates JWT, attaches req.user
        ↓
7. multer upload (optional)  — handles file upload for /upload route
        ↓
8. Controller function       — business logic, DB operations
        ↓
9. errorHandler (if error)   — global catch, sends JSON error response
```

### authMiddleware.js — Deep Dive
```javascript
// 1. Check for Authorization header
const authHeader = req.headers["authorization"];
if (!authHeader || !authHeader.startsWith("Bearer ")) → 401

// 2. Extract token
const token = authHeader.split(" ")[1];

// 3. Verify with JWT_SECRET
const decoded = jwt.verify(token, process.env.JWT_SECRET);
req.user = decoded;  // { id, role }
next();
```

### upload.js — Multer Configuration
- **Storage:** `multer.diskStorage` → saves to `uploads/` folder
- **Filename:** `Date.now() + random + original extension` (ensures uniqueness)
- **File filter:** Whitelist MIME types — PDF, PPT, DOC, images, plain text
- **Size limit:** 50MB max
- **File served:** `GET /uploads/<filename>` (static file server)

### Frontend API Communication
```
apiFetch<T>(endpoint, options) in api.ts:
├── Reads token from localStorage
├── Sets headers: Content-Type: application/json + Authorization: Bearer <token>
├── fetch(BASE_URL + endpoint, { ...options, headers })
├── If 401: clear storage, redirect to /
├── Parse JSON response
└── Throw error if !res.ok (with server's error message)
```

---

## 📌 8. KEY FEATURES EXPLANATION

### 🔐 Feature 1: Authentication (Register/Login)

**Steps:**
1. User fills the Auth form in `Auth.tsx`
2. On submit → `useAuth().login()` or `register()` called
3. `AuthContext` calls `authService.login({ email, password })`
4. `authService` calls `apiFetch("/auth/login", { method: "POST", body })`
5. Backend: `authController.login` → finds user by email → `user.comparePassword(password)` → bcrypt verify
6. If valid → `generateToken(user)` → signs JWT with `{ id, role }` payload
7. Returns `{ token, user }` JSON
8. Frontend: `AuthContext.persist(token, user)` → saves to state + localStorage
9. `Layout.tsx` checks `isAuthenticated` → renders the app

**Files involved:** `Auth.tsx`, `AuthContext.tsx`, `authService.ts`, `api.ts`, `authRoutes.js`, `authController.js`, `User.js`

---

### 📚 Feature 2: Resource Upload

**Steps:**
1. User clicks "Upload" in `Resources.tsx`, fills form with file + title/type/subject
2. Frontend creates `FormData` object, appends file + metadata fields
3. `resourceService.upload(formData)` called — uses raw `fetch` (NOT apiFetch) to avoid setting `Content-Type` manually (browser must set multipart boundary)
4. `Authorization: Bearer <token>` header set manually
5. Server: `POST /api/resources/upload` → authMiddleware → multer upload.single("file")
6. `uploadResource` controller: validates required fields → builds `fileUrl` → calculates human-readable `size` → creates `Resource` document
7. Returns new resource JSON → `Resources.tsx` prepends it to the list → toast notification

**Files involved:** `Resources.tsx`, `resourceService.ts`, `resourceRoutes.js`, `resourceController.js`, `upload.js`, `authMiddleware.js`, `Resource.js`

---

### ⚡ Feature 3: Quiz Arena

**Steps:**
1. User opens `QuizArena.tsx`, selects a subject
2. `quizRoutes GET /api/quiz/subject/:subject` fetched → filtered questions from DB
3. `QuizTimerWidget` component counts down per question
4. User selects answer → checked against `question.correct` index
5. Score tallied → confetti animation via `Confetti.tsx` if passed threshold
6. XP awarded (locally updated via `updateUser()` in AuthContext)

**Files involved:** `QuizArena.tsx`, `QuizTimerWidget.tsx`, `Confetti.tsx`, `quizRoutes.js`, `quizController.js`, `QuizQuestion.js`, `AuthContext.tsx`

---

### 🏠 Feature 4: Study Rooms

**Steps:**
1. `StudyRooms.tsx` → `studyRoomService.getAll()` → `GET /api/study-rooms` → sorted (activeNow first, then by members)
2. User clicks "Create Room" → form modal → `studyRoomService.create(data)` → `POST /api/study-rooms` (auth required)
3. `CollaborativeRoom.tsx` at `/study-rooms/:id` → `studyRoomService.getById(id)` → fetches room details
4. Inside room: live chat area, drag-and-drop whiteboard (React DnD), member avatars

**Files involved:** `StudyRooms.tsx`, `CollaborativeRoom.tsx`, `studyRoomService.ts`, `studyRoomRoutes.js`, `studyRoomController.js`, `StudyRoom.js`

---

### 📊 Feature 5: Analytics Dashboard

**Steps:**
1. `Analytics.tsx` mounts → calls four parallel API requests:
   - `analyticsService.getWeeklyStats()` → `/api/analytics/weekly-stats` (auth)
   - `analyticsService.getStudyHours()` → `/api/analytics/study-hours`
   - `analyticsService.getQuizPerformance()` → `/api/analytics/quiz-performance`
   - `analyticsService.getContributions()` → `/api/analytics/contributions`
2. Data fed into Recharts components: `BarChart`, `LineChart`, `PieChart`, `RadarChart`
3. Weekly stats shown as cards (XP, streak, rank, quizzes)

**Files involved:** `Analytics.tsx`, `analyticsService.ts`, `analyticsRoutes.js`, `analyticsController.js`, `User.js`, `Resource.js`

---

### 🏆 Feature 6: Leaderboard

**Steps:**
1. `Leaderboard.tsx` → `userService.getAll()` → `GET /api/users`
2. `userController.getAllUsers` → sorts all users by `xp: -1` → assigns `rank` in response
3. Users rendered as ranked cards with avatar, level, badge, XP bar, streak

**Files involved:** `Leaderboard.tsx`, `userService.ts`, `userRoutes.js`, `userController.js`, `User.js`

---

## 📌 9. SECURITY & OPTIMIZATION

### Security Practices

| Practice | Implementation |
|---|---|
| **Password Hashing** | bcrypt with salt rounds = 12 (computationally expensive, resists brute force) |
| **JWT Auth** | Stateless tokens; secret stored in `.env`, 7-day expiry |
| **JWT Verification** | `jwt.verify()` on every protected request; if invalid → 401 |
| **Field Protection** | `updateUser` explicitly deletes `password`, `email`, `role` from req.body before update |
| **CORS Policy** | Only `http://localhost:5173` whitelisted (not wildcard `*`) |
| **Password not returned** | `.select("-password")` on all user queries |
| **Input Normalization** | Email trimmed + lowercased before DB operations |
| **File Type Whitelist** | Multer rejects non-whitelisted MIME types |
| **File Size Limit** | 50MB cap via multer config |
| **Error messages** | Generic "Invalid credentials" (doesn't leak whether email or password is wrong) |

### Data Validation
- **Mongoose schema-level:** `required`, `unique`, `enum`, `minlength`, `trim`, `lowercase`
- **Controller-level:** Manual checks before DB calls (e.g., `if (!name || !email || !password)`)
- **Frontend:** React Hook Form with validation rules

### Performance Optimization
- `.lean()` on Mongoose queries → returns plain JS objects (not Mongoose documents), faster and lighter
- `select("-password")` → excludes unnecessary field from wire
- `sort()` at DB level (not in JS) → MongoDB index utilization
- **Vite** for frontend → native ES module HMR, faster builds than Webpack
- `AnimatePresence` only animates components that are present in the React tree
- **Component-level code splitting** via dynamic imports (potential)
- `apiFetch` re-uses the same base URL constant

---

## 📌 10. DEPLOYMENT & EXECUTION

### Running the Project Locally

#### Prerequisites
- Node.js (v18+ recommended)
- MongoDB running locally on port 27017
- npm or pnpm

#### Step-by-Step Setup

```bash
# 1. Clone the project
git clone <repo-url>
cd "EduSync Collaborative Learning Platform"

# 2. Install frontend dependencies
npm install

# 3. Install backend dependencies
cd server
npm install
cd ..

# 4. Set up environment variables
# The server/.env is pre-configured:
# MONGO_URI=mongodb://localhost:27017/edusync
# PORT=5000
# JWT_SECRET=edusync_super_secret_jwt_key_2024

# 5. Start MongoDB (if not running as a service)
# On Windows: net start MongoDB
# On Mac: brew services start mongodb-community

# 6. Run both frontend + backend simultaneously
npm run dev
```

**`npm run dev` runs:**
- `vite` → frontend dev server at `http://localhost:5173`
- `cd server && npm run dev` → backend with nodemon at `http://localhost:5000`
- Both run **concurrently** via the `concurrently` package

#### Port Configuration
| Service | URL |
|---|---|
| Frontend (Vite) | `http://localhost:5173` |
| Backend (Express) | `http://localhost:5000` |
| MongoDB | `mongodb://localhost:27017/edusync` |

### Environment Variables
```env
MONGO_URI=mongodb://localhost:27017/edusync   # MongoDB connection string
PORT=5000                                       # Express server port
JWT_SECRET=edusync_super_secret_jwt_key_2024   # JWT signing secret
```

> **⚠️ For production:** Change `JWT_SECRET` to a long random string, use `MONGO_URI` for MongoDB Atlas cloud, set `NODE_ENV=production`, and serve frontend via `npm run build` + a static host (Vercel, Netlify) or serve from Express.

### Build for Production
```bash
# Frontend build
npm run build   # Outputs to /dist folder

# Backend
cd server && npm start   # Uses node directly (no nodemon)
```

---

## 📌 11. VIVA QUESTIONS & ANSWERS

### 🔵 Conceptual Questions

**Q1: What is a REST API? Is this project RESTful?**
> REST (Representational State Transfer) is an architectural style where resources are accessed via standard HTTP methods (GET, POST, PUT, DELETE) using URLs. Yes, EduSync is RESTful — each collection (users, rooms, resources) has its own endpoint, uses proper HTTP verbs, returns JSON, and is stateless (no server-side session; auth via JWT token in every request).

**Q2: What is JWT and why use it over sessions?**
> JWT (JSON Web Token) is a compact, digitally-signed token containing a JSON payload. Format: `header.payload.signature`. EduSync uses it because:
> - **Stateless** — server doesn't store session; any server instance can validate the token
> - **Scalable** — works across multiple server instances without shared session store
> - **Self-contained** — user `id` and `role` embedded in token, reducing DB lookups

**Q3: What is bcrypt and why is it better than MD5/SHA?**
> bcrypt is an **adaptive password hashing function** with a cost factor (work factor). EduSync uses `saltRounds = 12`. It's better than MD5/SHA because:
> - Each hash is unique (salt prevents rainbow table attacks)
> - Deliberately slow (cost factor makes brute-force impractical)
> - Can be made slower as hardware improves (increase rounds)

**Q4: What is Mongoose and what problem does it solve?**
> Mongoose is an **ODM (Object Document Mapper)** for MongoDB. Without it, MongoDB returns raw documents with no structure guarantee. Mongoose provides:
> - **Schemas** — define shape and validation rules
> - **Middleware hooks** — pre/post save hooks (e.g., hashing password)
> - **Instance methods** — like `comparePassword()`
> - **Query builders** — `.find()`, `.lean()`, `.select()`

**Q5: What is CORS and why is it needed?**
> CORS (Cross-Origin Resource Sharing) is a browser security mechanism that blocks requests from a different origin. Since the frontend (`:5173`) and backend (`:5000`) are on different ports, the browser sees them as different origins. `cors({ origin: "http://localhost:5173" })` tells the browser to allow these cross-origin requests.

**Q6: What is the React Context API?**
> Context API is React's built-in global state management. It avoids "prop drilling" (passing data through many nested components). `AuthContext` creates a context, `AuthProvider` provides state to all children, and `useAuth()` hook consumes it anywhere in the tree.

**Q7: What is SPA (Single Page Application)?**
> An SPA loads ONE HTML page and dynamically updates content via JavaScript. React Router handles routing client-side — URLs change but no full page reload happens. This gives a faster, app-like experience.

---

### 🟡 Code-Related Questions

**Q8: How does the like toggle work for resources?**
> `likeResource` controller:
> 1. Find resource by ID
> 2. Check if `req.user.id` is in `resource.likedBy` array
> 3. If already liked → `pull()` (remove) and decrement `resource.likes`
> 4. If not liked → `push()` and increment `resource.likes`
> 5. Save and return `{ likes, liked }`
> This prevents double-liking using MongoDB array membership check.

**Q9: Why does the file upload use raw `fetch` instead of `apiFetch`?**
> `apiFetch` sets `Content-Type: application/json` by default. File uploads require `multipart/form-data` with a browser-generated **boundary string**. If you set `Content-Type` manually for multipart, the boundary is missing and multer can't parse the file. By using raw `fetch` without setting `Content-Type`, the browser automatically sets the correct multipart header with boundary.

**Q10: What is `.lean()` in Mongoose and when should you use it?**
> `.lean()` tells Mongoose to skip creating full Mongoose Document instances and instead return plain JavaScript objects. This is faster and uses less memory. Use it when you don't need Mongoose document methods (like `.save()`), which is the case for most GET endpoints that just return data.

**Q11: How does password upgrading work in the User model?**
> The `comparePassword` method has a legacy path: if `this.password` doesn't start with `"$2"` (bcrypt prefix), it treats it as plain text. If it matches, it **re-hashes and saves** the password automatically. This allows seeded/legacy users to log in and silently upgrade to bcrypt — a transparent migration strategy.

**Q12: How does the `authMiddleware` pass user info to controllers?**
> Express request object (`req`) is passed through all middleware. `authMiddleware` adds `req.user = decoded` (the JWT payload `{ id, role }`). Controllers then access `req.user.id` to know which user is making the request.

---

### 🟢 Design Decision Questions

**Q13: Why MongoDB over a relational database?**
> Educational data (like user profiles with varying fields, activity logs, flexible resource metadata) benefits from flexible schemas. MongoDB allows adding new fields without migrations. The document model maps directly to JavaScript objects, reducing transformation overhead.

**Q14: Why Vite over Create React App (CRA)?**
> Vite uses native ES modules for dev serving (no bundling in dev), resulting in near-instant HMR (Hot Module Replacement). CRA uses Webpack which bundles everything, making large projects slow. Vite also has better TypeScript support out of the box.

**Q15: Why use React Router v7 instead of v6 or older?**
> React Router v7 introduces `createBrowserRouter` with improved data loading patterns, parallel route loading, and better TypeScript support. It also supports the `Component` property instead of `element`, which is cleaner for lazy loading.

**Q16: Why store JWT in localStorage instead of cookies?**
> For this project, localStorage is simpler to implement. However, in production, **httpOnly cookies are more secure** (not accessible to JavaScript, protecting against XSS). The current approach is fine for development and demo purposes, with the trade-off understood.

**Q17: Why concurrently? What does it do?**
> `concurrently "vite" "npm run server"` runs both frontend and backend processes in one terminal with color-coded output. Without it, you'd need two separate terminal windows, which is inconvenient during development.

---

## 📌 12. SIMPLE EXPLANATION MODE

### For a Beginner or Non-Technical Examiner

Imagine **EduSync is like a school clubhouse on the internet**, but for college students.

Here's how it works using simple analogies:

**Think of it like this:**
- 🏠 **Study Rooms** = Like joining a WhatsApp group for studying — you can see who's there and chat
- 📚 **Resources** = Like a shared Google Drive where students upload and download notes
- ⚡ **Quiz Arena** = Like Kahoot! — pick a topic, answer timed questions, get a score
- 🏆 **Leaderboard** = Like a scoreboard in a sports match — who has the most XP points wins
- 📊 **Analytics** = Like your school progress report — but in graphs and charts
- 🎮 **XP & Levels** = Like levels in a video game — study more → earn XP → level up

**How the app works behind the scenes:**

The app has two main parts:
1. **The front of the shop (Frontend)** — what you see: buttons, pages, animations. Built with React (like Lego blocks for websites)
2. **The back of the shop (Backend)** — the engine room: stores data, handles logins, manages files. Built with Node.js and Express (like a waiter who goes to the kitchen and brings back your order)
3. **The data warehouse (Database)** — MongoDB, where all student info, rooms, resources, and quiz questions are stored (like a filing cabinet)

When you click "Login":
- Your browser sends your email + password to the backend
- The backend checks if it matches what's stored (password is "scrambled" for safety)
- If correct, it gives you a special pass (JWT token) — like a wristband at a concert
- Every time you want to do something, you show this wristband
- When you click logout, the wristband is thrown away

---

## 📌 13. SUMMARY FOR QUICK REVISION

### ⏱ 1-Minute Explanation
EduSync is a full-stack collaborative learning platform built with React + TypeScript on the frontend and Node.js + Express + MongoDB on the backend. It features JWT authentication, study rooms, a resource library with file uploads, a quiz arena, a leaderboard, and analytics. The frontend communicates with the backend via a REST API using a centralized `apiFetch` wrapper that auto-attaches auth tokens. The backend follows the MVC pattern with Mongoose schemas, JWT middleware, multer for file uploads, and a global error handler.

---

### ⏱ 3-Minute Explanation
**EduSync** is a gamified online learning platform where students collaborate, compete, and share resources.

**Tech Stack:** React 18 + TypeScript (Vite) for the SPA frontend, Node.js + Express v5 for the REST API backend, and MongoDB with Mongoose ODM for the database. Authentication is handled via JWT tokens — issued on login, stored in localStorage, and sent as Bearer tokens on every authenticated request. Passwords are hashed using bcrypt with cost factor 12.

**Architecture:** Client-server REST architecture with MVC on the backend. The frontend's `apiFetch()` wrapper centralizes all API calls and auto-attaches the auth header. React Context API manages global auth state. React Router v7's `createBrowserRouter` handles client-side routing, and `AnimatePresence` from Framer Motion provides smooth page transitions.

**Key Features:**
- Authentication (register/login with JWT)
- Study Rooms (CRUD, drag-and-drop collaborative board)
- Resource Library (file upload via multer, filter, like/unlike toggle)
- Quiz Arena (subject-filtered questions, timer, XP rewards)
- Leaderboard (XP-sorted user ranking)
- Analytics (Recharts: bar, line, pie, radar charts from real DB data)
- Gamification (XP, levels, badges, streaks, confetti)

**Security:** bcrypt hashing, JWT verification middleware, CORS whitelisting, field protection on update, file type + size validation, and `.select("-password")` on all user queries.

---

### 🔑 Key Bullet Points for Last-Minute Revision

**Architecture:**
- [x] React SPA + Express REST API + MongoDB
- [x] MVC pattern on backend
- [x] Client-server architecture
- [x] JWT stateless authentication

**Frontend:**
- [x] React 18 + TypeScript + Vite
- [x] React Router v7 (createBrowserRouter)
- [x] AuthContext (Context API) for global state
- [x] apiFetch wrapper auto-injects Bearer token
- [x] Motion (Framer Motion) for animations
- [x] Recharts for analytics charts
- [x] React DnD for drag-and-drop

**Backend:**
- [x] Node.js + Express v5
- [x] 8 route groups, 8 controllers
- [x] authMiddleware → JWT verify → req.user
- [x] multer → disk storage → /uploads
- [x] global errorHandler (4-arg Express)
- [x] logger middleware

**Database:**
- [x] MongoDB local (:27017/edusync)
- [x] 6 collections: User, StudyRoom, Resource, Activity, Achievement, QuizQuestion
- [x] Mongoose ODM with schemas + pre-save hooks
- [x] .lean() for performance on GET requests

**Security:**
- [x] bcrypt (saltRounds = 12)
- [x] JWT (7-day expiry)
- [x] CORS (whitelisted origin only)
- [x] Field protection (password/email/role blocked on update)
- [x] File type + size validation (multer)

**Port Map:**
- Frontend: `localhost:5173`
- Backend: `localhost:5000`
- MongoDB: `localhost:27017`

**Run Command:** `npm run dev` (runs both via concurrently)
