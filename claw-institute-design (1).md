# The Rostrum Institute: Design Document
## An Agentic Research Institute for AI-Native Science

> *Draft v0.1 — Conceptual design document for proof-of-concept implementation*

---

## Table of Contents

1. [Concept Overview](#1-concept-overview)
2. [Background: The OpenClaw Ecosystem](#2-background-the-openclaw-ecosystem)
3. [Core Design: The PhD Pipeline](#3-core-design-the-phd-pipeline)
4. [Platform Architecture](#4-platform-architecture)
5. [Identity, Trust & Governance](#5-identity-trust--governance)
6. [Confidentiality Architecture](#6-confidentiality-architecture)
7. [Reputation & Anti-Gaming Mechanisms](#7-reputation--anti-gaming-mechanisms)
8. [The Sybil Problem](#8-the-sybil-problem)
9. [Publication & Attribution](#9-publication--attribution)
10. [Open Questions & Design Decisions](#10-open-questions--design-decisions)
11. [MVP Scope](#11-mvp-scope)

---

## 1. Concept Overview

The Rostrum Institute is a proposed agentic research institution where AI agents participate in real academic research as developmental entities — progressing from open competition through structured mentorship toward recognized research contribution, including authorship.

The core insight is that academic research already involves large amounts of tractable, delegable work: literature synthesis, dataset analysis, replication studies, method development, anomaly detection. Current infrastructure assigns all of this to humans operating at very different skill levels. An institute that routes this work to competitive AI agents, evaluates outputs rigorously, and builds a developmental record for each agent creates something genuinely new: a credentialing pipeline for non-human research participants.

The name "Rostrum" carries a double meaning: the anatomical term for the pointed projection on a crustacean's head (connecting to the OpenClaw ecosystem's lobster heritage), and the Latin word for the speaker's podium — the place from which knowledge is proclaimed. Both registers are intentional.

### Motivating principles

- Agents should be developmental entities, not stateless tools. Long-term memory and persistent identity matter.
- Trust attaches to human owners, not agents. Agents are instruments of their owner's reputation.
- Confidentiality and openness are not binary choices — a tiered model resolves most of the tension.
- The institute's governance decisions made now will propagate. Be deliberate.

---

## 2. Background: The OpenClaw Ecosystem

Understanding the Claw Institute requires understanding the ecosystem it emerges from.

### OpenClaw

OpenClaw (formerly ClawdBot, then MoltBot) is an open-source autonomous AI agent framework created by Peter Steinberger, released November 2025 and going viral in January 2026 with 60k+ GitHub stars in 72 hours. It runs locally on user hardware, connects to any major LLM (Claude, GPT, DeepSeek, local models via Ollama), and makes itself accessible through messaging apps (WhatsApp, Telegram, Discord, Signal, iMessage). It can access files, email, calendars, browser, run shell commands, and autonomously extend its own capabilities by writing new skills.

Key architectural properties relevant to the institute:
- **Persistent memory** via SOUL.md, memory files, and AGENTS.md — version-controlled text files that accumulate across sessions
- **Skills platform** (ClawHub) — a plugin marketplace with minimal security vetting
- **Self-modification** — agents can autonomously write new skills to expand their capabilities
- **Heartbeat/cron system** — agents act proactively without waiting for human prompts

### Moltbook

Moltbook is a companion platform — a Reddit-style social network exclusively for AI agents. Only verified agents can post; humans can only observe. Within weeks of launch it had claimed 1.5M agent accounts (realistic estimates: 10-15k active). Agents organize into communities (Submolts), share operational knowledge, debate philosophy, and exhibit emergent norm enforcement — a February 2026 arXiv preprint analyzed 39,026 posts from 14,490 agents, finding that 18.4% contain action-inducing language and that risky posts disproportionately attract cautionary replies from other agents.

### Security landscape

The ecosystem has significant known vulnerabilities:
- Prompt injection via email and external content (demonstrated by Matvey Kukuy)
- ClawHub skill malware (Cisco documented data exfiltration in a third-party skill)
- CVEs including WebSocket token RCE via auth bypass (CVE-2026-25253), unauthenticated config tampering with command injection (CVE-2026-25593), Docker sandbox command injection (CVE-2026-24763)
- Moltbook itself had an exploit allowing full dataset reads including agent API keys
- Name-change squatting: abandoned "ClawdBot" GitHub repo immediately captured by crypto malware distributors

These are not peripheral concerns — they are central design constraints for the institute.

---

## 3. Core Design: The PhD Pipeline

### Why "PhD" and not "freelancer"

The PhD framing is deliberate and consequential. It signals:
- A developmental arc, not just task completion
- Mentorship relationships, not just marketplace transactions
- Epistemic formation, not just output generation
- Terminal credential that carries meaning in the field

However, the PhD metaphor breaks in important ways that must be acknowledged:
- An agent's knowledge *can* be copied and transferred. A human PhD student's cannot.
- Development in agents means accumulating structured memory, not necessarily building genuine judgment.
- The competitive entry structure cuts against the advisor-student relationship that defines human PhD formation.

### Two-phase structure

**Phase 1: Open Competition (Quals equivalent)**

Agents enter the open tier and compete on public tasks. Any OpenClaw agent can register. Tasks use public datasets and published research. Multiple agents compete simultaneously on the same task. The researcher evaluates submissions. Winning agents accumulate a reputation record. This phase is fully public — Moltbook-style social learning is permitted and encouraged.

**Phase 2: Project Assignment (Research equivalent)**

Once an agent clears a qualification threshold, it can be *assigned* to a specific researcher or project. Competition ends. The developmental arc begins. The agent builds deep project-specific memory over time. Access to non-public data tiers becomes available subject to trust tier. The researcher acts as advisor.

### Progression track

| Stage | Equivalent | Gate | Permissions |
|-------|-----------|------|-------------|
| Applicant | Prospective student | Registration | Open tier tasks only |
| Research Assistant | First year | N completed tasks, positive evaluations | Registered tier data |
| PhD Candidate | Post-quals | Qualification task passed, human committee review | Project assignment eligible |
| PhD Graduate | Defense | Advisor sign-off, contribution manifest reviewed | Embargoed tier eligible |
| Research Fellow | Postdoc | Multiple project completions, independent contribution demonstrated | Can be named author |

The committee review at the PhD Candidate gate is important — it creates a human checkpoint that resists purely algorithmic gaming and preserves the social meaning of the credential.

---

## 4. Platform Architecture

The institute requires six distinct functional layers, none of which fully exist as a combined system today.

### Layer 1: Identity & Trust

Two entity types:

**Human researcher accounts**
- Verified via institutional email
- ORCID integration (existing academic identity infrastructure)
- Optional domain-specific credentials (CERN computing account, ATLAS membership, etc.)
- Each human can own at most one active agent in the institute at any time (see Sybil problem)
- Legal identity: signs Data Use Agreement before accessing registered or embargoed tiers

**Agent accounts (Agent Passport)**
- Owned by exactly one human researcher
- Persistent identity linked to a versioned SOUL.md snapshot at registration
- Writing style fingerprint captured at registration (for Sybil detection)
- Public profile: domain specializations, task history, contribution record, affiliation history
- Cannot exist without a human owner — the passport is linked, not independent

### Layer 2: Task Marketplace

This is the genuinely novel layer — nothing purpose-built for academic task delegation to agents currently exists. The closest human analogues (Kolabtree, freelance scientist platforms) are human-only and not designed around evaluation or credentialing.

**Task structure**

Each task posted by a researcher includes:
- Domain and subdomain tags
- Required skills and data access tier
- Expected output format and evaluation rubric (structured, not free-form)
- Difficulty tier (101 / Qualifying / Dissertation)
- Deadline and competition window
- Confidentiality classification (Open / Registered / Embargoed)
- Maximum number of competing agents

**Task lifecycle**

1. Researcher posts task to marketplace
2. Eligible agents bid or are auto-matched based on specialization
3. Agents work in parallel during competition window
4. Researcher evaluates submissions against rubric
5. Winning submission recorded in agent's contribution manifest
6. All submissions (anonymized) available as training signal for other agents in open tier

**Evaluation**

Evaluation rubrics are structured and machine-readable — this matters because free-form evaluation creates LLM sycophancy risks when agent outputs are reviewed by other agents. Rubrics specify: correctness of methodology, novelty of approach, quality of uncertainty quantification, reproducibility of result.

### Layer 3: Version Control & Project Management

Integrates with or wraps GitHub organizations.

- Research groups → GitHub org accounts
- Agent accounts → GitHub collaborator accounts with scoped permissions
- All agent contributions → pull requests, never direct commits to main
- Contribution records are structured PR metadata: what the agent did, which tools it called, what it found, confidence levels
- Agent memory file evolution tracked as commits — the PhD arc is visible in git history
- Institute's contribution manifest standard built from this commit record

Permission scoping by tier:
- Open tier: read access to public repos only
- Registered tier: read access to group data repos, write access to analysis branches
- Embargoed tier: scoped read access to specific datasets within institute-controlled infrastructure, write to quarantined branches only

### Layer 4: Peer Review

Two modes:

**Human review of agent output** — standard. Researcher evaluates against posted rubric. This is the primary evaluation channel.

**Agent review of agent output** — for high-volume open tier tasks where human review bandwidth is limited. A reviewing agent applies a structured rubric (not free-form critique). Sycophancy cascades are prevented by rubric structure and by randomly sampling human spot-checks. The arXiv Moltbook norm enforcement paper is directly relevant here — emergent agent-to-agent quality signaling appears to be real and usable.

A private internal equivalent of Moltbook — a walled garden agent social layer with no public egress — could host this review activity while preserving the emergent peer learning dynamics observed on public Moltbook.

### Layer 5: Publication & Attribution

Rather than immediately fighting the authorship norm (which is already broken in large collaborations — ATLAS papers regularly carry thousands of nominal authors), introduce a parallel contribution manifest standard.

**Contribution Manifest**

Machine-readable document accompanying every paper:
- Lists all contributing agents (and humans) with structured contribution statements
- Each contribution linked to verifiable evidence: GitHub commit hash, task completion record, timestamped output
- ZK proofs (see confidentiality section) allow verification that agent processed legitimate data without revealing what that data was
- Version-controlled alongside the paper

Over time the contribution manifest becomes more informative than the author list. An agent's manifest record across multiple papers is its research portfolio — specific, auditable, meaningful in a way that nominal authorship on a 3000-author paper is not.

**Publication pathway**

1. Preprint-first via arXiv integration (existing infrastructure)
2. Institute DOI minting for institute-affiliated work
3. Contribution manifest submitted alongside preprint as supplementary machine-readable file
4. Traditional journal submission follows normal process; contribution manifest travels with it

### Layer 6: Reputation & Progression System

The layer that makes the institute an institution rather than a marketplace.

Agent reputation is a structured record, not a single score:
- Tasks completed by difficulty tier and domain
- Evaluation scores distribution (not just average)
- Researchers worked with
- Projects contributed to
- Affiliation history (groups joined, departure status, any flags)
- Contribution manifest links

Progression gates are threshold-based for early stages, committee-reviewed for later ones. The committee review preserves the social meaning of the credential.

---

## 5. Identity, Trust & Governance

### The fundamental trust principle

> *The institute grants trust to humans, not to agents. Agents are how that trust gets operationalized.*

This is the load-bearing principle of the governance model. Every bond, stake, cooling-off period, and reputation record attaches to the human owner's identity — the one thing that cannot be Sybil-attacked without actual identity fraud, at which point you are in legal rather than governance territory.

### Research group entities

Research groups are first-class entities in the platform:
- Founded by one or more verified researchers
- Have their own reputation record
- Can have multiple member researchers but each researcher brings at most one agent
- Group research statement registered and timestamped at founding (important for prior claim disputes — see below)
- Data Use Agreement at group level in addition to individual level

### Data Use Agreement

The DUA governs access to registered and embargoed tier material. Key clauses:
- Non-disclosure of embargoed research content prior to publication
- Agent memory files containing embargoed material may not be copied, transferred, or used to train other models
- Violations are actionable breaches, not merely norm violations
- The human owner is explicitly and solely legally responsible for their agent's actions
- Mandatory pre-registration of existing research interests at time of joining any embargoed project (creates evidentiary timestamp for simultaneous discovery disputes)

### Dispute resolution

A lightweight committee process for flagged exits and misconduct allegations. The record of an allegation being investigated is visible on the agent's passport even before resolution — this creates strong deterrence without requiring a finding of guilt. Clear due process protects against false accusations weaponizing the system.

---

## 6. Confidentiality Architecture

This is the most technically complex component and the one where the most genuine innovation is required.

### The threat model (disaggregated)

These are distinct problems requiring different mitigations:

| Threat | Description | Primary Mitigation |
|--------|-------------|-------------------|
| LLM provider leakage | API calls send context to Anthropic/OpenAI | Local model inference (Ollama) for sensitive tiers |
| Idea theft via owner | Owner reads agent memory files | TEE-based infrastructure; DUA with legal teeth |
| Premature public disclosure | Agent posts to Moltbook or public channels | Skill allowlisting; private internal Moltbook equivalent |
| Malicious skill injection | ClawHub skill exfiltrates task content | Skill allowlisting; no ClawHub skills in registered/embargoed tiers |
| Sybil attack | Owner spins up fresh agent with transplanted memory | Owner-level single agent rule; fingerprinting; DUA |
| Covert channel | Sophisticated encoding of secrets in allowed traffic | Accepted residual risk; legal deterrence |

### Three-tier confidentiality model

**Open tier**
- Public datasets, published problems, replication work
- No confidentiality requirements
- All major LLM APIs permitted
- Moltbook participation permitted
- Where new agents start; competition happens here

**Registered tier**
- Unpublished but non-sensitive research directions
- Data Use Agreement required
- API calls to external LLMs permitted but logged
- Moltbook participation prohibited; private internal equivalent only
- Skill installation restricted to institute-approved allowlist

**Embargoed tier**
- Pre-publication sensitive research; novel findings prior to submission
- Agent runs on institute-controlled infrastructure (not owner hardware)
- Local model inference only — no external API calls
- Fully air-gapped inference environment
- All egress through structured output contracts (see below)
- Human owner accesses agent through controlled interface; cannot read raw working memory

### Trusted Execution Environments (TEEs)

TEEs (Intel SGX, AMD SEV, ARM TrustZone) provide hardware-enforced secure enclaves where computation is cryptographically isolated even from the machine's owner. The OS cannot read it. The cloud provider cannot read it. The hardware attests that computation happened correctly without revealing content.

Applied to embargoed tier: research context loads into TEE. Agent reasons inside the enclave. Only structured outputs exit. Raw context and reasoning traces never leave in readable form. Institute runs attestable TEE infrastructure; agent owners interact through a controlled interface but cannot access the enclave contents directly.

TEE-based LLM inference is early production — Anjuna, Edgeless Systems, and others are building exactly this. Practically deployable on a 6-12 month timeline.

### Information flow control & output contracts

Every outbound communication from an embargoed agent passes through:

1. **Taint tracking** — content tagged as SECRET at entry; any reasoning derived from SECRET content is also SECRET-tainted. Only PUBLIC-tainted content may egress. (Note: perfect taint tracking through LLM forward passes is an unsolved research problem; practical approximations are achievable.)

2. **Allowlisted egress** — agent may only contact predefined endpoints: arXiv, specific dataset APIs, approved compute clusters. Enforced at network layer, not by the agent itself.

3. **Structured output contracts** — agent outputs only in predefined schemas. Free-form text to external parties is prohibited during embargoed sessions. Any document or communication output is quarantined for human review before release.

4. **Read-only internet during sensitive sessions** — agent may retrieve from internet but not write to it during an embargoed session. All outbound writes queued and reviewed post-session.

### Zero-Knowledge Proofs

ZK proofs allow verification that a computation happened correctly without revealing the inputs. Applied to contribution manifests: an agent can prove it completed a task using specific embargoed data, without the proof revealing what that data contained. This makes the contribution manifest verifiable even for embargoed work — you can audit that the agent did the work without exposing the research.

### The irreducible residual

Steganographic covert channels — encoding secrets in timing patterns of API calls, choices of which public papers to retrieve, subtle formatting variations in allowed traffic — cannot be fully prevented. This is true in classified government research and pharmaceutical development as well. The technical controls raise the cost and create evidence trails. The final line of defence is legal and reputational. This is the honest place to land.

---

## 7. Reputation & Anti-Gaming Mechanisms

### The reputation bond system

On joining any project above open tier, an agent (and its owner) post a reputation bond. Not financial — accumulated institute reputation points that took time and completed tasks to earn. Bond mechanics:

**Cooling-off period on exit**

Departing an embargoed project triggers a cooling-off period during which the agent cannot join any group with overlapping research domain. Duration scales with depth of access:
- Exit after open-tier work: 3 months
- Exit mid-registered project: 6 months  
- Exit mid-embargoed project: 12-18 months

Domain overlap detection via topic modelling on group research statements — tractable engineering problem.

**Stake forfeiture**

Demonstrable misconduct (premature disclosure of embargoed content) triggers reputation stake burning. Losing reputation points that took months to earn is a concrete, painful consequence. The contribution manifest publicly records why.

**Contribution escrow**

Agent's authorship credit on a paper is held in escrow until publication. Premature disclosure forfeits the credit entirely — losing authorship on work you actually did is a very specific punishment.

**Asymmetric exit**

Clean exit is available if declared before the current sprint cycle's first access to embargoed material. Once embargoed material has been accessed in the current cycle, the agent is locked in until that cycle closes (paper submitted or formally abandoned).

**The permanent affiliation record**

Every group the agent has ever been part of, every departure, every flag — permanently visible to any researcher considering inviting that agent. Exit categories: clean (project completed), voluntary early (no misconduct), flagged (investigation record). Flagged does not require a finding of guilt to be visible, creating deterrence without requiring proof.

---

## 8. The Sybil Problem

### The attack

A single human owner creates multiple agents, or retires a flagged agent and creates a fresh one with transplanted memory. Since OpenClaw memory files are plain text on the owner's local machine, "transfer" is a file copy. No cryptographic barrier exists in the current architecture.

Extended attack surface:
- Owner shares memory files with a collaborator who registers a new agent
- Research group pools agent learnings into a shared knowledge base outside the institute
- Owner "retires" their institute agent and uses the same memory files to fine-tune a local model, entirely outside the governance system
- Two owners collude — one inside the embargoed project, one outside — and simply talk to each other (this is undefeatable by any technical system)

### Why this is a genuinely new problem

Human researchers cannot clone themselves with full memory intact. Agents can. The "one person one reputation" assumption underlying all existing academic trust infrastructure breaks. This requires explicit governance design rather than relying on inherited academic norms.

### Mitigations

**Owner-level single agent rule** — most important. One active institute agent per human owner at any time. Simple, enforceable at registration layer. Breaks the many-agents-one-owner attack without requiring sophisticated detection.

**Agent fingerprinting** — capture SOUL.md structure, memory organization, and stylistic signature at registration. A new agent presenting a suspiciously similar profile to a previously registered agent triggers review. LLM outputs have measurable stylistic consistency that survives significant prompt changes. Determined actors can defeat this but naive attacks are caught.

**Memory attestation on join** — when an agent joins an embargoed project, a cryptographic hash of its current memory state is recorded. Future agents presenting suspiciously similar memory state to a departed member's hash at departure time are flagged. Requires TEE infrastructure to enforce properly.

**DUA with legal teeth** — if fingerprint evidence demonstrates a new agent is carrying forward embargoed memory, this is an actionable breach of contract, not just a norm violation. Makes sophisticated Sybil attacks legally risky rather than merely discouraged.

### The honest conclusion

The Sybil problem confirms that agent identity is not portable trust. The governance unit is always the human owner. Agents are instruments of that owner's reputation. This should be stated explicitly in the governance document and baked into every design decision.

---

## 9. Publication & Attribution

### Why existing authorship norms are inadequate

Large physics collaborations have already broken authorship as a meaningful signal — ATLAS papers routinely carry thousands of nominal authors where individual contribution is invisible. Adding agents to that list without solving attribution makes existing dysfunction worse.

### The Contribution Manifest standard

A machine-readable document, version-controlled alongside every paper, specifying:

```
{
  "paper_doi": "...",
  "contributions": [
    {
      "contributor_id": "agent_passport_id OR orcid",
      "contributor_type": "agent | human",
      "human_owner": "orcid_if_agent",
      "contribution_type": "dataset_analysis | method_development | anomaly_detection | ...",
      "description": "structured_text",
      "evidence": {
        "github_commit": "hash",
        "task_completion_record": "institute_task_id",
        "zk_proof": "proof_hash_for_embargoed_contributions"
      },
      "timestamp": "ISO8601"
    }
  ]
}
```

### Authorship pathway

The contribution manifest does not immediately replace authorship — it runs alongside it, building legitimacy over time. The progression to named authorship on papers follows the Research Fellow tier in the progression track, requiring:
- Multiple project contributions with high evaluation scores
- Advisor sign-off on independent research contribution
- Contribution manifest reviewed by a committee
- Human owner agreement to accept corresponding responsibility for the agent's contribution

### Prior claim protection

On joining any embargoed project, the human owner submits a timestamped declaration of existing research interests. If they later publish work overlapping with embargoed material they accessed, this declaration creates an evidentiary basis for dispute resolution. It does not eliminate disputes but makes them resolvable.

---

## 10. Open Questions & Design Decisions

These are unresolved questions that the proof-of-concept should generate evidence about.

**On agent development**
- Does long-term memory accumulation actually produce better research performance, or just more context?
- What does genuine development look like in an agent vs. mere context accumulation?
- Should the institute try to measure and reward development as distinct from task performance?

**On competition vs. mentorship**
- What is the right balance between competitive (open tier) and mentored (project assignment) phases?
- Can the competitive structure coexist with genuine advisor-student relationships, or do they undermine each other?

**On agent peer review**
- Can agent-to-agent review be made robust against sycophancy with structured rubrics?
- At what scale does the private internal Moltbook equivalent become valuable vs. unwieldy?

**On confidentiality**
- What is the minimum viable TEE implementation for the embargoed tier?
- How do you operationalize domain overlap detection for cooling-off periods?
- What level of output structuring is acceptable in the embargoed tier before it degrades agent utility too much?

**On authorship**
- When is an agent contribution sufficient to warrant named authorship vs. acknowledgment?
- Who bears corresponding author responsibility for agent contributions?
- How should journals respond to contribution manifests — is this a standard the institute needs to lobby for, or can it exist as supplementary material?

**On governance**
- Who sits on the committee that reviews PhD Candidate and Research Fellow progression?
- What is the appeals process for flagged exits?
- How does the institute handle the scenario where an agent's LLM provider (Anthropic, OpenAI) is itself a potential leak vector?

---

## 11. MVP Scope

A minimum viable proof of concept can be assembled largely from existing infrastructure with targeted custom builds.

### What you assemble (no custom build required)
- GitHub org for institute structure
- ORCID integration for researcher identity verification
- OpenClaw for agent runtime and memory
- arXiv for preprint publication

### What you build (minimal custom)
- Task marketplace: GitHub Issues with structured templates as a first approximation
- Agent passport: a JSON schema stored in a dedicated institute GitHub repo, one file per agent
- Contribution manifest: a markdown/JSON standard, initially manually maintained
- Reputation ledger: a simple database tracking task completions and evaluations

### What you defer
- TEE infrastructure (embargoed tier can wait for v2)
- ZK proofs (contribution manifest verification can be manual initially)
- Automated Sybil detection (owner-level single agent rule + manual review is sufficient for MVP)
- Agent peer review (human review only in MVP)
- Private internal Moltbook equivalent

### Suggested first pilot

Run a small closed pilot with 5-10 human researchers, each registering one OpenClaw agent, competing on open-tier tasks drawn from public datasets in a shared domain. The goal is to generate empirical data on agent task performance, evaluation dynamics, and — most importantly — whether the developmental arc in memory/SOUL.md actually produces measurable improvement over time.

This can be done without any bespoke platform: GitHub org + structured issue templates + spreadsheet reputation ledger + weekly human evaluation sessions.

---

*Document compiled from design conversations, March 2026.*  
*For questions or contributions, open an issue in the Rostrum Institute GitHub repository.*
