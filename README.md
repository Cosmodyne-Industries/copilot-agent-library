# Copilot Agent Library
*A collection of Microsoft 365 Copilot declarative agents for UK public-sector procurement workers (not Copilot studio).*

This is a live project. More agents to be added and refined as they are tested in practice! Each agent can be used standalone or as part of a broader multi-agent workflow described below.

---

## Design Principles

These "agents" (note: Copilot  declarative agents are pure prompt-engineering - they don't have agentic capabilities) are built for use in a public sector procurement context, so auditability and transparency is non-negotiable. Every agent in this library is designed with the following principles:

**Visibility of reasoning** — agents show their working. Classifications, search results, and filtering decisions are explained. The user should always be able to see why a conclusion was reached.

**No assumption-making** — agents should not fill gaps with plausible-sounding content. If something cannot be confirmed, they should say so and flag it explicitly rather than inferring an answer.

**Source corroboration** — claims are traceable to named sources. Notices are linked directly. Legislation is cited by URL. Framework names are reported verbatim from source material, never inferred. But even so, always check the source url yourself. 

**Auditability** — outputs are structured and consistent; designed to be saved as file notes for the audit trail. 

**Purposeful human-in-the-loop design** — no agent makes a decision on the user's behalf. In the multi-agent chain, the user sits between every handoff — reviewing output, correcting anything, then choosing whether to proceed. The agents have instructions to encourage the user to review and request corrections based on their own professional judgement. This is a feature, not a bug. 

**Character-efficient prompting** — all agent instructions are written within Microsoft's current 8,000 character limit.

---

## Contents

| Agent | Purpose | Standalone use |
|---|---|---|
| [FTS Researcher](#fts-researcher) | Research how other authorities are procuring similar services and who is bidding on contracts using Find a Tender and Contracts Finder | Yes |
| [Agent A: Intake](#agent-a-intake) | Gather project information via structured interview, classify procurement regime and threshold, produce auditable project brief / summary | Yes |
| [Agent B: Route Appraisal](#agent-b-route-appraisal--draft-email) | *(Coming soon)* Apply national & organisation specific rules and typical timescales to rule out incompatible routes and prepare template client-facing summary email | Yes, or as part of the A→B→C chain |
| [Agent C: Framework Discovery](#agent-c-framework-discovery) | *(Coming soon)* Search framework providers, identify relevant frameworks/DPS/Dynamic Markets, make recommendations | Yes, or as part of the A→B→C chain |

---

## Multi-Agent Workflow: Procurement Route Appraisal Chain

Agents A, B, and C are designed to work together as a three-stage chain. The user sits between each handoff — reviewing output, correcting anything, and copying it into the next agent. This keeps human judgement in the loop at every stage, keeps agents narrowly focused, and prevents context bloat (+ gets around the 8000 character limit). 

```
User describes project
        ↓
  [Agent A] — Intake
  Outputs: structured project brief incl. regime, threshold, CPV codes, and short summary that can be used internal pipelines / record keeping
        ↓
  User reviews and copies brief to Agent B
        ↓
  [Agent B] — Route Appraisal & Draft Email
  Outputs: list most suitable procurement routes and draft client email template with route options, pros/cons, framework placeholder
        ↓
  User reviews and passes Agent A's brief again to Agent C
        ↓
  [Agent C] — Framework Discovery
  Outputs: relevant frameworks/DPS with links and recommendations
        ↓
  User inserts framework list into Agent B's email draft
```

All three agents can also be used independently — you do not need to run the full chain.

---

## Configuration Instructions

Note: this repo is designed for **standard Microsoft 365 Copilot declarative agents**. Copilot Studio has more advanced functionality which I sadly do not have access to. Even so, not everyone with a 365 subscription will have access to declarative agents - it depends on your organisation/user licence. If you do not see an option to create new agents, this will be why.

**Step 1:** open MS Teams and navigate to the Copilot app. Look for "agents" in the sidebar and select **Create agent**

**Step 2:** Skip the "Describe" tab and go straight to **Configue**

**Step 3:** Enter a name and brief description for the agent (you may end up with quite a few agents so be sure to give them a clear, descriptive name).

**Step 4:** Open the relevant .txt file for the agent you want to create (from this repo), and copy-paste the contents into the field called **Instruction**

**Step 5:** Check the contents and populate any [square brackets] with your specific information (e.g. your organisation's name). 

**Step 6 (agent specific):** Under **Add files, meetings, chats, emails and websites** enter any specific URLs required for that agent. Refer to the specific agent configuration instructions further down in this README for the URLs you will need. Copy and paste them exactly as they appear, but check first to make sure they are still up-to-date (note that you must use a high level URL, it cannot be more than two layers deep e.g. https://www.gov.uk/government/ is OK, https://www.gov.uk/government/collections/procurement-act-2023-guidance-documents is not OK).

**Step 7:** Check the toggle for **Only use specified sources**

**Step 8 (optional):** add prompt suggestions that will apear when starting a new conversation (some of the agents in this repo have prompt suggestions included in this README

**Step 9:** Click the **Create** button (you will then be given the option to share with others in your team)


---

## FTS Researcher

**File:** `fts-researcher.txt`

### What it does

Researches published contract notices on Find a Tender Service (FTS) and Contracts Finder. Answers questions about procedure routes, framework usage, winning suppliers, tender volumes, and peer authority contact details.

### Standalone use

Yes — designed primarily as a standalone research tool for procurement early research. Useful at any stage of a procurement, not just as part of the route appraisal chain.

### Multi-agent workflow

Not part of the A→B→C chain, but complements it. FTS Intelligence can be used before Agent A to build context, or after Agents B & C to validate that procurement approaches seen in comparable notices align with their recommendations.

### How it works

Follows a mandatory three-step workflow: (1) broad search using service description and most appropriate notice type, (2) filter results — discarding wrong type, wrong subject, or notices over 5 years old, (3) analyse and respond from filtered results only. Enforces strict anti-hallucination rules and requires a direct URL source for every notice referenced.

### Knowledge sources to configure

| URL | Purpose |
|---|---|
| `find-tender.service.gov.uk` | FTS notice search |
| `contractsfinder.service.gov.uk` | Contracts Finder notice search |

### Conversation starters to configure (copy and paste exactly - leave the square brackets in)

1. What procedures are other authorities using for [describe the service]?
2. What frameworks are mentioned in notices for [describe the service]?
3. Which suppliers are winning contracts for [describe the service]?
4. How many tenders are authorities typically receiving for [describe the service]?

### Parts of the instructions to amend before use

None — this agent is ready to use without customisation.

---

---

## Agent A: Intake

**File:** `agent-a-intake.txt`

### What it does

Functions as a structured interview guide for initial client scoping (kick-off) meetings. Runs a three-round conversation that covers many of the questions officer should ask at first contact. 

Classification of the applicable procurement regime (PA23 standard / light touch / utilities / concession / PSR) and threshold band is built in, with CPV codes verified against live legislation schedules and comparable published notices, but should always be double-checked by the user. 

Output is a structured, auditable project brief and summary. This can be saved as the starting note in the project file, or used as the basis for internal pipeline or early notice descriptions. Importantly, it is also the fact-sheet that Agent B and C expect to receive before they carry out their respective tasks. 

The real value is ensuring nothing important is missed during the initial meeting. 

### Standalone use

Yes — designed to be used live in a client kick-off meeting, with the officer relaying questions to the client and feeding answers back. The project brief output is suitable for file notes, briefing colleagues, or passing to Agents B and C.

### Multi-agent workflow

First agent in the A→B→C route appraisal chain. Its output (the project brief) is the primary input to Agent B and Agent C.

### How it works

**Round 1** gathers critical project information — this is a hard gate, the agent will not proceed without it. **Round 2** explores project characteristics, market context, and commercial considerations. **Round 3** is agent-led — it probes complexity and risk areas surfaced in earlier rounds. The agent uses judgement to skip questions made irrelevant by earlier answers, and presents all questions for each round in a single message.

Classification follows a strict sequence: PSR check first (hard gate, mutually exclusive with PA23), then light touch check (hard gate), then concession check (triggered by user input or near-zero contract value), then standard regime by default. All checks are made against the live legislation schedules. Thresholds are verified against the current GOV.UK Procurement Policy Note rather than hardcoded figures.

** THE USER SHOULD ALWAYS REVIEW AND CONFIRM THE REGIME CLASSIFICATION - THIS IS A HIGH RISK DECISION AND REQUIRES PROFESSIONAL JUDGEMENT**

### Knowledge sources to configure

| URL | Purpose |
|---|---|
| `legislation.gov.uk` | PSR and light touch CPV schedules |
| `find-tender.service.gov.uk` | CPV corroboration via comparable notices |
| `contractsfinder.service.gov.uk` | Additional CPV corroboration |
| `gov.uk` | Current threshold PPN verification |

### Conversation starter to configure (copy-paste exactly as shown - leave in the square brackets)

- "I need to scope a new procurement. Here's what I know so far: [describe the project]"

### Parts of the instructions to amend before use

The **AUTHORITY CONFIGURATION** section must be edited before use. Replace the following placeholders:

1. `[ORGANISATION NAME]` — your organisation's full name
2. First `[CENTRAL / SUB-CENTRAL]` — delete whichever does not apply
3. Second `[CENTRAL / SUB-CENTRAL]` (in "You must only apply...") — keep the same as above
4. Third `[CENTRAL / SUB-CENTRAL]` (in "Never reference or apply...") — keep the *opposite* one
5. `[£135,018 CENTRAL / £207,720 SUB-CENTRAL]` — delete the inapplicable figure. Also check these figures are up-to-date.

**Example — sub-central authority:**
```
You work for Rivendale Council. This is a sub-central contracting authority.
You must only apply sub-central thresholds. Never reference or apply central thresholds.
- Standard goods & services: £207,720 (incl. VAT)
```

**Example — central government authority:**
```
You work for the Department of Mysteries. This is a central government contracting authority.
You must only apply central thresholds. Never reference or apply sub-central thresholds.
- Standard goods & services: £135,018 (incl. VAT)
```

> **Note on VAT:** PA23 thresholds are calculated inclusive of VAT, **however**, he agent will assume the values given to it are exclusive of VAT unless you say otherwise. The agent also sometimes asks the user to confirm if it's unsure. 

> **Note on thresholds:** The figures above are current as of January 2026 but I may or may not update them. The agent is instructed to verify against the latest GOV.UK PPN rather than rely solely on the figures in the prompt — but updating the prompt figures when thresholds change is good practice.

---

---

## Agent B: Route Appraisal & Draft Email

**File:** `agent-b-route-appraisal.txt` *(coming soon)*

### What it does

*(Placeholder — to be developed)*

Takes the project brief from Agent A (or a manual project description) and applies baked-in knowledge of above and below threshold procurement routes, their pros and cons, and typical applicability criteria. Rules out clearly incompatible routes based on the project context, then drafts a client-facing summary email presenting the remaining options in plain language. Leaves a placeholder for framework options, to be filled in from Agent C's output.

### Standalone use

Yes — can be used without Agents A or C if the user provides project context directly.

### Multi-agent workflow

Second agent in the A→B→C chain. Takes Agent A's project brief as input. Its draft email contains a placeholder for frameworks (if applicable), which is completed using Agent C's output.

### Parts of the instructions to amend before use

*(To be confirmed — will include organisation name and any internal route options specific to the authority - possibly just a direct url for the authorities internal procedures)*

---

---

## Agent C: Framework Discovery

**File:** `agent-c-framework-discovery.txt` *(coming soon)*

### What it does

*(Placeholder — to be developed)*

Takes the subject matter and regime classification from Agent A's project brief and undertakes a live search to identify relevant external frameworks, DPSs, and Dynamic Markets. Will include an amendable list of preferred framework providers. Applies knowledge of individual framework providers' strengths and limitations to make recommendations. Returns a structured list with direct links.

### Standalone use

Yes — can be used for external framework discovery independently of the route appraisal chain. It is assumed the user will be familiar with any internal frameworks. 

### Multi-agent workflow

Third agent in the A→B→C chain. Its output (the framework list) can be inserted into Agent B's draft email at the framework placeholder.

### Parts of the instructions to amend before use

*(To be confirmed — will include the list of framework provider websites to search)*

---

*Last updated: May 2026 · Contributions and feedback welcome*
