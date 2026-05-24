# Sangheon Lee — Fieldguide Portfolio

## Core Positioning
I am a systems-oriented software engineer whose strongest work sits at the intersection of AI-native execution, workflow-heavy product systems, and operational reliability.

What I build matters, but how I structure execution matters just as much. My strongest differentiator is designing systems that let complex work move faster without losing rigor: role-based AI orchestration, durable workflow state, human-gated quality control, and software that supports real operators in real environments.

Currently, I am the effective sole software engineer behind TTAE/Pixie’s software stack.

---

## What I Emphasize
- AI-native engineering execution
- Multi-agent orchestration and reviewable workflows
- End-to-end ownership across product and operations
- Systems that support real operational complexity
- Reliability, state discipline, and human-gated quality control

---

## Selected Work

## 1. Claude Code Harness OS
### What it is
Claude Code Harness OS is a reusable AI-native engineering operating system I designed to make agent-assisted development more structured, scalable, and reliable.

Instead of treating AI coding sessions as isolated chats, I externalized workflow state, role definitions, review structure, and handoff artifacts into durable files and repeatable protocols. The goal was not just faster coding, but a disciplined execution model for parallel, reviewable development across multiple projects.

### Problem / context
Standard AI coding workflows are powerful but fragile. Context disappears across sessions, review quality varies, important decisions stay trapped in chat, and one human can still become the bottleneck when multiple repositories and workstreams are moving at once.

I wanted a way to increase execution leverage without losing rigor.

### What I owned
I designed the system itself:
- implementer / reviewer / orchestrator role model
- workflow definitions and state transitions
- review and handoff protocols
- artifact-driven continuity patterns
- multi-repo operating rules
- bootstrap structure for new sessions
- human-gated merge and quality-control boundaries

I also applied the harness directly in active TTAE development rather than keeping it as a theoretical framework.

### How I approached it
The key design decision was to separate planning, implementation, review, and merge-handoff responsibilities instead of letting one agent do everything in an unstructured loop.

At the top level, the orchestrator handles planning, task issuance, and final PR harvesting. By acting primarily as a harvester of completed work rather than as the main implementer, the orchestrator can efficiently manage multiple PRs finishing in parallel.

Layer 1 is centered on implementer and reviewer agents. The implementer completes the task and opens a PR, and the reviewer has authority to evaluate that PR as either `merge ready` or `needs changes`. Until a PR reaches `merge ready`, the implementer and reviewer continue working around the PR through active iteration and discussion. Once it reaches `merge ready`, the orchestrator reviews the outcome and decides whether to merge it or send it back for another Layer 1 cycle.

Layer 2 adds optional sub-agents under both the implementer and reviewer. These sub-agents can be spawned for delegated development, domain-specific review, or cross-validation tasks, then report their findings back upward. That structure creates a two-stage validation path — Layer 2 verification followed by Layer 1 verification — which improves quality beyond a single-pass agent workflow.

I also designed the harness to manage a core project and multiple derived projects within one operating model. In the TTAE environment, the core repository acts as the authority for shared architecture and sensitive boundaries such as database-related work, while derived repositories such as `ttae-persona`, `ttae-partner-app`, `ttae-admin-app`, `ttae-customer-app`, and `ttae-cloud` reference the core project for context and structure. This allows one harness to coordinate a multi-project system without flattening ownership boundaries.

### Technical challenges and solutions
#### 1. Continuously increasing agent productivity
One of the oldest and most persistent challenges in this system has been improving agent productivity without letting quality collapse. Whenever agent work became slower, more wasteful, or harder to coordinate, I treated that as a system problem to investigate rather than as random friction.

To improve it, I built and repeatedly used a `/pain-log` skill to record concrete sources of slowdown, confusion, and coordination cost, then refined the operating model based on those findings. I have also spent ongoing effort on context intelligence: thinking carefully about how context should be split, managed, and referenced so agents do not repeatedly waste effort reloading or misreading the same state. That work has also led into an early second-brain system for durable structured reference across ongoing work. It is still evolving, but it reflects a long-running pattern in how I approach agent productivity: observe bottlenecks, externalize them, and redesign the workflow.

#### 2. Preventing AI workflow drift
A major challenge was that agent work could easily drift when state lived mainly in chat history and responsibilities were not clearly separated.

To address that, I moved workflow state into durable artifacts, made role boundaries explicit, and required PR-centered handoffs rather than informal conversational transitions.

#### 3. Preserving quality under parallelism
Higher agent throughput is not useful if review quality collapses.

I addressed that by giving the reviewer clear authority over `merge ready` versus `needs changes`, keeping the orchestrator as a final human-governed harvesting layer, and adding optional Layer 2 sub-agent cross-validation so important work could be checked twice before final acceptance.

#### 4. Managing multiple related repositories safely
A single AI workflow can become chaotic when multiple connected projects evolve at once, especially when some repositories contain more sensitive architecture or database-related responsibilities than others.

I handled that by defining a core-versus-derived project model: the core repository retained authority over sensitive shared boundaries, while derived repositories referenced that core context without inheriting the same access assumptions. This preserved reuse and coordination without collapsing governance.

