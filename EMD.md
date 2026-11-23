# EMD: Unconference organizer

**USRs:** Alex (Organizer), Pat (Attendee), Riley (Attendee), Jordan (Attendee)
**SVCs:** Unconference

**COMs:**
- COM Set Conference Dates
  - properties: eventId, startDate, endDate
  - from: SCR Setup Wizard
  - to: EVT Conference Dates Set
- COM Add Conference Room
  - properties: eventId, room
  - from: SCR Setup Wizard
  - to: EVT Conference Room Added
- COM Add Session Span
  - properties: eventId, spanId, label, startTime, endTime
  - from: SCR Setup Wizard
  - to: EVT Session Span Added
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
  - properties: topicId, date, spanId, room, sessionId
  - from: SCR Agenda Builder
  - to: EVT Session Scheduled

**EVTs:**
- EVT Conference Dates Set (Unconference)
  - properties: eventId, startDate, endDate
  - from: COM Set Conference Dates
  - to: RMO Conference Setup, RMO Agenda Builder, RMO Agenda
- EVT Conference Room Added (Unconference)
  - properties: eventId, room
  - from: COM Add Conference Room
  - to: RMO Conference Setup, RMO Agenda Builder, RMO Agenda
- EVT Session Span Added (Unconference)
  - properties: eventId, spanId, label, startTime, endTime
  - from: COM Add Session Span
  - to: RMO Conference Setup, RMO Agenda Builder, RMO Agenda
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
  - to: RMO Topic Ballot, RMO Agenda Builder, RMO Agenda
- EVT Topic Voted (Unconference)
  - properties: voteId, topicId, attendeeId
  - from: COM Cast Vote
  - to: RMO Agenda Builder
- EVT Registration Closed (Unconference)
  - properties: eventId
  - from: COM Close Registration
  - to: none
- EVT Session Scheduled (Unconference)
  - properties: sessionId, topicId, date, spanId, room
  - from: COM Schedule Session
  - to: RMO Agenda Builder (later slice), RMO Agenda

**RMOs:**
- RMO Conference Setup (Unconference)
  - properties: startDate, endDate
  - collections: rooms [room]; spans [spanId, label, startTime, endTime]
  - from: EVT Conference Dates Set, EVT Conference Room Added, EVT Session Span Added
  - to: SCR Setup Wizard (summary)
  - (Agenda grids expand startDate..endDate into each calendar day sessions may use)
- RMO Signup QR (Unconference)
  - properties: eventId, title, signupUrl
  - from: EVT Registration Opened (by eventId; MAP Signup Url: signupToken → signupUrl)
  - to: SCR Signup QR; SCR Signup Form title-views (Pat, Riley, Jordan)
- RMO Registrations (Unconference)
  - collections: Registrations [attendeeId, name, eventId]
  - from: EVT Attendee Registered (by attendeeId)
  - to: SCR Signup Confirmation; SCR Attendees
  - (Appears twice: after Pat registers with one row; again at end of signup with all three for the organizer. Second slice projects only Riley/Jordan on top of Pat — do not re-join Pat’s EVT.)
- RMO Topic Ballot (Unconference)
  - collections: Topic Ballot [topicId, title, kind, name]; lookups [attendeeId, name]
  - from: EVT Attendee Registered, EVT Topic Suggested (join by attendeeId)
  - to: SCR Topic Ballot
- RMO Agenda Builder (Unconference)
  - collections: dates [date] (from startDate..endDate); rooms [room]; spans [spanId, label, startTime, endTime]; unassigned [topicId, title, kind, voteCount]; schedule [date, spanId, room, topicId, title]
  - from: EVT Conference Dates Set, EVT Conference Room Added, EVT Session Span Added, EVT Topic Suggested, EVT Topic Voted (join by topicId); later slice also from EVT Session Scheduled
  - to: SCR Agenda Builder
  - (Appears twice: first after votes with empty schedule; second after Schedule Session COMs, projecting only Session Scheduled on top of that prior state — do not re-join the first slice’s EVTs.)
- RMO Agenda (Unconference)
  - collections: Agenda [date, spanId, room, topicId, title, kind]; lookups [topicId, title, kind]; dates [date] (from startDate..endDate); rooms [room]; spans [spanId, label]
  - from: EVT Conference Dates Set, EVT Conference Room Added, EVT Session Span Added, EVT Topic Suggested (lookups), EVT Session Scheduled (join lookups for title, kind)
  - to: SCR Agenda

**WFLs:**
- WFL1 Set up conference: SLC1–SLC8, ends SCR Setup Wizard (summary)
- WFL2 Open registration: SLC9–SLC10, ends SCR Signup QR
- WFL3 Sign up as an attendee: SLC11–SLC20, ends SCR Attendees
- WFL4 Suggest a topic: SLC21–SLC30, ends EVT Topic Suggested (10 suggestions)
- WFL5 Vote on topics: SLC31–SLC41, ends EVT Topic Voted (10 votes; T-3 and T-9 never receive a vote)
- WFL6 Close registration: SLC42, ends EVT Registration Closed
- WFL7 Build the agenda: SLC43–SLC53, ends SCR Agenda (8 slots filled)

