# **Frontend Engineer Assignment (No Code, High-Quality UI Focus)**

## **Evaluation Criteria**

* Frontend tech selection and reasoning (framework, state, data fetching)
* UI architecture (routing, component design, reusable patterns)
* API calling strategy (error handling, retries, abort, pagination)
* Browser-level caching + offline-friendly patterns
* Debugging + observability (logging, tracing, error boundaries)
* Security basics on client (token handling, safe downloads, XSS considerations)
* UX quality for async jobs (progress, partial results, resilience)

---

## **Problem 1: Video-to-Notes Platform (Frontend System Design)**

**Goal:** Upload video → job runs async → user sees status + outputs: Summary.md, highlights (timestamps), assets. [READ MORE ABOUT THE PROJECT](./Video-summary-platform.md)

**Your solution must include**

* **Screens:** Upload, Jobs list, Job detail (status/logs), Results (markdown + highlights)
* **UI states:** loading, queued, processing, success, failed, retry, partial output
* **API calling plan:** how you poll/stream job progress (polling vs SSE), abort on navigation
* **Caching:** what to cache in browser (job list, job detail, results), TTL strategy, invalidation
* **Debugging plan:** how you would debug “stuck processing” from frontend side (network logs, correlation id display)

**Your Solution for problem 1:**

1️: Screens

I would design 4 main screens:

Upload Screen
User uploads video using drag & drop or file input.
Show upload progress bar.

Jobs List Screen
Show all jobs with status badges:
Queued, Processing, Done, Failed.

Job Detail Screen
Show detailed job status, progress, and basic logs.

Results Screen
Show:

Summary (rendered markdown)

Highlights with timestamps

Downloadable assets

Routing example:

/upload
/jobs
/jobs/:id
/results/:id

2️: UI States

Each job will clearly show:

Loading (while fetching)

Queued

Processing (with progress bar or spinner)

Partial Output (if summary ready but highlights still processing)

Success

Failed (with Retry button)

User should always see current status clearly.

3️: API Calling Plan

Upload video → POST /jobs

Check job status → GET /jobs/:id

I will use polling every 5 seconds using setInterval().

When:

Status = success or failed → stop polling.

If user leaves the page:

Clear interval to stop polling.

Retry strategy:

Retry failed request up to 3 times with small delay.

If backend supports real-time updates:

We can improve later using streaming.

4️: Caching (Simple Version)

Store jobs list in React state.

Refresh automatically every 30 seconds.

Job detail fetched when user opens detail page.

Results stored in state after job completes.

On retry → clear previous result state.

No complex offline caching needed.

5️: Debugging Plan

If job is stuck in processing:

Check browser Network tab.

Verify API response.

Show job ID in UI.

Show “Last updated at” timestamp.

If API fails → show clear error message.

---

## **Problem 2: LinkedIn Automation Platform (Frontend System Design)**

**Goal:** Connect LinkedIn → persona setup → draft preview → approve → schedule → posting history. [READ MORE ABOUT THE PROJECT](./linkedin-automation.md)

**Your solution must include**

* **Screens:** Connect, Persona editor, Drafts (3 variants), Approval, Scheduler, Post history
* **Form UX:** persona inputs validation, topic input rules, guardrails for scheduling
* **API calling:** draft generation request lifecycle, optimistic UI vs strict confirmation
* **Caching:** drafts caching, schedule list caching, refetch triggers after approval/post
* **Debugging:** how you surface posting failures to user and capture details for support

**Your Solution for problem 2:**

1️: Screens

Connect LinkedIn Screen
User connects LinkedIn account.

Persona Editor Screen
User writes persona details (bio, tone, industry).

Draft Preview Screen
Show 3 generated post variants.

Approval Screen
User selects and confirms one draft.

Scheduler Screen
User selects date and time for posting.

Post History Screen
Show all posted and scheduled posts with status.

2️: Form UX

Persona bio must have minimum length (for example 50 characters).

Topic input has maximum character limit.

Prevent scheduling in past date/time.

Show user’s timezone clearly.

Show simple validation messages below input fields.

All validation handled using React state.

