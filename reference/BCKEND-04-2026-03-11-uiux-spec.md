# BCKEND-04 — Relay: UI/UX Screens Specification
**Date:** 2026-03-11  
**Purpose:** Complete screen-by-screen spec for all views built and designed. Reference before any UI work.

---

## Design System

### Palette
```css
--bg:           #F7F4EF   /* Warm cream — all page backgrounds */
--surface:      #FFFFFF   /* White — card backgrounds */
--surface2:     #F2EFE9   /* Off-white — secondary surfaces, code headers */
--surface3:     #EAE6DF   /* Muted — tertiary, hover states */
--border:       #DDD8D0   /* Default border */
--border-mid:   #C8C2B8   /* Stronger border, focus states */
--text:         #1A1714   /* Near-black primary text */
--text-2:       #4A4540   /* Secondary text */
--text-3:       #8A8480   /* Muted/meta text */

/* Ember — primary accent */
--ember:        #C94A10
--ember-bg:     #FDF1EC
--ember-border: #F0C4AE

/* Semantic colours */
--green:        #1E7A48   /* Success, Warm, Hot, connected */
--green-bg:     #EDF7F2
--amber:        #9A6A00   /* Warning, inferred, caution */
--amber-bg:     #FDF6E3
--blue:         #1A5EA8   /* Info, upcoming, Cronofy */
--blue-bg:      #EEF4FC
--purple:       #6A30B8   /* Nylas, email */
--purple-bg:    #F4EFFC
```

**Rule: NO DARK BACKGROUNDS. Ever. Any background must be readable in morning light.**

### Typography
```
Display / headings: Instrument Serif (italic variant for emphasis)
Body:               DM Sans
Monospace/code:     Geist Mono (data, timestamps, field names, badges)
```

### Warmth Chips
```
Hot:     background #FDE8E0, color #B83A1A
Warm:    background amber-bg, color amber
Cooling: background blue-bg, color blue
Cold:    background #F0F0F0, color #6A6A6A
```

---

## Screen 1 — Today View (Primary Surface)

### Purpose
The only screen most founders open. Answers: "What do I need to do today?"  
Opens at 7:45am, 3 taps, done.

### Layout
```
[Topbar]
  Left:  "Good morning, Arjun." + date + "N deals need attention"
  Tabs:  Follow-ups [N] | Drafts [N] | Upcoming [N] | Off Track [N]

[Content area, max-width 860px]
  Section heading: "Most urgent"
  [Urgency-ranked card list]
  [Load more — dashed border]
  [Completed toggle → expands completed items]
```

### Tab Colours (active state)
- Follow-ups: ember background
- Drafts: green background
- Upcoming: blue background
- Off Track: amber background

### Card — Collapsed State
```
[Type dot] [Name · Company · Role]  [Warmth chip]  [Urgency chip]
           [One-line summary of why this needs attention]
           [Meta: Last: date · Met: duration · N threads]
                                                           [Chevron ▾]
```

### Card — Expanded State
Border elevates. Chevron rotates 180°. Expanded area slides in below.

**Follow-up card expanded:**
```
[Context block — border-left: 3px ember]
  RECENT CONTEXT
  "28 Feb call — Pricing discussion. Priya asked about..." [Inferred tag]

[Timeline]
  28 Feb  45-min pricing call. You sent follow-up same day.
  20 Feb  Priya replied within 2 hours. Asked 4 questions.
  8 Feb   First demo. 55 mins. Ran over — good sign.

[Actions row]
  [Compose follow-up]  [Snooze 3 days]  [Not needed]
  or: tomorrow · 1 week · custom
```

**Draft card expanded:**
```
[Draft box — white, border, slightly inset]
  To: marcus@orionsys.com  ·  Subject: Re: Kodo pilot — next steps
  ─────────────────────────────────────
  Hi Marcus,
  Thanks for the time this morning...
  [full draft text]

[Amber context block]
  ⚑ CONTEXT NOTE
  "Draft based on pre-call email thread — call content not recorded.
   If the draft is wrong, Marcus will correct it."

[Actions row]
  [Send]  [Edit then send]  [Discard]
```

**Upcoming card expanded:**
```
[Context block — amber border]
  PRE-CALL BRIEF  [⚑ Inferred]
  Last discussed: ROI numbers...
  Key concern: Board meeting 18 Mar...
  Relationship: Referred by Priya...

[Timeline — same component as follow-up]

[Actions row]
  [Looks right]  [Edit brief]
```

