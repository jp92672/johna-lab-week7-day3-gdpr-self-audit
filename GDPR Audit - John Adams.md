# GDPR Audit - John Adams

## Kick-off

### Data processing brief

**System:** Virtual Coffee Chat Bot (Week 5 Project). An AI chatbot on John Adams's personal site. Recruiters and hiring managers ask it questions about his work history, skills, and projects, book a call via Calendly, or send him a message via Resend. Built with n8n (self-hosted, Docker), OpenAI (LLM and embeddings), Pinecone (vector store), and Cohere (reranking).

**Status:** currently a prototype, not yet handling real recruiter traffic. Gaps identified below (retention, DPAs, consent) are pre-launch requirements, not current violations.

**Does the system process personal data?**
Yes, in two categories. First, John's own career data in the knowledge base: name, employer history, skills, achievements, CliftonStrengths results, contact details. Second, personal data belonging to recruiters and hiring managers who use the chat: names, email addresses, employer/company names, and whatever they type into open-ended chat text. The second category is higher risk. It is third-party data collected in an unfiltered, unstructured format.

- **What personal data does the system process?**
  Names, email addresses, employer/company names, location/region information, and free-text interview-context content typed by recruiters. Also feedback data: thumbs-up/down ratings and error logs tied to each conversation. Separately, John's own career history, skills, and narrative content sits in the knowledge base as source material for generating answers. This is data about John, not the recruiters.

- **Where does that data come from?**
  Directly from the recruiter, typed into the chat in real time, plus whatever they submit when booking a call or sending a message. Nothing is pulled from an existing database or a third-party source. All of it is first-party, submitted by the user at the point of interaction.

- **What is the data used for?** (each purpose listed separately)
  1. Answering the recruiter's question in the conversation.
  2. Booking a call (Calendly) and sending an email on John's behalf (Resend) when the recruiter requests it.
  3. Logging the conversation, including ratings and errors, so John can review it afterward to improve the bot's answers and find gaps in his own career information.

- **Who processes it?**
  John is the controller. He sets the purpose and means of processing. Third-party processors: OpenAI (inference and embeddings), Pinecone (vector storage/retrieval), Cohere (reranking), Calendly (scheduling), Resend (email delivery). n8n is self-hosted in Docker by John and does not send data to an n8n-the-company entity, so it is not a third-party processor. The infrastructure provider hosting that Docker instance is not yet identified. This is an open item.

- **Where is the data stored and processed?**
  Not EU, and residency is otherwise unconfirmed. All identified vendors are non-EU: OpenAI, Pinecone, Calendly, Resend, and Cloudflare (if it logs identifiable traffic) are US-based; Cohere is Canada-based. The Docker host running n8n is unconfirmed. No data residency has been reviewed with any vendor, and no transfer mechanism (e.g. Standard Contractual Clauses) is in place.

- **Does the system make or assist in any decision that affects people?**
  No automated decision-making affects recruiters. The bot answers questions and books calls; it does not score, rank, or filter anyone. There is no human review before the bot acts: it answers, books, and sends email in real time. John reviews conversation logs afterward to catch errors and improve the knowledge base, which is retrospective quality control, not a pre-action approval step. This missing pre-action checkpoint, particularly for autonomous email sending, is a design-level compliance concern.

---

## CFU checkpoints

### 1. Recognize

**Categories of personal data present:** names, email addresses, employer/company names, location/region data, free-text interview-context content, and behavioral/feedback data (ratings, error logs). Also John's own career and contact data in the knowledge base.

**Special-category data (Article 9):** low risk but not zero. Nothing in the design solicits health, biometric, political, religious, or similar data. But the chat is open text, so a recruiter could type sensitive information about themselves unprompted (for example a disability accommodation need). This would be inferable from a stored transcript, not solicited.

**Crosses an EU border?** Yes. Recruiters can be anywhere, including the EU. Every identified vendor is non-EU (US or Canada). Any EU-based recruiter's data leaves the EU as soon as the bot processes their message.

### 2. Apply

Full audit worksheet below: Phase 1 (personal data inventory), Phase 2 (role map), Phase 3 (lawful basis), Phase 4 (risk and rights), Phase 5 (law stacking). Lawful basis is justified per purpose. `TBD — legal review` is used where the answer is not yet settled.

### 3. Integrate

Client recommendation memo below (Phase 6), reflecting the audit findings: bottom line, top three actions, residual risks.

### 4. Verify

**Accountability test:** could compliance be demonstrated to a regulator using only the documentation that currently exists for this project? No. This audit document is the only compliance documentation that exists. There is no signed DPA with any vendor, no privacy notice, no written retention policy, no LIA on file beyond what is written here, no DPIA, and no records of processing activities. If asked to show compliance today, the only evidence available would be this self-audit itself, which is a first-pass assessment, not proof of compliance.

---

## Core