**MAPs:**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR (SLC10)

**ERRs:**
- ERR Name Required, on COM Register Attendee (SLC17) → SCR Signup Form (SLC18)
- ERR Already Registered, on COM Register Attendee (GWT after SLC19 success): attendee already has EVT Attendee Registered

**Diagram:**

| | SLC1 | SLC2 | SLC3 | SLC4 | SLC5 | SLC6 | SLC7 | SLC8 | SLC9 | SLC10 | SLC11 | SLC12 | SLC13 | SLC14 | SLC15 | SLC16 | SLC17 | SLC18 | SLC19 | SLC20 | SLC21 | SLC22 | SLC23 | SLC24 | SLC25 | SLC26 | SLC27 | SLC28 | SLC29 | SLC30 | SLC31 | SLC32 | SLC33 | SLC34 | SLC35 | SLC36 | SLC37 | SLC38 | SLC39 | SLC40 | SLC41 | SLC42 | SLC43 | SLC44 | SLC45 | SLC46 | SLC47 | SLC48 | SLC49 | SLC50 | SLC51 | SLC52 | SLC53 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **WFL** | WFL1 Set up conference | WFL1 | WFL1 | WFL1 | WFL1 | WFL1 | WFL1 | WFL1 | WFL2 Open registration | WFL2 | WFL3 Sign up as an attendee | WFL3 | WFL3 | WFL3 | WFL3 | WFL3 | WFL3 | WFL3 | WFL3 | WFL3 | WFL4 Suggest a topic | WFL4 | WFL4 | WFL4 | WFL4 | WFL4 | WFL4 | WFL4 | WFL4 | WFL4 | WFL5 Vote on topics | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL5 | WFL6 Close registration | WFL7 Build the agenda | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 | WFL7 |
| **UBA: Alex (Organizer)** | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Setup Wizard | SCR Open Registration Form | SCR Signup QR |  |  |  |  |  |  |  |  |  | SCR Attendees |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | SCR Close Registration | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda Builder | SCR Agenda |
| **UBA: Pat (Attendee)** |  |  |  |  |  |  |  |  |  |  | SCR Signup Form (title) | SCR Signup Form | SCR Signup Confirmation |  |  |  |  |  |  |  | SCR Suggest Topic Form |  | SCR Suggest Topic Form |  |  | SCR Suggest Topic Form |  |  | SCR Suggest Topic Form |  | SCR Topic Ballot | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  |  |  |  |  |  |  |  |  |  | SCR Agenda |
| **UBA: Riley (Attendee)** |  |  |  |  |  |  |  |  |  |  |  |  |  | SCR Signup Form (title) | SCR Signup Form |  |  |  |  |  |  | SCR Suggest Topic Form |  | SCR Suggest Topic Form |  |  | SCR Suggest Topic Form |  |  |  | SCR Topic Ballot |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  |  |  |  |  |  |  |  |  |  |  |  | SCR Agenda |
| **UBA: Jordan (Attendee)** |  |  |  |  |  |  |  |  |  |  |  |  |  |  |  | SCR Signup Form (title) | SCR Signup Form | SCR Signup Form | SCR Signup Form |  |  |  | SCR Suggest Topic Form |  | SCR Suggest Topic Form |  |  | SCR Suggest Topic Form |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  | SCR Topic Ballot |  |  |  |  |  |  |  |  |  |  |  |  | SCR Agenda |
| **MBA** | COM Set Conference Dates | COM Add Conference Room | COM Add Conference Room |  | COM Add Session Span | COM Add Session Span |  | RMO Conference Setup | COM Open Registration | RMO Signup QR ← MAP Signup Url |  | COM Register Attendee | RMO Registrations |  | COM Register Attendee |  | COM Register Attendee |  | COM Register Attendee | RMO Registrations | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | COM Suggest Topic | RMO Topic Ballot | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Cast Vote | COM Close Registration | RMO Agenda Builder | COM Schedule Session | COM Schedule Session | COM Schedule Session | COM Schedule Session | COM Schedule Session | COM Schedule Session | COM Schedule Session | COM Schedule Session | RMO Agenda Builder | RMO Agenda |
| **LBA: Unconference** | EVT Conference Dates Set | EVT Conference Room Added | EVT Conference Room Added |  | EVT Session Span Added | EVT Session Span Added |  |  | EVT Registration Opened |  |  | EVT Attendee Registered |  |  | EVT Attendee Registered |  | ERR Name Required |  | EVT Attendee Registered |  | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested | EVT Topic Suggested |  | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Topic Voted | EVT Registration Closed |  | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled | EVT Session Scheduled |  |  |
| **GWT** | Set Dates → Dates Set | Add Room → Room Added | Add Room → Room Added |  | Add Span → Span Added | Add Span → Span Added |  | Conference Setup → … | Open Registration → … | Signup QR → … |  | Register → … | Registrations → … |  |  |  | Register → Name Required |  | Register → … | Registrations → … | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Suggest → Topic Suggested | Topic Ballot → … |  |  |  |  |  |  |  |  |  |  |  | Agenda Builder → … | Schedule → … |  |  |  |  |  |  |  | Agenda Builder → … | Agenda → … |

