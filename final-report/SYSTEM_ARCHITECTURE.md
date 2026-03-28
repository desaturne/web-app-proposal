# StudyBug - System Architecture & Technical Documentation

## Table of Contents
1. [Application Overview](#application-overview)
2. [What the App Does](#what-the-app-does)
3. [System Tracking Mechanisms](#system-tracking-mechanisms)
4. [How Data is Being Used](#how-data-is-being-used)
5. [System Architecture](#system-architecture)
6. [Flowcharts and Diagrams](#flowcharts-and-diagrams)

---

## Application Overview

**StudyBug** is a comprehensive, full-stack productivity and study management web application designed specifically for students. It combines gamification elements, task management, focused study sessions using the Pomodoro technique, and knowledge assessment tools into a unified platform.

### Core Purpose
The application aims to help students:
- Stay focused during study sessions
- Organize their academic tasks and schedules
- Track their learning progress over time
- Maintain consistency through gamification (XP, streaks, levels)
- Test knowledge through automatically generated quizzes

### Target Users
- Students at all educational levels
- Self-learners wanting to track progress
- Anyone seeking to improve productivity through structured study sessions

---

## What the App Does

### Core Modules

#### 1. Dashboard (Learning Progress Tracker)
The central hub for users featuring:
- **3-Phase Learning Progress**: Foundations & Basics, Building Momentum, Peak Mastery
- **5 Levels per Phase**: Users progress through 15 total levels
- **XP System**: Earn XP by completing quizzes and tasks
- **Streak Tracking**: Daily login/activity streaks for consistency
- **Productivity Score**: Calculated from completed tasks, focus sessions, and quiz performance
- **Quiz Generation**: Upload documents and generate MCQ quizzes automatically
- **Report Export**: Export productivity data as CSV or PDF

#### 2. Study Room (Virtual Study Environment)
An immersive, gamified study space with:
- **Pomodoro Timer**: Customizable focus sessions (default 25 minutes)
- **Break Management**: Automatic break reminders with configurable duration
- **Task Selection**: Select specific tasks to work on during focus sessions
- **Progress Tracking**: Track completed pomodoro sessions per task
- **Interactive Hotspots**: Clickable elements for Calendar, Todo, Bookshelf, Spotify, Trash
- **Video Background**: Ambient study environment
- **Focus Timeline**: View focus history by day/week/month

#### 3. Task Management (Todo)
Comprehensive task management including:
- **Task Creation**: Title, description, priority, estimated pomodoros
- **Priority Levels**: Low (1), Medium (3), High (5) with color coding
- **Status Tracking**: Pending, In Progress, Completed, Cancelled
- **Progress Percentage**: Track completion percentage for each task
- **Target Date**: Set deadlines for tasks
- **Sync with Calendar**: Tasks appear on the calendar view

#### 4. Calendar
Full-featured calendar system:
- **Monthly View**: Visual calendar grid
- **Multi-day Events**: Events spanning multiple days
- **Event Types**: Reminder, Exam, Deadline, Study Session, Other
- **Color Coding**: Different colors for different event types
- **Drag & Drop**: Reschedule tasks by dragging
- **Side Panel**: Quick access to selected date's items

#### 5. Notes
Quick note-taking system:
- **Rich Notes**: Create and manage notes
- **Image Support**: Add up to 5 images per note
- **Search**: Search through all notes
- **Color Coding**: Random pastel colors for organization

#### 6. Journal
Daily journaling feature:
- **Daily Entries**: One journal entry per day
- **Mood Tracking**: 8 mood options (Happy, Excited, Sad, Tired, Thinking, Cool, Party, Peaceful)
- **Word Count**: Automatic word count
- **Calendar Navigation**: Browse entries by date

#### 7. Bookshelf (Files)
Document management:
- **File Upload**: Store documents (PDF, TXT supported)
- **Quiz Integration**: Use documents for quiz generation
- **File Management**: View, download, delete files

#### 8. Focus Timeline
Detailed focus analytics:
- **Daily View**: Hourly breakdown of focus sessions
- **Weekly View**: Week-by-week summary
- **Monthly View**: Calendar heat map
- **Statistics**: Total focus time, sessions, productivity metrics

#### 9. Trash
Soft-delete system:
- **Restore Items**: Recover accidentally deleted items
- **Permanent Delete**: Empty trash or delete individually

#### 10. Spotify Integration
- **Music Widget**: Embedded Spotify playback for background music

---

## System Tracking Mechanisms

### 1. User Activity Tracking

```
┌─────────────────────────────────────────────────────────────────┐
│                    USER ACTIVITY TRACKING                        │
├─────────────────────────────────────────────────────────────────┤
│  • last_activity_date → Tracks daily user activity              │
│  • streak_days → Consecutive days of activity                   │
│  • xp_points → Cumulative experience points                     │
│  • Session tokens → Stored in localStorage with expiry          │
└─────────────────────────────────────────────────────────────────┘
```

### 2. Task Progress Tracking

The system tracks task progress through multiple mechanisms:

| Mechanism | Description |
|-----------|-------------|
| `completion_percentage` | 0-100% progress indicator |
| `status` | pending, in_progress, completed, cancelled |
| `estimated_minutes` | Time estimation for planning |
| `pomodoro tracking` | Sessions completed vs estimated |

### 3. Focus Session Tracking

```
Focus Session Lifecycle:
┌──────────┐    ┌────────────┐    ┌──────────┐    ┌───────────┐
│  START   │───>│ IN_PROGRESS │───>│  PAUSED  │───>│ COMPLETED │
└──────────┘    └────────────┘    └──────────┘    └───────────┘
     │                │                               │
     │                └───────────────────────────────┘
     │                        CANCELLED
     └────────────────────────────────┘
```

**Tracked Data:**
- `session_id`: Unique identifier
- `task_id`: Associated task (optional)
- `duration_minutes`: Planned duration
- `actual_time_spent`: Real time spent
- `start_time`, `end_time`: Timestamps
- `status`: Session outcome

### 4. Learning Progress Tracking

```
Learning Phases Structure:
┌─────────────────────────────────────────────────────────────┐
│  PHASE 1: Foundations & Basics                              │
│  ├── Level 1 ⭐                                              │
│  ├── Level 2 ⭐                                              │
│  ├── Level 3 ⭐                                              │
│  ├── Level 4 ○                                              │
│  └── Level 5 ○                                              │
├─────────────────────────────────────────────────────────────┤
│  PHASE 2: Building Momentum                                 │
│  └── [5 Levels - Locked until Phase 1 complete]             │
├─────────────────────────────────────────────────────────────┤
│  PHASE 3: Peak Mastery                                      │
│  └── [5 Levels - Locked until Phase 2 complete]             │
└─────────────────────────────────────────────────────────────┘
```

### 5. Productivity Calculation

The productivity score is calculated using:

```
Productivity Rate = (Completed Items / Total Items) × 100

Where Items include:
• Completed Tasks / Total Tasks
• Completed Quiz Levels / Total Levels
• Completed Focus Sessions / Total Sessions
```

### 6. Quiz Generation Mechanism

```
Document Upload Flow:
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│ Upload File │────>│ Extract Text │────>│ Parse Content   │
│ (PDF/TXT)   │     │ (pdf.js)     │     │ (Sentences)     │
└─────────────┘     └──────────────┘     └─────────────────┘
                                                 │
                                                 ▼
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│ Return MCQs │<────│ Generate     │<────│ Word Frequency  │
│ (5 questions)│     │ Questions    │     │ Analysis        │
└─────────────┘     └──────────────┘     └─────────────────┘
```

**Algorithm Steps:**
1. Extract text from uploaded PDF/TXT file
2. Split text into sentences (length > 45 chars)
3. Build word frequency map (excluding stop words)
4. Identify key terms from each sentence
5. Create fill-in-the-blank questions
6. Generate distractors from top frequent words
7. Return 5 MCQs with shuffled options

---

## How Data is Being Used

### Data Flow Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                           DATA USAGE OVERVIEW                             │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  USER INPUT                    PROCESSING                    OUTPUT       │
│  ──────────                    ──────────                    ──────       │
│  • Tasks                       • Status updates              • Dashboard  │
│  • Notes                       • Progress calculation        • Reports    │
│  • Journal entries             • XP/Streak tracking          • Timeline   │
│  • Files                       • Quiz generation             • Exports    │
│  • Calendar events             • Session management          • Analytics  │
│  • Focus sessions              • Data synchronization                     │
│                                                                           │
└──────────────────────────────────────────────────────────────────────────┘
```

### Data Storage & Retrieval

| Data Type | Storage | Usage |
|-----------|---------|-------|
| User Profile | `users` table | Authentication, personalization |
| Tasks | `tasks` table | Todo list, calendar sync, pomodoro tracking |
| Notes | `notes` table | Quick reference, search |
| Journal | `journal_entries` table | Daily reflection, mood tracking |
| Files | `files` table | Quiz generation, document storage |
| Events | `calendar_events` table | Scheduling, reminders |
| Focus Sessions | `focus_sessions` table | Analytics, productivity tracking |
| Progress | `progress` + `learning_levels` | Gamification, XP system |
| Trash | `trash` table | Soft delete, recovery |

### Data Synchronization

The system maintains data consistency across modules:

```
Task Update Flow:
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│ Update Task │────>│ Update DB    │────>│ Dispatch Event  │
│ Status      │     │              │     │ studybug:tasks- │
└─────────────┘     └──────────────┘     │ updated         │
                                         └─────────────────┘
                                                 │
                    ┌────────────────────────────┼────────────────────────┐
                    ▼                            ▼                        ▼
            ┌───────────────┐          ┌───────────────┐        ┌───────────────┐
            │ Calendar View │          │ Pomodoro View │        │ Dashboard     │
            │ Refreshes     │          │ Refreshes     │        │ Refreshes     │
            └───────────────┘          └───────────────┘        └───────────────┘
```

### Authentication & Security

```
Authentication Flow:
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│ User Login  │────>│ Supabase Auth│────>│ JWT Token       │
│ (Email/PW)  │     │ Verification │     │ Generated       │
└─────────────┘     └──────────────┘     └─────────────────┘
                                                 │
                                                 ▼
                            ┌─────────────────────────────────┐
                            │ Session stored in localStorage  │
                            │ { access_token, expires_at }    │
                            └─────────────────────────────────┘
                                                 │
                                                 ▼
                            ┌─────────────────────────────────┐
                            │ Token sent with every API call  │
                            │ Authorization: Bearer <token>   │
                            └─────────────────────────────────┘
```

---

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              STUDYBUG ARCHITECTURE                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                        FRONTEND (React 19)                          │   │
│    │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐  │   │
│    │  │Dashboard │ │StudyRoom │ │  Notes   │ │ Journal  │ │ Calendar │  │   │
│    │  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘  │   │
│    │  ┌──────────────────────────────────────────────────────────────┐   │   │
│    │  │              API Service Layer (api.js)                      │   │   │
│    │  └──────────────────────────────────────────────────────────────┘   │   │
│    │  ┌──────────────────────────────────────────────────────────────┐   │   │
│    │  │           Supabase Client (Auth) + LocalStorage              │   │   │
│    │  └──────────────────────────────────────────────────────────────┘   │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                       │
│                                      │ HTTP/REST API                         │
│                                      ▼                                       │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                        BACKEND (Node.js/Express)                    │   │
│    │  ┌──────────────────────────────────────────────────────────────┐   │   │
│    │  │                    Express Server (Port 5000)                │   │   │
│    │  └──────────────────────────────────────────────────────────────┘   │   │
│    │  ┌──────────────────────────────────────────────────────────────┐   │   │
│    │  │         Route Handlers (auth, tasks, notes, journal,         │   │   │
│    │  │         calendar, files, focus-sessions, progress, quiz)     │   │   │
│    │  └──────────────────────────────────────────────────────────────┘   │   │
│    │  ┌──────────────────────────────────────────────────────────────┐   │   │
│    │  │              Middleware (Auth, Error Handling)               │   │   │
│    │  └──────────────────────────────────────────────────────────────┘   │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                      │                                       │
│                    ┌─────────────────┼─────────────────┐                     │
│                    ▼                 ▼                 ▼                     │
│    ┌───────────────────┐ ┌───────────────────┐ ┌───────────────────┐        │
│    │   PostgreSQL DB   │ │   Supabase Auth   │ │  Supabase Storage │        │
│    │   (Data Storage)  │ │ (Authentication)  │ │   (File Storage)  │        │
│    └───────────────────┘ └───────────────────┘ └───────────────────┘        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Technology Stack

#### Frontend
| Technology | Purpose |
|------------|---------|
| React 19 | UI Framework |
| React Router DOM 7 | Client-side routing |
| Supabase JS | Authentication client |
| CSS | Styling (custom CSS files) |

#### Backend
| Technology | Purpose |
|------------|---------|
| Node.js | Runtime environment |
| Express.js 4.18 | Web framework |
| PostgreSQL | Primary database |
| pg (node-postgres) | PostgreSQL client |
| Supabase | Authentication service |
| pdf.js (pdfjs-dist) | PDF parsing for quiz generation |
| JWT | Token-based authentication |
| Helmet | Security headers |
| Morgan | Request logging |

### Database Schema

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          DATABASE ENTITY RELATIONSHIP                        │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│    ┌─────────────────┐                                                       │
│    │     users       │                                                       │
│    ├─────────────────┤                                                       │
│    │ id (PK)         │◄──────────────────────────────────────────────┐      │
│    │ supabase_user_id│                                               │      │
│    │ email           │                                               │      │
│    │ name            │                                               │      │
│    │ xp_points       │                                               │      │
│    │ streak_days     │                                               │      │
│    │ last_activity   │                                               │      │
│    └────────┬────────┘                                               │      │
│             │                                                        │      │
│    ┌────────┴────────┬───────────────────┬────────────────┬──────────┴───┐  │
│    ▼                 ▼                   ▼                ▼              ▼  │
│ ┌──────────┐   ┌──────────┐      ┌───────────┐    ┌──────────┐   ┌────────┐│
│ │  tasks   │   │  notes   │      │  journal  │    │  files   │   │ trash  ││
│ ├──────────┤   ├──────────┤      ├───────────┤    ├──────────┤   ├────────┤│
│ │ task_id  │   │ note_id  │      │ entry_id  │    │ file_id  │   │trash_id ││
│ │ user_id  │   │ user_id  │      │ user_id   │    │ user_id  │   │user_id ││
│ │ title    │   │ content  │      │ entry_date│    │ name     │   │item_data│
│ │ status   │   │ color    │      │ content   │    │ file_url │   │item_type│
│ │ priority │   │ images   │      │ mood      │    │ file_type│   └────────┘│
│ └────┬─────┘   └──────────┘      └───────────┘    └──────────┘             │
│      │                                                                      │
│      ▼                                                                      │
│ ┌──────────────┐    ┌─────────────────┐    ┌──────────────────────┐        │
│ │focus_sessions│    │calendar_events  │    │ learning_sections    │        │
│ ├──────────────┤    ├─────────────────┤    ├──────────────────────┤        │
│ │ session_id   │    │ event_id        │    │ section_id           │        │
│ │ user_id      │    │ user_id         │    │ user_id              │        │
│ │ task_id (FK) │    │ event_date      │    │ title                │        │
│ │ duration_min │    │ end_date        │    │ unit                 │        │
│ │ start_time   │    │ event_text      │    └──────────┬───────────┘        │
│ │ status       │    │ event_type      │               │                    │
│ └──────────────┘    └─────────────────┘               ▼                    │
│                                              ┌──────────────────────┐        │
│                                              │  learning_levels     │        │
│                                              ├──────────────────────┤        │
│                                              │ level_id             │        │
│                                              │ section_id (FK)      │        │
│                                              │ is_completed        │        │
│                                              │ xp_earned           │        │
│                                              └──────────────────────┘        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Flowcharts and Diagrams

### 1. System Block Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          SYSTEM BLOCK DIAGRAM                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                        PRESENTATION LAYER                            │   │
│    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│    │  │   Landing   │  │    Auth     │  │  Dashboard  │  │ StudyRoom  │  │   │
│    │  │    Page     │  │    Page     │  │    Page     │  │    Page    │  │   │
│    │  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘  │   │
│    │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌────────────┐  │   │
│    │  │    Notes    │  │   Journal   │  │  Calendar   │  │   Modals   │  │   │
│    │  │    Page     │  │    Page     │  │   Modal     │  │  (Various) │  │   │
│    │  └─────────────┘  └─────────────┘  └─────────────┘  └────────────┘  │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                        │                                     │
│                                        ▼                                     │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                          SERVICE LAYER                               │   │
│    │  ┌────────────────────────────────────────────────────────────────┐ │   │
│    │  │                    API Service (api.js)                        │ │   │
│    │  │  • authAPI  • tasksAPI  • notesAPI  • journalAPI              │ │   │
│    │  │  • calendarAPI  • filesAPI  • focusSessionsAPI                │ │   │
│    │  │  • progressAPI  • quizAPI  • trashAPI  • schedulesAPI         │ │   │
│    │  └────────────────────────────────────────────────────────────────┘ │   │
│    │  ┌────────────────────┐  ┌────────────────────┐                    │   │
│    │  │  Supabase Client   │  │   Local Storage    │                    │   │
│    │  │  (Authentication)  │  │  (Session/User)    │                    │   │
│    │  └────────────────────┘  └────────────────────┘                    │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                        │                                     │
│                                        ▼                                     │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                         APPLICATION LAYER                            │   │
│    │  ┌────────────────────────────────────────────────────────────────┐ │   │
│    │  │                  Express.js Server (Port 5000)                 │ │   │
│    │  └────────────────────────────────────────────────────────────────┘ │   │
│    │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐   │   │
│    │  │ Auth Routes │ │ Task Routes │ │Note Routes  │ │Journal Routes│  │   │
│    │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘   │   │
│    │  ┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐   │   │
│    │  │Calendar Rts │ │ File Routes │ │Focus Routes │ │ Quiz Routes │   │   │
│    │  └─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘   │   │
│    │  ┌────────────────────────────────────────────────────────────────┐ │   │
│    │  │              Middleware (Auth, Error Handler)                  │ │   │
│    │  └────────────────────────────────────────────────────────────────┘ │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                        │                                     │
│                                        ▼                                     │
│    ┌─────────────────────────────────────────────────────────────────────┐   │
│    │                           DATA LAYER                                 │   │
│    │  ┌─────────────────────┐  ┌─────────────────────┐                   │   │
│    │  │    PostgreSQL DB    │  │   Supabase Auth     │                   │   │
│    │  │  • users            │  │  • User Management  │                   │   │
│    │  │  • tasks            │  │  • Session Tokens   │                   │   │
│    │  │  • notes            │  │  • Password Reset   │                   │   │
│    │  │  • journal_entries  │  └─────────────────────┘                   │   │
│    │  │  • calendar_events  │                                            │   │
│    │  │  • files            │  ┌─────────────────────┐                   │   │
│    │  │  • focus_sessions   │  │  Supabase Storage   │                   │   │
│    │  │  • learning_sections│  │  (File Storage)     │                   │   │
│    │  │  • trash            │  └─────────────────────┘                   │   │
│    │  └─────────────────────┘                                            │   │
│    └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2. Context Diagram (Level 0 DFD)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           CONTEXT DIAGRAM                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│                        ┌──────────────────┐                                 │
│                        │                  │                                 │
│                        │     Student      │                                 │
│                        │    (User)        │                                 │
│                        │                  │                                 │
│                        └────────┬─────────┘                                 │
│                                 │                                            │
│              ┌──────────────────┼──────────────────┐                        │
│              │                  │                  │                        │
│              ▼                  ▼                  ▼                        │
│    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐             │
│    │ Login/Register  │ │ Manage Tasks    │ │ Study Sessions  │             │
│    │ Credentials     │ │ & Events        │ │ (Pomodoro)      │             │
│    └────────┬────────┘ └────────┬────────┘ └────────┬────────┘             │
│             │                   │                   │                       │
│             └───────────────────┼───────────────────┘                       │
│                                 │                                            │
│                                 ▼                                            │
│              ┌──────────────────────────────────────────┐                   │
│              │                                          │                   │
│              │              STUDYBUG SYSTEM              │                   │
│              │                                          │                   │
│              │  • User Authentication                   │                   │
│              │  • Task Management                       │                   │
│              │  • Calendar & Events                     │                   │
│              │  • Focus Session Tracking                │                   │
│              │  • Notes & Journal                       │                   │
│              │  • Quiz Generation                       │                   │
│              │  • Progress & Gamification               │                   │
│              │  • Report Export                         │                   │
│              │                                          │                   │
│              └──────────────────────────────────────────┘                   │
│                                 │                                            │
│              ┌──────────────────┼──────────────────┐                        │
│              │                  │                  │                        │
│              ▼                  ▼                  ▼                        │
│    ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐             │
│    │   User Data     │ │ Progress Reports│ │ Quiz Questions  │             │
│    │   & Statistics  │ │ (CSV/PDF)       │ │ from Documents  │             │
│    └─────────────────┘ └─────────────────┘ └─────────────────┘             │
│                                                                              │
│              External Entities:                                              │
│              ┌─────────────────┐ ┌─────────────────┐                        │
│              │  Supabase Auth  │ │  Spotify API    │                        │
│              │  (External)     │ │  (External)     │                        │
│              └─────────────────┘ └─────────────────┘                        │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 3. Data Flow Diagram (Level 1 DFD)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    DATA FLOW DIAGRAM (Level 1)                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌─────────────┐                         ┌─────────────────────────────┐   │
│  │   Student   │                         │      D1: Users Database     │   │
│  │   (User)    │                         │  (profiles, stats, XP)      │   │
│  └──────┬──────┘                         └──────────────┬──────────────┘   │
│         │                                               │                    │
│         │  ┌────────────────────────────────────────────┘                   │
│         │  │                                                                │
│         ▼  ▼                                                                │
│  ┌─────────────────┐      User Data       ┌─────────────────────────────┐   │
│  │                 │ ────────────────────>│                             │   │
│  │   P1: Process   │                      │   D2: Tasks Database        │   │
│  │ Authentication  │ <────────────────────│   (todos, status)           │   │
│  │                 │     Session Token    │                             │   │
│  └────────┬────────┘                      └──────────────┬──────────────┘   │
│           │                                               │                    │
│           │                                               │                    │
│           ▼                                               ▼                    │
│  ┌─────────────────┐                            ┌─────────────────────────┐  │
│  │   P2: Manage    │◄─────── Task Data ────────>│  D3: Calendar Events    │  │
│  │   Tasks         │                            │  (events, schedules)    │  │
│  └────────┬────────┘                            └─────────────────────────┘  │
│           │                                                                  │
│           │                                                                  │
│           ▼                                                                  │
│  ┌─────────────────┐      Session Data      ┌─────────────────────────────┐ │
│  │   P3: Track     │ ──────────────────────>│   D4: Focus Sessions       │ │
│  │ Focus Sessions  │ <──────────────────────│   (duration, timestamps)    │ │
│  │   (Pomodoro)    │      Statistics        │                             │ │
│  └────────┬────────┘                        └─────────────────────────────┘ │
│           │                                                                  │
│           ├──────────────────────┐                                          │
│           │                      │                                          │
│           ▼                      ▼                                          │
│  ┌─────────────────┐    ┌─────────────────┐                                │
│  │   P4: Manage    │    │   P5: Generate  │                                │
│  │   Notes &       │    │   Quizzes       │                                │
│  │   Journal       │    │   from Files    │                                │
│  └────────┬────────┘    └────────┬────────┘                                │
│           │                      │                                          │
│           ▼                      ▼                                          │
│  ┌─────────────────┐    ┌─────────────────────────────────┐                │
│  │ D5: Notes &     │    │   D6: Files (Documents)         │                │
│  │ Journal Entries │    │   (PDFs, TXT for quiz gen)      │                │
│  └─────────────────┘    └─────────────────────────────────┘                │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │                         P6: Calculate Progress                       │    │
│  │  ┌───────────┐    ┌───────────┐    ┌───────────┐                    │    │
│  │  │ Tasks     │    │ Focus     │    │ Quiz      │                    │    │
│  │  │ Completed │    │ Sessions  │    │ Levels    │                    │    │
│  │  └─────┬─────┘    └─────┬─────┘    └─────┬─────┘                    │    │
│  │        │                │                │                           │    │
│  │        └────────────────┼────────────────┘                           │    │
│  │                         ▼                                            │    │
│  │              ┌─────────────────────┐                                │    │
│  │              │ Productivity Score  │ ────> D7: Learning Progress    │    │
│  │              │ XP Calculation      │       (levels, streaks)        │    │
│  │              └─────────────────────┘                                │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘

Legend:
  P# = Process
  D# = Data Store
  ───> = Data Flow
```

### 4. Workflow Diagram - User Journey

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         USER WORKFLOW DIAGRAM                                │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  START                                                                       │
│    │                                                                         │
│    ▼                                                                         │
│  ┌─────────────────┐                                                        │
│  │  Landing Page   │                                                        │
│  │  (Introduction) │                                                        │
│  └────────┬────────┘                                                        │
│           │                                                                 │
│           ▼                                                                 │
│  ┌─────────────────┐     ┌─────────────────┐                               │
│  │   New User?     │──Yes─>│  Registration   │                               │
│  └────────┬────────┘     └────────┬────────┘                               │
│           │ No                    │                                         │
│           ▼                       │                                         │
│  ┌─────────────────┐              │                                         │
│  │     Login       │<─────────────┘                                         │
│  └────────┬────────┘                                                        │
│           │                                                                 │
│           ▼                                                                 │
│  ┌─────────────────┐                                                        │
│  │   Dashboard     │<────────────────────────────────────────┐              │
│  │  (Main Hub)     │                                         │              │
│  └────────┬────────┘                                         │              │
│           │                                                  │              │
│           ├─────────────────┬─────────────────┬─────────────┤              │
│           ▼                 ▼                 ▼             ▼              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ Start Quiz   │  │ View Notes   │  │ Open Journal │  │ Study Room   │   │
│  │ (Levels)     │  │              │  │              │  │              │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│         │                 │                 │                 │           │
│         ▼                 ▼                 ▼                 ▼           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │
│  │ Select File  │  │ Create/Edit  │  │ Write Entry  │  │ Select Task  │   │
│  │ for Quiz     │  │ Notes        │  │ + Mood       │  │ for Focus    │   │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘   │
│         │                 │                 │                 │           │
│         ▼                 │                 │                 ▼           │
│  ┌──────────────┐         │                 │         ┌──────────────┐    │
│  │ Answer MCQs  │         │                 │         │ Pomodoro     │    │
│  │ (5 questions)│         │                 │         │ Timer        │    │
│  └──────┬───────┘         │                 │         └──────┬───────┘    │
│         │                 │                 │                │            │
│         ▼                 │                 │                ▼            │
│  ┌──────────────┐         │                 │         ┌──────────────┐    │
│  │ Earn XP      │         │                 │         │ Break Time   │    │
│  │ + Progress   │         │                 │         │ (5 min)      │    │
│  └──────┬───────┘         │                 │         └──────┬───────┘    │
│         │                 │                 │                │            │
│         └─────────────────┴─────────────────┴────────────────┘            │
│                                   │                                         │
│                                   ▼                                         │
│                          ┌─────────────────┐                               │
│                          │   Update Stats  │                               │
│                          │   • XP          │                               │
│                          │   • Streak      │                               │
│                          │   • Productivity│                               │
│                          └────────┬────────┘                               │
│                                   │                                         │
│                                   ▼                                         │
│                          ┌─────────────────┐                               │
│                          │  Export Report  │                               │
│                          │  (CSV/PDF)?     │                               │
│                          └────────┬────────┘                               │
│                                   │                                         │
│                                   ▼                                         │
│                          ┌─────────────────┐                               │
│                          │     Logout      │                               │
│                          └────────┬────────┘                               │
│                                   │                                         │
│                                   ▼                                         │
│                                 END                                         │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 5. Sequence Diagram - Focus Session

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                 SEQUENCE DIAGRAM: FOCUS SESSION                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌───────┐          ┌───────────┐         ┌────────┐        ┌───────────┐  │
│  │ User  │          │ StudyRoom │         │   API  │        │ Database  │  │
│  └───┬───┘          └─────┬─────┘         └───┬────┘        └─────┬─────┘  │
│      │                    │                   │                   │        │
│      │  Click Start Focus │                   │                   │        │
│      │───────────────────>│                   │                   │        │
│      │                    │                   │                   │        │
│      │                    │  Select Task      │                   │        │
│      │<───────────────────│                   │                   │        │
│      │                    │                   │                   │        │
│      │  Confirm Selection │                   │                   │        │
│      │───────────────────>│                   │                   │        │
│      │                    │                   │                   │        │
│      │                    │  POST /focus-sessions/start           │        │
│      │                    │──────────────────>│                   │        │
│      │                    │                   │  INSERT session   │        │
│      │                    │                   │──────────────────>│        │
│      │                    │                   │                   │        │
│      │                    │                   │  session_id       │        │
│      │                    │                   │<──────────────────│        │
│      │                    │  session_id       │                   │        │
│      │                    │<──────────────────│                   │        │
│      │                    │                   │                   │        │
│      │  Timer Starts      │                   │                   │        │
│      │<───────────────────│                   │                   │        │
│      │                    │                   │                   │        │
│      │  ┌─────────────────────────────────────────────────────┐  │        │
│      │  │              25 MINUTE FOCUS SESSION                │  │        │
│      │  └─────────────────────────────────────────────────────┘  │        │
│      │                    │                   │                   │        │
│      │  [Optional] Pause  │                   │                   │        │
│      │───────────────────>│                   │                   │        │
│      │                    │  POST /focus-sessions/{id}/pause     │        │
│      │                    │──────────────────>│                   │        │
│      │                    │                   │  UPDATE status    │        │
│      │                    │                   │──────────────────>│        │
│      │                    │                   │                   │        │
│      │  [Optional] Resume │                   │                   │        │
│      │───────────────────>│                   │                   │        │
│      │                    │  POST /focus-sessions/{id}/resume    │        │
│      │                    │──────────────────>│                   │        │
│      │                    │                   │  UPDATE status    │        │
│      │                    │                   │──────────────────>│        │
│      │                    │                   │                   │        │
│      │  Timer Complete    │                   │                   │        │
│      │<───────────────────│                   │                   │        │
│      │                    │                   │                   │        │
│      │                    │  POST /focus-sessions/{id}/end        │        │
│      │                    │──────────────────>│                   │        │
│      │                    │                   │  UPDATE session   │        │
│      │                    │                   │──────────────────>│        │
│      │                    │                   │                   │        │
│      │                    │                   │  UPDATE task      │        │
│      │                    │                   │  (progress %)     │        │
│      │                    │                   │──────────────────>│        │
│      │                    │                   │                   │        │
│      │  Break Time (5min) │                   │                   │        │
│      │<───────────────────│                   │                   │        │
│      │                    │                   │                   │        │
│      │  ┌─────────────────────────────────────────────────────┐  │        │
│      │  │              5 MINUTE BREAK                         │  │        │
│      │  └─────────────────────────────────────────────────────┘  │        │
│      │                    │                   │                   │        │
│      │  Next Session?     │                   │                   │        │
│      │<───────────────────│                   │                   │        │
│      │                    │                   │                   │        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 6. Component Interaction Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPONENT INTERACTION DIAGRAM                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                          FRONTEND COMPONENTS                          │  │
│  │                                                                        │  │
│  │   ┌─────────────┐     ┌─────────────┐     ┌─────────────┐            │  │
│  │   │   App.js    │────>│   Router    │────>│   Routes    │            │  │
│  │   │  (Root)     │     │             │     │             │            │  │
│  │   └─────────────┘     └─────────────┘     └──────┬──────┘            │  │
│  │                                                   │                   │  │
│  │    ┌──────────────────────────────────────────────┼───────────────┐   │  │
│  │    │                      PAGES                   │               │   │  │
│  │    │   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌───┴───────┐       │   │  │
│  │    │   │ Landing  │ │   Auth   │ │Dashboard │ │ StudyRoom │       │   │  │
│  │    │   └──────────┘ └──────────┘ └──────────┘ └───────────┘       │   │  │
│  │    │   ┌──────────┐ ┌──────────┐ ┌──────────┐                     │   │  │
│  │    │   │  Notes   │ │ Journal  │ │  Modals  │                     │   │  │
│  │    │   └──────────┘ └──────────┘ └──────────┘                     │   │  │
│  │    └───────────────────────────────────────────────────────────────┘   │  │
│  │                                   │                                    │  │
│  │    ┌──────────────────────────────┼───────────────────────────────┐    │  │
│  │    │                    SHARED COMPONENTS                         │    │  │
│  │    │   ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐       │    │  │
│  │    │   │Protected │ │ Loading  │ │  Modal   │ │  Clock   │       │    │  │
│  │    │   │  Route   │ │  Screen  │ │          │ │          │       │    │  │
│  │    │   └──────────┘ └──────────┘ └──────────┘ └──────────┘       │    │  │
│  │    │   ┌──────────┐ ┌──────────┐ ┌──────────┐                     │    │  │
│  │    │   │ Hotspot  │ │   Cat    │ │ QuizModal│                     │    │  │
│  │    │   └──────────┘ └──────────┘ └──────────┘                     │    │  │
│  │    └───────────────────────────────────────────────────────────────┘    │  │
│  │                                   │                                    │  │
│  │    ┌──────────────────────────────┼───────────────────────────────┐    │  │
│  │    │                      SERVICES                                │    │  │
│  │    │   ┌──────────────────────────────────────────────────────┐  │    │  │
│  │    │   │                    api.js                            │  │    │  │
│  │    │   │  • authAPI  • tasksAPI  • notesAPI  • journalAPI    │  │    │  │
│  │    │   │  • calendarAPI  • filesAPI  • focusSessionsAPI      │  │    │  │
│  │    │   │  • progressAPI  • quizAPI  • trashAPI               │  │    │  │
│  │    │   └──────────────────────────────────────────────────────┘  │    │  │
│  │    │   ┌──────────────────────────────────────────────────────┐  │    │  │
│  │    │   │                  supabase.js                         │  │    │  │
│  │    │   │  • createClient  • auth hooks  • session management  │  │    │  │
│  │    │   └──────────────────────────────────────────────────────┘  │    │  │
│  │    └───────────────────────────────────────────────────────────────┘    │  │
│  │                                   │                                    │  │
│  └───────────────────────────────────┼────────────────────────────────────┘  │
│                                      │                                       │
│                                      │ HTTP/REST                             │
│                                      ▼                                       │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                          BACKEND ROUTES                                │  │
│  │                                                                        │  │
│  │   ┌─────────────────────────────────────────────────────────────────┐ │  │
│  │   │                      /api/*                                      │ │  │
│  │   │   ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐              │ │  │
│  │   │   │  /auth  │ │ /tasks  │ │ /notes  │ │/journal │              │ │  │
│  │   │   └─────────┘ └─────────┘ └─────────┘ └─────────┘              │ │  │
│  │   │   ┌─────────┐ ┌─────────┐ ┌─────────┐ ┌─────────┐              │ │  │
│  │   │   │/calendar│ │ /files  │ │  /quiz  │ │ /trash   │              │ │  │
│  │   │   └─────────┘ └─────────┘ └─────────┘ └─────────┘              │ │  │
│  │   │   ┌──────────────────┐ ┌──────────────────┐                    │ │  │
│  │   │   │ /focus-sessions  │ │    /progress     │                    │ │  │
│  │   │   └──────────────────┘ └──────────────────┘                    │ │  │
│  │   └─────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                        │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                      │                                       │
│                                      ▼                                       │
│  ┌───────────────────────────────────────────────────────────────────────┐  │
│  │                          DATABASE                                      │  │
│  │                                                                        │  │
│  │   ┌───────────────────────────────────────────────────────────────┐  │  │
│  │   │                      PostgreSQL                                │  │  │
│  │   │   users │ tasks │ notes │ journal_entries │ calendar_events  │  │  │
│  │   │   files │ focus_sessions │ learning_sections │ learning_levels│  │  │
│  │   │   progress │ trash │ schedules                              │  │  │
│  │   └───────────────────────────────────────────────────────────────┘  │  │
│  │                                                                        │  │
│  └───────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Summary

StudyBug is a well-architected full-stack application that follows modern web development practices:

- **Frontend**: React-based SPA with component-based architecture
- **Backend**: RESTful API with Express.js
- **Database**: PostgreSQL with normalized schema
- **Authentication**: Supabase Auth with JWT tokens
- **Real-time Sync**: Event-driven updates across components
- **Gamification**: XP, streaks, and levels for user engagement
- **Analytics**: Comprehensive tracking of focus sessions and productivity

The application successfully integrates multiple productivity tools into a cohesive study management platform, making it an effective solution for students seeking to improve their study habits and track their learning progress.