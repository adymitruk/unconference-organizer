# EMD: Unconference organizer

**USRs:** Alex (Organizer), Pat (Attendee), Riley (Attendee), Jordan (Attendee)
**SVCs:** Unconference

**COMs:**
- COM Open Registration
  - properties: title, eventId, signupToken
  - from: SCR Open Registration Form
  - to: EVT Registration Opened
- COM Register Attendee
  - properties: eventId, signupToken, name, attendeeId
  - from: SCR Signup Form
  - to: EVT Attendee Registered
  - fails: ERR Name Required → SCR Signup Form; ERR Already Registered
- COM Suggest Topic
  - properties: eventId, attendeeId, title, kind, topicId
  - from: SCR Suggest Topic Form
  - to: EVT Topic Suggested
- COM Cast Vote
  - properties: topicId, attendeeId, voteId
  - from: SCR Topic Ballot
  - to: EVT Topic Voted
- COM Close Registration
  - properties: eventId
  - from: SCR Close Registration
  - to: EVT Registration Closed
- COM Schedule Session
  - properties: topicId, slot, room, sessionId
  - from: SCR Agenda Builder
  - to: EVT Session Scheduled

**EVTs:**
- EVT Registration Opened (Unconference)
  - properties: eventId, title, signupToken
  - from: COM Open Registration
  - to: RMO Signup QR
- EVT Attendee Registered (Unconference)
  - properties: attendeeId, eventId, name
  - from: COM Register Attendee
  - to: RMO Registrations, RMO Topic Ballot
- EVT Topic Suggested (Unconference)
  - properties: topicId, eventId, attendeeId, title, kind
  - from: COM Suggest Topic
  - to: RMO Topic Ballot, RMO Agenda Builder
- EVT Topic Voted (Unconference)
  - properties: voteId, topicId, attendeeId
  - from: COM Cast Vote
  - to: RMO Agenda Builder
- EVT Registration Closed (Unconference)
  - properties: eventId
  - from: COM Close Registration
  - to: none
- EVT Session Scheduled (Unconference)
  - properties: sessionId, topicId, slot, room
  - from: COM Schedule Session
  - to: RMO Agenda

**RMOs:**
- RMO Signup QR (Unconference)
  - properties: eventId, title, signupUrl
  - from: EVT Registration Opened (by eventId; MAP Signup Url: signupToken → signupUrl)
  - to: SCR Signup QR; SCR Signup Form title-views (Pat, Riley, Jordan)
- RMO Registrations (Unconference)
  - properties: attendeeId, name, eventId
  - from: EVT Attendee Registered (by attendeeId)
  - to: SCR Signup Confirmation
- RMO Topic Ballot (Unconference)
  - collections: Topic Ballot [topicId, title, kind, name]; lookups [attendeeId, name]
  - from: EVT Attendee Registered, EVT Topic Suggested (join by attendeeId)
  - to: SCR Topic Ballot
- RMO Agenda Builder (Unconference)
  - collections: [topicId, title, kind, voteCount]
  - from: EVT Topic Suggested, EVT Topic Voted (join by topicId)
  - to: SCR Agenda Builder
- RMO Agenda (Unconference)
  - collections: Agenda [sessionId, topicId, title, kind, slot, room]; lookups [topicId, title, kind]
  - from: EVT Topic Suggested (lookups by topicId), EVT Session Scheduled (by sessionId; join lookups for title, kind)
  - to: SCR Agenda

**WFLs:**
- WFL1 Open registration: SLC1–SLC2, ends SCR Signup QR
- WFL2 Sign up as an attendee: SLC3–SLC11, ends EVT Attendee Registered
- WFL3 Suggest a topic: SLC12–SLC14, ends EVT Topic Suggested
- WFL4 Vote on topics: SLC15–SLC18, ends EVT Topic Voted
- WFL5 Close registration: SLC19, ends EVT Registration Closed
- WFL6 Build the agenda: SLC20–SLC23, ends SCR Agenda


