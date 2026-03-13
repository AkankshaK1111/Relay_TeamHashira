# Relay — UI/UX Screens Specification
**Date:** 2026-03-11  
**Purpose:** Complete screen-by-screen specification for Claude Code implementation  
**Reference prototype:** `relay-ux-complete.html` (all screens already built — use as visual reference)

---

## Design system

### Tokens
```css
/* Colour */
--ink:        #16120E;   /* Primary text */
--ink-2:      #4A4238;   /* Secondary text */
--ink-3:      #8C7E72;   /* Labels, tertiary */
--ink-4:      #B8AFA7;   /* Disabled, placeholder */
--parch:      #F7F3EE;   /* App background */
--parch-2:    #EDE7DF;   /* Elevated surface, input bg */
--parch-3:    #E2D9CE;   /* Border light */
--border:     #D8CEC3;   /* Standard border */
--bord-str:   #C0B3A4;   /* Strong border */
--surface:    #FFFFFF;   /* Card background */
--ember:      #C0460A;   /* Primary action, brand */
--ember-l:    #F5E4DA;   /* Ember tint bg */
--ember-m:    #E07050;   /* Ember mid */
--green:      #2E6E48;   /* Success, positive signal */
--green-l:    #DEF0E6;   /* Green tint bg */
--amber:      #9A6A0A;   /* Warning */
--amber-l:    #F5EACC;   /* Amber tint bg */
--blue:       #1E4E7A;   /* Info */
--blue-l:     #DDE9F5;   /* Blue tint bg */
--red:        #A83020;   /* Error, dead */
--red-l:      #FDECEA;   /* Red tint bg */

/* Spacing */
--r:          10px;      /* Standard border radius */
--r-sm:       6px;       /* Small border radius */
--r-lg:       14px;      /* Large border radius */
--sidebar-w:  240px;     /* App sidebar width */
--header-h:   56px;      /* App header height */

/* Shadows */
--shadow-sm:  0 1px 4px rgba(22,18,14,0.08);
--shadow-md:  0 4px 20px rgba(22,18,14,0.10);
--shadow-lg:  0 12px 40px rgba(22,18,14,0.14);
```

### Typography
```css
/* Display / headings */
font-family: 'Fraunces', serif;
font-weight: 400;  /* Use italic (em) for brand emphasis */

/* Body / UI */
font-family: 'DM Sans', sans-serif;

/* Data / mono */
font-family: 'DM Mono', monospace;

/* Google Fonts import */
@import url('https://fonts.googleapis.com/css2?family=Fraunces:ital,opsz,wght@0,9..144,300;0,9..144,400;0,9..144,500;1,9..144,300;1,9..144,400&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;0,9..40,600&family=DM+Mono:wght@400;500&display=swap');
```

### Warmth chip component
```html
<!-- Hot -->
<span class="warmth-chip hot">Hot</span>
<!-- Warm -->
<span class="warmth-chip warm">Warm</span>
<!-- Cooling -->
<span class="warmth-chip cooling">Cooling</span>
<!-- Cold -->
<span class="warmth-chip cold">Cold</span>
```
```css
.warmth-chip { font-size:11px; font-weight:700; padding:2px 9px; border-radius:10px; }
.warmth-chip.hot     { background:#FFF0C0; color:#8A6200; }
.warmth-chip.warm    { background:var(--green-l); color:var(--green); }
.warmth-chip.cooling { background:var(--amber-l); color:var(--amber); }
.warmth-chip.cold    { background:var(--blue-l); color:var(--blue); }
```

---

## App shell

**Layout:** Fixed top header (56px) + fixed left sidebar (240px) + scrollable main content area  
**Background:** `var(--parch)` throughout

### Top header
- Left: Logo `Rel[a]y` in Fraunces, italic `a` in ember
- Centre: Page title (changes with active view)
- Right: Pending badge (links to pending queue) + user avatar

### Sidebar navigation
```
Today              [badge: count]
Pipeline
Pending            [badge: count]
Contacts
────────────────
Email digest
✦ Additions        [highlighted, ember accent]
SMS retrieval
Mobile capture
────────────────
Settings
```

---

## Screen 1 — Onboarding: Welcome

**Route:** First screen, no auth  
**Layout:** Centred card, max-width 520px

**Content:**
- Eyebrow: `RELAY` in DM Mono, ember colour, letter-spaced
- Headline: `Your deals remember themselves.` in Fraunces, 38px
- Subheading: Body copy about the product
- Name input field (labelled "Your name")
- Company input field (labelled "Company")
- CTA button: "Get started →" (ember, full width on small, auto on large)
- Below CTA: "Takes 3 minutes. No card required."

