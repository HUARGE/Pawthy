PET HEALTH PORTAL — PAWTHY
SENG 321 / BM 321 — Software Engineering | Sprint 1 — Project Foundation
Status: Collaborative Working Draft — not yet final submission
Version: 0.5 (API v0.6.3 alignment and editorial review)
Updated: 4 November 2026
Team: Seven students (named assignments to be inserted)
Repository: HUARGE / Pawthy (exact repository URL to be verified)
Draft status. This document is aligned with the supplied Pawthy OpenAPI v0.6.3 working contract, including reinstated owner-entered Health Diary functionality and the veterinarian-managed visual calendar. It contains 6 Epics and 13 sequential User Stories, with provisional 105 Story Points. API, effort estimates, named role assignments and GitHub evidence still require final team verification. This is a collaborative draft, not a verified final submission.
Document Contents
Project Proposal
System Vision and Boundary (with system context diagram)
Initial Product Backlog — 6 Epics, 13 User Stories
Team Roles, RACI and Team Review Notes
GitHub Organization and Git-Flow — evidence to be completed
OpenAPI Draft v0.6.3 — contract summary and traceability
Submission Readiness Checklist
1. Project Proposal
Project Name: Pet Health Portal (Pawthy)
Project Type: Web-Based Veterinary Health Management and Pet Owner Diary System
Course: SENG 321 / BM 321 — Software Engineering
Review State: Collaborative draft aligned to the supplied v0.6.3 API; implementation and repository evidence pending review
1.1 Problem Statement and Supporting Evidence
Problem Statement
Pet owners are responsible for monitoring their animals' health, remembering important symptoms, following vaccination schedules, and attending veterinary appointments. However, health-related information can be difficult to organize over time, particularly when an owner has multiple pets.
Veterinarians maintain professional medical records and explain examination findings and treatments during clinic visits. Pet owners may find it difficult to remember every detail afterward or recall small changes in their pets' condition between appointments. For example, decreased appetite, unusual behavior, or digestive symptoms may occur at different times and be forgotten by the next veterinary visit.
Pet Health Portal aims to address these difficulties through a centralized website where pet owners maintain personal health diaries, review veterinary records, follow vaccination information, and view the clinic's published availability before contacting the clinic directly.
Supporting Evidence — Initial Veterinary Interview
One interview was conducted with a veterinary medicine graduate who had practical experience in a veterinary clinic. The interviewee reported that the clinic used a computer-based record system and that health information was communicated during consultations. The interview indicated that owners could benefit from being able to revisit these records afterward, and from recording symptoms between visits to explain them more clearly.
The interview also noted that vaccination information might be recorded through different practices and that telephone reminders were used. It did not establish vaccination follow-up as a widespread problem. Existing phone/message-based appointment arrangements were not described as a major difficulty; accordingly, the team removed online appointment booking and retained an informational calendar and clinic contact workflow.
These findings represent one qualitative interview. They should not be generalized to all clinics or pet owners without further validation. Usability and stakeholder feedback will be collected throughout the project.
1.2 Stakeholder Identification and Analysis
Primary Stakeholders
Pet Owners. Owners need convenient access to multiple pet profiles, personal symptom observations, veterinarian-entered medical and vaccination information, reminders, clinic availability and contact details. Owners cannot modify professional clinical records.
Veterinarian / Clinic Administrator. The initial system supports one veterinarian in one clinic. The veterinarian creates owner accounts and initial pet profiles, maintains professional clinical records, reviews owner observations, records vaccinations, manages the clinic's calendar and manually enters appointments arranged by telephone or in person. No additional clinic staff accounts are planned.
Secondary Stakeholders
Project Development Team. The seven-member team maintains requirements, architecture, implementation, testing, GitHub workflow and documentation.
Course Instructor. Evaluates requirements, engineering decisions, collaboration, deliverables and repository evidence.
Stakeholder
Project Influence
System Dependence
Main Needs / Expectations
Pet Owners
Medium
High
Accurate pet history; simple diary; vaccination reminders; readable calendar and clinic contact information
Veterinarian / Clinic Administrator
High
High
Authorized clinical records; owner diary access; vaccine management; manual schedule updates
Development Team
High
Medium
Testable requirements; manageable workload; clear ownership and technical interfaces
Course Instructor
High
Low
Traceable decisions; documented deliverables; verifiable individual contributions
The influence and dependence labels are preliminary stakeholder-analysis judgments for team review.
1.3 Proposed Solution and Expected Outcomes
Proposed Solution
Pet Health Portal is a web-based platform with two role-specific interfaces: Pet Owner and Veterinarian/Administrator. During an owner's initial clinic visit, the veterinarian creates the account, associates the first pet profile and gives the owner a secure system-generated temporary password in person. A mandatory first-login password change protects access. One account can manage several pets.
The platform provides pet identity and personal profile information; voluntary, timestamped symptom observations; history calendars and owner-reported statistical charts; professional veterinary medical records; manually entered vaccination histories and due dates; website-based vaccination reminders; a veterinarian-managed day/week calendar with recurring weekly hours, manually selected available/busy blocks, and manually recorded appointments; a clinic contact number; and a persistent Light/Dark Mode preference.
Appointments: Owners do not request or book appointments within the website. They review the published calendar and contact the veterinarian by phone or in person. The veterinarian records agreed appointments manually and updates the calendar. The online booking, approval queue, multi-pet duration summing and automatic tolerance-based slot reservation are out of scope.
Expected Outcomes
Make veterinarian-entered medical information accessible to authorized owners between visits.
Support owners in organizing observations and communicating relevant symptom patterns.
Help the veterinarian review owner-reported observations alongside professional records.
Organize vaccination history and website-only reminders without generating medical advice.
Make clinic schedule information and contact details easier to find while preserving the clinic's existing appointment process.
Support multiple pets within one owner account while enforcing role-based access.
Preliminary Success Metrics
Metric
Measurement
Proposed Target
Validation Method
Health diary usability
Participants successfully create and find a symptom observation
At least 85% task success
Guided usability tasks
Medical information accessibility
Participants locate a pet's previous veterinary record and vaccination information
At least 85% task success
Guided usability tasks
Calendar and contact usability
Participants find clinic availability/busy times and identify how to contact the clinic
At least 85% task success
Guided usability tasks
Role-based access control
Defined authorized/unauthorized access test cases pass
100% of defined test cases
Security and authorization tests
Multiple-pet account support
One owner can view only their own multiple registered pets
All defined scenarios pass
Functional acceptance tests
These are initial evaluation targets, not achieved outcomes. The calendar task replaces the retired online-booking usability metric.
1.4 Feasibility Assessment
Technical Feasibility and Proposed Stack
Component
Selected / Proposed Technology
Purpose
Frontend
HTML, CSS, JavaScript
Responsive owner and veterinarian pages
Backend
Python / Django
Server-side logic, authorization, REST API
Database
MySQL
Persistent account, pet and clinical data
API architecture
REST, documented with OpenAPI
Contract between frontend and backend
Collaboration
Git and GitHub
Issues, feature branches, review history, release evidence
Open-source license
Apache License 2.0
Reuse, redistribution and explicit patent-license terms
The authentication, diary, medical records, reminder logic and manual calendar management are suitable for a student software-engineering prototype, subject to implementation and testing. Removing online booking reduces scheduling complexity, although day/week display, recurring hours, availability exceptions and interval-conflict checks still require development. The technical team must confirm the final implementation approach.
Data Feasibility
Fictional or appropriately anonymized pet profiles, examinations, diary observations, vaccinations and sample appointments can be used during development and acceptance testing. Actual patient medical data are not required for the class prototype.
Integration Feasibility
The initial version is a standalone website with no connection to national pet registries, external veterinary databases, SMS/email services or payment processors. Website notifications relate primarily to vaccination reminders; appointment communication remains through existing clinic contact methods.
Operational Feasibility
The portal supplements rather than replaces an existing clinic workflow. The veterinarian maintains professional clinical data and the schedule; owners maintain personal observations and browse appropriate clinic information. The interface should remain simple and usable without advanced technical knowledge.
Team and Schedule Feasibility
The system is being planned and developed by a seven-person team under the course's sprint schedule. Large stories (for example, pet-photo management, diary charts and reminder workflows) should be broken into sprint-sized implementation tasks. Story Point estimates do not represent guaranteed delivery capacity.
Legal, Security and Ethical Feasibility
The portal must protect personal account information and veterinary records through secure authentication, role-based permissions and appropriate handling of credentials and photos. Owners cannot modify professional medical records. The application does not provide diagnosis, treatment recommendations or emergency medical care.
1.5 Initial Release Scope
Included Features
Feature Area
Included Capability
Main Role
Account Management
Vet-created owner accounts; one-time temporary password; mandatory password change
Veterinarian / Owner
Access Control
Owner–pet authorization and vet-only clinical updates
Both
Pet Profiles
Multiple pets; vet-controlled identity; owner-only private notes/photos; archive status
Both
Daily Health Diary
Optional timestamped symptom entries; multiple entries per day; owner-only editing
Owner; vet read-only
Diary History
Calendar, symptom frequency and timeline charts; no AI diagnosis
Both
Medical Records
Vet-controlled visit types and flexible clinical fields; owner read-only
Both
Vaccination Records
Manual administered/next-due dates and species-specific vaccine name choices
Vet create; owner view
Vaccination Reminders
Seven-day lead time, due today and overdue, website-only
Both
Availability Calendar
Day/week time-block interface; recurring weekly hours; manual available/busy periods; privacy-safe owner view
Vet manage; owner view
Manual Appointment Records
Vet creates, reschedules and cancels phone/in-person appointments; minimum 10 minutes; no overlaps
Veterinarian
Clinic Contact Details
Phone/urgent contact information beside calendar and on homepage
Vet manage; owner view
Theme
Manual Light/Dark switch with saved per-user preference
Both
Excluded / Deferred Features
Excluded Feature
Reason or Boundary
Owner-initiated online appointment booking and approval
Removed by team after scope/complexity review
Automatic scheduling with duration tolerance and slot reservation
Not needed for the chosen phone/in-person process
Multiple veterinary clinics and additional clinic staff roles
Initial pilot limited to one clinic and veterinarian
Owner self-registration or pet ownership transfers
Clinic-managed access and registration
AI diagnosis, drug advice and automated vaccine recommendations
Professional judgment remains with veterinarian
Official pet passports / national registries
No external government/clinical integrations
Payments, invoices and billing
Outside the health-record focus
SMS, email, external push and automated emergency service
Website-only features; direct clinic contact
Reproductive/estrous-cycle tracking
Not part of initial requirements
MVP Distinction
The first working MVP should prioritize authentication, a basic pet profile, owner diary entry and veterinarian-controlled medical records. Advanced charts, some reminder workflows and interface personalization may be implemented later according to the team's confirmed sprint plan. The full initial release scope does not mean every feature must appear in the first MVP increment.
1.6 Assumptions, Risks and Validation Actions
Assumptions
ID
Assumption
A-01
The initial system serves one veterinary clinic with one veterinarian/administrator.
A-02
The veterinarian creates owner accounts and initial pet profiles at the first visit.
A-03
Pet owners have internet access and a compatible browser.
A-04
Personal health diary entries are voluntary; days without entries are missing data.
A-05
The veterinarian maintains professional medical and vaccination data and manually enters agreed appointments.
A-06
Appointments are arranged outside the website by telephone or in person.
Risk Assessment
ID
Risk
Impact
Mitigation Strategy
R-01
Delivery scope exceeds available student development time
High
Prioritize core features and split large stories across sprints
R-02
Unauthorized access to records or owner-only photos
High
Authentication, role enforcement and negative authorization tests
R-03
Calendar availability becomes outdated or inconsistent with phone bookings
Medium
Vet-managed updates; test conflict prevention and privacy-safe calendar visibility
R-04
Health diary interface discourages regular use
Medium
Optional reporting and usability evaluation
R-05
Owners miss website-only vaccine reminders
Medium
Clear visibility in dashboard; usability testing
R-06
Temporary credentials are mishandled
High
Secure random one-time display; required first-login change
R-07
Mismatch among weekly hours, manual availability blocks and appointments
Medium
Apply documented precedence rules and test AVAILABLE, BUSY and CLOSED calendar intervals
Validation Actions
ID
Validation Action
Purpose
Proposed Evidence
V-01
Review the completed veterinary interview
Separate observed needs from unverified assumptions
Interview notes / requirements trace
V-02
Review refined stories and latest OpenAPI with technical team
Identify feasibility and inconsistencies
Meeting decisions and revised backlog
V-03
Conduct usability tests
Test diary entry, medical record access, calendar/contact information
At least 85% task completion per defined task
V-04
Perform authentication and authorization tests
Verify owners cannot access other users' data or modify clinical records
All defined access tests pass
V-05
Test manual calendar, conflicts and vaccine reminders
Check recurring hours, AVAILABLE/BUSY blocks, private data filtering, appointment overlap and vaccine due dates
Acceptance scenarios and test results
The team will continue to assess requirements through design review, implementation and usability feedback. The initial veterinary interview is not representative of all clinics.
1.7 Open-Source License Decision
Selected License: Apache License 2.0.
The license permits use, modification and distribution of original project source code, including commercial reuse, subject to its conditions. It includes an explicit patent grant. The repository should contain the full LICENSE file and any notices required by third-party dependencies. This license does not authorize disclosure of private owner information or clinical data.
2. System Vision and Boundary
2.1 System Vision
Pet Health Portal is a web-based platform for a single veterinary clinic, combining personal pet-health observations with veterinarian-maintained records. Pet owners can view associated pets, record symptoms when relevant, access veterinary medical histories, review vaccination information and reminders, and inspect the clinic calendar before contacting the clinic to arrange appointments. The veterinarian creates accounts and pet profiles, maintains professional records and manually updates the clinic's appointments and availability. The system supports communication and record organization, not professional medical diagnosis.
2.2 Target Users
Pet Owner: Can view their own pets' profiles and clinical histories; record, edit and delete personal observations; manage owner-only notes and photos; view vaccination reminders; view the clinic calendar and contact details. Cannot book online, change clinic availability or modify veterinary records.
Veterinarian / Clinic Administrator: Can create owner accounts and initial pet profiles; maintain clinic-controlled pet information and medical/vaccination records; review diary entries; manage recurring working hours and available/busy calendar blocks; manually enter, edit and cancel appointments arranged outside the website; and update clinic contact details.
2.3 System Responsibilities
Authenticate users, force initial temporary-password change, and apply role-based access.
Store owner accounts, multiple associated pet profiles and access-controlled photographs.
Store optional owner-submitted symptom entries and provide calendar/statistical history without diagnosis.
Maintain veterinarian-controlled examinations and vaccinations with read-only access for owners.
Display website-based vaccination reminders based on veterinarian-entered due dates.
Provide a day/week clinic calendar combining recurring weekly hours, veterinarian-selected AVAILABLE/BUSY blocks, exceptional closures and manually recorded appointments.
Support veterinarian-only manual appointment creation, editing and cancellation.
Display clinic phone/contact information for scheduling appointments outside the website.
Save each user's manually selected Light/Dark interface theme.
2.4 System Boundary
Inside the System Boundary
Owner authentication and profile authorization; pet records and personal diary entries.
Professional clinical and vaccination data, website reminders and statistical views.
Calendar display, veterinarian-maintained weekly working hours, manually selected availability blocks and exceptional blocked periods.
Manual veterinarian appointment records and clinic contact information.
User interface preferences and related application storage.
Outside the System Boundary
Owner-submitted online appointment requests, approval/rejection workflows and automatic booking algorithms.
Additional clinics or clinic staff accounts, external medical databases, national registration systems and official pet passports.
AI-assisted diagnosis, treatment planning or automatic vaccine schedule recommendations.
Billing, online payments, SMS/email/external push notifications and automated emergency service.
Ownership transfer and reproductive-cycle tracking.
2.5 External Interactions
External Actor
Data / Actions Sent to Portal
Data / Information Received from Portal
Pet Owner
Login credentials; personal health observations; private pet notes/photos; theme selection
Authorized pet profiles, clinical/vaccine histories, diary charts, reminder information, clinic calendar and contact details
Veterinarian / Administrator
Owner account creation; clinical/vaccination records; manual appointment entries; weekly schedule and available/busy block updates; clinic contact updates
Assigned pets, owner-entered observations, clinic-wide reminders, calendar records and authorized owner data
The initial release has no third-party authentication or scheduling integration. Direct telephone calls and in-person visits occur outside the software boundary.
2.6 Constraints and Assumptions
Constraints: Single clinic, one veterinarian administrator and a pet-owner role; Django and MySQL backend; HTML/CSS/JavaScript frontend; REST API architecture; browser-based access; seven-person academic team; no external messaging or payment provider.
Assumptions: Accounts are provisioned at the clinic; owners have browser access; personal diary entries are voluntary; the veterinarian maintains professional health data and the appointment calendar; fictional or anonymized information can support testing.
Calendar contract baseline (OpenAPI v0.6.3 draft): The veterinarian defines recurring weekly working hours and may override specific time ranges with AVAILABLE or BUSY blocks. Existing confirmed appointments remain BUSY, while periods outside working hours appear CLOSED unless explicitly opened. Only the veterinarian may create appointment records after phone or in-person contact. The server validates a minimum 10-minute duration and rejects overlapping appointments; owner-facing responses omit private appointment details. These definitions remain subject to technical-team acceptance, not a claim that the endpoints are implemented.
2.7 System Context Diagram
�
Figure 1. Single-clinic system context. Pet Owner and Veterinarian/Administrator are external actors. The Pet Health Portal is the system boundary; its database and web interface are internal implementation details.
3. Initial Product Backlog
Status: Team reviewed major scope decisions; API draft v0.6.3 supports the requirements; effort estimates, names and final technical review remain pending.
Backlog size: Six Epics, 13 sequentially numbered User Stories.
Provisional total: 105 Story Points, not a sprint commitment or measured velocity.
3.1 Epic Overview
Epic
Name
User Stories
Count
EP-01
User Authentication & Account Management
US-01–US-03
3
EP-02
Pet Profile Management
US-04
1
EP-03
Daily Health Diary
US-05–US-06
2
EP-04
Medical Records & Vaccinations
US-07–US-10
4
EP-05
Appointment Calendar & Manual Management
US-11–US-12
2
EP-06
Interface Personalization
US-13
1
3.2 Detailed User Stories
The acceptance criteria below describe agreed functionality except where a technical-alignment note explicitly identifies an unresolved decision. All Story Points and proposed implementation leads remain subject to the seven-person team's confirmation.
EP-01 — User Authentication & Account Management
US-01 — Create Pet Owner Accounts
Priority: Must Have (Rank 1)
Provisional Story Points: 5
Dependencies: Database and authentication setup
Suggested Implementation Lead: Backend Developer 1 (subject to team review)
User Story: As a veterinarian, I want to create individual accounts for pet owners so that they can securely access the platform and manage information about their pets.
Acceptance Criteria:
Account Creation
Only the veterinarian/administrator may create pet owner accounts through a Create User option in the administrative dashboard.
Accounts are created at the owner's first clinic visit and use a unique login identity.
The system generates a secure temporary password and displays it once to the veterinarian for in-person handover.
An owner account can be associated with multiple pet profiles; a separate login is not required for each pet.
Pet owners cannot self-register; unauthorized attempts to create accounts are rejected.
US-02 — First Login and Password Change
Priority: Must Have (Rank 2)
Provisional Story Points: 5
Dependencies: US-01
Suggested Implementation Lead: Backend Developer 1 (subject to team review)
User Story: As a pet owner, I want to log in using temporary credentials and change my password during my first login so that I can securely access my account.
Acceptance Criteria:
Login and Password Change
The pet owner can log in using credentials provided in person by the veterinarian.
A temporary password must be replaced before the owner accesses normal platform features.
After replacement, the previous temporary password is invalid.
Invalid credentials are rejected and passwords are never stored as plain text.
The pet owner may access the website later rather than being required to log in during the clinic visit.
US-03 — Role-Based Access Control
Priority: Must Have (Rank 3)
Provisional Story Points: 8
Dependencies: US-01, US-02
Suggested Implementation Lead: Backend Developer 1 (subject to team review)
User Story: As a system user, I want access to records and functions to be restricted according to my role so that personal and veterinary information remains protected.
Acceptance Criteria:
Pet Owner Permissions
Owners can access only their own accounts and associated pets.
Owners may create, view, edit and delete diary entries and personal tracking records they created.
Owners can view veterinarian-created medical and vaccination records but cannot create, edit or delete them.
Veterinarian Permissions
The veterinarian navigates through an owner record to access assigned pets, without logging in as that owner.
The veterinarian may view owner-created observations but cannot change or delete those entries.
Only the veterinarian creates/updates professional medical and vaccination records and maintains the clinic appointment calendar.
Unauthorized read or modification operations must be rejected by the server.
EP-02 — Pet Profile Management
US-04 — Manage Pet Profiles
Priority: Must Have (Rank 4)
Provisional Story Points: 13
Dependencies: US-01, US-03
Suggested Implementation Lead: Backend Developer 2 (subject to team review)
User Story: As a pet owner, I want to access and manage multiple pet profiles under one account so that I can organize the information for each animal.
Acceptance Criteria:
Pet Profiles and Identity
Only the veterinarian creates pet profiles and associates them with an existing owner account.
An owner may have multiple pets; the veterinarian opens the owner record and selects a pet.
The veterinarian controls the pet name, species, breed, birth date or estimated age, sex and optional microchip number.
Common species are selectable; Other Species supports manual entry of uncommon animals.
The veterinarian can upload and replace a clinical identification photo.
Personal Information
Owners may manage their own pet photos and private profile notes and can see the clinical identification photo.
The veterinarian sees the clinical identification photo but cannot view or edit the owner's private photo gallery and profile notes.
Veterinary identity fields are read-only to owners.
Pet Status and Archiving
The veterinarian can mark a pet as deceased or no longer active under the clinic's care.
Owners may archive a pet from their active list while retaining access to its historical information.
Archiving does not permanently delete clinical records; future vaccine reminders stop for deceased pets.
Transfer between owners or clinics and normal permanent deletion of clinical records are out of scope.
EP-03 — Daily Health Diary
US-05 — Record Health Observations
Priority: Must Have (Rank 5)
Provisional Story Points: 8
Dependencies: US-03, US-04
Suggested Implementation Lead: Frontend Developer 1 (subject to team review)
User Story: As a pet owner, I want to record symptoms or relevant health observations when I notice changes so that I can later share a useful history with my veterinarian.
Acceptance Criteria:
Observation Entry
Owners can create multiple timestamped entries for the same pet on the same day; entry is voluntary and not required daily.
The form offers predefined groups: Digestive Issues, Eating & Drinking, Energy & Behavior, Skin & Physical Symptoms, Other Symptoms and Normal / No Concerns.
Owners may select multiple symptoms and add optional free-text notes; at least one observation or note is required to save.
Each entry is linked to its selected pet and retains its own date and time.
Permissions and Meaning
Owners can view, edit and delete their own observations.
The veterinarian can read observations for assigned pets but cannot modify owner-created diary entries.
Missing diary days are treated as unrecorded data, not confirmation of normal health.
The application does not produce medical diagnoses from diary entries.
US-06 — Review Health Diary History
Priority: Should Have (Rank 6)
Provisional Story Points: 13
Dependencies: US-03, US-04, US-05
Suggested Implementation Lead: Frontend Developer 1 (subject to team review)
User Story: As a pet owner or veterinarian, I want to review past observations through a calendar and graphical summaries so that I can identify reported changes over time.
Acceptance Criteria:
History and Calendar
Owners see histories only for their pets; the veterinarian accesses authorized pets through the dashboard.
Observations are accessible in chronological history and by selecting a date on a calendar.
Multiple entries recorded on a day remain separate and keep their timestamps.
Normal / No Concerns entries are distinguishable from symptom-related entries; unrecorded days are not marked healthy.
Statistical Summaries
Both roles can view Symptom Frequency Charts and Symptom Timeline Charts for selectable date ranges.
Charts count owner-reported observations, not clinically confirmed disease incidence.
Charts reflect changes when a recorded entry is edited or deleted; no automated diagnosis is generated.
EP-04 — Medical Records & Vaccinations
US-07 — Manage Veterinary Medical Records
Priority: Must Have (Rank 7)
Provisional Story Points: 8
Dependencies: US-03, US-04
Suggested Implementation Lead: Backend Developer 2 (subject to team review)
User Story: As a veterinarian, I want flexible medical records for each visit so that routine check-ups, vaccinations, illnesses and treatments can be documented without unnecessary fields.
Acceptance Criteria:
Record Creation
Only the veterinarian may create and update medical records, each linked to a pet profile.
Visit date and visit type are required; available types include Routine Check-up, Vaccination, Illness, Follow-up and Other.
Optional expandable fields include Reason for Visit, Clinical Findings, Diagnosis, Treatment, Prescribed Medication and Additional Clinical Notes.
Routine appointments and vaccinations do not require a diagnosis or treatment field to be filled in.
An empty optional field is missing information, not a normal clinical finding.
Record Management
The veterinarian can review/update previous records, while owners can view but cannot change or delete them.
Vaccination-specific details are handled in US-09.
Payment, invoicing and financial record keeping are deferred.
US-08 — View Veterinary Medical Records
Priority: Must Have (Rank 8)
Provisional Story Points: 5
Dependencies: US-03, US-04, US-07
Suggested Implementation Lead: Frontend Developer 2 (subject to team review)
User Story: As a pet owner, I want to review my pet's examination history so that I can revisit previous medical details when needed.
Acceptance Criteria:
Medical History Access
Owners can access medical records only for associated pets, listed most recent first.
Each history item includes visit date and visit type, and can be opened to inspect details.
Owners can view every veterinarian-completed field, including examination reasons, findings, diagnoses, treatment, medication and additional clinical notes.
Unfilled optional fields may be omitted; all clinical record fields are read-only to owners.
Vaccination-specific information is available through the vaccination section.
US-09 — Manage Vaccination Information
Priority: Must Have (Rank 9)
Provisional Story Points: 8
Dependencies: US-03, US-04, US-07
Suggested Implementation Lead: Backend Developer 2 (subject to team review)
User Story: As a veterinarian, I want to record vaccinations using predefined options and manual entries so that histories remain organized for common and uncommon pet species.
Acceptance Criteria:
Species and Vaccine Catalog
The system provides common species choices; uncommon animals can be entered under Other Species.
Species-relevant common vaccine names are predefined by the development team and validated with veterinary expertise where applicable.
The veterinarian can choose a listed vaccine name or enter an unlisted name manually.
Species selection does not automatically assign vaccinations or compute a schedule.
Vaccination Records
Only the veterinarian creates/updates vaccination entries, each linked to a pet.
Vaccine Name and Administration Date are required; Next Due Date and Additional Notes are optional.
The veterinarian enters dates and makes medical decisions; the system does not recommend or calculate future vaccine dates.
Multiple vaccination records are retained in each pet's history, viewable but not editable by the owner.
An in-app reminder exists only when Next Due Date is provided; changing that date updates the reminder.
US-10 — View Vaccination Reminders
Priority: Must Have (Rank 10)
Provisional Story Points: 13
Dependencies: US-03, US-04, US-09
Suggested Implementation Lead: Full-Stack Developer (subject to team review)
User Story: As a pet owner or veterinarian, I want website reminders for upcoming and overdue vaccinations so that scheduled vaccination dates are easier to follow.
Acceptance Criteria:
Reminder Behavior
A reminder becomes visible seven days before a veterinarian-entered Next Due Date.
A reminder remains visible on the due date and while overdue; records with no Next Due Date generate none.
Reminder status is Upcoming, Due Today or Overdue; the associated pet, vaccine and due date are visible.
Updating the due date updates its reminder; reminders stop for deceased pets.
Visibility and Completion
Owners see their associated pets' reminders; the veterinarian sees a consolidated upcoming/due/overdue list with owner and pet details.
When the veterinarian logs a new administered vaccination and explicitly links it to a previous vaccination, the previous reminder automatically becomes Completed without deleting history.
If the new vaccination has a Next Due Date, a new reminder is produced; otherwise no new reminder is generated.
Reminders use the website only, without email, SMS or external push notifications.
EP-05 — Appointment Calendar and Manual Management
US-11 — Manage Appointment Availability
Priority: Must Have (Rank 11)
Provisional Story Points: 8
Dependencies: US-03 — Role-Based Access Control
Suggested Implementation Lead: Full-Stack Developer (subject to team review)
User Story: As a veterinarian, I want to manage my clinic's availability through a simple visual calendar so that pet owners can see available and busy time periods before contacting the clinic.
Acceptance Criteria:
Visual Calendar Interface
The veterinarian can open a simple calendar widget in the administrative dashboard, with day and week views and visible time intervals.
The veterinarian can select a date and a start/end time directly on the calendar to create or edit a time block.
Available and Busy/Unavailable time blocks are visually distinguishable in both calendar views.
The calendar is inspired by basic Google Calendar time-block interaction; advanced drag-and-drop editing, calendar sharing and third-party synchronization are not required.
Availability Management
Only the veterinarian can create, edit and remove available or busy time blocks.
Every block has a valid start and end time; the end must be later than the start.
Blocks can be used for extra working periods, breaks, leave or other clinic commitments. Appointments recorded manually under US-12 appear as occupied periods. Conflicting explicit blocks are rejected.
The veterinarian can revise the calendar when clinic availability changes.
The veterinarian can define recurring weekly working days and hours. Explicit AVAILABLE and BUSY blocks adjust that baseline; confirmed appointments remain occupied and periods outside published hours appear CLOSED unless explicitly opened.
Pet Owner Calendar and Contact
Pet owners can view a simplified read-only calendar showing published available and busy time periods.
Other owners' names, pet information and private reasons for busy periods are not shown.
The clinic's telephone/contact information is displayed near the calendar and on the homepage.
Owners contact the clinic by phone or in person; online appointment requests, booking and veterinarian-approval queues are excluded.
US-12 — Manage Scheduled Appointments
Priority: Must Have (Rank 12)
Provisional Story Points: 8
Dependencies: US-03, US-04, US-11
Suggested Implementation Lead: Full-Stack Developer (subject to team review)
User Story: As a veterinarian, I want to record and manage appointments arranged by telephone or in person using my visual calendar so that the clinic's occupied periods remain accurate.
Acceptance Criteria:
Manual Appointment Entry
Only the veterinarian can create appointments through the administrative calendar; pet owners cannot book appointments on the website.
The veterinarian can select a date and time interval in the calendar widget to create a new appointment.
The veterinarian can select an existing pet owner and one or more associated pets, with optional visit reasons or notes.
Each appointment has a start and end time; the end must be later than the start, and the minimum duration is 10 minutes.
Recorded appointments appear as occupied time blocks on the clinic calendar and update the owner-visible busy view.
Editing and Cancellation
The veterinarian can select an appointment from the calendar to view its details and to edit, reschedule or cancel it.
Rescheduling rechecks availability and updates the associated time block. Cancellation frees the occupied period; cancelled entries remain in clinic history.
The system rejects appointments that overlap active appointments, unavailable periods, or time outside the published clinic availability; the veterinarian must select a valid interval.
Calendar Privacy and Scope
The veterinarian sees owner, pet and visit details; pet owners see availability status only, without other clients' personal information.
Pet owners contact the clinic directly to arrange or change appointments; no online booking, approval queue or automatic booking notifications are required.
Automated visit-duration estimates, plus/minus duration tolerances and external Google Calendar integration are out of scope.
EP-06 — Interface Personalization
US-13 — Switch Interface Theme
Priority: Could Have (Rank 13)
Provisional Story Points: 3
Dependencies: Authentication and basic frontend
Suggested Implementation Lead: Frontend Developer 1 (subject to team review)
User Story: As a system user, I want to switch manually between Light Mode and Dark Mode so that I can use the website with my preferred appearance.
Acceptance Criteria:
Theme Selection and Persistence
Both pet owners and the veterinarian can change between Light and Dark Mode using a visible toggle without logging out.
The setting affects navigation, forms, calendars, dashboards and other main user interface elements.
The selected theme is saved for the user and restored on a later login.
Light Mode is the default when no saved preference exists; automatic device-theme detection is not required.
Both themes remain readable and do not change saved pet, owner or clinical information.
3.2A Feature and Task Backlog — Initial Planning Entries
The initial Product Backlog includes Epic → Feature → User Story → Task traceability. The six feature entries below group existing user stories without adding new requirements. Feature Story Points are roll-ups of the corresponding provisional User Story estimates and must not be added to the 105 SP total again. The task-hour estimates and suggested role owners are preliminary planning entries, not completed work, team commitments, or verified assignments. Each task needs a named assignee and a matching GitHub Issue before execution.
Feature Records
Feature
Parent Epic
Related Stories
Feature / Outcome
Priority
SP Roll-up
Dependency
Proposed Owner
FT-01
EP-01
US-01–US-03
Account access and role-based permissions
Must
18
Authentication / database baseline
ARCH / INT
FT-02
EP-02
US-04
Pet profiles and controlled personal information
Must
13
FT-01
INT / QA
FT-03
EP-03
US-05–US-06
Owner Health Diary and history visualization
Must / Should
21
FT-01, FT-02
INT / QA
FT-04
EP-04
US-07–US-10
Clinical records, vaccinations and reminders
Must
34
FT-01, FT-02
INT / QA
FT-05
EP-05
US-11–US-12
Clinic availability and manual appointments
Must
16
FT-01, FT-02
ARCH / INT
FT-06
EP-06
US-13
User-selectable interface theme
Could
3
Base frontend shell
INT / QA
Feature Acceptance Checkpoints
FT-01 — Account access and role-based permissions: Vet provisions owner accounts; temporary password change and owner/pet authorization work. Full acceptance criteria are in US-01–US-03.
FT-02 — Pet profiles and controlled personal information: Vet controls clinical identity; owners manage their own private photos/notes; archiving retains history. Full acceptance criteria are in US-04.
FT-03 — Owner Health Diary and history visualization: Owner-only diary edits and time-stamped history; vet can view, not edit; charts do not diagnose. Full acceptance criteria are in US-05–US-06.
FT-04 — Clinical records, vaccinations and reminders: Vet controls medical/vaccine records; owners read; due-date reminders follow defined rules. Full acceptance criteria are in US-07–US-10.
FT-05 — Clinic availability and manual appointments: Vet manages visual schedule and phone/in-person appointments; owner sees private-data-free availability. Full acceptance criteria are in US-11–US-12.
FT-06 — User-selectable interface theme: Light/Dark selection persists per user and remains readable. Full acceptance criteria are in US-13.
Task Records — Proposed Implementation Breakdown
The following tasks are not marked as completed. Task estimates are provisional hours, while User Story/Feature estimates use Story Points; the units are different and should not be summed together. ARCH, INT and QA refer to the formal team roles in Section 4, not confirmed individual assignees. Before implementation, the team should assign one named owner to each task, create/link its GitHub Issue, and refine its time estimate.
Task
Feature
Task / Completion Checkpoint
Priority
Estimate (h)
Dependencies
Proposed Role
T-01
FT-01
Define owner-account schema and secure temporary-credential flow. Done when: Schema and first-login sequence reviewed against US-01–02.
Must
6
DB/auth setup
ARCH
T-02
FT-01
Implement login, password replacement and role guards. Done when: First login forces change; unauthorized cross-owner actions are blocked.
Must
10
T-01
INT
T-03
FT-01
Prepare role and authentication acceptance tests. Done when: Positive/negative access cases trace to US-01–03.
Must
4
T-02
QA
T-04
FT-02
Design pet-to-owner and clinical/personal field separation. Done when: Schema identifies vet-only and owner-private fields.
Must
4
T-01
ARCH
T-05
FT-02
Implement profile management, photos/notes and archiving. Done when: Vet profile controls and owner private information follow US-04.
Must
12
T-04
INT
T-06
FT-02
Test multi-pet isolation and archived record retention. Done when: Access and archive tests pass for sample owners and pets.
Must
5
T-05
QA
T-07
FT-03
Define symptom categories and timestamped observation schema. Done when: Optional multiple daily observations are supported.
Must
4
T-04
ARCH
T-08
FT-03
Implement owner diary entry CRUD and authorized vet viewing. Done when: Owner CRUD works; veterinarian access remains read-only.
Must
12
T-07, T-02
INT
T-09
FT-03
Build diary calendar and symptom summaries. Done when: History/charts preserve timestamps and never classify missing days as healthy.
Should
12
T-08
INT
T-10
FT-04
Design flexible visit and vaccination record schemas. Done when: Required/optional fields and species-specific vaccine choices are defined.
Must
6
T-04
ARCH
T-11
FT-04
Implement vet clinical records and owner read-only views. Done when: Medical/vaccine create/edit permissions match US-07–09.
Must
12
T-10, T-02
INT
T-12
FT-04
Implement reminders based on veterinarian-entered due dates. Done when: Seven-day upcoming, due, overdue and completion linking work.
Must
10
T-11
INT
T-13
FT-04
Test reminder transitions and clinical authorization. Done when: Date/status cases and owner read-only restrictions pass.
Must
6
T-12
QA
T-14
FT-05
Specify weekly hours, overrides and appointment conflicts. Done when: AVAILABLE/BUSY/CLOSED precedence and overlap rules documented.
Must
6
T-02
ARCH
T-15
FT-05
Build vet day/week calendar and manual appointment CRUD. Done when: Vet can create/edit/cancel; 10-minute minimum and overlap checks apply.
Must
14
T-14, T-05
INT
T-16
FT-05
Provide owner-safe availability and clinic contact display. Done when: Owner sees only time status and contact, never client details.
Must
8
T-15
INT
T-17
FT-05
Test conflict, cancellation, interval and privacy cases. Done when: Invalid overlaps rejected; cancellation releases busy time.
Must
6
T-15, T-16
QA
T-18
FT-06
Define readable Light/Dark interface tokens. Done when: Principal pages share accessible colors and contrast.
Could
4
Frontend shell
INT
T-19
FT-06
Implement theme toggle and per-user persistence. Done when: Saved choice is restored at the next login.
Could
5
T-18, T-02
INT
T-20
FT-06
Verify theme consistency and preference retention. Done when: Forms, calendars and dashboard remain readable in both themes.
Could
3
T-19
QA
Traceability and ownership rule: Each Feature references its parent Epic and existing User Stories; each Task references one Feature. Completion requires evidence in GitHub Issues, commits and reviewed Pull Requests. The team must replace role-level proposed owners with actual assignees and revise estimates before treating these as a committed sprint plan.
3.3 Prioritized Backlog Summary
User Story
Priority
SP
Dependencies
Suggested Lead
US-01 — Create Pet Owner Accounts
Must
5
Auth/database setup
Backend 1
US-02 — First Login and Password Change
Must
5
US-01
Backend 1
US-03 — Role-Based Access Control
Must
8
US-01, US-02
Backend 1
US-04 — Manage Pet Profiles
Must
13
US-01, US-03
Backend 2
US-05 — Record Health Observations
Must
8
US-03, US-04
Frontend 1
US-06 — Review Health Diary History
Should
13
US-03, US-04, US-05
Frontend 1
US-07 — Manage Veterinary Medical Records
Must
8
US-03, US-04
Backend 2
US-08 — View Veterinary Medical Records
Must
5
US-03, US-04, US-07
Frontend 2
US-09 — Manage Vaccination Information
Must
8
US-03, US-04, US-07
Backend 2
US-10 — View Vaccination Reminders
Must
13
US-03, US-04, US-09
Full-Stack
US-11 — Manage Appointment Availability
Must
8
US-03
Full-Stack
US-12 — Manage Scheduled Appointments
Must
8
US-03, US-04, US-11
Full-Stack
US-13 — Switch Interface Theme
Could
3
Auth / frontend
Frontend 1
Total provisional estimate