**MAPs:**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR (SLC2)

**ERRs:**
- ERR Name Required, on COM Register Attendee (SLC9) → SCR Signup Form (SLC10)
- ERR Already Registered, on COM Register Attendee (GWT after SLC11 success): attendee already has EVT Attendee Registered

**Diagram:**

| | SLC1 | SLC2 | SLC3 | SLC4 | SLC5 | SLC6 | SLC7 | SLC8 | SLC9 | SLC10 | SLC11 | SLC12 | SLC13 | SLC14 | SLC15 | SLC16 | SLC17 | SLC18 | SLC19 | SLC20 | SLC21 | SLC22 | SLC23 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **WFL** | WFL1 Open registration | WFL1 | WFL2 Sign up as an attendee | WFL2 | WFL2 | WFL2 | WFL2 | WFL2 | WFL2 | WFL2 | WFL2 | WFL3 Suggest a topic | WFL3 | WFL3 | WFL4 Vote on topics | WFL4 | WFL4 | WFL4 | WFL5 Close registration | WFL6 Build the agenda | WFL6 | WFL6 | WFL6 |
| **UBA: Alex (Organizer)** | SCR Open Registration Form | SCR Signup QR | | | | | | | | | | | | | | | | | SCR Close Registration | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda |
| **UBA: Pat (Attendee)** | | | SCR Signup Form (title) | SCR Signup Form | SCR Signup Confirmation | | | | | | | SCR Suggest Topic Form | | | SCR Topic Ballot | SCR Topic Ballot | | | | | | | SCR Agenda |
| **UBA: Riley (Attendee)** | | | | | | SCR Signup Form (title) | SCR Signup Form | | | | | | SCR Suggest Topic Form | | SCR Topic Ballot | | SCR Topic Ballot | | | | | | SCR Agenda |
| **UBA: Jordan (Attendee)** | | | | | | | | SCR Signup Form (title) | SCR Signup Form | SCR Signup Form | SCR Signup Form | | | SCR Suggest Topic Form | SCR Topic Ballot | | | SCR Topic Ballot | | | | | SCR Agenda |
| **MBA** | COM Open Registration | RMO Signup QR ← MAP Signup Url | | COM Register Attendee | RMO Registrations | | COM Register Attendee | | COM Register Attendee | | COM Register Attendee | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | RMO Topic Ballot | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Close Registration | RMO Agenda Builder | COM Schedule Session | COM Schedule Session | RMO Agenda |
| **LBA: Unconference** | EVT Registration Opened | | | EVT Attendee Registered | | | EVT Attendee Registered | | ERR Name Required | | EVT Attendee Registered | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Registration Closed | | EVT Session Scheduled | EVT Session Scheduled | |
| **GWT** | Open Registration → Registration Opened | Signup QR → Registration Opened → Signup QR | | Register Attendee → Attendee Registered | Registrations → Attendee Registered → Registrations | | | | Register Attendee → Name Required | | Register Attendee → Attendee Registered → Register Attendee → Already Registered | | | | Topic Ballot → (Attendee Registered → Topic Ballot lookup) ×3 → (Topic Suggested → Topic Ballot row) ×3 | | | | | Agenda Builder → (Topic Suggested → Agenda Builder) ×3 → (Topic Voted → Agenda Builder) ×3 | Topic Suggested ×2 → Schedule Session → Session Scheduled → Schedule Session → Session Scheduled | | Agenda → (Topic Suggested → lookups) ×3 → (Session Scheduled → Agenda row) ×2 |
**Slice types:**
- COM slices: SLC1, SLC4, SLC7, SLC9, SLC11, SLC12, SLC13, SLC14, SLC16, SLC17, SLC18, SLC19, SLC21, SLC22
- RMO slices: SLC2, SLC5, SLC15, SLC20, SLC23
- NAV slices: SLC3, SLC6, SLC8, SLC10

