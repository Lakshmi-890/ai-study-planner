# Design: AI Study Planner

## 1. User Flow
Signup/Login → Onboarding: Set exam_date + daily_hours → Upload Syllabus PDF → AI Parsing → Auto-generated Schedule → Dashboard → Daily Task Completion → Analytics

## 2. Page List & Components

### Page: `/login` & `/signup`
- `AuthForm` - Supabase Auth with email/password
- `GoogleAuthButton` - OAuth login

### Page: `/onboarding`
- `ExamSetupForm` - Inputs: exam_name, exam_date, daily_hours
- `SubjectTags` - Add multiple subjects

### Page: `/upload`
- `FileUploader` - Drag-drop PDF, max 5MB, accepts text PDFs only
- `ParsingLoader` - Shows "AI extracting topics..." with progress
- `ErrorBanner` - For image-only PDFs: "Please upload text-based PDF"

### Page: `/dashboard`
- `StreakCalendar` - GitHub-style daily activity heatmap
- `TodayTasks` - Task cards with status: Pending/In Progress/Done
- `ProgressRing` - Overall completion % circular chart
- `QuickStats` - Cards: Total Tasks, Done, Hours Studied

### Page: `/analytics`
- `SubjectProgressBars` - Recharts horizontal bars per subject
- `WeeklyHoursChart` - Bar chart of study hours last 7 days
- `WeakTopicsAlert` - List topics <50% done within 7 days of exam

## 3. Design System - Tailwind CSS
**Colors:**
- Primary: `#3B82F6` blue-500
- Success: `#10B981` green-500 
- Warning: `#F59E0B` amber-500
- Danger: `#EF4444` red-500
- Background: `#0F172A` slate-900 dark, `#FFFFFF` light

**Fonts:** Inter for UI, JetBrains Mono for code/data

**Components:** shadcn/ui - Button, Card, Dialog, Progress, Toast

## 4. Responsive Breakpoints
Mobile: 360px, Tablet: 768px, Desktop: 1024px+
Mobile-first approach - Hamburger menu, bottom nav for dashboard