**Off Track card expanded:**
```
[Stage compare — two boxes side by side]
  [GREEN BOX]            →    [AMBER BOX]
  YOUR STAGE                  SIGNALS SUGGEST
  Proposal Sent               Cooling / Stalled

[Evidence context block]
  "18 days no reply after proposal..."

[Actions row]
  [Update stage]  [Keep current]  [Send a nudge]
```

### Completed Section
Below load-more. Collapsed by default.  
"▸ Show completed today (3)" → click → expands. Strikethrough text, 60% opacity.

---

## Screen 2 — Pending Queue

### Purpose
Write-back approvals. Founder signs off before Relay writes to Notion/Sheets.  
Badge: "Pending (N)" in sidebar.

### Layout
```
[Topbar]
  "Pending approvals"
  "N items need your sign-off before Notion gets updated"

[Actions bar]
  [Approve all (except stage changes)]  [Filter by type]

[Section: Stage changes — individual approval required]
  [Pending items — type = stage change]

[Section: Notes & records — approve all or individually]
  [Pending items — type = note / contact / date update]
```

### Pending Item
```
[Type icon — 36×36 rounded] [Title + description]  [Approve] [Keep/Discard]
```

Icon backgrounds:
- Stage change: amber-bg
- Note/record: green-bg
- New contact: blue-bg

**Key rule:** "Approve all" button physically excludes stage-change items. Stage changes always individual.

---

## Screen 3 — Pipeline View (Secondary Surface)

### Purpose
Secondary view. Founders who want the kanban. Not the primary daily surface.

### Layout
```
[Topbar]
  "Pipeline" + "N active deals · Last synced N mins ago"

[Horizontal scroll — pipeline columns]
  [On Radar] [In Conversation] [Qualified] [Proposal Out] [Closing]
```

### Pipeline Column
```
STAGE NAME          N deals
─────────────────────────────
[Deal card]
[Deal card]
[Deal card]
```

### Deal Card
```
[Name]
[Company]
────────────────────
[₹Value]   [Warmth chip]
```

Click → navigates to contact record.

---

## Screen 4 — Contact Record

### Purpose
Full history of one prospect. On-demand retrieval ("what did we discuss?"). 

### Layout
```
[Topbar]
  ← Back to Today
  [Name]
  [Role · Company · email]

[Contact hero card]
  [Avatar 52×52]  [Name] [Role]
                  [Signal boxes row]
                  Days silent | Interactions | Warmth score | Momentum
                                                                     [Warmth chip] [Stage]

[Ask box — search bar style]
  🔍 Ask anything about [Name] — what did we discuss? what's blocking this deal?
                                                                            [Ask]

[Ask result — ember-bg, Inferred label + answer text]  ← appears after query

[Section: Interaction timeline]
  [Timeline items — most recent first]

[Section: Notion notes]
  [Context block — blue border — showing raw tracker data]
```

### Signal Boxes (4 boxes in a row)
```
[  12  ]   [  4  ]   [  41  ]   [  ↓  ]
Days silent Interact.  Warmth    Momentum
```

### Ask Box Interaction
- Founder types: "what's blocking this deal?"
- Relay returns: reconstructed meaning from email + calendar + notes
- Always labelled: "⚑ Inferred from email threads + calendar — not recorded"
- Returns meaning, not documents

---

## Screen 5 — Settings

### Purpose
Connection management. Write-back rules. Ingestion config.

### Layout
```
[Section: Email]
  [Gmail — connected]
  [Outlook / Hotmail — + Connect]

[Section: Calendar]
  [Google Calendar — connected]
  [Calendly — + Connect]

[Section: Deal tracker (write-back target)]
  [Notion — Primary] 
  [Google Sheets — Read only]
  [Airtable — + Connect]

[Section: Write-back rules]
  [Append only — never overwrite]    Active
  [Stage changes require approval]   Active
  [Ingestion window: 6 months]       [Expand to 12 mo]
```

### Connection Row
```
[Icon 34×34]  [Name]           [pill: Connected / Read only / Primary]
              [Status text]    or  [pill: + Connect]
```

---

## Screen 6 — Onboarding (7 steps)

### Step 1 — Welcome
```
Relay.

The CRM that works while you sell.

Your name: [___________]
Your startup: [___________]

                    [Continue →]
```