### Phase 1: Personal data inventory

| Data category | Source | Purpose(s) | Retention period (known/estimated) | Crosses EU border? |
|---|---|---|---|---|
| Recruiter chat text (names, companies, interview context, anything typed) | Typed directly into chat by the recruiter | Answering the recruiter's question | TBD — legal review, no retention policy exists, kept indefinitely by default | Yes |
| Recruiter chat text (same category, different purpose) | Typed directly into chat by the recruiter | Logging to improve the bot and find gaps in John's career info | TBD — legal review, no retention policy exists, kept indefinitely by default | Yes |
| Recruiter name and email address | Submitted when requesting a booking or message | Booking a call (Calendly) | TBD — legal review, held by Calendly per their own retention terms, not reviewed | Yes |
| Recruiter name and email address | Submitted when requesting a booking or message | Sending an email on John's behalf (Resend) | TBD — legal review, held by Resend per their own retention terms, not reviewed | Yes |
| Feedback/behavioral data (thumbs up/down, error logs) | Generated by the recruiter's interaction with the bot | Improving bot answers, finding gaps in John's career info | TBD — legal review, no retention policy exists | Yes |
| John's own career data (name, employer history, skills, achievements, CliftonStrengths, contact info) | Entered by John into the knowledge base | Generating answers to recruiter questions (retrieval) | Indefinite, source material maintained by John | No, stored in Pinecone (US) but this is John's own data, not a third party's |

**Purpose limitation flag:** the same recruiter chat text is used for two different purposes: answering the question in the moment, and logging that same text afterward to train/improve the bot. These are not the same processing activity even though the underlying data is identical. A recruiter who expects their question to be answered does not automatically expect that same text to be retained and reused for model improvement. This is the incompatible-purpose risk called out in the lab instructions, and it is why Phase 3 assigns these two purposes different lawful bases rather than treating them as one.

### Phase 2: Role map

| Entity | Role (controller / processor / joint controller) | Processing activity | DPA in place? |
|---|---|---|---|
| Your client | N/A. This is a self-audit; John is both the operator and the beneficiary of the system, not a separate client. | — | — |
| You / your team | Controller. John sets the purpose and means of processing. | Designs the bot, maintains the knowledge base, reviews logs, decides what data is collected and why. | N/A, controller does not need a DPA with itself |
| LLM API provider (OpenAI) | Processor | Generates chat responses and embeddings from recruiter questions and the knowledge base. | Not confirmed. Needed before launch. |
| Any other vendor: Pinecone | Processor | Stores and retrieves knowledge base vectors. | Not confirmed. Needed before launch. |
| Any other vendor: Cohere | Processor | Reranks retrieved search results. | Not confirmed. Needed before launch. |
| Any other vendor: Calendly | Processor | Books calls using recruiter name and email. | Not confirmed. Needed before launch. |
| Any other vendor: Resend | Processor | Sends email on John's behalf using recruiter name and email. | Not confirmed. Needed before launch. |
| Any other vendor: Cloudflare | Processor, if it logs identifiable traffic. Not yet confirmed. | Possible traffic/request logging. | Not confirmed. Needed before launch if it processes identifiable data. |
| Docker host (n8n infrastructure) | Processor, role unconfirmed until the provider is identified | Hosts the self-hosted n8n instance that runs the workflows and stores chat_log/error_log data | Not identified. Open item. |

n8n itself is not listed as a separate processor. It is self-hosted in Docker by John and does not send data to an n8n-the-company entity, so there is no third party there to sign a DPA with. The actual gap is the unidentified Docker hosting provider, listed above.

**International transfers:** every confirmed vendor is non-EU (OpenAI, Pinecone, Calendly, Resend, Cloudflare in the US; Cohere in Canada). No transfer mechanism, such as Standard Contractual Clauses, has been reviewed or confirmed with any vendor. TBD — legal review.

### Phase 3: Lawful basis assessment

| Purpose | Proposed lawful basis | One-line justification | Flag for legal review? |
|---|---|---|---|
| Answering the recruiter's question | Legitimate interests | Processing the message is unavoidable to answer it; consent doesn't fit because it must be freely withdrawable, and a message already being answered can't be un-processed mid-conversation. | No |
| Booking a call (Calendly) | Consent | Triggered only by the recruiter's explicit request to book, a distinct affirmative action that shares their name and email with a second vendor. | No |
| Sending an email on John's behalf (Resend) | Consent | Same reasoning as booking: a distinct affirmative action, not required just to get an answer. | No |
| Logging the conversation to improve the bot | Consent | A separate, lower-expectation purpose than answering the question. Needs its own opt-in, not folded into the legitimate-interests basis for answering. | No |

**Legitimate Interests Assessment (LIA)** for "answering the recruiter's question":