**Arrows between SLCs:**
- RMO Signup QR (SLC2) → SCR Signup Form (Pat) (SLC3)
- RMO Signup QR (SLC2) → SCR Signup Form (Riley) (SLC6)
- RMO Signup QR (SLC2) → SCR Signup Form (Jordan) (SLC8)
- ERR Name Required (SLC9) → SCR Signup Form (SLC10)
- EVT Registration Opened (SLC1) → MAP Signup Url → RMO Signup QR (SLC2)
- EVT Attendee Registered (SLC4) → RMO Registrations (SLC5)
- EVT Attendee Registered (SLC4) → RMO Topic Ballot (SLC15)
- EVT Attendee Registered (SLC7) → RMO Topic Ballot (SLC15)
- EVT Attendee Registered (SLC11) → RMO Topic Ballot (SLC15)
- EVT Topic Suggested (SLC12) → RMO Topic Ballot (SLC15)
- EVT Topic Suggested (SLC13) → RMO Topic Ballot (SLC15)
- EVT Topic Suggested (SLC14) → RMO Topic Ballot (SLC15)
- EVT Topic Suggested (SLC12) → RMO Agenda Builder (SLC20)
- EVT Topic Suggested (SLC13) → RMO Agenda Builder (SLC20)
- EVT Topic Suggested (SLC14) → RMO Agenda Builder (SLC20)
- EVT Topic Voted (SLC16) → RMO Agenda Builder (SLC20)
- EVT Topic Voted (SLC17) → RMO Agenda Builder (SLC20)
- EVT Topic Voted (SLC18) → RMO Agenda Builder (SLC20)
- EVT Topic Suggested (SLC12) → RMO Agenda (SLC23)
- EVT Topic Suggested (SLC13) → RMO Agenda (SLC23)
- EVT Topic Suggested (SLC14) → RMO Agenda (SLC23)
- EVT Session Scheduled (SLC21) → RMO Agenda (SLC23)
- EVT Session Scheduled (SLC22) → RMO Agenda (SLC23)

**Properties:**

**WFL1 Open registration (SLC1–SLC2)**

**SLC1 (COM slice)**
- SCR Open Registration Form (Alex):
  - heading Open Registration
  - column
    - text box Conference (title) = Adaptech Unconference 2026
  - row, right-aligned
    - button Open → COM Open Registration