**Slice types:**
- COM slices: SLC1, SLC2, SLC3, SLC5, SLC6, SLC9, SLC12, SLC15, SLC17, SLC19, SLC21, SLC22, SLC23, SLC24, SLC25, SLC26, SLC27, SLC28, SLC29, SLC30, SLC32, SLC33, SLC34, SLC35, SLC36, SLC37, SLC38, SLC39, SLC40, SLC41, SLC42, SLC44, SLC45, SLC46, SLC47, SLC48, SLC49, SLC50, SLC51
- RMO slices: SLC8, SLC10, SLC13, SLC20, SLC31, SLC43, SLC52, SLC53
- NAV slices: SLC4, SLC7, SLC11, SLC14, SLC16, SLC18

**Arrows between SLCs:**
- EVT Conference Dates Set (SLC1) → RMO Conference Setup (SLC8)
- EVT Conference Room Added (SLC2) → RMO Conference Setup (SLC8)
- EVT Conference Room Added (SLC3) → RMO Conference Setup (SLC8)
- EVT Session Span Added (SLC5) → RMO Conference Setup (SLC8)
- EVT Session Span Added (SLC6) → RMO Conference Setup (SLC8)
- EVT Conference Dates Set (SLC1) → RMO Agenda Builder (SLC43)
- EVT Conference Room Added (SLC2) → RMO Agenda Builder (SLC43)
- EVT Conference Room Added (SLC3) → RMO Agenda Builder (SLC43)
- EVT Session Span Added (SLC5) → RMO Agenda Builder (SLC43)
- EVT Session Span Added (SLC6) → RMO Agenda Builder (SLC43)
- EVT Conference Dates Set (SLC1) → RMO Agenda (SLC53)
- EVT Conference Room Added (SLC2) → RMO Agenda (SLC53)
- EVT Conference Room Added (SLC3) → RMO Agenda (SLC53)
- EVT Session Span Added (SLC5) → RMO Agenda (SLC53)
- EVT Session Span Added (SLC6) → RMO Agenda (SLC53)
- RMO Signup QR (SLC10) → SCR Signup Form (Pat) (SLC11)
- RMO Signup QR (SLC10) → SCR Signup Form (Riley) (SLC14)
- RMO Signup QR (SLC10) → SCR Signup Form (Jordan) (SLC16)
- ERR Name Required (SLC17) → SCR Signup Form (SLC18)
- EVT Registration Opened (SLC9) → MAP Signup Url → RMO Signup QR (SLC10)
- EVT Attendee Registered (SLC12) → RMO Registrations (SLC13)
- EVT Attendee Registered (SLC15) → RMO Registrations (SLC20)
- EVT Attendee Registered (SLC19) → RMO Registrations (SLC20)
- EVT Attendee Registered (SLC12) → RMO Topic Ballot (SLC31)
- EVT Attendee Registered (SLC15) → RMO Topic Ballot (SLC31)
- EVT Attendee Registered (SLC19) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC21) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC22) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC23) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC24) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC25) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC26) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC27) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC28) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC29) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC30) → RMO Topic Ballot (SLC31)
- EVT Topic Suggested (SLC21) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC22) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC23) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC24) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC25) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC26) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC27) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC28) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC29) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC30) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC32) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC33) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC34) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC35) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC36) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC37) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC38) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC39) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC40) → RMO Agenda Builder (SLC43)
- EVT Topic Voted (SLC41) → RMO Agenda Builder (SLC43)
- EVT Topic Suggested (SLC21) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC22) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC23) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC24) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC25) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC26) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC27) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC28) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC29) → RMO Agenda (SLC53)
- EVT Topic Suggested (SLC30) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC44) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC45) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC46) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC47) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC48) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC49) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC50) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC51) → RMO Agenda Builder (SLC52)
- EVT Session Scheduled (SLC44) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC45) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC46) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC47) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC48) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC49) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC50) → RMO Agenda (SLC53)
- EVT Session Scheduled (SLC51) → RMO Agenda (SLC53)

**Properties:**

**WFL1 Set up conference (SLC1–SLC8)**

Dates are a single range (startDate..endDate); that range is every calendar day sessions may use. Rooms and session times still use Add / Next (or Finish).

