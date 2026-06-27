# PRD: AI Study Planner - Personalized Study Schedule & Tracker

## 1. Problem Statement
College students spend 3-4 hours weekly manually planning what to study. 70% don't follow systematic revision, leading to last-minute stress and poor retention. No visibility on syllabus completion % until exam week.

## 2. Target Users
Primary: College students, competitive exam aspirants (18-25 age) 
Secondary: Working professionals doing certifications

## 3. Core Features - P0 Must Have
### 3.1 User Auth & Onboarding
- Sign up/login via Supabase Auth
- Set profile: Exam name, exam date, subjects, daily hours available
- RLS policies: Users can only access their own data

### 3.2 AI Syllabus Parser
- Upload PDF syllabus → Extract text via pdf-parse
- Gemini API prompt: "Extract all subjects, units, topics from this syllabus. Return JSON with {subject, units[{name, topics[], estimated_hours}]}"
- Edge case: If PDF is image-only → Show "Please upload text PDF" error

### 3.3 Smart Schedule Generator  
- Input: Topics JSON + exam_date + hours/day
- Algorithm: Distribute topics using weightage = (days_left / total_topics) * difficulty_multiplier
- Spaced repetition: Auto-schedule revision on day 1, 3, 7, 14 after first study
- Output: Day-wise task list stored in `tasks` table

### 3.4 Task Dashboard
- View: Today’s tasks grouped by subject
- Actions: Mark Pending/In Progress/Done
- Auto-reschedule: If task missed, redistribute to next 3 days with +1 priority
- Edge case: If exam_date < 7 days → Switch to "Crash Mode" = only high-weightage topics

### 3.5 Analytics Dashboard
- Metrics: Overall completion %, Subject-wise progress bars, Weekly hours chart
- Streak counter: Increment if ≥1 task done per day. Reset if missed.
- Weak area: Subjects with <50% completion within 7 days of exam

## 4. P1 Nice to Have
1. Pomodoro timer: 25-min focus sessions, auto-log to `study_sessions` table
2. Flashcard generator: Gemini API converts notes → Q&A pairs
3. Export: Download weekly plan as CSV/PDF
4. Revision alerts: Email via Resend API 1 day before scheduled revision

## 5. Database Schema - Supabase PostgreSQL
**Table: profiles**
- id uuid FK auth.users
- exam_name text
- exam_date date  
- daily_hours int

**Table: syllabus**
- id uuid PK
- user_id uuid FK profiles.id
- file_url text
- parsed_json jsonb

**Table: tasks**
- id uuid PK
- user_id uuid FK profiles.id
- subject text
- topic text
- due_date date
- status enum: pending, in_progress, done
- is_revision bool
- priority int

**Table: streaks**
- user_id uuid PK FK profiles.id
- current_streak int
- longest_streak int
- last_active_date date

## 6. API Routes - Next.js 14
1. `POST /api/parse-syllabus` → PDF → Gemini → JSON
2. `POST /api/generate-schedule` → JSON + exam_date → Insert tasks
3. `PATCH /api/task/:id` → Update status + trigger reschedule logic
4. `GET /api/analytics` → Calculate completion %, streaks from tasks table

## 7. Acceptance Criteria for MVP
1. User can signup, upload PDF, see generated 30-day plan
2. Marking task "Done" updates progress % on dashboard instantly
3. Missing a task pushes it to tomorrow automatically
4. Streak increases only if at least 1 task done per day
5. Deployed on Vercel with working Supabase RLS