**Behaviour:**
- Both fields required before CTA becomes active
- On submit → advance to Screen 2 (Email connect)
- Progress bar at top: 1/6 filled

---

## Screen 2 — Onboarding: Email Connect

**Route:** Onboarding step 2  
**HARD GATE:** Cannot advance without connecting email

**Content:**
- Step indicator: `2 of 6`
- Headline: `Connect your email` in Fraunces
- Body copy: "Relay reads your email metadata — sender, timing, threads. Never content."
- Privacy reassurance: "We never read your email body. Ever."
- Large connect button: Gmail logo + "Connect Gmail" (ember background)
- Secondary options: Outlook / Other (smaller, outlined)
- "Why is this required?" expandable explanation

**Connected state (after OAuth simulation):**
- Button becomes green ✓ "Gmail connected"
- Animated status line: "Reading 6 months of history…"
- Progress bar fills to show ingestion progress
- After 2-3s: "Found 847 threads · 23 active conversations" 
- CTA "Continue →" becomes active

**Behaviour:**
- CTA disabled until email connected
- Clicking CTA while disconnected: show inline error "Email connection required to continue"
- Connected → advance to Screen 3

---

## Screen 3 — Onboarding: Calendar Connect

**Route:** Onboarding step 3  
**Optional:** Can skip

**Content:**
- Step indicator: `3 of 6`
- Headline: `Connect your calendar`
- Body copy: "Relay detects meeting starts and ends to trigger follow-ups automatically."
- Value prop callout: "Without calendar: Relay emails only. With calendar: Relay emails + meeting-triggered drafts."
- Connect button: "Connect Google Calendar" + "Connect Outlook Calendar"
- Skip link below: "Skip for now — add later in Settings"

**Connected state:**
- Green ✓ animation
- "Calendar connected — triggers are live"
- Continue becomes active

---

## Screen 4 — Onboarding: Tracker Import

**Route:** Onboarding step 4  
**Optional:** Can skip (but strongly nudged)

**Content:**
- Step indicator: `4 of 6`
- Headline: `Import your existing pipeline`
- Body copy: "Connect your tracker for Day 1 deal context. Relay reads it and keeps it updated."
- Three platform cards: Notion / Google Sheets / Airtable (each with icon, connect button)
- CSV option: "Or upload a CSV file" — smaller, below the cards
- Skip: "Skip — start fresh" (smaller text link)

**Connected state:**
- Card shows ✓ green, "18 deals found"
- Other cards dim slightly
- Progress indicator: connecting animation
- "Reading your pipeline…" → "Found 18 deals · 6 stages detected"

---

## Screen 5 — Onboarding: Ingestion Interstitial

**Route:** Onboarding step 5 (auto-advance, no user action required except classify)

**Content:**
Three animated progress bars, each filling at a different rate:
```
Email ████████████████████████░░░░░░ 847 threads
Calendar ██████████████████████░░░░░░░░ 12 meetings
Notion ████████████████████░░░░░░░░░░ 18 deals
```

After bars reach ~60%:
**Quick classify cards surface** — 3 cards, each showing a contact with partial data:
```
[Card] Priya Sharma · Acme Corp
       Last email: 18 days ago
       "I think we've seen this in our analytics..."
       
       [Prospect ✓]  [Not a prospect]
```

- "Skip" link in corner
- Total time: 30–45 seconds
- Progress continues filling while classify cards are shown

**Behaviour:**
- Classify taps = calibration signal. Skipping is fine.
- After classify (or skip): bars finish filling → advance to Screen 6

---

## Screen 6 — Onboarding: Tracker Mapping

**Route:** Onboarding step 6  
**Only shown if tracker was connected in step 4**

**Content:**
- Headline: "Map your pipeline to Relay"
- Subheading: "We've pre-filled what we can. Confirm the ones marked below."

**Confidence-tiered display:**

**Tier 1 — 85%+ confidence:** Auto-mapped silently. Shown as a small summary line at the top: "✓ 12 fields mapped automatically"

**Tier 2 — 50–85% confidence:** Show each field with a "confirm" chip:
```
"Stage" column → Deal stage          [Looks right ✓]  [Change]
"Company" column → Company name      [Looks right ✓]  [Change]
"Last contact" column → Last interaction date  [Looks right ✓]  [Change]
```

