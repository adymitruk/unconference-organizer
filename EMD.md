# EMD: Unconference organizer

**USRs:** Alex (Organizer), Pat (Attendee), Riley (Attendee), Jordan (Attendee)
**SVCs:** Unconference

**WFLs:**
- WFL1 Open registration: SLC1–SLC2, ends SCR Signup QR
- WFL2 Sign up as an attendee: SLC3–SLC6, ends EVT Attendee Registered
- WFL3 Suggest a topic: SLC7–SLC9, ends EVT Topic Suggested
- WFL4 Vote on topics: SLC10–SLC13, ends EVT Topic Voted
- WFL5 Close registration: SLC14, ends EVT Registration Closed
- WFL6 Build the agenda: SLC15–SLC18, ends SCR Agenda

**MAPs:**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR (SLC2)

**ERRs:**

**Joins (RMO, matched by id, all EVTs in one SWL):**
- RMO Signup QR (Unconference): EVT Registration Opened, by eventId
- RMO Signup Confirmation (Unconference): EVT Attendee Registered, by attendeeId
- RMO Topic Ballot (Unconference): EVT Topic Suggested ⋈ EVT Attendee Registered, by attendeeId
- RMO Agenda Builder (Unconference): EVT Topic Suggested ⋈ EVT Topic Voted, by topicId
- RMO Agenda (Unconference): EVT Session Scheduled, by sessionId

**Diagram:**

| | SLC1 | SLC2 | SLC3 | SLC4 | SLC5 | SLC6 | SLC7 | SLC8 | SLC9 | SLC10 | SLC11 | SLC12 | SLC13 | SLC14 | SLC15 | SLC16 | SLC17 | SLC18 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **WFL** | WFL1 Open registration | WFL1 | WFL2 Sign up as an attendee | WFL2 | WFL2 | WFL2 | WFL3 Suggest a topic | WFL3 | WFL3 | WFL4 Vote on topics | WFL4 | WFL4 | WFL4 | WFL5 Close registration | WFL6 Build the agenda | WFL6 | WFL6 | WFL6 |
| **UBA: Alex (Organizer)** | SCR Open Registration Form | SCR Signup QR | | | | | | | | | | | | SCR Close Registration | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda |
| **UBA: Pat (Attendee)** | | | SCR Signup Form | SCR Signup Confirmation | | | SCR Suggest Topic Form | | | SCR Topic Ballot | SCR Topic Ballot | | | | | | | SCR Agenda |
| **UBA: Riley (Attendee)** | | | | | SCR Signup Form | | | SCR Suggest Topic Form | | | | SCR Topic Ballot | | | | | | SCR Agenda |
| **UBA: Jordan (Attendee)** | | | | | | SCR Signup Form | | | SCR Suggest Topic Form | | | | SCR Topic Ballot | | | | | SCR Agenda |
| **MBA** | COM Open Registration | RMO Signup QR ← MAP Signup Url | COM Register Attendee | RMO Signup Confirmation | COM Register Attendee | COM Register Attendee | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | RMO Topic Ballot | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Close Registration | RMO Agenda Builder | COM Schedule Session | COM Schedule Session | RMO Agenda |
| **LBA: Unconference** | EVT Registration Opened | | EVT Attendee Registered | | EVT Attendee Registered | EVT Attendee Registered | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Registration Closed | | EVT Session Scheduled | EVT Session Scheduled | |

**Slice types:**
- COM slices: SLC1, SLC3, SLC5, SLC6, SLC7, SLC8, SLC9, SLC11, SLC12, SLC13, SLC14, SLC16, SLC17
- RMO slices: SLC2, SLC4, SLC10, SLC15, SLC18

**Arrows between SLCs (EVT → RMO):**
- EVT Registration Opened (SLC1) → MAP Signup Url → RMO Signup QR (SLC2)
- EVT Attendee Registered (SLC3) → RMO Signup Confirmation (SLC4)
- EVT Attendee Registered (SLC3) → RMO Topic Ballot (SLC10)
- EVT Attendee Registered (SLC5) → RMO Topic Ballot (SLC10)
- EVT Attendee Registered (SLC6) → RMO Topic Ballot (SLC10)
- EVT Topic Suggested (SLC7) → RMO Topic Ballot (SLC10)
- EVT Topic Suggested (SLC8) → RMO Topic Ballot (SLC10)
- EVT Topic Suggested (SLC9) → RMO Topic Ballot (SLC10)
- EVT Topic Suggested (SLC7) → RMO Agenda Builder (SLC15)
- EVT Topic Suggested (SLC8) → RMO Agenda Builder (SLC15)
- EVT Topic Suggested (SLC9) → RMO Agenda Builder (SLC15)
- EVT Topic Voted (SLC11) → RMO Agenda Builder (SLC15)
- EVT Topic Voted (SLC12) → RMO Agenda Builder (SLC15)
- EVT Topic Voted (SLC13) → RMO Agenda Builder (SLC15)
- EVT Session Scheduled (SLC16) → RMO Agenda (SLC18)
- EVT Session Scheduled (SLC17) → RMO Agenda (SLC18)