1. **Is the interest legitimate?** Yes. The concrete business need is responding to a message the recruiter chose to send, which is the core function of the bot.
2. **Is the processing necessary?** Yes. There is no less intrusive way to answer a question than reading it. Processing the message is the minimum needed to deliver the response.
3. **Does the individual's interest override?** No. A recruiter who opens the chat and types a question has a reasonable expectation that the message will be read and answered. This does not extend to retention or reuse of that text, which is why logging is handled separately under consent.

### Phase 4: Risk and rights analysis

**Special category data (Article 9):** low risk but not zero. The bot does not solicit health, biometric, political, religious, or similar data. But it is open text, so a recruiter could type sensitive information about themselves in a single message, for example a disability accommodation request. This would be inferable from a stored transcript rather than solicited. A guardrail can reduce this risk: the system prompt can instruct the bot to decline to engage with sensitive personal topics and remind the user not to share them, but this only reduces the risk, it does not eliminate it, since the message still reaches the model and gets logged before any guardrail response is generated. No Article 9 condition currently applies because the design does not intentionally collect this data; if it does arrive, it has no lawful basis to be retained.

**Automated decision-making (Article 22):** no. The bot answers questions and books calls but does not score, rank, or filter recruiters. There is no decision with legal or similarly significant effects. The one design gap is that the bot acts (answers, books, emails) without human review before the action happens; John only reviews logs afterward. This is a design and transparency concern, not an Article 22 trigger, since no decision about a person is being made.

**DPIA trigger:** likely required. At least two EDPB criteria apply. First, large-scale processing once the bot is public, since any recruiter who visits the site can trigger data collection. Second, innovative technology, given the AI agent, RAG pipeline, and cross-border processing across five non-EU vendors. Two or more criteria applying means a DPIA is generally required before public launch.

**Data subject rights friction:** the two most likely friction points are right of access ("what did you store about me?") and right to erasure ("delete my chat"). Neither has a workflow today. No automated way exists to locate one recruiter's data across chat_log, error_log, Calendly, and Resend, or to delete it on request. Right to object to profiling is not relevant, since the bot does not profile. The intended design is that these requests route to John directly rather than being resolved by the bot itself.

### Phase 5: Law stacking check

- **AI Act cross-check:** limited risk, Article 50 transparency. The bot generates AI content and talks to recruiters without currently disclosing it is AI. Article 50 adds a disclosure obligation GDPR does not require on its own; GDPR separately requires a lawful basis and privacy notice for the data processing itself.
- **ePrivacy check:** no cookies or tracking pixels in the custom chat UI. The `@n8n/chat` widget's own storage behavior has not been verified, and it may write a session identifier to browser localStorage, which would engage ePrivacy consent requirements. This is unconfirmed and open.
- **Data Act check:** N/A. No connected product, IoT, or cloud-switching data involved.

### Phase 6: Compliance memo

**TO:** Data Protection Officer / Legal Counsel
**RE:** GDPR audit, Virtual Coffee Chat Bot

**Bottom line: proceed with conditions.** The bot itself is low risk. It does not score, rank, or profile anyone, and it makes no automated decision affecting a person. It is currently a single-user prototype, not yet handling real recruiter traffic. But it cannot go live as-is. There is no consent flow for logging, no retention policy, and no confirmed data processing agreement with any vendor handling recruiter data. These are governance gaps, not design flaws, and they must close before a real recruiter's data ever reaches this system.

**Top three actions:**

1. Confirm the vendor list and get DPAs signed: OpenAI, Pinecone, Cohere, Calendly, Resend, and Cloudflare if it logs identifiable traffic. Identify the Docker hosting provider, currently unconfirmed, and get a DPA there too. This has to happen first, since the DPIA below needs each vendor's transfer route and retention terms as an input.
2. Build a two-part opt-in: legitimate interests covers answering the recruiter's message, consent covers logging and covers booking/email. These are separate purposes with separate expectations and need separate lines, not one bundled disclosure.
3. Complete a DPIA before any public launch. At least two EDPB criteria apply (large-scale processing, innovative technology), which is enough to require one.

**Residual risks:**

- A recruiter can type sensitive personal information into open chat text. A guardrail can decline the topic and warn the user not to share it, but the message still reaches the model and gets logged before that guardrail fires. This risk can be reduced, not eliminated.
- Third-party infrastructure logging, including Cloudflare and the `@n8n/chat` widget's localStorage behavior, has not been fully audited. Some data handling may sit outside direct visibility even after the actions above are complete.
- A written retention policy and a retention policy that actually deletes data across every processor are different projects. The second is an operational commitment, not a document.

**What this memo is not:** a legal opinion, a DPIA, or a certification. Legal counsel must review this assessment before it is relied on for any compliance decision.

## Reinforce

Not attempted. Marked optional in the lab instructions ("if you finish the core tasks early") and out of scope for this submission.

## Stretch

Not attempted. Marked optional in the lab instructions and out of scope for this submission.

---
