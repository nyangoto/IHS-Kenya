# IHS Kenya Admin Portal Refresh & Contractor Experience Capture — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add contractor experience capture (step 9), refresh the admin portal with collapsible panels, audit history, email popover, project management, and role-scoped security/audit — all as static HTML demo pages.

**Architecture:** Static HTML pages using Tailwind CDN + Iconify Lucide icons + Inter font. No backend, no JS frameworks. Interactive elements (collapsibles, slide-overs, popovers, modals) use vanilla JS + CSS transitions. Admin portal gets a utilitarian-refined aesthetic refresh; contractor portal gets surgical edits only.

**Tech Stack:** HTML5, Tailwind CSS (CDN), Iconify (Lucide icon set), Inter (Google Fonts), vanilla JavaScript (inline `<script>` blocks for interactivity).

**Spec:** `docs/superpowers/specs/2026-03-28-admin-portal-refresh-design.md`

---

## Phase 1: Contractor Portal — File Renumbering & New Step

### Task 1: Rename contractor portal files to make room for Step 9

**Files:**
- Rename: `contractor-portal/12-contractor-review.html` → `contractor-portal/13-contractor-review.html`
- Rename: `contractor-portal/13-contractor-confirmation.html` → `contractor-portal/14-contractor-confirmation.html`
- Rename: `contractor-portal/14-contractor-tracking.html` → `contractor-portal/15-contractor-tracking.html`

- [ ] **Step 1: Rename files in reverse order (avoid collisions)**

```bash
cd "D:/IHS-Kenya/IHS-Kenya/contractor-portal"
mv 14-contractor-tracking.html 15-contractor-tracking.html
mv 13-contractor-confirmation.html 14-contractor-confirmation.html
mv 12-contractor-review.html 13-contractor-review.html
```

- [ ] **Step 2: Update all internal navigation links that reference the old filenames**

Files to update with find-and-replace:

In `11-contractor-nca-license.html`:
- `prefetch` href: `12-contractor-review.html` → `12-contractor-project-references.html` (will point to new step 9)
- "Next" button href: `12-contractor-review.html` → `12-contractor-project-references.html`

In `13-contractor-review.html` (formerly 12):
- `prefetch` href: `13-contractor-confirmation.html` → `14-contractor-confirmation.html`
- "Back" link: update to `12-contractor-project-references.html`
- "Submit" button href: `13-contractor-confirmation.html` → `14-contractor-confirmation.html`

In `14-contractor-confirmation.html` (formerly 13):
- Track Application button href: `14-contractor-tracking.html` → `15-contractor-tracking.html`

In `15-contractor-tracking.html` (formerly 14):
- No outbound links to update.

- [ ] **Step 3: Verify all renamed files open in browser and links work**

