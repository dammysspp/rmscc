# Weekly Co-Curricular Activities Reporting System

A modern, responsive, single-file HTML5/JS Web Application MVP designed for managing and tracking weekly co-curricular activities. Features email authentication, role-based access control (RBAC), multi-level reporting dashboards, live Supabase PostgreSQL synchronization, and real client-side CSV & multi-sheet Excel exports.

---

## ✨ Features

- **Email Authentication Portal**: Secure email-only access control.
- **Role-Based Access Control (RBAC)**:
  - **Club View**: Assigned club leads (e.g. Samuel Okon - ICT Lead) are automatically locked to their designated club. They can only submit reports and view history for their specific club.
  - **Admin View**: Administrators (e.g. `aderonke.aiyedun@rolemodelschool.com`) have full system privileges, access to global KPI dashboards, user permission management, and custom club management.
- **Database Synchronization**: Integrated with **Supabase (PostgreSQL)** for real-time CRUD operations on `club_reports`, `users`, and `clubs`.
- **Granular Export Capabilities**:
  - **CSV Exports**: Export joint master reports or separate club/week reports.
  - **Excel Exports (.xlsx)**: Export single-sheet spreadsheets.
  - **Club Workbooks**: Multi-sheet Excel workbook with dedicated tabs for each active club.
  - **Weekly Workbooks**: Multi-sheet Excel workbook with dedicated tabs for each reporting week/date.
- **Mobile Responsive Design**: Fully responsive layout optimized for mobile portrait, mobile landscape, tablets, and desktops using Tailwind CSS.

---

## 🚀 Quick Deployment to GitHub Pages

1. **Create a GitHub Repository**:
   - Create a new repository on [GitHub.com](https://github.com).
2. **Push Code**:
   - Push `index.html` and `README.md` to the `main` branch.
3. **Enable GitHub Pages**:
   - Go to **Repository Settings** $\rightarrow$ **Pages**.
   - Set **Source** to `Deploy from a branch` and select `main` branch / `root`.
   - Save. Your application will be live globally in ~1 minute!

---

## 🗄️ Database Setup (Supabase SQL)

Run the following SQL snippet in your Supabase SQL Editor:

```sql
-- 1. Create club_reports Table
CREATE TABLE IF NOT EXISTS public.club_reports (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ DEFAULT now(),
    club_name TEXT NOT NULL,
    reporting_date DATE NOT NULL,
    submitted_by TEXT NOT NULL,
    role_title TEXT NOT NULL,
    enrolled_members INTEGER NOT NULL,
    present_members INTEGER NOT NULL,
    absent_members INTEGER NOT NULL,
    attendance_rate NUMERIC(5, 2) NOT NULL,
    activity_title TEXT NOT NULL,
    activity_description TEXT,
    key_deliverables TEXT,
    activity_status TEXT NOT NULL,
    challenges TEXT,
    impact_level TEXT NOT NULL,
    immediate_action TEXT,
    suggestions TEXT,
    planned_activity TEXT,
    support_needed TEXT
);

-- 2. Create users Table
CREATE TABLE IF NOT EXISTS public.users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ DEFAULT now(),
    email TEXT UNIQUE NOT NULL,
    full_name TEXT NOT NULL,
    role TEXT NOT NULL DEFAULT 'member',
    role_title TEXT,
    assigned_club TEXT NOT NULL,
    priority_level INTEGER NOT NULL DEFAULT 4
);

-- 3. Create clubs Table
CREATE TABLE IF NOT EXISTS public.clubs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    created_at TIMESTAMPTZ DEFAULT now(),
    name TEXT UNIQUE NOT NULL
);

-- 4. Initial Admin Account
INSERT INTO public.users (email, full_name, role, role_title, assigned_club, priority_level)
VALUES ('aderonke.aiyedun@rolemodelschool.com', 'Aderonke Aiyedun', 'admin', 'Administrator', 'ALL', 1)
ON CONFLICT (email) DO NOTHING;

-- 5. Row Level Security Policies
ALTER TABLE public.club_reports ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.users ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.clubs ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow public read on club_reports" ON public.club_reports FOR SELECT USING (true);
CREATE POLICY "Allow public insert on club_reports" ON public.club_reports FOR INSERT WITH CHECK (true);

CREATE POLICY "Allow public read on users" ON public.users FOR SELECT USING (true);
CREATE POLICY "Allow public insert on users" ON public.users FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow public delete on users" ON public.users FOR DELETE USING (true);

CREATE POLICY "Allow public read on clubs" ON public.clubs FOR SELECT USING (true);
CREATE POLICY "Allow public insert on clubs" ON public.clubs FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow public delete on clubs" ON public.clubs FOR DELETE USING (true);
```