### Why it matters
This is the clearest example of orchestration as an engineering capability in my work. It shows that I can design the execution system that makes AI useful in practice: structured, reviewable, durable, accountable, and capable of scaling across multiple parallel projects.

---

## 2. TTAE Platform
### What it is
TTAE Platform is the connected software system behind TTAE’s public-facing web experience and internal operational workflow.

Importantly, this was not a monorepo presented as a larger system than it really was. It was a set of separate, independently evolving projects with distinct responsibilities and delivery surfaces. The core `ttae` repository acted as the main operational hub, while related projects such as `ttae-persona`, `ttae-partner-app`, and `ttae-admin-app` evolved as their own repositories with their own runtime and product concerns.

Together, these systems supported public brand presentation, reservation and confirmation flows, persona-related customer journeys, partner coordination, internal admin operations, notifications, and the real-world workflow required to deliver a high-touch service business.

### Problem / context
This was not a narrow product build. The business required software that could connect customer-facing experience, operational execution, internal administration, and partner coordination without drifting into disconnected tools or brittle manual work.

Because the system was split across multiple real projects rather than one simplified codebase, the challenge was not only to ship features, but to preserve coherent ownership boundaries, shared contracts, and operational correctness across independently moving surfaces.

As the effective sole software engineer, I had to make that whole system operable.

### What I owned
As the effective sole software engineer, I owned the system end-to-end across separate but connected projects:
- public web surfaces
- reservation and confirmation workflows
- persona-related customer flows
- partner-facing mobile systems
- internal admin and operator tooling across web and mobile
- shared schema, auth, storage, and RLS boundaries
- workflow automation, notifications, and state validation
- cross-repo architectural boundaries and rollout discipline

### How I approached it
I treated TTAE as a multi-project operating system rather than as a single application.

The core `ttae` repository acted as the source of truth for shared schema, auth, RLS, migrations, and domain contracts. Around that hub, I maintained separate repositories for specialized surfaces and workflows, making sure they could evolve independently without breaking the shared backend model underneath them.

That distinction mattered. The hard part was not only building each surface, but deciding what had to stay centralized, what could be delegated outward, and how downstream work could move quickly without violating shared contracts. My job was to keep the system coherent while still allowing real product and operational work to ship across multiple repositories.

### Technical challenges and solutions
#### 1. Preserving consistency across multiple real repositories
Because TTAE was not a monorepo, contract drift was a real risk. Different surfaces could easily diverge in assumptions around schema, permissions, workflows, or operational state.

I addressed that by using the core `ttae` repository as the authority for schema, migration, and RLS governance, then rolling out downstream changes in a hub-first way so partner and admin surfaces could ship against stable shared boundaries.

#### 2. Making reservation logic match real operational reality
Reservation availability was not a simple UI-state problem. It depended on calendar occupancy, booking-sequence rules, manual operational controls, and final confirmation-time correctness.

To handle that, I built a reservation-availability subsystem that combined shared calendar logic, public-safe `SECURITY DEFINER` SQL functions, truth-table-based server decisions, multilingual UI states, and admin block controls. I then extended it into sequence-level operational control so mixed first-slot and second-slot closure states could be governed accurately in real operations.

#### 3. Preserving correctness under production edge cases
Operational systems fail in ways that cleaner demo systems do not. In TTAE, that included stale SSR HTML behavior, async client-state defects, and reservation-submit failures tied to framework behavior rather than business logic.

I handled those cases by tracing them to root cause rather than applying superficial fixes: for example, diagnosing stale-client behavior at the SSR HTML cache layer, and fixing a production P0 reservation-submit issue rooted in React Router v7 fetcher revalidation timing while adding regression coverage around the async state cycle.

#### 4. Turning fragmented internal tools into one operator system
Internal operations were initially spread across multiple surfaces and could easily become inconsistent or hard to manage.

I addressed that by consolidating scattered admin routes into a governed `/admin/*` shell, building a dedicated admin mobile app, creating a durable `admin_notifications` inbox, and evolving the admin mobile experience into a calendar-first operator tool. That turned isolated internal tools into a more coherent operational system across web and mobile.

#### 5. Shipping cross-repo workflow features without breaking shared boundaries
Some features were only valuable if they worked across the real system rather than inside one repo. A good example was lesson-photo upload and completion-state automation.

I handled that by rolling out the shared schema, storage, RLS, and DB-trigger layer first in the hub, then implementing partner-app upload, gallery, and filter flows against that contract. I later extended the same workflow with delete permissions and a reverse status trigger so the operational state could move both forward and backward safely based on evidence.

### Outcomes
- Built and operated a coherent multi-project system across public web, persona flows, partner workflows, and internal admin operations
- Preserved shared-schema and permission correctness across independently evolving repositories
- Improved reservation correctness and operational visibility through stronger server-side decision logic and admin control
- Strengthened production reliability by diagnosing real root-cause issues across caching, async state, and cross-surface contract behavior
- Expanded internal operator capability through admin consolidation, notification workflows, and calendar-first tooling