Open `11-contractor-nca-license.html` in browser. Click through to review → confirmation → tracking. All links should resolve (review won't work yet since file 12 doesn't exist — that's expected).

- [ ] **Step 4: Commit**

```bash
git add contractor-portal/
git commit -m "chore: renumber contractor portal files 12-14 → 13-15 for step 9 insertion"
```

---

### Task 2: Create Step 9 — Project References page

**Files:**
- Create: `contractor-portal/12-contractor-project-references.html`

This page follows the exact same layout pattern as files 04–11: sticky header, breadcrumb, progress bar, content card, footer nav.

- [ ] **Step 1: Create the file with full page structure**

Create `contractor-portal/12-contractor-project-references.html`. The page must include:

**Head section:** Same `<head>` as `11-contractor-nca-license.html` — Tailwind CDN, Iconify, Inter font, same CSS custom properties (`.brand-navy`, `.brand-bg-navy`, `.brand-red`, `.brand-bg-red`, `.text-charcoal`, `.bg-light-gray`, view transition styles). Title: `IHS Kenya | Project References`. Prefetch: `13-contractor-review.html`.

**Header:** Identical to other wizard pages — IHS Kenya logo, nav links (Home, Projects, Eligibility, Process, Support), Portal Login + Browse Projects buttons.

**Breadcrumb:** `Projects > Riverside Heights Development > Apply > Project References`

**Progress bar (9 steps):** Steps 1–8 all completed (green circles with check icons). Step 9 active (navy circle with "9"). Labels: Consent, Eligibility, Registration, KRA PIN, Tax Comp., CR12, NCA Reg., NCA License, **Experience** (active).

The progress bar HTML pattern for a completed step:
```html
<div class="flex flex-col items-center">
  <div class="w-8 h-8 rounded-full bg-green-500 text-white flex items-center justify-center text-xs font-bold"><iconify-icon icon="lucide:check" class="text-sm"></iconify-icon></div>
  <span class="text-[10px] text-green-600 mt-1.5 font-medium whitespace-nowrap">Label</span>
</div>
<div class="flex-grow h-0.5 bg-green-500 mx-1"></div>
```

The progress bar HTML pattern for the active step (step 9):
```html
<div class="flex flex-col items-center">
  <div class="w-8 h-8 rounded-full flex items-center justify-center text-xs font-bold text-white" style="background-color:#00365A">9</div>
  <span class="text-[10px] mt-1.5 font-bold whitespace-nowrap brand-navy">Experience</span>
</div>
```

**Title area:**
```html
<div class="flex items-center justify-between mb-8">
  <div>
    <h2 class="text-2xl font-black brand-navy tracking-tight">Project References</h2>
    <p class="text-sm text-gray-400">Demonstrate relevant Kenyan building experience (minimum 3 projects)</p>
  </div>
  <span class="text-xs font-bold uppercase tracking-widest brand-navy">Step 9 of 9</span>
</div>
```

**Instruction banner:**
```html
<div class="bg-blue-50 border border-blue-100 rounded-lg p-4 mb-8">
  <div class="flex items-start gap-3">
    <iconify-icon icon="lucide:info" class="text-blue-600 text-lg mt-0.5"></iconify-icon>
    <div>
      <p class="text-sm text-blue-800 font-semibold">Provide at least 3 completed building projects in Kenya within the last 5 years.</p>
      <p class="text-xs text-blue-600 mt-1">At least one project must list you as the Main Contractor. Projects outside Kenya may be listed for reference but do not count toward the minimum.</p>
    </div>
  </div>
</div>
```

**Repeatable project cards (3 initially):** Each card is a `<div>` with class `project-card` containing the 7 fields from the spec. The first card is expanded, cards 2 and 3 are collapsed (showing only project name placeholder + year).

Each expanded card:
```html
<div class="project-card bg-white border border-gray-200 rounded-lg mb-4" data-card-index="1">
  <!-- Card Header (clickable to collapse) -->
  <button type="button" onclick="toggleCard(this)" class="w-full flex items-center justify-between p-4 text-left">
    <div class="flex items-center gap-3">
      <span class="w-7 h-7 rounded-full brand-bg-navy text-white flex items-center justify-center text-xs font-bold">1</span>
      <span class="text-sm font-bold brand-navy card-title">Project 1</span>
    </div>
    <iconify-icon icon="lucide:chevron-down" class="text-gray-400 transition-transform duration-250 card-chevron"></iconify-icon>
  </button>
  <!-- Card Body -->
  <div class="card-body overflow-hidden transition-all duration-250 ease-out" style="max-height: 600px; opacity: 1;">
    <div class="px-4 pb-5 grid grid-cols-1 md:grid-cols-2 gap-5 border-t border-gray-100 pt-5">
      <!-- Project Name -->
      <div class="col-span-2">
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Project Name <span class="text-red-500">*</span></label>
        <input type="text" placeholder="e.g. Riverside Heights Phase 1" class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400">
      </div>
      <!-- Client / Employer -->
      <div>
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Client / Employer <span class="text-red-500">*</span></label>
        <input type="text" placeholder="e.g. Kenya Housing Authority" class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400">
      </div>
      <!-- County -->
      <div>
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">County <span class="text-red-500">*</span></label>
        <select class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm text-gray-700 focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400 bg-white">
          <option value="">Select county...</option>
          <option>Baringo</option><option>Bomet</option><option>Bungoma</option><option>Busia</option>
          <option>Elgeyo-Marakwet</option><option>Embu</option><option>Garissa</option><option>Homa Bay</option>
          <option>Isiolo</option><option>Kajiado</option><option>Kakamega</option><option>Kericho</option>
          <option>Kiambu</option><option>Kilifi</option><option>Kirinyaga</option><option>Kisii</option>
          <option>Kisumu</option><option>Kitui</option><option>Kwale</option><option>Laikipia</option>
          <option>Lamu</option><option>Machakos</option><option>Makueni</option><option>Mandera</option>
          <option>Marsabit</option><option>Meru</option><option>Migori</option><option>Mombasa</option>
          <option>Murang'a</option><option>Nairobi</option><option>Nakuru</option><option>Nandi</option>
          <option>Narok</option><option>Nyamira</option><option>Nyandarua</option><option>Nyeri</option>
          <option>Samburu</option><option>Siaya</option><option>Taita-Taveta</option><option>Tana River</option>
          <option>Tharaka-Nithi</option><option>Trans-Nzoia</option><option>Turkana</option><option>Uasin Gishu</option>
          <option>Vihiga</option><option>Wajir</option><option>West Pokot</option>
        </select>
      </div>
      <!-- Contract Value -->
      <div>
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Contract Value (KES) <span class="text-red-500">*</span></label>
        <input type="number" min="0" placeholder="e.g. 150000000" class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400">
      </div>
      <!-- Year Completed -->
      <div>
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Year Completed <span class="text-red-500">*</span></label>
        <select class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm text-gray-700 focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400 bg-white">
          <option value="">Select year...</option>
          <option>2025</option><option>2024</option><option>2023</option><option>2022</option><option>2021</option><option>2020</option>
        </select>
      </div>
      <!-- Contractor Role -->
      <div class="col-span-2">
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-2">Your Role <span class="text-red-500">*</span></label>
        <div class="flex gap-6">
          <label class="flex items-center gap-2 cursor-pointer">
            <input type="radio" name="role-1" value="main" class="accent-[#00365A]">
            <span class="text-sm font-medium">Main Contractor</span>
          </label>
          <label class="flex items-center gap-2 cursor-pointer">
            <input type="radio" name="role-1" value="jv" class="accent-[#00365A]">
            <span class="text-sm font-medium">JV Partner</span>
          </label>
          <label class="flex items-center gap-2 cursor-pointer">
            <input type="radio" name="role-1" value="sub" class="accent-[#00365A]">
            <span class="text-sm font-medium">Subcontractor</span>
          </label>
        </div>
      </div>
      <!-- Scope Description -->
      <div class="col-span-2">
        <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Scope Description <span class="text-red-500">*</span></label>
        <textarea maxlength="200" placeholder="Brief description of works undertaken (max 200 characters)" class="w-full px-4 py-3 border border-gray-200 rounded-lg text-sm focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400 resize-none h-20"></textarea>
        <p class="text-[10px] text-gray-400 mt-1 text-right">0 / 200</p>
      </div>
    </div>
  </div>
</div>
```

Cards 2 and 3 have collapsed body: `style="max-height: 0; opacity: 0; overflow: hidden;"` and rotated chevron `style="transform: rotate(-90deg)"`.

**"Add Project" button** (hidden initially, shown after 3 cards are present, max 6):
```html
<button type="button" id="add-project-btn" onclick="addProjectCard()" class="w-full py-3 border-2 border-dashed border-gray-200 rounded-lg text-sm font-semibold text-gray-400 hover:border-gray-300 hover:text-gray-500 transition-colors flex items-center justify-center gap-2 mb-8">
  <iconify-icon icon="lucide:plus"></iconify-icon> Add Another Project (max 6)
</button>
```

**Main Contractor validation warning** (shown/hidden dynamically):
```html
<div id="main-contractor-warning" class="bg-amber-50 border border-amber-100 rounded-lg p-3 mb-6 hidden">
  <div class="flex items-center gap-2">
    <iconify-icon icon="lucide:alert-triangle" class="text-amber-600"></iconify-icon>
    <p class="text-xs text-amber-700 font-medium">At least one project must list you as the Main Contractor to proceed.</p>
  </div>
</div>
```

**Footer navigation** (same pattern as other wizard pages):
```html
<div class="flex items-center justify-between">
  <a href="11-contractor-nca-license.html" class="text-sm font-semibold text-gray-400 hover:text-gray-600 transition-colors flex items-center gap-2">
    <iconify-icon icon="lucide:arrow-left"></iconify-icon> Back to Step 8
  </a>
  <div class="flex items-center gap-3">
    <button class="px-5 py-2.5 border-2 border-gray-100 rounded text-sm font-semibold text-gray-500 hover:border-gray-200 transition-colors">Save Draft</button>
    <a href="13-contractor-review.html" class="brand-bg-red text-white text-sm font-semibold px-6 py-2.5 rounded shadow-sm hover:shadow-md transition-all flex items-center gap-2">
      Review & Submit <iconify-icon icon="lucide:arrow-right"></iconify-icon>
    </a>
  </div>
</div>
```

**Inline JavaScript** (before `</body>`):
```html
<script>
function toggleCard(btn) {
  const card = btn.closest('.project-card');
  const body = card.querySelector('.card-body');
  const chevron = card.querySelector('.card-chevron');
  const isOpen = body.style.maxHeight !== '0px';
  body.style.maxHeight = isOpen ? '0px' : '600px';
  body.style.opacity = isOpen ? '0' : '1';
  chevron.style.transform = isOpen ? 'rotate(-90deg)' : 'rotate(0deg)';
}

let cardCount = 3;
function addProjectCard() {
  if (cardCount >= 6) return;
  cardCount++;
  // Clone card 1 pattern, update index and radio name attributes
  const container = document.getElementById('project-cards');
  const template = container.querySelector('.project-card');
  const clone = template.cloneNode(true);
  clone.setAttribute('data-card-index', cardCount);
  clone.querySelector('.card-title').textContent = 'Project ' + cardCount;
  clone.querySelector('span.rounded-full').textContent = cardCount;
  // Update radio button names to be unique per card
  clone.querySelectorAll('input[type="radio"]').forEach(r => r.name = 'role-' + cardCount);
  // Clear all input values
  clone.querySelectorAll('input[type="text"], input[type="number"], textarea').forEach(i => i.value = '');
  clone.querySelectorAll('select').forEach(s => s.selectedIndex = 0);
  clone.querySelectorAll('input[type="radio"]').forEach(r => r.checked = false);
  // Add remove button
  const removeBtn = document.createElement('button');
  removeBtn.type = 'button';
  removeBtn.className = 'text-xs text-red-500 hover:text-red-700 font-semibold ml-auto mr-2';
  removeBtn.textContent = 'Remove';
  removeBtn.onclick = function() { clone.remove(); cardCount--; updateAddBtn(); };
  clone.querySelector('button').appendChild(removeBtn);
  container.appendChild(clone);
  updateAddBtn();
}

function updateAddBtn() {
  document.getElementById('add-project-btn').style.display = cardCount >= 6 ? 'none' : 'flex';
}
</script>
```

**Footer:** Same full-width footer as other contractor portal pages.

- [ ] **Step 2: Pre-fill demo data in card 1**

Card 1 should show realistic demo data (not empty):
- Project Name: "Greenfield Residential Estate Phase 2"
- Client: "National Housing Corporation"
- County: Nairobi (selected)
- Contract Value: 280000000
- Year: 2023 (selected)
- Role: Main Contractor (checked)
- Scope: "Construction of 120-unit affordable housing complex including civil works, structural, and finishing"

Card 2 demo data (collapsed):
- Title shows "Mombasa Port Access Road Upgrade · 2022"

Card 3 demo data (collapsed):
- Title shows "Kisumu Lakeside Commercial Centre · 2024"

- [ ] **Step 3: Open in browser and verify**

Open `contractor-portal/12-contractor-project-references.html`. Verify:
- Progress bar shows 9 steps, steps 1-8 green, step 9 navy
- Card 1 expanded with demo data, cards 2-3 collapsed
- Clicking collapsed card headers expands/collapses with smooth animation
- "Add Project" button works (up to 6), "Remove" appears on cards 4+
- Back link goes to step 8, Next goes to review
- Style matches other wizard pages exactly

- [ ] **Step 4: Commit**

```bash
git add contractor-portal/12-contractor-project-references.html
git commit -m "feat: add contractor project references page (step 9)"
```

---

### Task 3: Update progress bars on all wizard pages (04–11)

**Files:**
- Modify: `contractor-portal/04-contractor-welcome-consent.html`
- Modify: `contractor-portal/05-contractor-contact-eligibility.html`
- Modify: `contractor-portal/06-contractor-business-registration.html`
- Modify: `contractor-portal/07-contractor-kra-pin.html`
- Modify: `contractor-portal/08-contractor-tcc.html`
- Modify: `contractor-portal/09-contractor-cr12.html`
- Modify: `contractor-portal/10-contractor-nca-registration.html`
- Modify: `contractor-portal/11-contractor-nca-license.html`

Each page currently shows an 8-step progress bar. All must be updated to 9 steps by adding the "Experience" step after "NCA License".

- [ ] **Step 1: On each file, add Step 9 indicator after the Step 8 indicator in the progress bar**

After the Step 8 element (NCA License), add a connector line and the Step 9 element. The step 9 element appearance depends on which page you're editing:

On pages where step 9 is **future** (files 04–11), add after the step 8 element:
```html
<div class="flex-grow h-0.5 bg-gray-200 mx-1"></div>
<!-- Step 9 - Future -->
<div class="flex flex-col items-center">
  <div class="w-8 h-8 rounded-full border-2 border-gray-200 text-gray-400 flex items-center justify-center text-xs font-bold">9</div>
  <span class="text-[10px] text-gray-400 mt-1.5 font-medium whitespace-nowrap">Experience</span>
</div>
```

Also update the "Step N of 8" text to "Step N of 9" on each page.

- [ ] **Step 2: On file 11 (NCA License), update Step 8 from "active" to remain active, and Step 9 as "future"**

File 11 is Step 8, so Step 8 stays active (navy) and Step 9 is future (gray border). The text changes from "Step 8 of 8" to "Step 8 of 9".

- [ ] **Step 3: Verify in browser**

Open each page (04 through 11) and confirm:
- Progress bar shows 9 steps
- Correct step is highlighted as active
- "Experience" label appears as the 9th step
- Step counter reads "Step N of 9"

- [ ] **Step 4: Commit**

```bash
git add contractor-portal/04-*.html contractor-portal/05-*.html contractor-portal/06-*.html contractor-portal/07-*.html contractor-portal/08-*.html contractor-portal/09-*.html contractor-portal/10-*.html contractor-portal/11-*.html
git commit -m "feat: update all wizard pages to show 9-step progress bar"
```

---

### Task 4: Add filters to Projects Listing page

**Files:**
- Modify: `contractor-portal/01-contractor-projects-listing.html`

- [ ] **Step 1: Add NCA Category and Bid Category filter dropdowns**

Find the existing filter/search area (above the project cards). Add two dropdowns alongside any existing region filter:

```html
<div class="flex flex-wrap items-center gap-4 mb-8">
  <!-- Existing region filter (if any) stays -->
  <div>
    <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">NCA Category</label>
    <select class="px-4 py-2.5 border border-gray-200 rounded-lg text-sm text-gray-700 bg-white focus:outline-none focus:border-blue-400">
      <option value="">All NCA Classes</option>
      <option>NCA 1 — Unlimited</option>
      <option>NCA 2 — Up to KES 500M</option>
      <option>NCA 3 — Up to KES 300M</option>
      <option>NCA 4 — Up to KES 200M</option>
      <option>NCA 5 — Up to KES 100M</option>
      <option>NCA 6 — Up to KES 50M</option>
      <option>NCA 7 — Up to KES 20M</option>
      <option>NCA 8 — Up to KES 10M</option>
    </select>
  </div>
  <div>
    <label class="block text-[10px] font-bold text-gray-400 uppercase tracking-widest mb-1.5">Bid Category</label>
    <select class="px-4 py-2.5 border border-gray-200 rounded-lg text-sm text-gray-700 bg-white focus:outline-none focus:border-blue-400">
      <option value="">All Categories</option>
      <option>Civil Works</option>
      <option>MEP Services</option>
      <option>Supply Services</option>
      <option>Interior Works</option>
      <option>Landscaping</option>
    </select>
  </div>
</div>
```

- [ ] **Step 2: Verify in browser**

Open `01-contractor-projects-listing.html`. Confirm dropdowns render, match existing page style, and sit naturally above the project cards.

- [ ] **Step 3: Commit**

```bash
git add contractor-portal/01-contractor-projects-listing.html
git commit -m "feat: add NCA category and bid category filters to projects listing"
```

---

### Task 5: Add Project References section to Review page

**Files:**
- Modify: `contractor-portal/13-contractor-review.html` (renamed from 12)

- [ ] **Step 1: Update progress bar to 9 steps (same pattern as Task 3) and update "Step N of N" text**

The review page isn't a numbered step — it shows all steps as completed. Add Step 9 (Experience) as a completed green step after Step 8 in the progress bar.

- [ ] **Step 2: Add Section 09 review block after the NCA Compliance section**

Find the last review section (currently "Section 07 & 08: NCA Compliance"). After it, add:

```html
<!-- Section 09: Project References -->
<div class="p-5 rounded-lg bg-white border border-gray-100">
  <div class="flex items-center justify-between mb-4">
    <div class="flex items-center gap-3">
      <div class="w-8 h-8 rounded-full bg-green-500 text-white flex items-center justify-center">
        <iconify-icon icon="lucide:check" class="text-sm"></iconify-icon>
      </div>
      <div>
        <h4 class="text-sm font-bold brand-navy">Section 09: Project References</h4>
        <p class="text-[10px] text-gray-400 uppercase tracking-widest">Kenyan Building Experience</p>
      </div>
    </div>
    <a href="12-contractor-project-references.html" class="text-xs font-bold brand-red hover:underline">Edit</a>
  </div>
  <div class="overflow-x-auto">
    <table class="w-full text-xs">
      <thead>
        <tr class="border-b border-gray-100">
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">Project</th>
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">Client</th>
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">County</th>
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">Value (KES)</th>
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">Year</th>
          <th class="text-left py-2 text-[10px] font-bold text-gray-400 uppercase tracking-widest">Role</th>
        </tr>
      </thead>
      <tbody class="text-sm">
        <tr class="border-b border-gray-50">
          <td class="py-2 font-semibold brand-navy">Greenfield Residential Estate Phase 2</td>
          <td class="py-2">National Housing Corporation</td>
          <td class="py-2">Nairobi</td>
          <td class="py-2">280,000,000</td>
          <td class="py-2">2023</td>
          <td class="py-2"><span class="px-2 py-0.5 bg-blue-50 text-blue-700 text-[10px] font-bold rounded">Main</span></td>
        </tr>
        <tr class="border-b border-gray-50">
          <td class="py-2 font-semibold brand-navy">Mombasa Port Access Road Upgrade</td>
          <td class="py-2">Kenya Ports Authority</td>
          <td class="py-2">Mombasa</td>
          <td class="py-2">145,000,000</td>
          <td class="py-2">2022</td>
          <td class="py-2"><span class="px-2 py-0.5 bg-slate-50 text-slate-600 text-[10px] font-bold rounded">JV</span></td>
        </tr>
        <tr>
          <td class="py-2 font-semibold brand-navy">Kisumu Lakeside Commercial Centre</td>
          <td class="py-2">Lake Region Economic Bloc</td>
          <td class="py-2">Kisumu</td>
          <td class="py-2">92,000,000</td>
          <td class="py-2">2024</td>
          <td class="py-2"><span class="px-2 py-0.5 bg-blue-50 text-blue-700 text-[10px] font-bold rounded">Main</span></td>
        </tr>
      </tbody>
    </table>
  </div>
  <div class="mt-3 flex items-center gap-2">
    <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
    <span class="text-xs text-green-600 font-medium">3 projects declared &bull; Main Contractor on 2 projects</span>
  </div>
</div>
```

- [ ] **Step 3: Verify in browser**

Open `13-contractor-review.html`. Confirm Section 09 appears after NCA sections, table renders correctly, and "Edit" link points to `12-contractor-project-references.html`.

- [ ] **Step 4: Commit**

```bash
git add contractor-portal/13-contractor-review.html
git commit -m "feat: add project references section to review page"
```

---

### Task 6: Fix Tracking page document checklist

**Files:**
- Modify: `contractor-portal/15-contractor-tracking.html` (renamed from 14)

- [ ] **Step 1: Replace the document verification checklist**

Find the checklist section that shows document verification statuses. Replace the items with the actual 7 documents collected. Remove "Reference Letters/Past Projects" and any other items not in the contractor intake.

The 7 items to show (with demo statuses):
```html
<!-- Document Verification Checklist -->
<div class="space-y-3">
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">Certificate of Incorporation</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">Proof of Business Address</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">KRA PIN Certificate</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">Tax Compliance Certificate</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">CR12 Extract</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:check-circle-2" class="text-green-500"></iconify-icon>
      <span class="text-sm font-medium">NCA Registration Certificate</span>
    </div>
    <span class="text-xs font-bold text-green-600">Verified</span>
  </div>
  <div class="flex items-center justify-between">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:clock" class="text-amber-500"></iconify-icon>
      <span class="text-sm font-medium">NCA Annual Practicing License</span>
    </div>
    <span class="text-xs font-bold text-amber-600">Pending</span>
  </div>
</div>
```

- [ ] **Step 2: Verify in browser**

Open `15-contractor-tracking.html`. Confirm checklist shows exactly 7 items matching the intake documents. No phantom items.

- [ ] **Step 3: Commit**

```bash
git add contractor-portal/15-contractor-tracking.html
git commit -m "fix: align tracking page checklist with actual intake documents"
```

---

## Phase 2: Admin Portal — Shared Foundation

### Task 7: Update sidebar navigation on all admin portal pages

**Files:**
- Modify: `admin-portal/02-ihs-kenya-admin-dashboard.html`
- Modify: `admin-portal/03-admin-portal-submissions-inbox.html`
- Modify: `admin-portal/04-submission-detail-3-pane.html`
- Modify: `admin-portal/05-reports-analytics-ihs-kenya-admin.html`

The sidebar `<nav>` on each admin page needs three changes:
1. Add "Project Management" link after "Performance Analytics"
2. Rename "Access Control" to "Security & Audit" and change icon
3. Update icon from `lucide:shield-half` to `lucide:shield-check`

- [ ] **Step 1: On each admin page, find the sidebar nav and apply these changes**

After the Performance Analytics link, add:
```html
<a href="06-project-management.html" class="flex items-center gap-3 px-4 py-3 sidebar-item rounded-lg text-white/70 hover:text-white transition-all group">
  <iconify-icon icon="lucide:folder-plus" class="text-xl"></iconify-icon>
  <span class="text-sm font-semibold">Project Management</span>
</a>
```

Replace the "Access Control" link:
```html
<!-- Old -->
<a href="#" class="flex items-center gap-3 px-4 py-3 sidebar-item rounded-lg text-white/70 hover:text-white transition-all group">
  <iconify-icon icon="lucide:shield-half" class="text-xl"></iconify-icon>
  <span class="text-sm font-semibold">Access Control</span>
</a>
```
```html
<!-- New -->
<a href="07-security-audit.html" class="flex items-center gap-3 px-4 py-3 sidebar-item rounded-lg text-white/70 hover:text-white transition-all group">
  <iconify-icon icon="lucide:shield-check" class="text-xl"></iconify-icon>
  <span class="text-sm font-semibold">Security & Audit</span>
</a>
```

Also replace the "Institutional Archive" dead link with "Project Management" if it exists, OR keep it and ensure Project Management is added as a new entry.

- [ ] **Step 2: Verify sidebar renders correctly on each page**

Open each admin page. Confirm sidebar shows: Operations Dashboard, Submission Pipeline (with badge), Institutional Archive, Performance Analytics, **Project Management** (new), divider, **Security & Audit** (renamed), System Settings.

- [ ] **Step 3: Commit**

```bash
git add admin-portal/02-*.html admin-portal/03-*.html admin-portal/04-*.html admin-portal/05-*.html
git commit -m "feat: update admin sidebar with project management and security & audit links"
```

---

### Task 8: Dashboard (02) — Global defaults rename + remove reviewer invitation

**Files:**
- Modify: `admin-portal/02-ihs-kenya-admin-dashboard.html`

- [ ] **Step 1: Rename "Eligibility Thresholds Manager" to "Global Eligibility Defaults"**

Find the thresholds section header. Change:
- Title: "Eligibility Thresholds Manager" → "Global Eligibility Defaults"
- Add subtitle below: `<p class="text-xs text-slate-400 mt-1">These defaults pre-fill when creating new bid categories. Per-bid overrides are set in Project Management.</p>`

- [ ] **Step 2: Add change log line below the threshold controls**

After the turnover/NCA/TCC controls, add:
```html
<div class="mt-4 pt-3 border-t border-slate-100 flex items-center justify-between">
  <p class="text-[10px] text-slate-400">Last changed: 26 Mar by Patrick Kamau (NCA Min: 5yr → 7yr)</p>
  <a href="07-security-audit.html" class="text-[10px] font-bold text-blue-600 hover:underline">View full history</a>
</div>
```

- [ ] **Step 3: Remove the Technical Reviewer Invitation section**

Find and delete the entire "Technical Reviewer Invitation" section (the section with email input, portfolio dropdown, and "Send Access Invitation" button). This functionality is now in Security & Audit → User Management.

- [ ] **Step 4: Verify in browser**

Open `02-ihs-kenya-admin-dashboard.html`. Confirm:
- Thresholds section shows new title and subtitle
- Change log line appears below controls
- "View full history" link points to `07-security-audit.html`
- Technical reviewer invitation section is gone

- [ ] **Step 5: Commit**

```bash
git add admin-portal/02-ihs-kenya-admin-dashboard.html
git commit -m "feat: rename thresholds to global defaults, add change log, remove reviewer invitation"
```

---

## Phase 3: Admin Portal — Core Page Reworks

### Task 9: Submissions Inbox (03) — Remove star rating, add status filter, add project references column

**Files:**
- Modify: `admin-portal/03-admin-portal-submissions-inbox.html`

- [ ] **Step 1: Add Status filter dropdown to the filter bar**

Find the existing filter controls (Project dropdown, Bid Category dropdown, search bar). Add a Status dropdown between the Bid Category dropdown and the search bar:

```html
<div>
  <select class="px-4 py-2.5 border border-slate-200 rounded-md text-xs font-semibold text-slate-600 bg-white focus:outline-none focus:border-blue-400">
    <option>All Statuses</option>
    <option>Verified</option>
    <option>Flagged</option>
    <option>Pending Docs</option>
    <option>Awaiting QS</option>
    <option>Under Review</option>
  </select>
</div>
```

- [ ] **Step 2: In the submissions table, remove the "Technical Rating" column**

Remove the star rating `<th>` from the header and the corresponding `<td>` from every row. This is the column showing "4.0 ★★★★☆" etc.

- [ ] **Step 3: Add "Project References" column after Compliance Status**

Add `<th>` in header:
```html
<th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">References</th>
```

Add `<td>` in each row with demo data. Use varied counts:
```html
<!-- For rows with main contractor experience -->
<td class="py-3 px-4">
  <span class="text-xs font-semibold text-slate-600">4 projects &bull; Main</span>
</td>

<!-- For rows with mixed experience -->
<td class="py-3 px-4">
  <span class="text-xs font-semibold text-slate-600">3 projects &bull; JV</span>
</td>
```

- [ ] **Step 4: Verify in browser**

Open `03-admin-portal-submissions-inbox.html`. Confirm:
- Status filter dropdown present
- No star rating column
- "References" column shows project counts
- Table still aligns properly

- [ ] **Step 5: Commit**

```bash
git add admin-portal/03-admin-portal-submissions-inbox.html
git commit -m "feat: add status filter and project references column, remove star rating from inbox"
```

---

### Task 10: Submission Detail (04) — Right pane rework with collapsible sections

**Files:**
- Modify: `admin-portal/04-submission-detail-3-pane.html`

This is the largest single task. The entire right pane (`<aside>` with class `w-[360px]`) gets rebuilt.

- [ ] **Step 1: Add collapsible CSS and JS utilities to the page**

Add to the `<style>` block:
```css
.collapsible-body {
  overflow: hidden;
  transition: max-height 250ms ease-out, opacity 250ms ease-out;
}
.collapsible-body.collapsed {
  max-height: 0 !important;
  opacity: 0;
}
.chevron-rotate {
  transition: transform 250ms ease-out;
}
.chevron-rotate.collapsed {
  transform: rotate(-90deg);
}
/* Slide-over */
.slide-over-backdrop {
  position: fixed; inset: 0; background: rgba(0,0,0,0.3);
  opacity: 0; transition: opacity 300ms ease-out; pointer-events: none; z-index: 60;
}
.slide-over-backdrop.active { opacity: 1; pointer-events: auto; }
.slide-over-panel {
  position: fixed; top: 0; right: 0; bottom: 0; width: 400px; background: white;
  transform: translateX(100%); transition: transform 300ms ease-out; z-index: 70;
  box-shadow: -4px 0 20px rgba(0,0,0,0.1);
}
.slide-over-panel.active { transform: translateX(0); }
/* Email popover */
.email-popover {
  position: fixed; bottom: 24px; right: 24px; width: 420px;
  background: white; border-radius: 12px; box-shadow: 0 8px 30px rgba(0,0,0,0.15);
  transform: translateY(20px); opacity: 0; transition: all 200ms ease-out;
  pointer-events: none; z-index: 80;
}
.email-popover.active { transform: translateY(0); opacity: 1; pointer-events: auto; }
/* Confirmation dialog */
.confirm-backdrop {
  position: fixed; inset: 0; background: rgba(0,0,0,0.4);
  opacity: 0; transition: opacity 200ms ease-out; pointer-events: none; z-index: 90;
}
.confirm-backdrop.active { opacity: 1; pointer-events: auto; }
.confirm-dialog {
  transform: scale(0.95); transition: transform 200ms ease-out;
}
.confirm-backdrop.active .confirm-dialog { transform: scale(1); }
```

Add before `</body>`:
```html
<script>
function toggleSection(id) {
  const body = document.getElementById(id + '-body');
  const chevron = document.getElementById(id + '-chevron');
  body.classList.toggle('collapsed');
  chevron.classList.toggle('collapsed');
}
function openSlideOver() {
  document.getElementById('audit-backdrop').classList.add('active');
  document.getElementById('audit-panel').classList.add('active');
}
function closeSlideOver() {
  document.getElementById('audit-backdrop').classList.remove('active');
  document.getElementById('audit-panel').classList.remove('active');
}
function toggleEmailPopover() {
  document.getElementById('email-popover').classList.toggle('active');
}
function showConfirm(action) {
  const el = document.getElementById('confirm-dialog');
  document.getElementById('confirm-action-text').textContent =
    action === 'approve' ? 'approve this contractor for prequalification' : 'reject this contractor\'s application';
  document.getElementById('confirm-btn').className = action === 'approve'
    ? 'px-6 py-2.5 rounded-md text-sm font-bold text-white bg-green-600 hover:bg-green-700'
    : 'px-6 py-2.5 rounded-md text-sm font-bold text-white bg-red-600 hover:bg-red-700';
  document.getElementById('confirm-btn').textContent = action === 'approve' ? 'Approve' : 'Reject';
  el.classList.add('active');
}
function closeConfirm() { document.getElementById('confirm-dialog').classList.remove('active'); }
</script>
```

- [ ] **Step 2: Add project references badge to left pane Application Context**

In the left pane, inside the "Application Context" card, add after the "Submitted" row:
```html
<div class="flex justify-between">
  <span class="text-[11px] text-slate-400">Experience</span>
  <span class="text-xs font-bold brand-navy">4 projects declared</span>
</div>
```

- [ ] **Step 3: Replace the entire right pane content with collapsible sections**

Replace everything inside the right `<aside>` (between the opening `<div class="p-6 ...">` and the sticky bottom actions) with:

**Section 1: Compliance Documents (collapsed by default)**
```html
<div class="mb-4">
  <button type="button" onclick="toggleSection('compliance')" class="w-full flex items-center justify-between py-3">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:clipboard-check" class="text-lg brand-navy"></iconify-icon>
      <span class="text-sm font-bold brand-navy uppercase tracking-tight">Compliance Documents</span>
      <span class="text-[10px] px-2 py-0.5 bg-emerald-50 text-emerald-700 font-bold rounded">7/7</span>
    </div>
    <iconify-icon id="compliance-chevron" icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
  </button>
  <div id="compliance-body" class="collapsible-body collapsed" style="max-height: 800px;">
    <div class="space-y-2 pb-4">
      <!-- Each document row -->
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">Certificate of Incorporation</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">Proof of Business Address</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">KRA PIN Certificate</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">API ✓</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">Tax Compliance Certificate</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">API ✓</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">CR12 Extract</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">NCA Registration Certificate</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">API ✓</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
      <div class="flex items-center justify-between p-3 rounded-md border border-emerald-100 bg-emerald-50/50 cursor-pointer hover:bg-emerald-50">
        <div class="flex items-center gap-3">
          <iconify-icon icon="lucide:check-circle-2" class="text-emerald-600"></iconify-icon>
          <span class="text-xs font-semibold text-slate-700">NCA Annual Practicing License</span>
        </div>
        <button type="button" class="text-[10px] font-bold text-slate-400 hover:text-slate-600">VIEW</button>
      </div>
    </div>
  </div>
</div>
```

**Section 2: Project References (expanded by default)**
```html
<div class="mb-4">
  <button type="button" onclick="toggleSection('references')" class="w-full flex items-center justify-between py-3 border-t border-slate-100">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:building-2" class="text-lg brand-navy"></iconify-icon>
      <span class="text-sm font-bold brand-navy uppercase tracking-tight">Project References</span>
      <span class="text-[10px] px-2 py-0.5 bg-blue-50 text-blue-700 font-bold rounded">4 Projects</span>
    </div>
    <iconify-icon id="references-chevron" icon="lucide:chevron-down" class="text-slate-400 chevron-rotate"></iconify-icon>
  </button>
  <div id="references-body" class="collapsible-body" style="max-height: 600px;">
    <div class="space-y-3 pb-4">
      <!-- Project card - Main Contractor (navy left border) -->
      <div class="p-3 rounded-md border border-slate-100 border-l-4 border-l-[#00365A]">
        <div class="flex items-center justify-between mb-1">
          <span class="text-xs font-bold brand-navy">Greenfield Residential Estate Phase 2</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-blue-50 text-blue-700 font-bold rounded">Main</span>
        </div>
        <p class="text-[11px] text-slate-500">National Housing Corporation &bull; Nairobi &bull; 2023</p>
        <p class="text-[11px] text-slate-400 mt-1">KES 280,000,000</p>
      </div>
      <!-- Project card - JV (slate left border) -->
      <div class="p-3 rounded-md border border-slate-100 border-l-4 border-l-slate-300">
        <div class="flex items-center justify-between mb-1">
          <span class="text-xs font-bold brand-navy">Mombasa Port Access Road Upgrade</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-slate-100 text-slate-600 font-bold rounded">JV</span>
        </div>
        <p class="text-[11px] text-slate-500">Kenya Ports Authority &bull; Mombasa &bull; 2022</p>
        <p class="text-[11px] text-slate-400 mt-1">KES 145,000,000</p>
      </div>
      <!-- Project card - Main Contractor -->
      <div class="p-3 rounded-md border border-slate-100 border-l-4 border-l-[#00365A]">
        <div class="flex items-center justify-between mb-1">
          <span class="text-xs font-bold brand-navy">Kisumu Lakeside Commercial Centre</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-blue-50 text-blue-700 font-bold rounded">Main</span>
        </div>
        <p class="text-[11px] text-slate-500">Lake Region Economic Bloc &bull; Kisumu &bull; 2024</p>
        <p class="text-[11px] text-slate-400 mt-1">KES 92,000,000</p>
      </div>
      <!-- Project card - Sub -->
      <div class="p-3 rounded-md border border-slate-100 border-l-4 border-l-slate-300">
        <div class="flex items-center justify-between mb-1">
          <span class="text-xs font-bold brand-navy">Nakuru CBD Mixed-Use Tower</span>
          <span class="text-[9px] px-1.5 py-0.5 bg-slate-100 text-slate-600 font-bold rounded">Sub</span>
        </div>
        <p class="text-[11px] text-slate-500">Rift Valley Developers &bull; Nakuru &bull; 2021</p>
        <p class="text-[11px] text-slate-400 mt-1">KES 65,000,000</p>
      </div>
    </div>
  </div>
</div>
```

**Section 3: Internal Notes (collapsed by default)**
```html
<div class="mb-6">
  <button type="button" onclick="toggleSection('notes')" class="w-full flex items-center justify-between py-3 border-t border-slate-100">
    <div class="flex items-center gap-2">
      <iconify-icon icon="lucide:message-square" class="text-lg brand-navy"></iconify-icon>
      <span class="text-sm font-bold brand-navy uppercase tracking-tight">Internal Notes</span>
    </div>
    <iconify-icon id="notes-chevron" icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
  </button>
  <div id="notes-body" class="collapsible-body collapsed" style="max-height: 500px;">
    <div class="pb-4">
      <!-- Previous notes (append-only log) -->
      <div class="space-y-3 mb-4">
        <div class="p-3 bg-slate-50 rounded-md">
          <div class="flex items-center gap-2 mb-1">
            <div class="w-5 h-5 rounded-full bg-slate-200 flex items-center justify-center text-[8px] font-bold text-slate-500">JO</div>
            <span class="text-[10px] font-bold text-slate-500">James Omolo</span>
            <span class="text-[10px] text-slate-400">&bull; 25 Mar, 16:40</span>
          </div>
          <p class="text-xs text-slate-600">Initial document review complete. All statutory docs verified. CR12 shows clean directorship — no flags from beneficial ownership check.</p>
        </div>
        <div class="p-3 bg-slate-50 rounded-md">
          <div class="flex items-center gap-2 mb-1">
            <div class="w-5 h-5 rounded-full brand-bg-red flex items-center justify-center text-[8px] font-bold text-white">PK</div>
            <span class="text-[10px] font-bold text-slate-500">Patrick Kamau</span>
            <span class="text-[10px] text-slate-400">&bull; 26 Mar, 10:15</span>
          </div>
          <p class="text-xs text-slate-600">Experience portfolio is strong — 3 of 4 projects as main contractor, all in housing/residential. Good fit for Riverside Heights civil works category.</p>
        </div>
      </div>
      <!-- New note input -->
      <textarea
        class="w-full h-20 p-3 bg-white border border-slate-200 rounded-md text-xs focus:outline-none focus:border-blue-400 focus:ring-1 focus:ring-blue-400 resize-none"
        placeholder="Add a note (will be timestamped and attributed to you)..."></textarea>
      <button type="button" class="mt-2 px-4 py-2 bg-slate-100 hover:bg-slate-200 rounded-md text-xs font-bold text-slate-600 transition-colors">Add Note</button>
    </div>
  </div>
</div>
```

**Action buttons below sections:**
```html
<div class="flex flex-col gap-2 mb-4">
  <button type="button" onclick="toggleEmailPopover()" class="w-full py-2.5 px-4 rounded-md border border-slate-200 text-xs font-bold brand-navy flex items-center justify-center gap-2 hover:bg-slate-50 transition-all">
    <iconify-icon icon="lucide:mail" class="text-base"></iconify-icon>
    Email Contractor
  </button>
  <button type="button" onclick="openSlideOver()" class="w-full py-2.5 px-4 rounded-md border border-slate-200 text-xs font-bold brand-navy flex items-center justify-center gap-2 hover:bg-slate-50 transition-all">
    <iconify-icon icon="lucide:history" class="text-base"></iconify-icon>
    View Audit History
  </button>
</div>
```

- [ ] **Step 4: Replace the sticky bottom CTA buttons with correct colors**

Replace the entire sticky bottom section:
```html
<div class="p-5 border-t border-slate-100 bg-slate-50 space-y-3">
  <div class="grid grid-cols-2 gap-3">
    <button type="button" onclick="showConfirm('reject')" class="py-3.5 px-2 rounded-md bg-red-600 text-white text-xs font-bold hover:bg-red-700 transition-all">
      REJECT
    </button>
    <button type="button" class="py-3.5 px-2 rounded-md bg-white border border-blue-200 text-blue-600 text-xs font-bold hover:bg-blue-50 transition-all">
      CLARIFICATION
    </button>
  </div>
  <button type="button" onclick="showConfirm('approve')" class="w-full py-3.5 rounded-md bg-green-600 text-white text-xs font-black shadow-lg shadow-green-900/20 hover:bg-green-700 active:scale-[0.98] transition-all uppercase tracking-wider">
    Approve Prequalification
  </button>
</div>
```

- [ ] **Step 5: Add the audit history slide-over panel (outside main layout, before `</body>`)**

```html
<!-- Audit History Slide-over -->
<div id="audit-backdrop" class="slide-over-backdrop" onclick="closeSlideOver()"></div>
<div id="audit-panel" class="slide-over-panel">
  <div class="h-full flex flex-col">
    <div class="p-6 border-b border-slate-100 flex items-center justify-between">
      <h3 class="text-sm font-black brand-navy uppercase tracking-tight flex items-center gap-2">
        <iconify-icon icon="lucide:history" class="text-lg"></iconify-icon> Audit History
      </h3>
      <button type="button" onclick="closeSlideOver()" class="p-2 hover:bg-slate-100 rounded-md transition-colors">
        <iconify-icon icon="lucide:x" class="text-lg text-slate-400"></iconify-icon>
      </button>
    </div>
    <div class="flex-1 overflow-y-auto p-6">
      <div class="space-y-4">
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-green-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:check" class="text-green-600 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">Patrick Kamau <span class="font-normal text-slate-400">added evaluation note</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">26 Mar 2026, 10:15 AM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:eye" class="text-blue-600 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">Patrick Kamau <span class="font-normal text-slate-400">viewed NCA Registration Certificate</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">26 Mar 2026, 10:08 AM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:eye" class="text-blue-600 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">Patrick Kamau <span class="font-normal text-slate-400">viewed Tax Compliance Certificate</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">26 Mar 2026, 10:02 AM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-slate-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:message-square" class="text-slate-500 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">James Omolo <span class="font-normal text-slate-400">added evaluation note</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">25 Mar 2026, 04:40 PM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-amber-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:mail" class="text-amber-600 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">James Omolo <span class="font-normal text-slate-400">emailed contractor — clarification on CR12</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">25 Mar 2026, 03:15 PM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-blue-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:log-in" class="text-blue-600 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">James Omolo <span class="font-normal text-slate-400">opened submission for review</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">25 Mar 2026, 02:30 PM</p>
          </div>
        </div>
        <div class="flex gap-3">
          <div class="w-8 h-8 rounded-full bg-slate-100 flex items-center justify-center flex-shrink-0">
            <iconify-icon icon="lucide:inbox" class="text-slate-500 text-sm"></iconify-icon>
          </div>
          <div>
            <p class="text-xs font-semibold text-slate-700">System <span class="font-normal text-slate-400">submission received — auto-verification passed</span></p>
            <p class="text-[10px] text-slate-400 mt-0.5">24 Oct 2024, 02:40 PM</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 6: Add the email composer popover (outside main layout, before `</body>`)**

```html
<!-- Email Popover -->
<div id="email-popover" class="email-popover">
  <div class="flex items-center justify-between px-4 py-3 border-b border-slate-100 rounded-t-xl bg-slate-50">
    <span class="text-xs font-bold brand-navy">New Message</span>
    <div class="flex items-center gap-1">
      <button type="button" onclick="toggleEmailPopover()" class="p-1 hover:bg-slate-200 rounded transition-colors">
        <iconify-icon icon="lucide:minus" class="text-sm text-slate-400"></iconify-icon>
      </button>
      <button type="button" onclick="toggleEmailPopover()" class="p-1 hover:bg-slate-200 rounded transition-colors">
        <iconify-icon icon="lucide:x" class="text-sm text-slate-400"></iconify-icon>
      </button>
    </div>
  </div>
  <div class="p-4 space-y-3">
    <div>
      <label class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">To</label>
      <input type="text" value="s.omolo@kijani.co.ke" class="w-full px-3 py-2 border border-slate-200 rounded-md text-xs mt-1 bg-slate-50" readonly>
    </div>
    <div>
      <label class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Subject</label>
      <input type="text" value="RE: IHS-2024-8756 — Clarification Required" class="w-full px-3 py-2 border border-slate-200 rounded-md text-xs mt-1 focus:outline-none focus:border-blue-400">
    </div>
    <div>
      <textarea class="w-full h-32 px-3 py-2 border border-slate-200 rounded-md text-xs focus:outline-none focus:border-blue-400 resize-none" placeholder="Type your message to the contractor..."></textarea>
    </div>
    <div class="flex items-center justify-between">
      <button type="button" class="px-5 py-2 bg-blue-600 text-white rounded-md text-xs font-bold hover:bg-blue-700 transition-colors flex items-center gap-2">
        <iconify-icon icon="lucide:send"></iconify-icon> Send & Log
      </button>
      <button type="button" onclick="toggleEmailPopover()" class="text-xs text-slate-400 hover:text-slate-600">Discard</button>
    </div>
  </div>
</div>
```

- [ ] **Step 7: Add the confirmation dialog (outside main layout, before `</body>`)**

```html
<!-- Confirmation Dialog -->
<div id="confirm-dialog" class="confirm-backdrop" onclick="closeConfirm()">
  <div class="h-full flex items-center justify-center">
    <div class="confirm-dialog bg-white rounded-xl shadow-2xl p-6 w-96" onclick="event.stopPropagation()">
      <div class="flex items-center gap-3 mb-4">
        <iconify-icon icon="lucide:alert-triangle" class="text-2xl text-amber-500"></iconify-icon>
        <h3 class="text-sm font-black brand-navy">Confirm Action</h3>
      </div>
      <p class="text-sm text-slate-600 mb-6">Are you sure you want to <span id="confirm-action-text" class="font-semibold">approve this contractor</span>? This action will be logged in the audit trail.</p>
      <div class="flex items-center justify-end gap-3">
        <button type="button" onclick="closeConfirm()" class="px-4 py-2 text-xs font-semibold text-slate-500 hover:text-slate-700">Cancel</button>
        <button type="button" id="confirm-btn" onclick="closeConfirm()" class="px-6 py-2.5 rounded-md text-sm font-bold text-white bg-green-600 hover:bg-green-700">Approve</button>
      </div>
    </div>
  </div>
</div>
```

- [ ] **Step 8: Remove the old compliance score section**

Delete the `<div>` containing the compliance score progress bar ("57%", "4 of 7 documents verified") if it wasn't already removed in Step 3.

- [ ] **Step 9: Verify in browser**

Open `04-submission-detail-3-pane.html`. Verify:
- Compliance Documents section is collapsed, clicking expands with smooth animation
- "API ✓" chips visible on KRA PIN, TCC, NCA docs
- Project References section is expanded, shows 4 project cards with navy/slate left borders
- Internal Notes section is collapsed, clicking shows previous notes + new note input
- CTA buttons: Reject (red), Clarification (blue), Approve (green)
- "Email Contractor" opens popover bottom-right with pre-filled email
- "View Audit History" opens slide-over from right with timestamped log
- Approve/Reject show confirmation dialog
- All transitions are smooth (250-300ms)
- No compliance score percentage visible

- [ ] **Step 10: Commit**

```bash
git add admin-portal/04-submission-detail-3-pane.html
git commit -m "feat: rework detail page right pane with collapsibles, audit slide-over, email popover"
```

---

### Task 11: Reports (05) — Fix category breakdown

**Files:**
- Modify: `admin-portal/05-reports-analytics-ihs-kenya-admin.html`

- [ ] **Step 1: Replace category labels in the donut chart section**

Find the chart breakdown showing "Engineering: 40%", "Civil Works: 25%", "IT & Telecom: 15%", "Logistics: 10%". Replace with:

- Civil Works: 35%
- MEP Services: 25%
- Supply Services: 18%
- Interior Works: 12%
- Landscaping: 10%

Update both the chart labels/legend and any percentage values in the HTML.

- [ ] **Step 2: Verify in browser**

Open `05-reports-analytics-ihs-kenya-admin.html`. Confirm category breakdown matches actual bid categories.

- [ ] **Step 3: Commit**

```bash
git add admin-portal/05-reports-analytics-ihs-kenya-admin.html
git commit -m "fix: align report categories with actual bid categories"
```

---

## Phase 4: Admin Portal — New Pages

### Task 12: Create Project Management page (06)

**Files:**
- Create: `admin-portal/06-project-management.html`

- [ ] **Step 1: Create the page with full structure**

The page uses the same admin portal shell: sidebar (with "Project Management" as `nav-active`), top header bar, and main content area.

**Head section:** Same as other admin pages. Title: `Project Management | IHS Kenya Admin`. Same CSS custom properties plus the collapsible styles from Task 10.

**Sidebar:** Same as updated sidebar from Task 7, with "Project Management" having `nav-active` class.

**Top header:** Same as other admin pages — date, notification bell.

**Main content — Two-zone layout:**
```html
<main class="flex-1 flex overflow-hidden">
  <!-- Zone A: Project List -->
  <aside class="w-[380px] bg-white border-r border-slate-100 flex flex-col flex-shrink-0">
    <div class="p-6 border-b border-slate-100 flex items-center justify-between">
      <h2 class="text-sm font-black brand-navy uppercase tracking-tight">Projects</h2>
      <button type="button" class="px-4 py-2 brand-bg-red text-white text-xs font-bold rounded-md hover:brightness-110 transition-all flex items-center gap-2">
        <iconify-icon icon="lucide:plus"></iconify-icon> New Project
      </button>
    </div>
    <div class="flex-1 overflow-y-auto p-4 space-y-3">
      <!-- Active project card (selected) -->
      <div class="p-4 rounded-lg border-2 border-[#00365A] bg-blue-50/30 cursor-pointer border-l-4 border-l-[#00365A]">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-sm font-bold brand-navy">Riverside Heights Development</h3>
          <span class="text-[9px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Open</span>
        </div>
        <p class="text-[11px] text-slate-500">Nairobi &bull; 5 bid categories &bull; 128 submissions</p>
      </div>
      <!-- Other project cards -->
      <div class="p-4 rounded-lg border border-slate-100 hover:border-slate-200 cursor-pointer transition-colors">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-sm font-bold brand-navy">Lakefront Office Towers</h3>
          <span class="text-[9px] px-2 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">Open</span>
        </div>
        <p class="text-[11px] text-slate-500">Kisumu &bull; 3 bid categories &bull; 45 submissions</p>
      </div>
      <div class="p-4 rounded-lg border border-slate-100 hover:border-slate-200 cursor-pointer transition-colors">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-sm font-bold brand-navy">Green Valley Estate</h3>
          <span class="text-[9px] px-2 py-0.5 bg-slate-100 text-slate-500 font-bold rounded">Draft</span>
        </div>
        <p class="text-[11px] text-slate-500">Nakuru &bull; 5 bid categories &bull; 0 submissions</p>
      </div>
      <div class="p-4 rounded-lg border border-slate-100 opacity-50 cursor-pointer">
        <div class="flex items-center justify-between mb-2">
          <h3 class="text-sm font-bold text-slate-400">TechPark Plaza</h3>
          <span class="text-[9px] px-2 py-0.5 bg-slate-100 text-slate-400 font-bold rounded">Closed</span>
        </div>
        <p class="text-[11px] text-slate-400">Nairobi &bull; 4 bid categories &bull; 67 submissions</p>
      </div>
    </div>
  </aside>

  <!-- Zone B: Project Detail -->
  <section class="flex-1 overflow-y-auto bg-slate-50 p-8">
    <div class="max-w-3xl">
      <div class="flex items-center justify-between mb-8">
        <div>
          <h2 class="text-xl font-black brand-navy">Riverside Heights Development</h2>
          <p class="text-xs text-slate-400 mt-1">Created 15 Jan 2024 &bull; Last modified 26 Mar 2026</p>
        </div>
        <span class="px-3 py-1.5 bg-emerald-100 text-emerald-700 text-xs font-bold rounded-md">Open</span>
      </div>

      <!-- Project Fields -->
      <div class="bg-white rounded-lg border border-slate-200 p-6 mb-6">
        <h3 class="text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-4">Project Details</h3>
        <div class="grid grid-cols-2 gap-5">
          <div class="col-span-2">
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Project Name</label>
            <input type="text" value="Riverside Heights Development" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
          </div>
          <div>
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Location</label>
            <input type="text" value="Riverside Heights, Nairobi" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
          </div>
          <div>
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">County</label>
            <select class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm bg-white focus:outline-none focus:border-blue-400">
              <option selected>Nairobi</option>
            </select>
          </div>
          <div class="col-span-2">
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Description</label>
            <textarea class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400 resize-none h-20">Mixed-use residential and commercial development featuring 400+ affordable housing units across 5 towers with ground-floor retail space.</textarea>
          </div>
          <div>
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Start Date</label>
            <input type="date" value="2024-06-01" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
          </div>
          <div>
            <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">End Date</label>
            <input type="date" value="2027-12-31" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
          </div>
        </div>
      </div>

      <!-- Bid Categories -->
      <div class="bg-white rounded-lg border border-slate-200 p-6 mb-6">
        <div class="flex items-center justify-between mb-4">
          <h3 class="text-[10px] font-bold text-slate-400 uppercase tracking-widest">Bid Categories</h3>
          <button type="button" class="text-xs font-bold text-blue-600 hover:text-blue-700 flex items-center gap-1">
            <iconify-icon icon="lucide:plus"></iconify-icon> Add Category
          </button>
        </div>
        <div class="space-y-3">
          <!-- Bid category card (expanded) -->
          <div class="border border-slate-200 rounded-md">
            <button type="button" onclick="toggleSection('bid-civil')" class="w-full flex items-center justify-between p-4">
              <div class="flex items-center gap-3">
                <span class="text-xs font-bold brand-navy">Civil Works</span>
                <span class="text-[10px] text-slate-400">NCA 3+ &bull; KES 50M+ &bull; 5yr+ &bull; Closes 30 Apr 2025</span>
              </div>
              <iconify-icon id="bid-civil-chevron" icon="lucide:chevron-down" class="text-slate-400 chevron-rotate"></iconify-icon>
            </button>
            <div id="bid-civil-body" class="collapsible-body" style="max-height: 400px;">
              <div class="px-4 pb-4 border-t border-slate-100 pt-4 grid grid-cols-2 gap-4">
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Category</label>
                  <select class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
                    <option selected>Civil Works</option>
                    <option>MEP Services</option><option>Supply Services</option>
                    <option>Interior Works</option><option>Landscaping</option><option>Other</option>
                  </select>
                </div>
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Min NCA Class</label>
                  <select class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
                    <option>NCA 1</option><option>NCA 2</option><option selected>NCA 3</option>
                    <option>NCA 4</option><option>NCA 5</option><option>NCA 6</option>
                    <option>NCA 7</option><option>NCA 8</option>
                  </select>
                </div>
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Min Turnover (KES)</label>
                  <input type="number" value="50000000" class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs">
                </div>
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Min Experience (Years)</label>
                  <input type="number" value="5" class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs">
                </div>
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Opens</label>
                  <input type="date" value="2025-01-15" class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs">
                </div>
                <div>
                  <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Deadline</label>
                  <input type="date" value="2025-04-30" class="w-full px-3 py-2.5 border border-slate-200 rounded-md text-xs">
                </div>
                <div class="col-span-2 flex justify-between items-center pt-2">
                  <a href="#" class="text-[10px] text-blue-600 font-semibold hover:underline">Defaults from global settings</a>
                  <button type="button" class="text-[10px] text-red-500 font-semibold hover:text-red-700">Remove Category</button>
                </div>
              </div>
            </div>
          </div>
          <!-- Collapsed bid category cards -->
          <div class="border border-slate-200 rounded-md">
            <button type="button" class="w-full flex items-center justify-between p-4">
              <div class="flex items-center gap-3">
                <span class="text-xs font-bold brand-navy">MEP Services</span>
                <span class="text-[10px] text-slate-400">NCA 4+ &bull; KES 30M+ &bull; 3yr+ &bull; Closes 30 Apr 2025</span>
              </div>
              <iconify-icon icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
            </button>
          </div>
          <div class="border border-slate-200 rounded-md">
            <button type="button" class="w-full flex items-center justify-between p-4">
              <div class="flex items-center gap-3">
                <span class="text-xs font-bold brand-navy">Supply Services</span>
                <span class="text-[10px] text-slate-400">NCA 6+ &bull; KES 10M+ &bull; 2yr+ &bull; Closes 15 May 2025</span>
              </div>
              <iconify-icon icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
            </button>
          </div>
          <div class="border border-slate-200 rounded-md">
            <button type="button" class="w-full flex items-center justify-between p-4">
              <div class="flex items-center gap-3">
                <span class="text-xs font-bold brand-navy">Interior Works</span>
                <span class="text-[10px] text-slate-400">NCA 5+ &bull; KES 20M+ &bull; 3yr+ &bull; Closes 15 May 2025</span>
              </div>
              <iconify-icon icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
            </button>
          </div>
          <div class="border border-slate-200 rounded-md">
            <button type="button" class="w-full flex items-center justify-between p-4">
              <div class="flex items-center gap-3">
                <span class="text-xs font-bold brand-navy">Landscaping & External</span>
                <span class="text-[10px] text-slate-400">NCA 7+ &bull; KES 5M+ &bull; 2yr+ &bull; Closes 30 May 2025</span>
              </div>
              <iconify-icon icon="lucide:chevron-down" class="text-slate-400 chevron-rotate collapsed"></iconify-icon>
            </button>
          </div>
        </div>
      </div>

      <!-- Save Actions -->
      <div class="flex items-center justify-end gap-3">
        <button type="button" class="px-6 py-3 border border-slate-200 rounded-md text-sm font-bold text-slate-500 hover:bg-slate-50 transition-colors">Save as Draft</button>
        <button type="button" class="px-6 py-3 bg-green-600 text-white rounded-md text-sm font-bold hover:bg-green-700 transition-colors">Publish Project</button>
      </div>
    </div>
  </section>
</main>
```

- [ ] **Step 2: Add the collapsible JS (same `toggleSection` function as Task 10)**

Include the same script block from Task 10 (toggleSection function).

- [ ] **Step 3: Verify in browser**

Open `06-project-management.html`. Verify:
- Two-zone layout: project list left, detail right
- Riverside Heights selected with navy border
- Bid categories expandable/collapsible
- Civil Works expanded showing all fields
- Other categories collapsed showing summary line
- Draft/Open/Closed badges render correctly
- Save buttons present

- [ ] **Step 4: Commit**

```bash
git add admin-portal/06-project-management.html
git commit -m "feat: add project management page with two-zone layout and bid category editor"
```

---

### Task 13: Create Security & Audit page (07)

**Files:**
- Create: `admin-portal/07-security-audit.html`

- [ ] **Step 1: Create the page with full structure**

Same admin shell. Sidebar with "Security & Audit" as `nav-active`. Top header.

**Main content — tabbed layout:**
```html
<main class="flex-1 overflow-y-auto bg-slate-50">
  <div class="p-8 max-w-6xl mx-auto">
    <h1 class="text-lg font-black brand-navy uppercase tracking-tight mb-6">Security & Audit</h1>

    <!-- Tabs -->
    <div class="flex border-b border-slate-200 mb-8">
      <button type="button" onclick="switchTab('users')" id="tab-users" class="px-6 py-3 text-sm font-bold border-b-2 border-[#00365A] brand-navy">User Management</button>
      <button type="button" onclick="switchTab('audit')" id="tab-audit" class="px-6 py-3 text-sm font-bold text-slate-400 border-b-2 border-transparent hover:text-slate-600 transition-colors">Audit Log</button>
    </div>

    <!-- Tab 1: User Management -->
    <div id="panel-users">
      <div class="flex items-center justify-between mb-6">
        <p class="text-xs text-slate-400">4 users with access</p>
        <button type="button" onclick="document.getElementById('invite-modal').classList.add('active')" class="px-4 py-2.5 brand-bg-red text-white text-xs font-bold rounded-md hover:brightness-110 transition-all flex items-center gap-2">
          <iconify-icon icon="lucide:user-plus"></iconify-icon> Invite User
        </button>
      </div>
      <div class="bg-white rounded-lg border border-slate-200 overflow-hidden">
        <table class="w-full">
          <thead class="bg-slate-50 border-b border-slate-200">
            <tr>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Name</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Email</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Role</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Project Scope</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Status</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Actions</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100">
            <tr class="hover:bg-slate-50 transition-colors">
              <td class="py-3 px-4">
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-full brand-bg-red flex items-center justify-center text-[9px] font-bold text-white">PK</div>
                  <span class="text-xs font-semibold brand-navy">Patrick Kamau</span>
                </div>
              </td>
              <td class="py-3 px-4 text-xs text-slate-500">p.kamau@ihs.co.ke</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-purple-100 text-purple-700 font-bold rounded">Super Admin</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">All Projects</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Active</span></td>
              <td class="py-3 px-4 text-xs text-slate-400">—</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors">
              <td class="py-3 px-4">
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-full bg-blue-100 flex items-center justify-center text-[9px] font-bold text-blue-700">JO</div>
                  <span class="text-xs font-semibold brand-navy">James Omolo</span>
                </div>
              </td>
              <td class="py-3 px-4 text-xs text-slate-500">j.omolo@ihs.co.ke</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">Project Admin</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Riverside Heights</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Active</span></td>
              <td class="py-3 px-4"><span class="text-xs font-bold text-blue-600 hover:underline cursor-pointer">Edit</span> &bull; <span class="text-xs font-bold text-red-500 hover:underline cursor-pointer">Revoke</span></td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors">
              <td class="py-3 px-4">
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-full bg-emerald-100 flex items-center justify-center text-[9px] font-bold text-emerald-700">SN</div>
                  <span class="text-xs font-semibold brand-navy">Sarah Njeri</span>
                </div>
              </td>
              <td class="py-3 px-4 text-xs text-slate-500">s.njeri@ihs.co.ke</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-amber-100 text-amber-700 font-bold rounded">Reviewer</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Riverside Heights → Civil Works</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Active</span></td>
              <td class="py-3 px-4"><span class="text-xs font-bold text-blue-600 hover:underline cursor-pointer">Edit</span> &bull; <span class="text-xs font-bold text-red-500 hover:underline cursor-pointer">Revoke</span></td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors">
              <td class="py-3 px-4">
                <div class="flex items-center gap-2">
                  <div class="w-7 h-7 rounded-full bg-slate-100 flex items-center justify-center text-[9px] font-bold text-slate-500">DK</div>
                  <span class="text-xs font-semibold brand-navy">Dr. Kibet (QS)</span>
                </div>
              </td>
              <td class="py-3 px-4 text-xs text-slate-500">kibet@external.co.ke</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-slate-100 text-slate-600 font-bold rounded">External QS</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Lakefront Towers → MEP</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-amber-100 text-amber-700 font-bold rounded">Expires 30 Apr</span></td>
              <td class="py-3 px-4"><span class="text-xs font-bold text-blue-600 hover:underline cursor-pointer">Edit</span> &bull; <span class="text-xs font-bold text-red-500 hover:underline cursor-pointer">Revoke</span></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <!-- Tab 2: Audit Log (hidden by default) -->
    <div id="panel-audit" class="hidden">
      <!-- Filter bar -->
      <div class="flex flex-wrap items-end gap-4 mb-6">
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Date Range</label>
          <div class="flex items-center gap-2">
            <input type="date" value="2026-03-21" class="px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
            <span class="text-xs text-slate-400">to</span>
            <input type="date" value="2026-03-28" class="px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
          </div>
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">User</label>
          <select class="px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
            <option>All Users</option>
            <option>Patrick Kamau</option><option>James Omolo</option><option>Sarah Njeri</option><option>System</option>
          </select>
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Action Type</label>
          <select class="px-3 py-2.5 border border-slate-200 rounded-md text-xs bg-white">
            <option>All Actions</option>
            <option>Logins</option><option>Submissions Reviewed</option><option>Approvals</option>
            <option>Rejections</option><option>Clarification Requests</option><option>Threshold Changes</option>
            <option>User Management</option><option>Emails Sent</option>
          </select>
        </div>
        <button type="button" class="px-4 py-2.5 bg-slate-100 hover:bg-slate-200 rounded-md text-xs font-bold text-slate-600 transition-colors flex items-center gap-2">
          <iconify-icon icon="lucide:download"></iconify-icon> Export Log
        </button>
      </div>

      <!-- Audit log table -->
      <div class="bg-white rounded-lg border border-slate-200 overflow-hidden">
        <table class="w-full">
          <thead class="bg-slate-50 border-b border-slate-200">
            <tr>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Timestamp</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">User</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Action</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Detail</th>
              <th class="text-left py-3 px-4 text-[10px] font-bold text-slate-400 uppercase tracking-widest">Target</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-slate-100">
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-green-500">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">26 Mar, 14:32</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">Patrick Kamau</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Approved</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Prequalification approved</td>
              <td class="py-3 px-4 text-xs text-slate-500">IHS-2024-8842 (Construct Kenya Ltd)</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-slate-200">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">26 Mar, 14:15</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">Patrick Kamau</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-slate-100 text-slate-600 font-bold rounded">Note Added</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">"TCC verified against iTax"</td>
              <td class="py-3 px-4 text-xs text-slate-500">IHS-2024-8756 (Kijani Infrastructure)</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-amber-400">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">26 Mar, 11:00</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">James Omolo</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-amber-100 text-amber-700 font-bold rounded">Threshold Changed</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">NCA Min Experience: 5yr → 7yr</td>
              <td class="py-3 px-4 text-xs text-slate-500">Riverside Heights → Civil Works</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-red-400">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">26 Mar, 09:45</td>
              <td class="py-3 px-4 text-xs font-semibold text-slate-400">System</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-red-100 text-red-700 font-bold rounded">Auto-Rejected</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">TCC expired</td>
              <td class="py-3 px-4 text-xs text-slate-500">IHS-2024-9501 (Mount Kenya Buildcon)</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-slate-200">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">25 Mar, 16:20</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">Patrick Kamau</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">User Invited</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Reviewer role granted</td>
              <td class="py-3 px-4 text-xs text-slate-500">s.njeri@ihs.co.ke</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-green-500">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">25 Mar, 15:45</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">James Omolo</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-emerald-100 text-emerald-700 font-bold rounded">Approved</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Prequalification approved</td>
              <td class="py-3 px-4 text-xs text-slate-500">IHS-2024-9244 (Mabati & Stone Works)</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-slate-200">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">25 Mar, 14:10</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">James Omolo</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-blue-100 text-blue-700 font-bold rounded">Email Sent</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Clarification request — CR12 update needed</td>
              <td class="py-3 px-4 text-xs text-slate-500">IHS-2024-8756 (Kijani Infrastructure)</td>
            </tr>
            <tr class="hover:bg-slate-50 transition-colors border-l-4 border-l-slate-200">
              <td class="py-3 px-4 text-xs text-slate-500 whitespace-nowrap">25 Mar, 09:00</td>
              <td class="py-3 px-4 text-xs font-semibold brand-navy">Patrick Kamau</td>
              <td class="py-3 px-4"><span class="text-[10px] px-2 py-0.5 bg-slate-100 text-slate-600 font-bold rounded">Login</span></td>
              <td class="py-3 px-4 text-xs text-slate-500">Google Workspace SSO</td>
              <td class="py-3 px-4 text-xs text-slate-500">—</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="flex items-center justify-between mt-4">
        <p class="text-xs text-slate-400">Showing 1–8 of 142 entries</p>
        <div class="flex items-center gap-2">
          <button class="px-3 py-1.5 border border-slate-200 rounded-md text-xs font-semibold text-slate-400">Previous</button>
          <button class="px-3 py-1.5 brand-bg-navy text-white rounded-md text-xs font-bold">1</button>
          <button class="px-3 py-1.5 border border-slate-200 rounded-md text-xs font-semibold text-slate-500 hover:bg-slate-50">2</button>
          <button class="px-3 py-1.5 border border-slate-200 rounded-md text-xs font-semibold text-slate-500 hover:bg-slate-50">3</button>
          <button class="px-3 py-1.5 border border-slate-200 rounded-md text-xs font-semibold text-slate-500 hover:bg-slate-50">Next</button>
        </div>
      </div>
    </div>
  </div>
</main>
```

**Invite User modal** (before `</body>`):
```html
<!-- Invite User Modal -->
<div id="invite-modal" class="confirm-backdrop" onclick="this.classList.remove('active')">
  <div class="h-full flex items-center justify-center">
    <div class="confirm-dialog bg-white rounded-xl shadow-2xl p-6 w-[440px]" onclick="event.stopPropagation()">
      <h3 class="text-sm font-black brand-navy mb-6 flex items-center gap-2">
        <iconify-icon icon="lucide:user-plus" class="text-lg"></iconify-icon> Invite User
      </h3>
      <div class="space-y-4">
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Email Address</label>
          <input type="email" placeholder="colleague@ihs.co.ke" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Role</label>
          <select class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm bg-white focus:outline-none focus:border-blue-400">
            <option value="">Select role...</option>
            <option>Project Admin</option>
            <option>Reviewer</option>
            <option>External QS</option>
          </select>
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Project Assignment</label>
          <select class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm bg-white focus:outline-none focus:border-blue-400">
            <option value="">Select project...</option>
            <option>Riverside Heights Development</option>
            <option>Lakefront Office Towers</option>
            <option>Green Valley Estate</option>
          </select>
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Bid Category <span class="font-normal text-slate-300">(Reviewer & External QS only)</span></label>
          <select class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm bg-white focus:outline-none focus:border-blue-400">
            <option value="">All categories</option>
            <option>Civil Works</option><option>MEP Services</option><option>Supply Services</option>
            <option>Interior Works</option><option>Landscaping</option>
          </select>
        </div>
        <div>
          <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-widest mb-1.5">Access Expiry <span class="font-normal text-slate-300">(required for External QS)</span></label>
          <input type="date" class="w-full px-4 py-3 border border-slate-200 rounded-md text-sm focus:outline-none focus:border-blue-400">
        </div>
      </div>
      <div class="flex items-center justify-end gap-3 mt-6">
        <button type="button" onclick="document.getElementById('invite-modal').classList.remove('active')" class="px-4 py-2 text-xs font-semibold text-slate-500 hover:text-slate-700">Cancel</button>
        <button type="button" class="px-6 py-2.5 brand-bg-red text-white rounded-md text-sm font-bold hover:brightness-110">Send Invitation</button>
      </div>
    </div>
  </div>
</div>
```

**Tab switching JS** (before `</body>`):
```html
<script>
function switchTab(tab) {
  document.getElementById('panel-users').classList.toggle('hidden', tab !== 'users');
  document.getElementById('panel-audit').classList.toggle('hidden', tab !== 'audit');
  document.getElementById('tab-users').classList.toggle('border-[#00365A]', tab === 'users');
  document.getElementById('tab-users').classList.toggle('brand-navy', tab === 'users');
  document.getElementById('tab-users').classList.toggle('border-transparent', tab !== 'users');
  document.getElementById('tab-users').classList.toggle('text-slate-400', tab !== 'users');
  document.getElementById('tab-audit').classList.toggle('border-[#00365A]', tab === 'audit');
  document.getElementById('tab-audit').classList.toggle('brand-navy', tab === 'audit');
  document.getElementById('tab-audit').classList.toggle('border-transparent', tab !== 'audit');
  document.getElementById('tab-audit').classList.toggle('text-slate-400', tab !== 'audit');
}
</script>
```

Include the same `confirm-backdrop` / `confirm-dialog` CSS from Task 10 in the style block.

- [ ] **Step 2: Verify in browser**

Open `07-security-audit.html`. Verify:
- User Management tab shows by default
- User table with 4 rows, correct role badges, correct scope
- "Invite User" button opens modal
- Clicking "Audit Log" tab switches view
- Audit log shows 8 entries with left-border color coding
- Filter dropdowns render
- Pagination present
- "Export Log" button visible

- [ ] **Step 3: Commit**

```bash
git add admin-portal/07-security-audit.html
git commit -m "feat: add security & audit page with user management and audit log tabs"
```

---

## Phase 5: Final Verification

### Task 14: Cross-page link verification and final check

**Files:** All modified/created files

- [ ] **Step 1: Verify all navigation links across both portals**

Walk through the full contractor flow:
1. `01-contractor-projects-listing.html` → project detail → eligibility → wizard steps 1-8 → step 9 → review → confirmation → tracking
2. Verify every "Next" and "Back" link works
3. Verify progress bar shows 9 steps on every wizard page

Walk through the admin portal:
1. Login → Dashboard → Inbox → Detail → back to Inbox
2. Dashboard → Project Management (new page)
3. Dashboard → Security & Audit (new page)
4. Verify sidebar links work on every admin page
5. Verify "View full history" on dashboard links to Security & Audit

- [ ] **Step 2: Visual consistency check**

Spot-check across admin pages:
- CTA colors consistent: Reject=red, Clarification=blue, Approve=green
- Status badges use the same style everywhere
- Collapsible animations smooth (250ms)
- No broken icons (all Lucide names correct)
- Font sizes and weights match the spec's typography hierarchy

- [ ] **Step 3: Commit any fixes from verification**

```bash
git add -A
git commit -m "fix: cross-page link and visual consistency corrections"
```

(Skip this commit if no fixes were needed.)

- [ ] **Step 4: Final commit — update README**

Update `README.md` with a brief description of the system:

```markdown
# IHS Kenya — Prequalification Portal

Contractor prequalification enlistment system for IHS Kenya development projects.

## Structure

- `public portal/` — Landing page (public-facing)
- `contractor-portal/` — 9-step prequalification wizard + tracking
- `admin-portal/` — Back-office review, project management, security & audit

## Running

Open any `.html` file in a browser. No build step required.
```

```bash
git add README.md
git commit -m "docs: update README with project structure"
```
