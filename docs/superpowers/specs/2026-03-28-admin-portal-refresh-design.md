# IHS Kenya — Admin Portal Refresh & Contractor Experience Capture

**Date**: 2026-03-28
**Status**: Approved
**Approach**: B — Admin Portal Refresh + Surgical Contractor Portal Edits
**Reference**: Website Developer Brief — Prequalification of Contractors (§2.0–§3.0)

---

## 1. Overview

The IHS Kenya prequalification system has a functional contractor intake portal (8-step wizard) and an admin back-office portal (5 pages). This design addresses:

1. **Missing contractor data capture** — the brief's §2.2 "Relevant Kenyan Experience" requirement is not collected.
2. **Admin portal gaps** — no project management, no role-scoped access control, no audit log, and the submission detail page has a compliance checklist that doesn't match the actual intake.
3. **Aesthetic uplift** — the admin portal gets a utilitarian-refined refresh while preserving brand DNA.

The contractor portal receives targeted edits only. The admin portal gets a full refresh with two new pages.

---

## 2. Contractor Portal Changes

### 2.1 New Step 9: Project References

**Purpose**: Capture §2.2 — minimum 3 completed Kenyan building projects in last 5 years.

**Position**: After Step 8 (NCA License), before Review. Progress bar updates to 9 steps.

**Labels**: Consent → Eligibility → Registration → KRA PIN → Tax Comp. → CR12 → NCA Reg. → NCA License → Experience → Review

**Repeatable project reference card** (minimum 3, maximum 6):

| Field | Type | Validation |
|-------|------|------------|
| Project Name | Text input | Required |
| Client / Employer | Text input | Required |
| County | Dropdown (47 Kenyan counties) | Required |
| Contract Value (KES) | Number input | Required, min 0 |
| Year Completed | Dropdown (2020–2025) | Required, within last 5 years |
| Contractor Role | Radio: Main Contractor / JV Partner / Subcontractor | Required |
| Scope Description | Textarea, 200 char max | Required |

**Behavior**:
- Cards are collapsible. First expanded, rest collapsed showing project name + year.
- "Add Project" button appears after 3rd card, hidden at 6.
- "Remove" button on cards 4–6 only.
- Validation: at least one project must have "Main Contractor" role — inline warning if none selected.
- Save Draft available, consistent with steps 4–8.

### 2.2 Projects Listing (01) — Filters

Add two filter dropdowns above project cards, alongside existing region filter:
- **NCA Category**: NCA 1 through NCA 8
- **Bid Category**: All Categories, Civil Works, MEP Services, Supply Services, Interior Works, Landscaping

Same pill-style dropdown styling as existing filters.

### 2.3 Review Page (now Step 10)

Add "Section 09: Project References" block to review summary. Shows project table (name, client, county, value, year, role) with Pass/Edit status. Pass condition: ≥3 projects, ≥1 as Main Contractor.

### 2.4 Tracking Page (14) — Fix Checklist

Replace current document verification checklist with the actual 7 documents collected:

1. Certificate of Incorporation
2. Proof of Business Address
3. KRA PIN Certificate
4. Tax Compliance Certificate
5. CR12 Extract
6. NCA Registration Certificate
7. NCA Annual Practicing License

Remove "Reference Letters/Past Projects" — project references are structured data, not uploaded documents.

### 2.5 Step Renumbering

- Steps 1–8: unchanged
- Step 9: Project References (new)
- Review becomes Step 10 (was implied after step 8)
- Confirmation page: follows review (step 10 submission)
- All wizard pages update their progress bar to reflect 9 steps.

---

## 3. Admin Portal — Submission Detail (04) Rework

### 3.1 Left Pane

Stays as-is with one addition: the "Application Context" card gets a project references count badge (e.g., "4 projects declared").

### 3.2 Center Pane

No changes. Document viewer works well.

### 3.3 Right Pane — Full Rework

Three collapsible sections replace the current flat checklist.

**Section 1: Compliance Documents (default collapsed)**
- All 7 uploaded docs listed with green checkmark + "Verified" badge.
- Each doc is clickable — loads it in center pane viewer.
- API-verified docs (KRA PIN, TCC, NCA) get an "API ✓" chip.
- Clicking the chip opens an inline expandable card:
  - Verification timestamp
  - Endpoint called (e.g., "KRA iTax Verification API")
  - Key response fields (taxpayer name, validity status, expiry)
  - "View Raw JSON" toggle for full response