### Step 2 — Email (Hard Gate)
```
Connect your email

This is how Relay learns your deals. It reads 6 months 
of history the moment you connect.

[📧 Gmail]      [📨 Outlook]     [🍎 Apple Mail]

No email, no Relay. This is the only required step.

                    [Continue →]   ← only appears after ≥1 connected
```

States: default → connecting (spinner) → connected (green tick + "Gmail connected · 847 threads")

### Step 3 — Calendar
```
Connect your calendar

This is how Relay knows when calls end — and starts 
drafting your follow-up automatically.

[📅 Google Calendar]    [📅 Outlook Calendar]
[🔗 Calendly]           [🔗 Cal.com]

                    [Continue →]
                    [Skip for now — I'll add this later]
```

### Step 4 — Deal Tracker
```
Connect your deal tracker

Relay reads what's already there on day one.
Writes back what it learns — without touching your data.

[📓 Notion]    [📊 Google Sheets]    [🗂 Airtable]    [📎 CSV upload]

                    [Continue →]
                    [Skip — I'll add this later]
```

### Step 5 — Ingestion Interstitial
```
Building your Relay.

  ████████████████████  847 threads scanned
  ████████████████████  38 meetings indexed
  █████████████████     22 contacts found
  ████████████████████  34 contacts scored
  ███████████████       18 likely prospects

Done. Ready to show you what we found.

                    [Continue →]
```
Rows animate in sequentially with progress bars filling and live counts appearing.

### Step 6 — Contact Classify
```
We found these people in your history.
Tell us who's a prospect — one tap each.

[P]  Priya Mehta · Vantara           [Prospect] [Not a prospect] [Not sure]
[M]  Marcus Lee · Orion Systems       [Prospect] [Not a prospect] [Not sure]
[R]  Rahul Sharma · Nexgen            [Prospect] [Not a prospect] [Not sure]
[T]  Tanvi Gupta · Clearfield         [Prospect] [Not a prospect] [Not sure]
[D]  Deepa Nair · Uplift              [Prospect] [Not a prospect] [Not sure]
[A]  Ananya Rao · BrightScale         [Prospect] [Not a prospect] [Not sure]

                    [Continue →]   ← unlocks after 3 classified
                    [Skip all — I'll do this later]
```

### Step 7 — Write-back preview + completion
```
This is what Relay will write to Notion.

Preview:
[Notion row for Marcus Lee]
+ [Relay · 11 Mar] Pilot scope call, 38 min. Scope doc due Thu. [Inferred]
append-only · never overwrites · requires your approval

One question before we finish:

"Last Activity" column in your Sheets maps to...
  ○ Column F — Last Contact Date    ← 71% match
  ○ Column G — Date Modified
  ○ Something else

                    [Confirm mapping]

────────────────────────────────────────────────────────

Relay is ready.

  34 contacts scored
  18 active deals
   5 need attention today

Hot deals right now:
  Marcus Lee · Orion Systems  HOT   ₹18L
  Rahul Sharma · Nexgen       HOT   ₹12.4L

                    [Open Today view →]
```

---

## Screen 7 — Transition (Onboarding → App)

Sequence on "Open Today view →":
1. Overlay fades in — Relay wordmark centred, progress bar
2. Labels: "Loading your deals… → Scoring 18 contacts… → Almost there… → Ready."
3. Onboarding shell hides
4. Overlay fades out
5. App fades in underneath
6. First-run amber banner: "Welcome, Arjun. Relay found 34 contacts and 18 active deals from your history. Everything below was built without you logging a single thing." — × to dismiss
7. Sidebar nav items animate in staggered
8. Land on Today view — fully loaded

---

## Interaction Patterns (Universal Rules)

### Context before action
Never show action buttons without showing why. Every card must explain the situation before asking the founder to do something.

### Escape hatch always present
Every card has a low-friction exit: Not needed / Discard / Skip / Keep current. Never trap the founder.

### Snooze pattern
Primary snooze = "3 days". Quick options: "tomorrow · 1 week · custom". All available in one line.

### [Inferred] labelling
Any content reconstructed by LLM must be tagged. Format: `⚑ Inferred — not recorded`.  
Confidence < 0.7: show confidence score alongside.

### Toast notifications
All completed actions trigger toast bottom-right: `✓ [Action description]`. Auto-dismiss after 2.8s.

### Warmth in context
Warmth chip always shown on cards. Warmth is not a number (never show 94.2 to the founder). Show: Hot / Warm / Cooling / Cold only.

### Urgency score — internal only
Urgency score drives sort order but is never shown to the founder. Shown internally in backend flow docs only.