**Tier 3 — <50% confidence:** Explicit ask:
```
What does "Status" map to?
○ Deal stage   ○ Lead source   ○ Priority   ○ Skip this field
```

**CTA:** "Launch Relay →" (ember, full width)

---

## Screen 6b — Launch transition

**Triggered by:** "Launch Relay →" click  
**Duration:** 2.4 seconds  
**Visual:** Full-screen dark overlay with animated loading bar and status messages:
- "Loading your deals…"
- "Scoring 18 contacts…"
- "Building your Today view…"
- "Ready."

→ Overlay fades, app shell appears

---

## Screen 7 — Today View

**Route:** `/today` — default app view  
**Primary use:** Morning review, daily action management

### Header section
```
Follow-ups [5]   Drafts [3]   Upcoming [2]   Off Track [1]
```
These are count badges, not navigation tabs. Clicking them filters the list below.

### Filter tabs
```
All  ·  Follow-ups  ·  Drafts  ·  Upcoming  ·  Off Track
```
`All` active by default.

### Item list — urgency ranked, highest first

Each item has a collapsed and expanded state. Collapsed shows the essentials. Expanded shows full context + action buttons.

#### Follow-up item (collapsed)
```
● [Warmth chip] [Name] · [Company]
  [Silence gap]: "18 days — last replied 20 Feb"
  [Action hint]: "Draft ready"         [Compose]  [⏱ Snooze]
```

#### Follow-up item (expanded)
```
● [Warmth chip] [Name] · [Company]
  Last interaction: [date + brief summary]
  Silence gap: [N days]
  Last email: "[subject line]"
  
  Context: [reconstruction summary with confidence]
  
  [Compose →]   [Snooze]   [Not needed]
```

#### Draft item (collapsed)
```
✉ Draft ready · [Name] · [Company]
  Post-meeting follow-up · Based on pre-call thread
  [Send →]  [Edit]
```

#### Draft item (expanded)
```
✉ Draft ready · [Name] · [Company]
  ─────────────────
  Subject: "Following up — our call today"
  
  Hi [Name],
  
  [Draft body preview — first 3 lines]
  ...
  ─────────────────
  Basis: Pre-call email thread (3 emails) · [Inferred — not recorded] ⓘ
  
  [Send →]   [Edit then send]   [Discard]
```

#### Upcoming meeting item
```
📅 [Name] · [Company] · Today 2:00 PM
   In 45 minutes
   [Expand for context →]
```

**Expanded:**
```
📅 [Name] · [Company] · Today 2:00 PM

  Context [Inferred — not recorded, confidence: 0.74]
  ────────────────────────────────────────────────────
  Last discussed: Pricing options (₹9.6L annual vs ₹1.1L/month)
  They asked about: API rate limits, SLA, data residency
  You promised to send: Renewal case study
  Their likely question today: Follow-up on proposal status
  
  [Looks right ✓]   [Edit]
```

#### Off Track item
```
⚠ Off Track · [Name] · [Company]
  Your Notion: "Proposal Sent"  ↔  Signals: In Conversation
  [Expand to see evidence →]
```

**Expanded:**
```
⚠ Stage mismatch — [Name] · [Company]
  ─────────────────────────────────────
  Your tracker says:    Proposal Sent
  Signals suggest:      In Conversation
  
  Evidence:
  • Proposal sent 20 Feb — no reply in 18 days
  • Email thread velocity dropped from 3/week to 0
  • No counter-proposal or pricing discussion detected
  
  [Update to In Conversation]   [Keep as Proposal Sent]
```

### Snooze modal
Opens on "Snooze" tap. Options:
- Tomorrow
- 2 days
- 3 days
- 1 week
- Custom…

### Completed section (bottom of list)
```
▸ Show completed today (3)
```
Expandable. Shows items completed this session. Clears at midnight.

---

## Screen 8 — Contact Record

**Route:** `/contact/:id`  
**Layout:** Two-column: main content (left, ~65%) + sidebar (right, ~35%)

### Hero section (top of main column)
```
[Initial avatar]  [Name] · [Company]
                  [Role]
                  [Warmth chip]  [Stage chip]  [Silence: "18d silent"]
                  Last interaction: [date]
```

### Ask / On-demand retrieval
```
[ Ask anything about this deal... ] [Enter ↵]
```
Live query input. Suggested queries shown as chips: "pricing" "blockers" "last asked" "commitments"