- Smooth height transition on expand/collapse (250ms ease-out).

**Section 2: Project References (default expanded)**
- Contractor's declared projects in compact card list.
- Each card: project name, client, county, value, year, role.
- Left border color: navy for Main Contractor, slate for JV/Sub.
- Expanded by default — this is the qualitative evaluation section.

**Section 3: Internal Notes (default collapsed)**
- Textarea for new observations.
- Previous notes shown with timestamp + author (e.g., "Patrick Kamau · 26 Mar, 14:22").
- Append-only log — new notes don't overwrite old ones.

### 3.4 CTA Buttons (sticky bottom)

| Button | Color | Behavior |
|--------|-------|----------|
| Reject | Red bg, white text | Confirmation dialog before executing |
| Request Clarification | Blue outline, blue text | No confirmation needed |
| Approve Prequalification | Green bg, white text | Confirmation dialog before executing |

Reject and Approve confirmation: "Are you sure? This action will be logged."

Role-based visibility:
- Super Admin + Project Admin: all three buttons visible.
- Reviewer: only "Request Clarification" + "Flag" (replaces Reject). Cannot see Approve.
- External QS: no action buttons. Read-only + notes.

### 3.5 Slide-over Panel: Audit History

- Triggered by "View Audit History" button below notes section.
- Slides in from right, overlays right pane (300ms translateX + backdrop).
- Timestamped log of every action on this submission:
  - "Sarah viewed TCC · 14:02"
  - "Patrick added note · 14:15"
  - "Patrick requested clarification · 14:30"
- Each entry: avatar initials + name + action + timestamp.
- Filterable by action type (views, notes, decisions, emails).
- Close button returns to right pane.

### 3.6 Popover: Email Contractor

- Triggered by "Email Contractor" button below notes section.
- Gmail/Apollo-style compose popup, anchored bottom-right of viewport.
- Pre-filled: To (contractor email), Subject ("RE: IHS-2024-XXXX — Clarification Required").
- Body textarea with placeholder guidance.
- "Send & Log" button — sends and appends to audit history.
- Minimize/close controls. Non-blocking — reviewer can browse docs while composing.

---

## 4. Admin Portal — Submissions Inbox (03) Rework

**Remove**: Technical Rating (5-star) column.

**Add**: Status filter dropdown alongside Project and Bid Category filters. Options: All Statuses, Verified, Flagged, Pending Docs, Awaiting QS, Under Review.

**Updated table columns**:

| Column | Content |
|--------|---------|
| Checkbox | Bulk select |
| Contractor Entity | Name, Ref ID, CR12 date |
| NCA Class / Experience | NCA level, years |
| Compliance Status | Status badge |
| Project References | Count + role summary (e.g., "4 projects · Main") |
| Actions | Context-sensitive buttons |

**Role-aware action buttons**:
- Reviewers: "Start Review", "Flag", "Request Docs"
- Project Admins / Super Admins: "Start Review", "Assign to Reviewer", "Flag & Notify"

---

## 5. Admin Portal — New Page: Project Management (06)

**Sidebar position**: Between "Performance Analytics" and "Administration" divider. Icon: `lucide:folder-plus`.

### 5.1 Two-Zone Layout

**Zone A — Project List (left, ~40%)**:
- Card list sorted by creation date (newest first).
- Each card: project name, location, status badge (Draft / Open / Closed), bid category count, submission count.
- "Create New Project" button at top.
- Active card gets navy left-border highlight.

**Zone B — Project Detail (right, ~60%)**:

**Project fields**:

| Field | Type | Notes |
|-------|------|-------|
| Project Name | Text input | Required |
| Location | Text + County dropdown | e.g., "Riverside Heights, Nairobi" |
| Description | Textarea, 500 char | Brief project scope |
| Project Timeline | Two date pickers (start → end) | Informational |
| Status | Toggle: Draft / Open / Closed | Draft = not visible to contractors |

**Bid Categories sub-section**:
- "Add Bid Category" button.
- Each category is an expandable card:

| Field | Type | Notes |
|-------|------|-------|
| Category Name | Dropdown: Civil Works, MEP Services, Supply Services, Interior Works, Landscaping, Other (custom) | Required |
| Minimum NCA Class | Dropdown: NCA 1–8 | Defaults to global threshold |
| Minimum Annual Turnover (KES) | Number input | Defaults to global threshold |
| Minimum Experience (Years) | Number input | Defaults to global threshold |
| Submission Window | Two date pickers (opens → deadline) | Required |

- Collapsed summary: "Civil Works · NCA 3+ · KES 50M+ · Closes 30 Apr 2025"
- "Defaults from global settings" link pre-fills from dashboard thresholds.

**Save behavior**: "Save as Draft" and "Publish Project" buttons. Publish confirmation: "This project will be visible to contractors immediately."

**Closed projects**: Admin can close manually, or auto-closes when all deadlines pass. Greyed out in list, stops accepting submissions.

---

## 6. Admin Portal — New Page: Security & Audit (07)

**Sidebar position**: Replaces "Access Control" under Administration. Icon: `lucide:shield-check`.

### 6.1 Tab 1: User Management

**User table columns**:

| Name | Email | Role | Project Scope | Status | Actions |
|------|-------|------|--------------|--------|---------|
| Patrick Kamau | p.kamau@ihs.co.ke | Super Admin | All Projects | Active | — |
| James Omolo | j.omolo@ihs.co.ke | Project Admin | Riverside Heights | Active | Edit · Revoke |
| Sarah Njeri | s.njeri@ihs.co.ke | Reviewer | Riverside Heights → Civil Works | Active | Edit · Revoke |
| Dr. Kibet (QS) | kibet@external.co.ke | External QS | Lakefront Towers → MEP | Expires 30 Apr | Edit · Revoke |

**Role definitions**:

| Role | Access | Can Review | Can Approve | Can Reject |
|------|--------|-----------|-------------|------------|
| Super Admin | Global | Yes | Yes | Yes |
| Project Admin | Per project | Yes | Yes | Yes |
| Reviewer | Per project + per bid category | Yes | No (escalate) | No (flag only) |
| External QS | Per project + per bid (read-only + notes) | View only | No | No |

**"Invite User" modal fields**:
- Email address
- Role (dropdown: Project Admin, Reviewer, External QS)
- Project assignment (dropdown, multi-select for Project Admin)
- Bid category assignment (shown only for Reviewer and External QS)
- Access expiry (required for External QS, optional for others)

Super Admin role can only be assigned by another Super Admin. Edit opens same modal pre-filled. Revoke shows confirmation dialog and logs action.

### 6.2 Tab 2: Audit Log

**Filter bar**:
- Date range picker (defaults to last 7 days)
- User filter dropdown
- Action type filter: All, Logins, Submissions Reviewed, Approvals, Rejections, Clarification Requests, Threshold Changes, User Management, Emails Sent

**Log table columns**: Timestamp, User, Action, Detail, Target.

- Left-border color coding: green (approvals), red (rejections), amber (threshold changes), slate (other).
- System-generated entries for auto-rejections.
- 25 rows per page with pagination.
- "Export Log" button — downloads filtered results as CSV.

---

## 7. Admin Portal — Dashboard (02) Updates

**Eligibility Thresholds Manager**:
- Rename to "Global Eligibility Defaults".
- Add subtitle: "These defaults pre-fill when creating new bid categories. Per-bid overrides are set in Project Management."
- Add change logging: most recent change shown below controls (e.g., "Last changed: 26 Mar by Patrick Kamau (5yr → 7yr)") with "View full history" link to Audit Log.

**Remove**: Technical Reviewer Invitation section. Now handled in Security & Audit → User Management.

**Sidebar navigation update** (all admin pages):
- Add "Project Management" with `lucide:folder-plus` between Performance Analytics and Administration divider.
- Rename "Access Control" to "Security & Audit" with `lucide:shield-check`.

---

## 8. Admin Portal — Reports (05) Update

Fix category breakdown donut chart to use actual bid categories: Civil Works, MEP Services, Supply Services, Interior Works, Landscaping. Remove "IT & Telecom" and "Logistics".