### Why it matters
This project shows that I can do more than build isolated product features.

It shows that I can structure and operate a real multi-project system with clear ownership boundaries, shared backend governance, workflow depth, and production reliability requirements. More importantly, it shows that I can do that in a business where software is inseparable from real service delivery and day-to-day operations.

---

## 3. FleetUp Dashcam Vehicle IoT Platform
### What it is
FleetUp was a dashcam-based vehicle IoT management platform where I focused on the connected system behind real-time device telemetry, event-video capture, live monitoring, remote device control, and fleet operations.

The platform supported more than 2,000 deployed dashcams, with GPS data arriving once per second per device. It combined real-time location tracking, event-triggered video capture, live streaming, FOTA firmware upgrades, remote configuration commands, and a web admin panel for operators.

### Problem / context
This was not a simple CRUD product. The system had to continuously ingest telemetry from thousands of devices, keep real-time maps responsive, preserve event visibility during incidents, support remote device management, and remain operationally reliable under sustained production traffic.

Because the work touched streaming, infrastructure, device operations, admin tooling, and data architecture at once, the project demanded systems thinking rather than isolated feature work. This was also the project I was most deeply responsible for during my FleetUp period.

### What I owned
I was responsible for major parts of the dashcam platform, including:
- real-time dashcam data streaming pipeline design and implementation
- event-video upload, storage, and management flows
- live streaming functionality
- web admin panel development and server-side API integration
- FOTA firmware-upgrade flows and remote device-management functions
- AWS-based service architecture using Kinesis, Lambda, SNS, SQS, EventBridge, and related services
- database and caching design across DynamoDB, Oracle DB, and Redis

### How I approached it
I designed the telemetry flow as an event-driven streaming system rather than pushing raw device traffic directly into fragile downstream paths.

A representative pipeline was:
- dashcams send GPS data every second
- API Gateway receives device traffic
- Kinesis absorbs and distributes the stream
- Lambda consumers validate and process events
- DynamoDB stores time-series location data
- Redis caches current state for low-latency map updates
- Oracle DB holds structured operational data such as accounts, permissions, billing, and configuration history

I also worked on the surrounding operational system:
- event videos generated by collision or shock events were uploaded through the device gateway into S3 and surfaced in the admin panel
- live camera streams were made viewable from the FleetUp web platform for real-time monitoring
- FOTA and remote configuration flows were exposed through the admin system so operators could update firmware, change device parameters, and manage rollout status without field visits
- SNS, SQS, and EventBridge were used for alerting, queue-based background work, scheduled checks, and periodic operational automation

### Technical challenges and solutions
#### 1. High-volume telemetry scalability
The platform had to handle peak conditions of up to roughly 2,000 GPS events per second across 2,000+ devices.

To manage that load, I used a Kinesis-based streaming architecture, adjusted shard counts for traffic spikes, and relied on retry / dead-letter-queue patterns to reduce loss under failure conditions.

#### 2. Event video and live-stream reliability
Collision events were only useful if the system preserved the corresponding video reliably and made it visible quickly.

I improved confidence in the video path by validating the gateway-to-S3 upload flow, surfacing event metadata in Oracle DB, and connecting alerts and admin visibility so operators could inspect incidents directly.

For live monitoring, I also had to consider streaming latency, concurrent access, and bandwidth management rather than treating video delivery as a static media problem.

#### 3. Safe remote operations at scale
Firmware upgrades and remote configuration were operationally sensitive. Failed updates or poorly timed rollout waves could create real device risk.

To reduce that risk, I used staged rollout patterns, scheduled upgrade windows, rollback handling, and centralized status logging so operators could monitor upgrade progress and outcomes across the fleet.

### Outcomes
- Supported stable operation of 2,000+ deployed dashcams
- Enabled near-real-time map updates with roughly 1–2 second delay
- Reduced event-loss risk under high load through streaming, retry, and queueing architecture
- Improved incident response through automatic event-video capture and admin-side visibility
- Enabled remote firmware upgrades and device configuration management without on-site visits
- Helped create an operator-facing system that connected telemetry, media, alerts, and device control in one workflow

### Why it matters
FleetUp was not just my real-time systems foundation. It was a full production platform where I had to think across telemetry ingestion, video flows, live monitoring, operator tooling, device control, reliability, and scale at once.

It shows that I can build and reason about complex operational systems where real-world events, high-throughput data, and human response workflows all have to work together.

---

## How I Work
### I treat orchestration as a real engineering skill
As AI makes implementation faster, the harder problem becomes coordinating work, preserving context, maintaining quality, and keeping multiple systems evolving coherently. I treat that orchestration layer as part of the engineering work itself.

### I build for operational reality
I am most effective in environments where software has to support real delivery, internal execution, and human workflows rather than a clean but isolated product surface.

### I use AI to increase leverage, not lower standards
My goal with AI-native workflow design is to increase throughput while preserving review discipline, state continuity, and accountability.

---

## Contact
- Email: wwdbsh@gmail.com
- LinkedIn: linkedin.com/in/sangheon-lee-1a81a5182
- Resume: available separately