On query:
```
─────────────────────────────────────────────────
Priya raised pricing on the 26 Feb call. You sent two 
options that evening: ₹9.6L annual or ₹1.1L/month. 
No reply since. Proposal opened 3× and forwarded once.
─────────────────────────────────────────────────
[Inferred — not recorded] · Confidence: 0.82
```

### Activity timeline
Chronological, most recent first. Each entry shows:
```
[Icon] [Type label]  [Date]
       [Brief description]
       [Reconstruction confidence if inferred]
```

Activity types and icons:
- `email_sent` → ↗ 
- `email_received` → ↙
- `meeting` → 📅
- `call_inferred` → 📞 + "Logged: Good call"
- `call_detected` → 📞 + "Call detected (23 min) — no outcome logged"
- `tracker_note` → 📝

### Sidebar content
- Deal summary card: stage, warmth, silence gap, next meeting
- Write-back status: "Last synced to Notion 2h ago"
- Quick actions: Compose email / Log call / Edit stage

---

## Screen 9 — Pending Queue

**Route:** `/pending`  
**Badge:** Shows count of pending items in sidebar nav

### Header
```
Pending (4)
[Approve all notes & contacts ✓]   [Filter ▾]
```

Filter tabs: `All · Stage changes · Notes · Contacts`

### Item types

#### Stage change (individual only — not in Approve all)
```
┌─────────────────────────────────────────────────
│ STAGE CHANGE · Priya Sharma · Acme Corp
│ In Conversation → Proposal Out
│ Signal: Post-call outcome "Sent proposal" on 11 Mar
│ 
│ [Approve stage change]   [Keep as In Conversation]
└─────────────────────────────────────────────────
```

#### Notion write-back (in Approve all)
```
┌─────────────────────────────────────────────────
│ NOTION UPDATE · Marcus Rodriguez · TechCorp
│ Adding to row "Marcus / TechCorp":
│   Activity: "Follow-up sent 11 Mar post-call"
│   Stage: In Conversation (no change)
│ 
│ [Approve ✓]   [Skip]
└─────────────────────────────────────────────────
```

#### Contact created (in Approve all)
```
┌─────────────────────────────────────────────────
│ NEW CONTACT · Sarah Chen · Meridian Labs
│ Found in email thread with Priya Sharma
│ Inferred role: "Technical evaluator"
│ 
│ [Add to Relay]   [Skip]
└─────────────────────────────────────────────────
```

---

## Screen 10 — Pipeline

**Route:** `/pipeline`  
**Layout:** Kanban view, 5 columns, horizontally scrollable

### Column structure
```
ON RADAR (4)    IN CONVERSATION (5)    QUALIFIED (3)    PROPOSAL OUT (4)    CLOSING (2)
```

### Deal card
```
┌──────────────────────
│ [Company name]
│ [Contact name]
│ [Warmth chip]  [Stage]
│ [Silence: "18d"]  or  [Meeting: "Today 2pm"]
└──────────────────────
```

Special states:
- Phantom alert (21+ days in stage, no response): amber left border + "⚠ No response in 21d"
- Cooling: amber tint background
- Hot + imminent meeting: green left border

Click any deal → navigates to Contact Record.

---

## Screen 11 — Additions View

**Route:** `/additions`  
**Purpose:** Show both post-MVP additions with full interactive demos embedded

### Addition 1 — 2-Tap Post-Call Capture
Dark banner header with badge chips (0 audio, 5s trigger, 2 taps)

**Before/after grid:** What Relay couldn't see vs can now see

**Interactive demo — two-column layout:**
- Left: Phone mockup (mini, ~180px wide)
  - Initial: lock screen state
  - After "Simulate call": green call screen with running timer, red end-call button
  - After end call: notification card with 4 outcome buttons
  - After tap: confirmation state showing "✓ Logged: [outcome]"
- Right: Step panel (4 steps, highlight active)
  - Step 1: "Call comes in" + Simulate button
  - Step 2: "23 minutes pass" (active while call running)
  - Step 3: "Notification fires in 5 seconds"
  - Step 4: "Data written" — fills with exact data model fields after outcome tap

### Addition 2 — SMS On-Demand Retrieval
Blue banner header

**Envoy comparison:** Two-column — what Envoy got right/wrong vs what Relay does

**Interactive SMS demo — two-column:**
- Left: iPhone mockup with iMessage UI (~210px wide), live input
- Right: 5 scenario buttons (Retrieval / Retrieval / Action / Action / Scope boundary)
  - Click any → loads conversation into phone + shows explanation panel
  - Type in input → live response matching closest scenario
  - Y/N confirmation flow active for draft/snooze scenarios