3️: API Calling Plan

Generate draft → POST /draft

Show loading spinner while generating.

Display 3 variants after response.

After user approves:

Send confirm request to backend.

Update UI only after successful response (no optimistic update).

After scheduling:

Refetch post history list to show latest status.

4️: Caching

Store drafts in React state.

Optionally save in localStorage for 10 minutes.

Post history fetched when screen loads.

After posting or approval → refetch data.

Keep caching simple, no complex logic.

5️: Debugging

If posting fails:

Show clear error message.

Show request ID if available.

Provide “Retry” button.

During development:

Use browser DevTools Network tab.

Use console logs for debugging.

---

## **Problem 3: DOCX Template → Bulk Generator (Frontend System Design)**

**Goal:** Upload template → review fields → single generate → bulk via CSV → ZIP download + per-row report. [READ MORE ABOUT THE PROJECT](./docs-template-output-generation.md)

**Your solution must include**

* **Screens:** Template upload, Field review/editor, Single fill form, Bulk upload, Bulk run status, Report table, Downloads
* **Field UI:** field types (text/number/date), required/default, inline validation
* **Bulk UX:** CSV upload constraints, mapping UI (optional), progress + partial success
* **Browser caching:** template metadata caching, field schema caching, bulk report pagination caching
* **Downloads:** safe download UX (signed URL flow assumed), progress indicator

**Your Solution for problem 3:**
1️: Screens

Template Upload Screen
User uploads DOCX template.

Field Review Screen
Show detected fields from template.

Single Fill Form Screen
User fills values for one document.

Bulk CSV Upload Screen
User uploads CSV for multiple documents.

Bulk Run Status Screen
Show processing status.

Report Table Screen
Show success and failed rows.

Downloads Screen
Show generated file download links.

2️: Field UI

Detect basic field types (text, number, date).

Show “Required” label if mandatory.

Inline validation using simple checks:

Empty required field

Invalid number format

Invalid date format

Show default value if available.

All validation handled using React state.

3️: Bulk UX

Validate CSV before upload:

Check file type (.csv)

Check required column headers

Show upload progress bar.

After submission:

Show row-by-row processing status.

Highlight failed rows in red.

Allow partial success (download successful ones).

If some rows fail:

Show error reason in report table.

4️: Caching (Simplified)

Store template metadata in React state.

Optionally save in localStorage for 1 hour.

Store field schema in state while working.

Bulk report:

Fetch paginated data from backend.

Store current page in state.

Refetch if user refreshes.

No advanced caching needed.

5️: Downloads

Backend provides secure download link.

Show download button.

Show simple loader while downloading.

If link expired → show message and allow regenerate.

---

## **Problem 4: Character-Based Video Series Generator (Frontend System Design)**

**Goal:** Define characters once → create episode from story → view episode package (script/scenes/assets/render plan). [READ MORE ABOUT THE PROJECT](./char-based-video-generation.md)

**Your solution must include**

* **Screens:** Character library, Relationship editor, Episode creator, Episode detail (scenes), Asset gallery
* **Consistency UX:** show “locked character profile” per episode, version badges
* **API calling:** long-running generation job UI (progress, resume)
* **Caching:** character library caching, episode package caching, asset thumbnails caching

**Your Solution for problem 4:**
A: Screens Design (Clean Version)
1. Character Library Screen

Purpose: Manage all created characters.

UI Sections:

Search bar (name / tags)

Filter (active / archived)

Character cards (thumbnail, name, last updated, version badge)

“Create Character” button

Actions:

View

Edit

Duplicate

Archive

Caching:

Character list stored in React state.

Optionally saved in localStorage for 10 minutes.

Refetch when user refreshes or updates character.

2. Relationship Editor Screen

Purpose: Define relationships between characters.

UI:

Character selection dropdown (Character A & B)

Relationship type selector (friend, enemy, sibling etc.)

Simple visual layout showing connected characters

Save relationship button

State:

Local temporary state using useState

On save → API call

Update UI after successful response

3. Episode Creator Screen

Purpose: Create episode from story input.

UI Sections:

Title input

Story prompt textarea