105


The estimates are for backlog planning only; final Story Points and named implementation owners require team agreement.
3.4 Proposed MVP Breakdown
Delivery Group
Proposed Scope
Notes
Core working MVP
US-01, US-02, US-03, basic US-04, US-05, US-07, US-08
End-to-end authentication, pet and clinical-history journey
If capacity permits
Basic US-06 history and US-09 vaccination display
Prioritize simple history before charts
Later incremental scope
Advanced US-04/06/09, US-10, US-11, US-12
Subject to feature splitting and team capacity
Could Have
US-13
Theme preference
3.5 Refinement Priorities
Confirm provisional estimates and split large stories US-04, US-06 and US-10 into sprint-sized implementation tasks.
Validate initial common-species vaccine names with veterinary input before release.
Confirm the v0.6.3 calendar contract with the technical team: recurring hours, AVAILABLE/BUSY overrides, CLOSED periods and veterinarian-only manual appointments.
Verify the restored owner-created Health Diary endpoints (US-05/US-06), privacy permissions and charts against acceptance criteria.
Verify that the final OpenAPI keeps all x-user-stories references consistent with US-01–US-13 and passes a complete specification validator.
Record agreed names, scope, implementation owners and Sprint 1 evidence.
3.6 Definition of Ready (Proposed)
User, capability and intended value are explicit.
Acceptance Criteria can be tested and role-based access is defined.
Dependencies and API impact are understood.
Product and technical decisions affecting implementation are agreed or assigned for resolution.
Story Points and assigned responsible team member are confirmed before sprint commitment.
The work item is small enough to implement and test within one sprint.
3.7 Definition of Done (Proposed)
Agreed acceptance criteria pass.
Authorization, input validation and major error cases are verified.
Relevant frontend and backend functions match the current approved API contract.
The implementation is linked to a GitHub issue, reviewed by a teammate and merged under the defined process.
Tests, demonstration evidence and documentation are updated where appropriate.
3.8 Additional Interface Requirement — Clinic Contact
The homepage and appointment calendar display a clinic telephone/contact number maintained by the veterinarian. It supports direct communication for appointments and urgent concerns, but the website does not provide an automated emergency service or calling integration.
4. Team 10 Structure and RACI
The following formal course roles are taken from the BM 321 semester project specification. The seven team members must be named, and actual frontend/backend/full-stack responsibilities can be recorded separately from official academic roles. No named assignment is claimed here before confirmation.
4.1 Team Structure — Course Roles
Official Role Title
Main Required Outputs
Assigned Member
Product Owner and Requirements Engineer
Product vision, stakeholder consultation, user stories, acceptance criteria, prioritization and backlog
Deniz Alagöz - 240201031
Scrum Master and Agile Process Coach
Stand-ups, retrospective records, impediments, Agile workflow and RACI coordination
İclal Yılmazoğlu - 240201051
Lead Software Architect and Technical Lead
System architecture, C4/UML, API strategy, technical reviews
Mehmet Efe Şen - 240201056
Quality Assurance and Test Automation Engineer
Test plan, unit/integration/E2E tests, defect tracking and coverage
Sıla Gevrek - 230204041
DevOps and CI/CD Infrastructure Engineer
GitHub rules, CI/CD, Docker and deployment
Hüseyin Arda Gençağa - 220205028
Implementation and Integration Engineer
Feature implementation, frontend/backend integration, defect resolution and documentation support
Sudenaz Balcı - 240201010
Implementation and Integration Engineer
Feature implementation, frontend/backend integration, defect resolution and documentation support
Aybüke Nur Altınışık - 240201049
Seven-person team coverage: Assign all seven names across the course roles and explicitly document any shared role, primary/secondary responsibility, or other arrangement approved by the team and instructor. The specification names these role titles; it does not justify inventing an additional distinct academic title without confirmation.
4.2 RACI Definitions
R — Responsible: Performs the task and produces the artifact.
A — Accountable: Ultimately owns the outcome or acceptance decision.
C — Consulted: Provides input before decisions are made.
I — Informed: Receives updates and decisions.
4.3 Preliminary RACI Matrix
Abbreviations: PO = Product Owner and Requirements Engineer; SM = Scrum Master and Agile Process Coach; ARCH = Lead Software Architect and Technical Lead; QA = Quality Assurance and Test Automation Engineer; DEVOPS = DevOps and CI/CD Infrastructure Engineer; INT = Implementation and Integration Engineer.
Activity / Deliverable
PO
SM
ARCH
QA
DEVOPS
INT
Product vision, scope and priorities
A/R
C
C
C
I
C
Stakeholder interview and validation
A/R
I
C
C
I
I
User stories, acceptance criteria and backlog
A/R
C
C
C
I
C
System architecture and design decisions
C
I
A/R
C
C
R
OpenAPI draft and backend contract review
C
I
A/R
C
C
R
Functional, integration and security test plan
C
I
C
A/R
C
R
GitHub workflow and branch protection
I
C
C
C
A/R
R
Feature implementation and integration
C
C
C
R
C
A/R
Sprint ceremonies and progress tracking
C
A/R
C
C
I
R
Sprint submission evidence and coordination
R
A
R
R
R
R
This matrix is a proposed starting point. The team must finalize it with named owners and clear accountability for each artifact.
4.4 Team Review Notes — Completed Discussion
The team reviewed the initial feature scope and complexity during a group discussion. The following decisions reflect that discussion and have been applied in this version:
Review Topic
Team Decision
Documentation Effect
Online appointment booking
Remove owner-submitted appointment requests and veterinary approval queue
Remove former online-booking story and obsolete status flow
Appointment management
Continue using telephone/in-person communication; vet enters appointments manually
Renumber and simplify US-11 / US-12
Calendar
Use a simple day/week time-block widget with vet-managed recurring working hours and manual AVAILABLE/BUSY blocks
US-11/US-12 and v0.6.3 API align conceptually; no external calendar integration
Health Diary
Retain owner-entered observations, diary history and symptom statistics
US-05/US-06 restored in v0.6.3 API after omission from v0.6.2
Backlog IDs
Renumber all remaining stories sequentially
13 stories: US-01 through US-13
Estimation
Keep unaffected SP estimates; reduce US-11 to provisional 8 SP
New working total: 105 SP
Team accountability
Use formal course role titles; record frontend/backend responsibilities separately
Rewrite Team Structure and RACI
Document quality
Restore detailed stakeholder, metrics, scope, risk and validation tables
More readable, traceable academic draft
Still requiring final confirmation: Formal assignment of all seven members, acceptance of the new OpenAPI v0.6.3 contract and its calendar precedence rules, implementation details for the diary/chart interface, final Story Points and repository evidence. These are review tasks, not unresolved online-booking scope decisions.
5. GitHub Organization and Git-Flow Evidence — To Complete
Repository: HUARGE / Pawthy (URL to be inserted and verified). All seven members were invited to the repository. The team reported main, develop and a feature branch. PR #2, chore: set up repository structure and templates, was reviewed and merged into main during initial setup; future integration should follow the agreed Git-Flow path.
Evidence Item
Current Status
Evidence / Action Needed
Repository organization and access
Repository created; invitations reported complete
Insert exact URL and membership evidence
main and develop branches
Previously visible in repository
Insert branch URLs or screenshots
Active protection rulesets
Screenshot shows active rules for both main and develop, each listing 3 rules
Verify precise PR/approval/status-check requirements
Pull request / peer review example
PR #2 reviewed and merged
Insert link and reviewer evidence
Git-Flow merge convention
Setup PR merged feature branch into main
Confirm feature/* → develop → main policy and develop synchronization
Issue traceability
Setup issue #1 linked to PR #2
Insert issue/PR links
Apache License 2.0
Selected in proposal
Verify repo LICENSE content
Release tag and submission commit
Not yet verified
Insert release/tag URL and exact final commit SHA
Instructor access
Repository visibility and instructor access not verified
Ensure repo is public or explicitly grant the instructor read access if private
Do not describe any rule or check as enforced until it is verified in the corresponding GitHub Ruleset.
Submission workflow: Changes in the Google Docs working copy are not automatically published to GitHub. Export the approved report as Markdown, place it with its diagram under docs/, commit the approved YAML under api/, and merge using the agreed PR workflow. Record links to the final reviewed files and, preferably, a tag or commit permalink so the instructor can identify the exact submitted revision.
6. OpenAPI Contract — Working Version 0.6.3
The working technical contract is Pawthy OpenAPI v0.6.3, developed from v0.6.2 and updated to reinstate the Health Diary functionality that had been omitted from the previous revision. The supplied YAML uses OpenAPI 3.1.0 and documents 36 API paths, 54 HTTP operations, and 63 schemas. The file is a draft interface specification, not evidence that the endpoints have been implemented or reviewed on GitHub. Preliminary YAML parsing and internal $ref resolution succeeded; formal OpenAPI validation and technical sign-off remain pending.
6.1 Contract and Repository Traceability
Contract Item
Documented State / Remaining Action
API version
0.6.3 (working draft, awaiting technical-team revision/sign-off)
Source draft
Pawthy_OpenAPI_v0.6.3_Health_Diary_Restored.yaml
Planned repository path
api/openapi.yaml — confirm actual committed filename and branch
Health Diary
Owner enters/edits/deletes their observations; both roles review authorized history and symptom statistics (US-05/US-06)
Appointment workflow
Phone/in-person arrangement, veterinarian-only manual creation and management; no online request/approval (US-11/US-12)
Availability calendar
Recurring working hours, explicit AVAILABLE/BUSY blocks, exceptional blocked times and privacy-safe day/week views
Security
Bearer authentication; role restrictions and owner-level access controls documented
Validation / review
Re-run OpenAPI linter and example/error checks; add reviewer, pull request and commit links
Repository URL
https://github.com/HUARGE/Pawthy
API file URL
https://github.com/HUARGE/Pawthy/blob/develop/api/openapi.yaml
6.2 Requirements-to-API Traceability
User Story Group
Representative Operations
Intent / Access
US-01–US-03 — Accounts and roles
POST /pet-owners, POST /auth/login, POST /auth/change-password
Vet provisions accounts; secure first login and authorized access
US-04 — Pet profiles
POST /pet-owners/{ownerId}/pets, GET /pets, personal photo/details routes
Vet-managed identity; owners' private information protected
US-05–US-06 — Health Diary
GET /symptoms, /pets/{petId}/observations, /pets/{petId}/observation-statistics
Owners create/change own observations; vet read-only; diary graphs
US-07–US-08 — Medical history
/pets/{petId}/medical-records
Vet maintains records; owner views own pets read-only
US-09–US-10 — Vaccines
/pets/{petId}/vaccinations, GET /vaccination-reminders
Vet-controlled vaccine records and in-app due-date reminders
US-11 — Clinic availability
/veterinarian/schedule, /availability/blocks, /availability/calendar
Vet sets schedule and blocks; public read-only availability
US-12 — Manual appointments
POST /appointments, PATCH /appointments/{appointmentId}, GET /veterinarian/calendar
Veterinarian creates/updates/cancels booked visits; owner cannot book online
US-13 — Interface theme
GET /me/theme, PATCH /me/theme
Save and restore Light/Dark Mode selection
API/review boundary. Keep the full YAML in its own repository file and link it from this report; it should not be pasted into this document. The representative routes above are only a readable traceability summary. Before final submission, replace placeholders with actual GitHub URLs and reconfirm the file after the technical team's changes. The names of endpoints and the API version may change during review.