---

## Screen 12 — Email Digest

**Route:** `/digest`  
**Purpose:** Show what the morning email looks like and simulate direct-from-email actions

**Content:**
Mock email client view showing the actual digest email:

```
From: Relay <relay@relay.app>
To: you@yourdomain.com
Subject: 3 drafts ready · 5 follow-ups · Tue 11 Mar

────────────────────────────────────────

Good morning, Arjun. Here's what needs attention.

DRAFTS (3)
──────────

✉ Priya Sharma · Acme Corp
  Post-call follow-up · 18d silent · Cooling
  [Send draft →]  [Discard]

...

FOLLOW-UPS (5)
──────────────

● Vikram Mehta · Nexus Systems
  9 days silent · Qualified stage
  [Compose →]  [Snooze 2d]  [Not needed]

...
```

**Actions:**
- Low-stakes (Snooze, Not needed, Discard): execute in-place with toast confirmation
- High-stakes (Send draft, Compose): show tooltip "Opens in Relay →" + link

---

## Screen 13 — SMS Retrieval

**Route:** `/sms`  
**Purpose:** Settings + demo for SMS retrieval feature

**Left column:**
- Your Relay number card: `+91 98765 00000` with "Save to contacts" button
- What you can ask: 5 example queries as chips

**Right column:**
- Phone mockup with live iMessage interface
- Pre-loaded conversation showing example exchange
- Live input: type anything, get a response
- Suggested queries: "Priya", "today", "pipeline", "Y"

---

## Screen 14 — Mobile Post-Call Capture

**Route:** `/mobile`  
**Purpose:** Explain + demo the 2-tap capture (simplified version, full version in Additions)

**Left column:**
- How it works: 4 numbered steps
- What each tap means: 4 outcome chips with descriptions

**Right column:**
- Phone mockup showing lock screen with Relay notification:
  ```
  [R] Relay              now
  Call ended · 23 min with Priya
  Priya Sharma · Acme Corp · How did it go?
  
  [Good call]  [Follow-up]  [Proposal]  [Dead]
  ```
- Tapping any outcome → shows confirmation state + toast

---

## Screen 15 — Settings

**Route:** `/settings`  
**Layout:** Two-column: nav tabs (left) + content (right)

### Tab: Connections
- Email: Connected ✓ Gmail (disconnect link)
- Calendar: Connected ✓ Google Calendar (disconnect)
- Tracker: Connected ✓ Notion (disconnect, change target)
- SMS number: `+91 98765 00000` (copy button)

### Tab: Digest preferences
- Delivery time: `7:00 AM` (time picker)
- Max items: `5` (stepper: 3/5/7/10)
- Item types: toggles for Follow-ups / Drafts / Upcoming / Off Track

### Tab: Write-back rules
- Primary target: Notion (dropdown: Notion / Google Sheets / Airtable)
- Auto-approve low-risk updates: toggle (notes, contact enrichment)
- Stage changes: "Always require approval" (locked, non-editable)
- Audit log: "View write history →" link

---

## Interaction states — global components

### Toast notification
```
position: fixed; bottom: 64px; right: 24px;
background: var(--ink); color: white;
padding: 11px 16px; border-radius: var(--r);
fade in/out; auto-dismiss 2.8s
```
Shows: action confirmation, errors, sync updates

### Snooze modal
```
position: fixed; centred overlay with backdrop
Options: Tomorrow / 2 days / 3 days / 1 week / Custom
[Confirm snooze]  [Cancel]
```

### Pending badge
```
position: in sidebar nav item and top header
background: var(--ember); color: white;
font-size: 11px; border-radius: 10px;
auto-updates when actions completed
```

### Inferred label
Appears on any context reconstruction output:
```
[Inferred — not recorded] · Confidence: 0.82
```
Font: DM Mono 11px, colour var(--ink-3). Info icon (ⓘ) with tooltip explaining reconstruction basis.

---

## Navigation model

**Onboarding:** Linear. Progress bar at top (1/6 through 6/6). Hard gate at step 2 (email). Other steps can be skipped.

**App:** Sidebar nav primary. Bottom proto-nav bar also present (dev tool, can be hidden in production). Active view highlighted in sidebar.

**Cross-view links:**
- Pipeline deal card click → Contact record
- Pending badge click → Pending queue
- Sidebar contact list → Contact record
- Today view "View full record →" → Contact record
- Draft "View context" → Contact record