Character selection (multi-select)

Locked character toggle

Generate Episode button

UX:

Once episode starts generating:

Show “Characters Locked for this Episode”

Show character version badge

Disable character editing for this episode

4. Episode Detail Screen (Scenes View)

Purpose: View generated episode package.

Sections:

Episode metadata

Locked characters + version badges

Scene list:

Scene number

Script text

Referenced characters

Scene status (draft / generated / rendered)

Tabs:

Script

Render Plan

Assets

Logs

5. Asset Gallery Screen

Purpose: View all episode assets.

UI:

Grid view

Filter by type (image/video/audio)

Download button

Preview modal

Caching:

Rely on browser HTTP cache.

Use lazy loading for images.

Do not use IndexedDB for now (keep simple).

 B: Consistency UX (Simple Version)

When episode is created, selected character data is saved with version number.

Episode shows: “Character Locked v2.1”

If character updated later:

Show “New version available” badge.

Old episode continues using original locked version unless manually updated.

 C: API Calling – Long Running Job UI (Simple)

Flow:

POST /episodes

Receive jobId

Poll GET /jobs/{id} every 5 seconds

Stop polling when status = completed or failed

UI:

Show progress percentage

Show current step (Script → Scenes → Assets → Render)

Show spinner while processing

Resume Logic:

Store jobId in localStorage

On page reload → check job status again

D: Caching Strategy (Fixed – No React Query)
1. Character Library

Store character list in React state.

Save in localStorage with timestamp.

If older than 10 minutes → fetch again.

Refetch after character update.

2. Episode Package

Store in React state.

Refetch if:

User manually refreshes

Job status changes

3. Asset Thumbnails

Use browser default caching.

Lazy load images.

No advanced IndexedDB caching.
---

## **Cross-Cutting** 

Answer these in **bullet points** (max 1 page total):

1. **Frontend stack choice**

* EDIT YOUR ANSWER HERE: Framework (Next.js/Vue/etc), state management, router, UI kit, why.
  I will use React (with JavaScript) for building the application.

HTML → Structure

CSS → Styling

JavaScript → Logic

React → Component-based architecture

For state management:

I will use React useState and useContext.

For server data handling, I can use custom hooks.

For routing:

I will use React Router.

Why this stack?

I am comfortable with it.

Easy to maintain.

Component-based structure makes screens reusable.

Good for building scalable UI.

2. **API layer design**

* Fetch/Axios choice, typed client generation (OpenAPI), error normalization, retries, request dedupe, abort controllers.
  `
For API calls:

I will use fetch() or Axios.

I will create a separate api.js file to manage all API calls.

Error Handling:

I will use try/catch.

Show user-friendly error messages.

Log error in console for debugging.

For long-running job:

After creating episode, backend gives jobId.

I will use setInterval() to poll job status every few seconds.

Stop polling when job is completed.

If user refreshes page:

Store jobId in localStorage.

Resume polling when page reloads.`

3. **Browser caching plan**

* What you cache (GET responses, derived state), where (memory, IndexedDB, localStorage), TTL/invalidation rules.
* How you handle “job status updates” without stale UI.
  `
What I will cache:

Character list

Episode details

Where:

Use localStorage for:

jobId

recently opened episode

Use React state for temporary caching.

TTL rule:

I can store timestamp in localStorage.

If data older than 10 minutes → fetch again.

For job status:

Never cache.

Always fetch fresh status.`

4. **Debugging & observability**

* Error boundaries, client-side logging approach, correlation id propagation, “report a problem” payload.
* How you would debug: slow uploads, failed downloads, intermittent 500s.
  `
 Use console.log() during development.

Use browser DevTools Network tab.

Add basic Error Boundary in React.

If something fails:

Check network request

Check backend response

Show proper error message

For file upload slow:

Check file size

Check network speed`

5. **Security basics**

* Token storage approach, CSRF considerations (if cookies), XSS avoidance for markdown rendering, safe file download patterns.
  `Store token in localStorage (basic implementation)

Avoid rendering raw HTML directly

Use safe rendering for user content

Validate file type before upload

Do not expose sensitive data in frontend`