**SLC1 (COM slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (active)
    - date picker Start (startDate) = 2026-06-12
    - date picker End (endDate) = 2026-06-13
    - row, right-aligned
      - button Next → COM Set Conference Dates
  - area Rooms (idle, empty)
  - area Session times (idle, empty)
- COM Set Conference Dates: eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13
- EVT Conference Dates Set (Unconference): eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13
- GWT timeline (COM Set Conference Dates):
  - COM Set Conference Dates: eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13
  - EVT Conference Dates Set (Unconference): eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13

**SLC2 (COM slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done; carried — no rmo-data)
    - label Start (startDate) = 2026-06-12
    - label End (endDate) = 2026-06-13
  - area Rooms (active)
    - text box Room (room) = Room A
    - row, right-aligned
      - button Add (pressed) → COM Add Conference Room
  - area Session times (idle, empty)
- COM Add Conference Room: eventId = E-1, room = Room A
  - (eventId from prior setup — client-side hidden data)
- EVT Conference Room Added (Unconference): eventId = E-1, room = Room A

**SLC3 (COM slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done)
  - area Rooms (active)
    - label (room) = Room A
    - text box Room (room) = Room B
    - row, right-aligned
      - button Add (pressed) → COM Add Conference Room
      - button Next → SCR Setup Wizard (Session times)
  - area Session times (idle, empty)
- COM Add Conference Room: eventId = E-1, room = Room B
- EVT Conference Room Added (Unconference): eventId = E-1, room = Room B

**SLC4 (NAV slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done)
  - area Rooms (active; carried — no rmo-data)
    - label (room) = Room A
    - label (room) = Room B
    - text box Room (room) = (empty)
    - row, right-aligned
      - button Add → COM Add Conference Room
      - button Next (pressed) → SCR Setup Wizard (Session times)
  - area Session times (idle, empty)

**SLC5 (COM slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done)
  - area Rooms (done)
  - area Session times (active)
    - text box Name (label) = Morning
    - text box Start (startTime) = 09:00
    - text box End (endTime) = 12:00
    - row, right-aligned
      - button Add (pressed) → COM Add Session Span
- COM Add Session Span: eventId = E-1, spanId = morning, label = Morning, startTime = 09:00, endTime = 12:00
- EVT Session Span Added (Unconference): eventId = E-1, spanId = morning, label = Morning, startTime = 09:00, endTime = 12:00

**SLC6 (COM slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done)
  - area Rooms (done)
  - area Session times (active)
    - label (label) = Morning · 09:00–12:00
    - text box Name (label) = Afternoon
    - text box Start (startTime) = 13:00
    - text box End (endTime) = 16:00
    - row, right-aligned
      - button Add (pressed) → COM Add Session Span
      - button Finish → SCR Setup Wizard (summary)
- COM Add Session Span: eventId = E-1, spanId = afternoon, label = Afternoon, startTime = 13:00, endTime = 16:00
- EVT Session Span Added (Unconference): eventId = E-1, spanId = afternoon, label = Afternoon, startTime = 13:00, endTime = 16:00

**SLC7 (NAV slice)**
- SCR Setup Wizard (Alex):
  - heading Set up conference
  - area Dates (done)
  - area Rooms (done)
  - area Session times (active; carried — no rmo-data)
    - label (label) = Morning · 09:00–12:00
    - label (label) = Afternoon · 13:00–16:00
    - text box Name (label) = (empty)
    - text box Start (startTime) = (empty)
    - text box End (endTime) = (empty)
    - row, right-aligned
      - button Add → COM Add Session Span
      - button Finish (pressed) → SCR Setup Wizard (summary)

**SLC8 (RMO slice)**
- RMO Conference Setup (Unconference):
  - startDate = 2026-06-12, endDate = 2026-06-13 (implies schedule days 2026-06-12, 2026-06-13)
  - rooms: Room A; Room B
  - spans: morning | Morning | 09:00–12:00; afternoon | Afternoon | 13:00–16:00
- SCR Setup Wizard (Alex):
  - heading Conference layout
  - area Dates
    - label Start (startDate) = 2026-06-12
    - label End (endDate) = 2026-06-13
  - area Rooms
    - label (room) = Room A
    - label (room) = Room B
  - area Session times
    - label (label) = Morning · 09:00–12:00
    - label (label) = Afternoon · 13:00–16:00
- EMD GWT timeline (RMO Conference Setup):
  - RMO Conference Setup (Unconference): (empty) startDate; endDate; rooms []; spans []
  - EVT Conference Dates Set (SLC1): eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13
  - RMO Conference Setup: startDate = 2026-06-12, endDate = 2026-06-13; rooms []; spans []
  - EVT Conference Room Added (SLC2): eventId = E-1, room = Room A
  - RMO Conference Setup: …; rooms [Room A]; spans []
  - EVT Conference Room Added (SLC3): eventId = E-1, room = Room B
  - RMO Conference Setup: …; rooms [Room A, Room B]; spans []
  - EVT Session Span Added (SLC5): …
  - RMO Conference Setup: …; spans [morning …]
  - EVT Session Span Added (SLC6): …
  - RMO Conference Setup: …; spans [morning …; afternoon …]

**WFL2 Open registration (SLC9–SLC10)**

**SLC9 (COM slice)**
- SCR Open Registration Form (Alex):
  - heading Open Registration
  - column
    - text box Conference (title) = Adaptech Unconference 2026
  - row, right-aligned
    - button Open → COM Open Registration
- COM Open Registration: title = Adaptech Unconference 2026, eventId = E-1, signupToken = tok-7f3a
  - (eventId from conference setup — client-side hidden data)
- EVT Registration Opened (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a
- GWT timeline (COM Open Registration):
  - EVT Conference Dates Set (SLC1): eventId = E-1, startDate = 2026-06-12, endDate = 2026-06-13
  - COM Open Registration: title = Adaptech Unconference 2026, eventId = E-1, signupToken = tok-7f3a
  - EVT Registration Opened (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a

**SLC10 (RMO slice)**
- MAP Signup Url: signupToken → signupUrl, on EVT Registration Opened → RMO Signup QR
- RMO Signup QR (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupUrl = https://unconf.example/signup/tok-7f3a
- SCR Signup QR (Alex):
  - heading Signup QR
  - column
    - label (title) = Adaptech Unconference 2026
    - label Signup link (signupUrl) = https://unconf.example/signup/tok-7f3a
    - label QR (qr) = signupUrl
- EMD GWT timeline (RMO Signup QR):
  - RMO Signup QR (Unconference): (empty) eventId, title, signupUrl
  - EVT Registration Opened (SLC9): eventId = E-1, title = Adaptech Unconference 2026, signupToken = tok-7f3a
  - RMO Signup QR (Unconference): eventId = E-1, title = Adaptech Unconference 2026, signupUrl = https://unconf.example/signup/tok-7f3a

**WFL3 Sign up as an attendee (SLC11–SLC20)**

**SLC11 (NAV slice)**
- SCR Signup Form (Pat):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC12 (COM slice)**
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

**SLC13 (RMO slice)**
- RMO Registrations (Unconference):
  - A-1 | Pat Lee | E-1
- SCR Signup Confirmation (Pat):
  - heading You're signed up
  - column
    - label (name) = Pat Lee
    - (attendeeId stays on the RMO / COM path — not on the wire)
- EMD GWT timeline (RMO Registrations):
  - RMO Registrations (Unconference): (empty) Registrations []
  - EVT Attendee Registered (SLC12): attendeeId = A-1, eventId = E-1, name = Pat Lee
  - RMO Registrations: A-1 | Pat Lee | E-1

**SLC14 (NAV slice)**
- SCR Signup Form (Riley):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC15 (COM slice)**
- SCR Signup Form (Riley):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = Riley Ng
  - row, right-aligned
    - button Sign up → COM Register Attendee
- COM Register Attendee: eventId = E-1, signupToken = tok-7f3a, name = Riley Ng, attendeeId = A-2
- EVT Attendee Registered (Unconference): attendeeId = A-2, eventId = E-1, name = Riley Ng

**SLC16 (NAV slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
  - row, right-aligned
    - button Sign up
- (no COM, no RMO)

**SLC17 (COM slice, ERR)**
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

**SLC18 (NAV slice)**
- SCR Signup Form (Jordan):
  - heading Sign Up
  - column
    - label (title) = Adaptech Unconference 2026
    - text box Name (name) = (empty)
    - label (message) = Name is required
  - row, right-aligned
    - button Sign up → COM Register Attendee
- (no COM, no RMO)

**SLC19 (COM slice)**
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

**SLC20 (RMO slice)**
- RMO Registrations (Unconference):
  - A-1 | Pat Lee | E-1
  - A-2 | Riley Ng | E-1
  - A-3 | Jordan Ortiz | E-1
- SCR Attendees (Alex):
  - heading Attendees
  - table Name: Pat Lee; Riley Ng; Jordan Ortiz
- EMD GWT timeline (RMO Registrations, attendees phase):
  - RMO Registrations: (SLC13 final — A-1 | Pat Lee | E-1)
  - EVT Attendee Registered (SLC15): attendeeId = A-2, eventId = E-1, name = Riley Ng
  - RMO Registrations: A-1 | Pat Lee | E-1; A-2 | Riley Ng | E-1
  - EVT Attendee Registered (SLC19): attendeeId = A-3, eventId = E-1, name = Jordan Ortiz
  - RMO Registrations: A-1 | Pat Lee | E-1; A-2 | Riley Ng | E-1; A-3 | Jordan Ortiz | E-1

**WFL4 Suggest a topic (SLC21–SLC30)**

Ten topic suggestions from attendees. T-3 (Lunch spots nearby) and T-9 (Coffee cart logistics) will never receive a vote.

**SLC21 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Event Sourcing in Practice
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation, topicId = T-1
- EVT Topic Suggested (Unconference): topicId = T-1, eventId = E-1, attendeeId = A-1, title = Event Sourcing in Practice, kind = presentation

**SLC22 (COM slice)**
- SCR Suggest Topic Form (Riley):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Remote Facilitation
    - dropdown Kind (kind) = fishBowl
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl, topicId = T-2
- EVT Topic Suggested (Unconference): topicId = T-2, eventId = E-1, attendeeId = A-2, title = Remote Facilitation, kind = fishBowl

**SLC23 (COM slice)**
- SCR Suggest Topic Form (Jordan):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Lunch spots nearby
    - dropdown Kind (kind) = suggestion
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion, topicId = T-3
- EVT Topic Suggested (Unconference): topicId = T-3, eventId = E-1, attendeeId = A-3, title = Lunch spots nearby, kind = suggestion

**SLC24 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = CQRS Patterns
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = CQRS Patterns, kind = presentation, topicId = T-4
- EVT Topic Suggested (Unconference): topicId = T-4, eventId = E-1, attendeeId = A-1, title = CQRS Patterns, kind = presentation

**SLC25 (COM slice)**
- SCR Suggest Topic Form (Riley):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Mob Programming Rituals
    - dropdown Kind (kind) = fishBowl
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-2, title = Mob Programming Rituals, kind = fishBowl, topicId = T-5
- EVT Topic Suggested (Unconference): topicId = T-5, eventId = E-1, attendeeId = A-2, title = Mob Programming Rituals, kind = fishBowl

**SLC26 (COM slice)**
- SCR Suggest Topic Form (Jordan):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Accessibility in SPAs
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-3, title = Accessibility in SPAs, kind = presentation, topicId = T-6
- EVT Topic Suggested (Unconference): topicId = T-6, eventId = E-1, attendeeId = A-3, title = Accessibility in SPAs, kind = presentation

**SLC27 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Domain Storytelling
    - dropdown Kind (kind) = fishBowl
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = Domain Storytelling, kind = fishBowl, topicId = T-7
- EVT Topic Suggested (Unconference): topicId = T-7, eventId = E-1, attendeeId = A-1, title = Domain Storytelling, kind = fishBowl

**SLC28 (COM slice)**
- SCR Suggest Topic Form (Riley):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Open Space Dynamics
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-2, title = Open Space Dynamics, kind = presentation, topicId = T-8
- EVT Topic Suggested (Unconference): topicId = T-8, eventId = E-1, attendeeId = A-2, title = Open Space Dynamics, kind = presentation

**SLC29 (COM slice)**
- SCR Suggest Topic Form (Jordan):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Coffee cart logistics
    - dropdown Kind (kind) = suggestion
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-3, title = Coffee cart logistics, kind = suggestion, topicId = T-9
- EVT Topic Suggested (Unconference): topicId = T-9, eventId = E-1, attendeeId = A-3, title = Coffee cart logistics, kind = suggestion

**SLC30 (COM slice)**
- SCR Suggest Topic Form (Pat):
  - heading Suggest a Topic
  - column
    - text box Topic (title) = Testing Event Models
    - dropdown Kind (kind) = presentation
  - row, right-aligned
    - button Suggest → COM Suggest Topic
- COM Suggest Topic: eventId = E-1, attendeeId = A-1, title = Testing Event Models, kind = presentation, topicId = T-10
- EVT Topic Suggested (Unconference): topicId = T-10, eventId = E-1, attendeeId = A-1, title = Testing Event Models, kind = presentation

**WFL5 Vote on topics (SLC31–SLC41)**

**SLC31 (RMO slice)**
- RMO Topic Ballot (Unconference):
  - lookup attendeeId → name: A-1 → Pat Lee; A-2 → Riley Ng; A-3 → Jordan Ortiz
  - one row per topic:
    - T-1 | Event Sourcing in Practice | presentation | Pat Lee
    - T-2 | Remote Facilitation | fishBowl | Riley Ng
    - T-3 | Lunch spots nearby | suggestion | Jordan Ortiz
    - T-4 | CQRS Patterns | presentation | Pat Lee
    - T-5 | Mob Programming Rituals | fishBowl | Riley Ng
    - T-6 | Accessibility in SPAs | presentation | Jordan Ortiz
    - T-7 | Domain Storytelling | fishBowl | Pat Lee
    - T-8 | Open Space Dynamics | presentation | Riley Ng
    - T-9 | Coffee cart logistics | suggestion | Jordan Ortiz
    - T-10 | Testing Event Models | presentation | Pat Lee
- SCR Topic Ballot (Pat / Riley / Jordan): table of all 10 topics with Vote buttons (rmo-data)
- EMD GWT timeline (RMO Topic Ballot):
  - RMO Topic Ballot: (empty) Topic Ballot []; lookups []
  - EVT Attendee Registered / EVT Topic Suggested in story order with RMO after each (lookups then rows grow to T-1…T-10)

**SLC32 (COM slice)**
- SCR Topic Ballot (Pat): Vote pressed on Remote Facilitation (T-2)
- COM Cast Vote: topicId = T-2, attendeeId = A-1, voteId = V-1
- EVT Topic Voted (Unconference): voteId = V-1, topicId = T-2, attendeeId = A-1

**SLC33 (COM slice)**
- SCR Topic Ballot (Riley): Vote pressed on Event Sourcing in Practice (T-1)
- COM Cast Vote: topicId = T-1, attendeeId = A-2, voteId = V-2
- EVT Topic Voted (Unconference): voteId = V-2, topicId = T-1, attendeeId = A-2

**SLC34 (COM slice)**
- SCR Topic Ballot (Jordan): Vote pressed on Event Sourcing in Practice (T-1)
- COM Cast Vote: topicId = T-1, attendeeId = A-3, voteId = V-3
- EVT Topic Voted (Unconference): voteId = V-3, topicId = T-1, attendeeId = A-3

**SLC35 (COM slice)**
- SCR Topic Ballot (Pat): Vote pressed on CQRS Patterns (T-4)
- COM Cast Vote: topicId = T-4, attendeeId = A-1, voteId = V-4
- EVT Topic Voted (Unconference): voteId = V-4, topicId = T-4, attendeeId = A-1

**SLC36 (COM slice)**
- SCR Topic Ballot (Riley): Vote pressed on Mob Programming Rituals (T-5)
- COM Cast Vote: topicId = T-5, attendeeId = A-2, voteId = V-5
- EVT Topic Voted (Unconference): voteId = V-5, topicId = T-5, attendeeId = A-2

**SLC37 (COM slice)**
- SCR Topic Ballot (Jordan): Vote pressed on Accessibility in SPAs (T-6)
- COM Cast Vote: topicId = T-6, attendeeId = A-3, voteId = V-6
- EVT Topic Voted (Unconference): voteId = V-6, topicId = T-6, attendeeId = A-3

**SLC38 (COM slice)**
- SCR Topic Ballot (Pat): Vote pressed on Accessibility in SPAs (T-6)
- COM Cast Vote: topicId = T-6, attendeeId = A-1, voteId = V-7
- EVT Topic Voted (Unconference): voteId = V-7, topicId = T-6, attendeeId = A-1

**SLC39 (COM slice)**
- SCR Topic Ballot (Riley): Vote pressed on Domain Storytelling (T-7)
- COM Cast Vote: topicId = T-7, attendeeId = A-2, voteId = V-8
- EVT Topic Voted (Unconference): voteId = V-8, topicId = T-7, attendeeId = A-2

**SLC40 (COM slice)**
- SCR Topic Ballot (Jordan): Vote pressed on Open Space Dynamics (T-8)
- COM Cast Vote: topicId = T-8, attendeeId = A-3, voteId = V-9
- EVT Topic Voted (Unconference): voteId = V-9, topicId = T-8, attendeeId = A-3

**SLC41 (COM slice)**
- SCR Topic Ballot (Pat): Vote pressed on Testing Event Models (T-10)
- COM Cast Vote: topicId = T-10, attendeeId = A-1, voteId = V-10
- EVT Topic Voted (Unconference): voteId = V-10, topicId = T-10, attendeeId = A-1

**WFL6 Close registration (SLC42)**

**SLC42 (COM slice)**
- SCR Close Registration (Alex):
  - heading Close Registration
  - Attendees = 3
  - button Close → COM Close Registration
- COM Close Registration: eventId = E-1
- EVT Registration Closed (Unconference): eventId = E-1

**WFL7 Build the agenda (SLC43–SLC53)**

Eight schedule slots (2 dates × 2 spans × 2 rooms). Organizer schedules the eight topics that received votes; T-3 and T-9 remain unassigned with voteCount 0. SLC43 Agenda Builder projects only earlier EVTs (setup, topics, votes) with an empty schedule — do not join Session Scheduled here. SLC44–SLC51 Schedule SCRs carry filled cells and trimmed unassigned by session from prior Schedule COMs. SLC52 is a second Agenda Builder RMO that projects only the Session Scheduled EVTs on top of that prior state. RMO Agenda (SLC53) projects Session Scheduled for attendees.

**SLC43 (RMO slice)**
- RMO Agenda Builder (Unconference):
  - dates: 2026-06-12; 2026-06-13
  - rooms: Room A; Room B
  - spans: morning | Morning | 09:00–12:00; afternoon | Afternoon | 13:00–16:00
  - unassigned (sorted by voteCount desc):
    - T-1 | Event Sourcing in Practice | presentation | 2
    - T-6 | Accessibility in SPAs | presentation | 2
    - T-10 | Testing Event Models | presentation | 1
    - T-2 | Remote Facilitation | fishBowl | 1
    - T-4 | CQRS Patterns | presentation | 1
    - T-5 | Mob Programming Rituals | fishBowl | 1
    - T-7 | Domain Storytelling | fishBowl | 1
    - T-8 | Open Space Dynamics | presentation | 1
    - T-3 | Lunch spots nearby | suggestion | 0
    - T-9 | Coffee cart logistics | suggestion | 0
  - schedule: 8 empty cells
- SCR Agenda Builder (Alex): Schedule grid radios + Unassigned radios + Schedule button
- EMD GWT timeline (RMO Agenda Builder):
  - RMO Agenda Builder: (empty) dates []; rooms []; spans []; unassigned []; schedule []
  - EVT Conference Dates Set / Room Added / Session Span Added / Topic Suggested ×10 / Topic Voted ×10 with RMO after each → final unassigned sorted by voteCount; schedule still empty

**SLC44 (COM slice)**
- SCR Agenda Builder (Alex): select Event Sourcing in Practice into 2026-06-12 / Morning / Room A
- COM Schedule Session: topicId = T-1, date = 2026-06-12, spanId = morning, room = Room A, sessionId = S-1
- EVT Session Scheduled: sessionId = S-1, topicId = T-1, date = 2026-06-12, spanId = morning, room = Room A

**SLC45 (COM slice)**
- SCR Agenda Builder (Alex): select Accessibility in SPAs into 2026-06-12 / Morning / Room B
- COM Schedule Session: topicId = T-6, date = 2026-06-12, spanId = morning, room = Room B, sessionId = S-2
- EVT Session Scheduled: sessionId = S-2, topicId = T-6, date = 2026-06-12, spanId = morning, room = Room B

**SLC46 (COM slice)**
- SCR Agenda Builder (Alex): select Remote Facilitation into 2026-06-12 / Afternoon / Room A
- COM Schedule Session: topicId = T-2, date = 2026-06-12, spanId = afternoon, room = Room A, sessionId = S-3
- EVT Session Scheduled: sessionId = S-3, topicId = T-2, date = 2026-06-12, spanId = afternoon, room = Room A

**SLC47 (COM slice)**
- SCR Agenda Builder (Alex): select CQRS Patterns into 2026-06-12 / Afternoon / Room B
- COM Schedule Session: topicId = T-4, date = 2026-06-12, spanId = afternoon, room = Room B, sessionId = S-4
- EVT Session Scheduled: sessionId = S-4, topicId = T-4, date = 2026-06-12, spanId = afternoon, room = Room B

**SLC48 (COM slice)**
- SCR Agenda Builder (Alex): select Mob Programming Rituals into 2026-06-13 / Morning / Room A
- COM Schedule Session: topicId = T-5, date = 2026-06-13, spanId = morning, room = Room A, sessionId = S-5
- EVT Session Scheduled: sessionId = S-5, topicId = T-5, date = 2026-06-13, spanId = morning, room = Room A

**SLC49 (COM slice)**
- SCR Agenda Builder (Alex): select Domain Storytelling into 2026-06-13 / Morning / Room B
- COM Schedule Session: topicId = T-7, date = 2026-06-13, spanId = morning, room = Room B, sessionId = S-6
- EVT Session Scheduled: sessionId = S-6, topicId = T-7, date = 2026-06-13, spanId = morning, room = Room B

**SLC50 (COM slice)**
- SCR Agenda Builder (Alex): select Open Space Dynamics into 2026-06-13 / Afternoon / Room A
- COM Schedule Session: topicId = T-8, date = 2026-06-13, spanId = afternoon, room = Room A, sessionId = S-7
- EVT Session Scheduled: sessionId = S-7, topicId = T-8, date = 2026-06-13, spanId = afternoon, room = Room A

**SLC51 (COM slice)**
- SCR Agenda Builder (Alex): select Testing Event Models into 2026-06-13 / Afternoon / Room B
- COM Schedule Session: topicId = T-10, date = 2026-06-13, spanId = afternoon, room = Room B, sessionId = S-8
- EVT Session Scheduled: sessionId = S-8, topicId = T-10, date = 2026-06-13, spanId = afternoon, room = Room B

**SLC52 (RMO slice)**
- RMO Agenda Builder (Unconference): same dates/rooms/spans as SLC43; schedule filled with S-1…S-8; unassigned = T-3, T-9 only
- SCR Agenda Builder (Alex): full grid filled; Unassigned shows T-3 and T-9
- EMD GWT timeline (RMO Agenda Builder, schedule phase):
  - RMO Agenda Builder: (SLC43 final state — unassigned sorted by voteCount; schedule empty)
  - EVT Session Scheduled ×8 (S-1…S-8) with RMO after each → schedule filled; unassigned T-3, T-9

**SLC53 (RMO slice)**
- RMO Agenda (Unconference): dates/rooms/spans from setup EVTs; lookups T-1…T-10 from Topic Suggested; 8 Agenda rows from Session Scheduled (join lookups for title, kind). T-3 and T-9 stay in lookups only.
- SCR Agenda (Alex / Pat / Riley / Jordan): full grid
  - 2026-06-12 Morning: Room A = Event Sourcing in Practice; Room B = Accessibility in SPAs
  - 2026-06-12 Afternoon: Room A = Remote Facilitation; Room B = CQRS Patterns
  - 2026-06-13 Morning: Room A = Mob Programming Rituals; Room B = Domain Storytelling
  - 2026-06-13 Afternoon: Room A = Open Space Dynamics; Room B = Testing Event Models
- EMD GWT timeline (RMO Agenda):
  - RMO Agenda: (empty) Agenda []; lookups []; dates []; rooms []; spans []
  - EVT Conference Dates Set / Room Added ×2 / Session Span Added ×2 → dates, rooms, spans
  - EVT Topic Suggested ×10 with RMO after each → lookups T-1…T-10
  - EVT Session Scheduled ×8 (S-1…S-8) with RMO after each → eight Agenda rows (title, kind from lookups)