- COM Open Registration: title = Adaptech Unconference 2026, eventId = E-1, signupToken = tok-7f3a
- EVT Registration Opened (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a
- GWT timeline (COM Open Registration):
  - COM Open Registration: title = Adaptech Unconference 2026, eventId = E-1, signupToken = tok-7f3a
  - EVT Registration Opened (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a

**SLC2 (RMO slice)**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR
- RMO Signup QR (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupUrl = https://unconf.example/signup/tok-7f3a
- SCR Signup QR (Alex):
  - heading Signup QR
  - column
    - label (title) = Adaptech Unconference 2026
    - label Signup link (signupUrl) = https://unconf.example/signup/tok-7f3a
    - label QR (qr) = signupUrl
- GWT timeline (RMO Signup QR):
  - RMO Signup QR (Unconference): (empty) eventId, title, signupUrl
  - EVT Registration Opened (SLC1): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a
  - RMO Signup QR (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupUrl = https://unconf.example/signup/tok-7f3a

**WFL2 Sign up as an attendee (SLC3–SLC11)**

**SLC3 (NAV slice)**
- SCR Signup Form (Pat):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC4 (COM slice)**
- SCR Signup Form (Pat):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = Pat Lee
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Pat Lee, attendeeId = A-1
- EVT Attendee Registered (Unconference): attendeeId = A-1, eventId = E-1, name = Pat Lee
- GWT timeline (COM Register Attendee):
  - COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Pat Lee, attendeeId = A-1
  - EVT Attendee Registered (Unconference): attendeeId = A-1, eventId = E-1, name = Pat Lee

**SLC5 (RMO slice)**
- RMO Registrations (Unconference): attendeeId = A-1, name = Pat Lee, eventId = E-1
- SCR Signup Confirmation (Pat):
  - heading You're signed up
  - column
    - label (name) = Pat Lee
    - (attendeeId stays on the RMO / COM path — not on the wire)
- GWT timeline (RMO Registrations):
  - RMO Registrations (Unconference): (empty) attendeeId, name, eventId
  - EVT Attendee Registered (SLC3): attendeeId = A-1, eventId = E-1, name = Pat Lee
  - RMO Registrations (Unconference): attendeeId = A-1, name = Pat Lee, eventId = E-1

**SLC6 (NAV slice)**
- SCR Signup Form (Riley):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC7 (COM slice)**
- SCR Signup Form (Riley):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = Riley Ng
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Riley Ng, attendeeId = A-2
- EVT Attendee Registered (Unconference): attendeeId = A-2, eventId = E-1, name = Riley Ng

**SLC8 (NAV slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC9 (COM slice, ERR)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = (empty)
- ERR Name Required (Unconference): name = (empty), message = "Name is required" → SCR Signup Form
- GWT timeline (COM Register Attendee; Then is the ERR):
  - COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = (empty)
  - ERR Name Required (Unconference): name = (empty), message = "Name is required"

**SLC10 (NAV slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
    - label (message) = Name is required
  - row, right-aligned
    - button Sign up → COM Register Attendee
- (no COM, no RMO)

**SLC11 (COM slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = Jordan Ortiz
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Jordan Ortiz, attendeeId = A-3
- EVT Attendee Registered (Unconference): attendeeId = A-3, eventId = E-1, name = Jordan Ortiz
- GWT timeline (two GWTs for COM Register Attendee; next When's Given = previous Given EVTs + Then EVTs):
  - COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Jordan Ortiz, attendeeId = A-3
  - EVT Attendee Registered (Unconference): attendeeId = A-3, eventId = E-1, name = Jordan Ortiz
  - COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Jordan Ortiz
  - ERR Already Registered (Unconference), EVT Attendee Registered already exists for name = Jordan Ortiz: name = Jordan Ortiz, message = "Already registered"


**WFL3 Suggest a topic (SLC12–SLC14)**

**SLC12 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Event Sourcing in Practice
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation, topicId = T-1
- EVT Topic Suggested (Unconference): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation

**SLC13 (COM slice)**
- SCR Suggest Topic Form (Riley):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Remote Facilitation
    - dropdown Kind (kind) = fishBowl
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl, topicId = T-2
- EVT Topic Suggested (Unconference): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl

**SLC14 (COM slice)**
- SCR Suggest Topic Form (Jordan):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Lunch spots nearby
    - dropdown Kind (kind) = suggestion
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion, topicId = T-3
- EVT Topic Suggested (Unconference): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion

**WFL4 Vote on topics (SLC15–SLC18)**

**SLC15 (RMO slice)**
- RMO Topic Ballot (Unconference):
  - lookup attendeeId → name (from EVT Attendee Registered): A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz
  - one row per topic (from EVT Topic Suggested ⋈ lookup by attendeeId): topicId | title | kind | name
    - T-1 | Event Sourcing in Practice | presentation | Pat Lee
    - T-2 | Remote Facilitation | fishBowl | Riley Ng
    - T-3 | Lunch spots nearby | suggestion | Jordan Ortiz
- SCR Topic Ballot (Pat):
  - heading Topics
  - table, one row per topic row of RMO Topic Ballot
    - columns: Topic (title) | Kind (kind) | Suggested by (name) | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Pat Lee | Vote
    - row: Remote Facilitation | fishBowl | Riley Ng | Vote
    - row: Lunch spots nearby | suggestion | Jordan Ortiz | Vote
  - (topicId per row stays as client-side hidden data for a later Cast Vote — not on the wire)
- SCR Topic Ballot (Riley): same as Pat
- SCR Topic Ballot (Jordan): same as Pat
- GWT timeline (RMO Topic Ballot; Attendee Registered updates lookup; Topic Suggested joins lookup by attendeeId for name):
  - RMO Topic Ballot (Unconference): (empty) Topic Ballot [topicId, title, kind, name]; lookups [attendeeId, name]
  - EVT Attendee Registered (SLC3): attendeeId = A-1, eventId = E-1, name = Pat Lee
  - RMO Topic Ballot (Unconference): Topic Ballot []; lookups [A-1 → Pat Lee]
  - EVT Attendee Registered (SLC5): attendeeId = A-2, eventId = E-1, name = Riley Ng
  - RMO Topic Ballot (Unconference): Topic Ballot []; lookups [A-1 → Pat Lee; A-2 → Riley Ng]
  - EVT Attendee Registered (SLC8): attendeeId = A-3, eventId = E-1, name = Jordan Ortiz
  - RMO Topic Ballot (Unconference): Topic Ballot []; lookups [A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz]
  - EVT Topic Suggested (SLC9): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation
  - RMO Topic Ballot (Unconference): Topic Ballot [T-1 | Event Sourcing in Practice | presentation | Pat Lee]; lookups [A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz]
  - EVT Topic Suggested (SLC10): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl
  - RMO Topic Ballot (Unconference): Topic Ballot [T-1 | Event Sourcing in Practice | presentation | Pat Lee; T-2 | Remote Facilitation | fishBowl | Riley Ng]; lookups [A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz]
  - EVT Topic Suggested (SLC11): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion
  - RMO Topic Ballot (Unconference): Topic Ballot [T-1 | Event Sourcing in Practice | presentation | Pat Lee; T-2 | Remote Facilitation | fishBowl | Riley Ng; T-3 | Lunch spots nearby | suggestion | Jordan Ortiz]; lookups [A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz]

**SLC16 (COM slice)**
- SCR Topic Ballot (Pat):
  - heading Topics
  - table, same layout as SLC15 (values carried from prior Topic Ballot — no rmo-data)
    - columns: Topic (title) | Kind (kind) | Suggested by (name) | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Pat Lee | Vote
    - row: Remote Facilitation | fishBowl | Riley Ng | Vote (pressed)
    - row: Lunch spots nearby | suggestion | Jordan Ortiz | Vote
  - (topicId T-2 from the pressed RMO row — client-side hidden data; not on the wire)
- COM Cast Vote: topicId = T-2, attendeeId = A-1, voteId = V-1
- EVT Topic Voted (Unconference): voteId = V-1, topicId = T-2, attendeeId = A-1

**SLC17 (COM slice)**
- SCR Topic Ballot (Riley):
  - heading Topics
  - table, same layout as SLC15 (values carried from prior Topic Ballot — no rmo-data)
    - columns: Topic (title) | Kind (kind) | Suggested by (name) | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Pat Lee | Vote (pressed)
    - row: Remote Facilitation | fishBowl | Riley Ng | Vote
    - row: Lunch spots nearby | suggestion | Jordan Ortiz | Vote
  - (topicId T-1 from the pressed RMO row — client-side hidden data; not on the wire)
- COM Cast Vote: topicId = T-1, attendeeId = A-2, voteId = V-2
- EVT Topic Voted (Unconference): voteId = V-2, topicId = T-1, attendeeId = A-2

**SLC18 (COM slice)**
- SCR Topic Ballot (Jordan):
  - heading Topics
  - table, same layout as SLC15 (values carried from prior Topic Ballot — no rmo-data)
    - columns: Topic (title) | Kind (kind) | Suggested by (name) | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Pat Lee | Vote (pressed)
    - row: Remote Facilitation | fishBowl | Riley Ng | Vote
    - row: Lunch spots nearby | suggestion | Jordan Ortiz | Vote
  - (topicId T-1 from the pressed RMO row — client-side hidden data; not on the wire)
- COM Cast Vote: topicId = T-1, attendeeId = A-3, voteId = V-3
- EVT Topic Voted (Unconference): voteId = V-3, topicId = T-1, attendeeId = A-3

**WFL5 Close registration (SLC19)**

**SLC19 (COM slice)**
- SCR Close Registration (Alex):
  - heading Close Registration
  - column
    - label (title) = Adaptech Unconference 2026
    - label Attendees (attendees) = 3
  - row, right-aligned
    - button Close → COM Close Registration
- COM Close Registration: eventId = E-1
- EVT Registration Closed (Unconference): eventId = E-1

**WFL6 Build the agenda (SLC20–SLC23)**

**SLC20 (RMO slice)**
- RMO Agenda Builder (Unconference):
  - one row per topic (from EVT Topic Suggested): topicId | title | kind | voteCount (starts at 0)
  - voteCount from EVT Topic Voted ⋈ by topicId
    - T-1 | Event Sourcing in Practice | presentation | 2
    - T-2 | Remote Facilitation | fishBowl | 1
    - T-3 | Lunch spots nearby | suggestion | 0
- SCR Agenda Builder (Alex):
  - heading Build Agenda
  - table, one row per row of RMO Agenda Builder
    - columns: Topic (title) | Kind (kind) | Votes (voteCount) | button Schedule → SCR Agenda Builder
    - row: Event Sourcing in Practice | presentation | 2 | Schedule
    - row: Remote Facilitation | fishBowl | 1 | Schedule
    - row: Lunch spots nearby | suggestion | 0 | Schedule
- GWT timeline (RMO Agenda Builder; Topic Suggested adds topic rows; Topic Voted joins by topicId to bump voteCount):
  - RMO Agenda Builder (Unconference): (empty) [topicId, title, kind, voteCount]
  - EVT Topic Suggested (SLC9): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 0
  - EVT Topic Suggested (SLC10): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 0; T-2 | Remote Facilitation | fishBowl | 0
  - EVT Topic Suggested (SLC11): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 0; T-2 | Remote Facilitation | fishBowl | 0; T-3 | Lunch spots nearby | suggestion | 0
  - EVT Topic Voted (SLC13): voteId = V-1, topicId = T-2, attendeeId = A-1
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 0; T-2 | Remote Facilitation | fishBowl | 1; T-3 | Lunch spots nearby | suggestion | 0
  - EVT Topic Voted (SLC14): voteId = V-2, topicId = T-1, attendeeId = A-2
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 1; T-2 | Remote Facilitation | fishBowl | 1; T-3 | Lunch spots nearby | suggestion | 0
  - EVT Topic Voted (SLC15): voteId = V-3, topicId = T-1, attendeeId = A-3
  - RMO Agenda Builder (Unconference): T-1 | Event Sourcing in Practice | presentation | 2; T-2 | Remote Facilitation | fishBowl | 1; T-3 | Lunch spots nearby | suggestion | 0

**SLC21 (COM slice)**
- SCR Agenda Builder (Alex):
  - heading Schedule Session
  - group Topic (read only; title/kind from prior SCR via session — not COM fields)
    - row: label (title) = Event Sourcing in Practice | label (kind) = presentation
    - (topicId from the selected Agenda Builder row — session for the COM)
  - column
    - text box Slot (slot) = morning
    - text box Room (room) = Room A
  - row, right-aligned
    - button Schedule → COM Schedule Session
- COM Schedule Session: topicId = T-1, slot = morning, room = Room A, sessionId = S-1
- EVT Session Scheduled (Unconference): sessionId = S-1, topicId = T-1, slot = morning, room = Room A
- GWT timeline (two GWTs for COM Schedule Session; next When's Given = previous Given EVTs + Then EVTs):
  - EVT Topic Suggested (SLC9): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation
  - EVT Topic Suggested (SLC10): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl
  - COM Schedule Session: topicId = T-1, slot = morning, room = Room A, sessionId = S-1
  - EVT Session Scheduled (Unconference): sessionId = S-1, topicId = T-1, slot = morning, room = Room A
  - COM Schedule Session: topicId = T-2, slot = afternoon, room = Room B, sessionId = S-2
  - EVT Session Scheduled (Unconference): sessionId = S-2, topicId = T-2, slot = afternoon, room = Room B

**SLC22 (COM slice)**
- SCR Agenda Builder (Alex):
  - heading Schedule Session
  - group Topic (read only; title/kind from prior SCR via session — not COM fields)
    - row: label (title) = Remote Facilitation | label (kind) = fishBowl
    - (topicId from the selected Agenda Builder row — session for the COM)
  - column
    - text box Slot (slot) = afternoon
    - text box Room (room) = Room B
  - row, right-aligned
    - button Schedule → COM Schedule Session
- COM Schedule Session: topicId = T-2, slot = afternoon, room = Room B, sessionId = S-2
- EVT Session Scheduled (Unconference): sessionId = S-2, topicId = T-2, slot = afternoon, room = Room B

**SLC23 (RMO slice)**
- RMO Agenda (Unconference):
  - lookups (every suggested topic, including those never scheduled): topicId | title | kind
    - T-1 | Event Sourcing in Practice | presentation
    - T-2 | Remote Facilitation | fishBowl
    - T-3 | Lunch spots nearby | suggestion
  - Agenda rows (scheduled sessions; title/kind from lookups by topicId): sessionId | topicId | title | kind | slot | room
    - S-1 | T-1 | Event Sourcing in Practice | presentation | morning | Room A
    - S-2 | T-2 | Remote Facilitation | fishBowl | afternoon | Room B
- SCR Agenda (Alex):
  - heading Agenda
  - table, one row per Agenda row of RMO Agenda
    - columns: Slot (slot) | Room (room) | Topic (title) | Kind (kind)
    - row: morning | Room A | Event Sourcing in Practice | presentation
    - row: afternoon | Room B | Remote Facilitation | fishBowl
- SCR Agenda (Pat): same as Alex
- SCR Agenda (Riley): same as Alex
- SCR Agenda (Jordan): same as Alex
- GWT timeline (RMO Agenda; Topic Suggested fills lookups — including topics with no votes; Session Scheduled adds Agenda rows joined by topicId):
  - RMO Agenda (Unconference): (empty) Agenda [sessionId, topicId, title, kind, slot, room]; lookups [topicId, title, kind]
  - EVT Topic Suggested (SLC9): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation
  - RMO Agenda (Unconference): Agenda []; lookups [T-1 | Event Sourcing in Practice | presentation]
  - EVT Topic Suggested (SLC10): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl
  - RMO Agenda (Unconference): Agenda []; lookups [T-1, T-2]
  - EVT Topic Suggested (SLC11): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion
  - RMO Agenda (Unconference): Agenda []; lookups [T-1, T-2, T-3]
  - EVT Session Scheduled (SLC18): sessionId = S-1, topicId = T-1, slot = morning, room = Room A
  - RMO Agenda (Unconference): Agenda [S-1 | T-1 | Event Sourcing in Practice | presentation | morning | Room A]; lookups [T-1..T-3]
  - EVT Session Scheduled (SLC19): sessionId = S-2, topicId = T-2, slot = afternoon, room = Room B
  - RMO Agenda (Unconference): Agenda [S-1 | T-1 | …; S-2 | T-2 | Remote Facilitation | fishBowl | afternoon | Room B]; lookups [T-1..T-3]