**Properties:**

**WFL1 Open registration (SLC1–SLC2)**

**SLC1 (COM slice)**
- SCR Open Registration Form (Alex):
  - heading Open Registration
  - column
    - text box title = Adaptech Unconference 2026
  - row, right-aligned
    - button Open → COM Open Registration
- COM Open Registration: title = Adaptech Unconference 2026, eventId = E-1, signupToken = tok-7f3a
- EVT Registration Opened (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a

**SLC2 (RMO slice)**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR
- RMO Signup QR (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupUrl = https://unconf.example/signup/tok-7f3a
- SCR Signup QR (Alex):
  - heading Signup QR
  - column
    - label title = Adaptech Unconference 2026
    - label signupUrl = https://unconf.example/signup/tok-7f3a
    - label qr = (show signupUrl as QR)

**WFL2 Sign up as an attendee (SLC3–SLC6)**

**SLC3 (COM slice)**
- SCR Signup Form (Pat):
  - heading Sign Up
  - column
    - label title = Adaptech Unconference 2026
    - text box name = Pat Lee
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Pat Lee, attendeeId = A-1
- EVT Attendee Registered (Unconference): attendeeId = A-1, eventId = E-1, name = Pat Lee

**SLC4 (RMO slice)**
- RMO Signup Confirmation (Unconference): attendeeId = A-1, name = Pat Lee, eventId = E-1
- SCR Signup Confirmation (Pat):
  - heading You're signed up
  - column
    - label attendeeId = A-1
    - label name = Pat Lee

**SLC5 (COM slice)**
- SCR Signup Form (Riley):
  - heading Sign Up
  - column
    - label title = Adaptech Unconference 2026
    - text box name = Riley Ng
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Riley Ng, attendeeId = A-2
- EVT Attendee Registered (Unconference): attendeeId = A-2, eventId = E-1, name = Riley Ng

**SLC6 (COM slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label title = Adaptech Unconference 2026
    - text box name = Jordan Ortiz
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Jordan Ortiz, attendeeId = A-3
- EVT Attendee Registered (Unconference): attendeeId = A-3, eventId = E-1, name = Jordan Ortiz

**WFL3 Suggest a topic (SLC7–SLC9)**

**SLC7 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box title = Event Sourcing in Practice
    - dropdown kind = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation, topicId = T-1
- EVT Topic Suggested (Unconference): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation

**SLC8 (COM slice)**
- SCR Suggest Topic Form (Riley):
  - heading Suggest a Topic
  - column
    - text box title = Remote Facilitation
    - dropdown kind = fishBowl
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl, topicId = T-2
- EVT Topic Suggested (Unconference): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl

**SLC9 (COM slice)**
- SCR Suggest Topic Form (Jordan):
  - heading Suggest a Topic
  - column
    - text box title = Lunch spots nearby
    - dropdown kind = suggestion
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion, topicId = T-3
- EVT Topic Suggested (Unconference): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion

**WFL4 Vote on topics (SLC10–SLC13)**

**SLC10 (RMO slice)**
- RMO Topic Ballot (Unconference), one row per topic: topicId | title | kind | name
  - T-1 | Event Sourcing in Practice | presentation | Pat Lee
  - T-2 | Remote Facilitation | fishBowl | Riley Ng
  - T-3 | Lunch spots nearby | suggestion | Jordan Ortiz
- SCR Topic Ballot (Pat):
  - heading Topics
  - table, one row per row of RMO Topic Ballot
    - columns: title | kind | name | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Pat Lee | Vote
    - row: Remote Facilitation | fishBowl | Riley Ng | Vote
    - row: Lunch spots nearby | suggestion | Jordan Ortiz | Vote

**SLC11 (COM slice)**
- SCR Topic Ballot (Pat):
  - heading Topics
  - table
    - columns: title | kind | button Vote → COM Cast Vote
    - row: Remote Facilitation | fishBowl | Vote (chosen)
- COM Cast Vote: topicId = T-2, attendeeId = A-1, voteId = V-1
- EVT Topic Voted (Unconference): voteId = V-1, topicId = T-2, attendeeId = A-1

**SLC12 (COM slice)**
- SCR Topic Ballot (Riley):
  - heading Topics
  - table
    - columns: title | kind | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Vote (chosen)
- COM Cast Vote: topicId = T-1, attendeeId = A-2, voteId = V-2
- EVT Topic Voted (Unconference): voteId = V-2, topicId = T-1, attendeeId = A-2

**SLC13 (COM slice)**
- SCR Topic Ballot (Jordan):
  - heading Topics
  - table
    - columns: title | kind | button Vote → COM Cast Vote
    - row: Event Sourcing in Practice | presentation | Vote (chosen)
- COM Cast Vote: topicId = T-1, attendeeId = A-3, voteId = V-3
- EVT Topic Voted (Unconference): voteId = V-3, topicId = T-1, attendeeId = A-3

**WFL5 Close registration (SLC14)**

**SLC14 (COM slice)**
- SCR Close Registration (Alex):
  - heading Close Registration
  - column
    - label title = Adaptech Unconference 2026
    - label attendees = 3
  - row, right-aligned
    - button Close → COM Close Registration
- COM Close Registration: eventId = E-1
- EVT Registration Closed (Unconference): eventId = E-1

**WFL6 Build the agenda (SLC15–SLC18)**

**SLC15 (RMO slice)**
- RMO Agenda Builder (Unconference), one row per topic with votes: topicId | title | kind | voteCount
  - T-1 | Event Sourcing in Practice | presentation | 2
  - T-2 | Remote Facilitation | fishBowl | 1
  - T-3 | Lunch spots nearby | suggestion | 0
- SCR Agenda Builder (Alex):
  - heading Build Agenda
  - table, one row per row of RMO Agenda Builder
    - columns: title | kind | voteCount | button Schedule → SCR Agenda Builder
    - row: Event Sourcing in Practice | presentation | 2 | Schedule
    - row: Remote Facilitation | fishBowl | 1 | Schedule
    - row: Lunch spots nearby | suggestion | 0 | Schedule

**SLC16 (COM slice)**
- SCR Agenda Builder (Alex):
  - heading Schedule Session
  - group Topic (read only)
    - row: label topicId = T-1 | label title = Event Sourcing in Practice | label kind = presentation
  - column
    - text box slot = morning
    - text box room = Room A
  - row, right-aligned
    - button Schedule → COM Schedule Session
- COM Schedule Session: topicId = T-1, title = Event Sourcing in Practice, kind = presentation, slot = morning, room = Room A, sessionId = S-1
- EVT Session Scheduled (Unconference): sessionId = S-1, topicId = T-1, title = Event Sourcing in Practice, kind = presentation, slot = morning, room = Room A

**SLC17 (COM slice)**
- SCR Agenda Builder (Alex):
  - heading Schedule Session
  - group Topic (read only)
    - row: label topicId = T-2 | label title = Remote Facilitation | label kind = fishBowl
  - column
    - text box slot = afternoon
    - text box room = Room B
  - row, right-aligned
    - button Schedule → COM Schedule Session
- COM Schedule Session: topicId = T-2, title = Remote Facilitation, kind = fishBowl, slot = afternoon, room = Room B, sessionId = S-2
- EVT Session Scheduled (Unconference): sessionId = S-2, topicId = T-2, title = Remote Facilitation, kind = fishBowl, slot = afternoon, room = Room B

**SLC18 (RMO slice)**
- RMO Agenda (Unconference), one row per session: sessionId | title | kind | slot | room
  - S-1 | Event Sourcing in Practice | presentation | morning | Room A
  - S-2 | Remote Facilitation | fishBowl | afternoon | Room B
- SCR Agenda (Alex):
  - heading Agenda
  - table, one row per row of RMO Agenda
    - columns: slot | room | title | kind
    - row: morning | Room A | Event Sourcing in Practice | presentation
    - row: afternoon | Room B | Remote Facilitation | fishBowl
- SCR Agenda (Pat): same as Alex
- SCR Agenda (Riley): same as Alex
- SCR Agenda (Jordan): same as Alex