No other changes.

---

## 9. Aesthetic Direction

**Approach**: Utilitarian-refined. Dense, readable, purposeful. Admin portal only.

### Brand DNA (unchanged)
- Navy `#00365A` — primary, sidebar, headings
- Red `#C41E3A` — destructive/urgent accent only
- Inter font family — consistent with contractor portal
- Tailwind CDN — static HTML demo
- Iconify Lucide icon set

### Color Palette Expansion
- Green `#16A34A` — approvals, verified states, Approve CTA
- Blue `#2563EB` — informational, clarification CTA, "under review" states
- Red reserved for rejection/destructive only (decoupled from brand accent in action contexts)
- Tighter slate scale: bg `#F8FAFC`, cards `#FFFFFF`, borders `#E2E8F0`

### Typography Hierarchy
- Page titles: `text-lg font-black uppercase tracking-tight`
- Section headers: `text-[10px] font-bold uppercase tracking-widest text-slate-400`
- Collapsible headers: `text-sm font-bold` with rotating chevron icon

### Motion (CSS-only)
- Collapsibles: `max-height` + `opacity` transition, 250ms ease-out
- Slide-over (audit history): `translateX` from off-screen, 300ms ease-out + backdrop
- Email popover: fade-in + `translateY`, 200ms
- Status badges: `background-color` transition
- Table row hover: subtle bg shift to slate-50
- Confirmation dialogs: backdrop fade + modal scale 95%→100%, 200ms

### Information Density
- Tighter table row padding (`py-2.5`)
- `rounded-md` on functional elements (not `rounded-lg`)
- Consistent `border-slate-200`
- Status badges: `text-[10px] font-black px-2 py-0.5 rounded` with solid backgrounds

### Explicit Exclusions
- No dark mode
- No glassmorphism, gradients, or decorative effects
- No custom fonts beyond Inter
- No skeleton loaders or spinners (static demo)
- No drag-and-drop or complex JS beyond collapsibles, slide-overs, and popovers

---

## 10. File Inventory

### Contractor Portal (edits)

**Current file-to-step mapping** (files 01–03 are pre-wizard):
- 04 = Step 1 (Consent), 05 = Step 2 (Contact), 06 = Step 3 (Registration)
- 07 = Step 4 (KRA PIN), 08 = Step 5 (TCC), 09 = Step 6 (CR12)
- 10 = Step 7 (NCA Reg), 11 = Step 8 (NCA License)
- 12 = Review, 13 = Confirmation, 14 = Tracking

**Approach**: Insert new file for Step 9, renumber review/confirmation/tracking to keep sequential ordering.

| File | Change |
|------|--------|
| `01-contractor-projects-listing.html` | Add NCA + bid category filters |
| `12-contractor-project-references.html` | **New file** — Step 9: Project References |
| `13-contractor-review.html` | **Renamed from 12**. Add project references section, update progress bar |
| `14-contractor-confirmation.html` | **Renamed from 13**. Update navigation links |
| `15-contractor-tracking.html` | **Renamed from 14**. Fix document checklist |
| All wizard pages (04–11) | Update progress bar to 9 steps, update "Next" link on file 11 to point to new file 12 |

### Admin Portal (refresh + new)
| File | Change |
|------|--------|
| `01-ihs-kenya-admin-portal-login.html` | No changes |
| `02-ihs-kenya-admin-dashboard.html` | Global defaults rename, remove reviewer invitation, sidebar nav, change logging |
| `03-admin-portal-submissions-inbox.html` | Remove star rating, add status filter, add project references column, role-aware actions |
| `04-submission-detail-3-pane.html` | Right pane rework (collapsibles, slide-over, popover, CTAs) |
| `05-reports-analytics-ihs-kenya-admin.html` | Fix category breakdown |
| `06-project-management.html` | **New file** — Project Management |
| `07-security-audit.html` | **New file** — Security & Audit |

---

## 11. Out of Scope

- Backend/API implementation (static HTML demo)
- Authentication system (login page exists as UI only)
- Real email sending (popover is UI demo)
- Real API verification responses (demo JSON data)
- Contractor portal restyling
- Dark mode
- Mobile responsiveness beyond existing breakpoints
- Production deployment
