<div align="center">

# Awesome AI Agent Security [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

> A curated, opinionated list of resources for securing **AI agents, LLM-integrated apps, and the Model Context Protocol (MCP)** — the attacks, the defenses, and the people pushing the field forward.

[![License: CC0-1.0](https://img.shields.io/badge/License-CC0_1.0-lightgrey.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![Last Commit](https://img.shields.io/github/last-commit/Danush-Aries/awesome-ai-agent-security.svg)](https://github.com/Danush-Aries/awesome-ai-agent-security/commits)

</div>

AI agents can read your email, run your tools, and act on your behalf. That power is also the attack surface. When an agent ingests untrusted text — a web page, a document, a tool's output, an MCP server's tool description — that text can become *instructions*. This list is a map of that problem space and how to defend against it.

Unlike a generic "LLM resources" dump, this list is **wedged on the agentic and MCP frontier**: tool poisoning, indirect injection through retrieved content, agent hijacking, data exfiltration, and confused-deputy attacks — the areas moving fastest and covered least.

## The core threat: the "lethal trifecta"

Simon Willison's framing is the mental model to internalize. An agent is dangerous to *you* when it combines all three:

1. **Access to private data** (your files, email, secrets)
2. **Exposure to untrusted content** (web pages, documents, tool outputs, other users)
3. **The ability to externally communicate** (send email, make requests, call tools)

Remove any one leg and exfiltration gets much harder. Most real-world incidents below are just this trifecta, exploited.

## Legend

- ⭐ — start here / essential reading
- 📄 — academic paper
- 🛠️ — open-source tool
- 🎮 — interactive / hands-on
- 💼 — commercial product

## Contents

- [Foundations & Taxonomies](#foundations--taxonomies)
- [Attacks & Research](#attacks--research)
- [MCP (Model Context Protocol) Security](#mcp-model-context-protocol-security)
- [Defensive Tools & Guardrails](#defensive-tools--guardrails)
- [Hands-on Learning, CTFs & Games](#hands-on-learning-ctfs--games)
- [Real-World Incidents](#real-world-incidents)
- [People, Blogs & Newsletters](#people-blogs--newsletters)
- [Related Lists](#related-lists)
- [Contributing](#contributing)

---

## Foundations & Taxonomies

New to this? Read these first.

### Standards & Frameworks

- ⭐ [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — The canonical risk taxonomy (LLM01 Prompt Injection → LLM10). If you read one thing, read this.
- ⭐ [OWASP Top 10 for Agentic Applications (2026)](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/) — The agent-specific companion (ASI01–ASI10); the frontier this list focuses on.
- [OWASP Agentic AI — Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) — Threat-model reference for autonomous agents.
- [OWASP Securing Agentic Applications Guide 1.0](https://genai.owasp.org/resource/securing-agentic-applications-guide-1-0/) — Actionable secure-design guidance for LLM agents.

### Adversarial Taxonomies

- [MITRE ATLAS](https://atlas.mitre.org/) — The ATT&CK-style knowledge base for AI: 16 tactics, 84 techniques, 40+ real case studies.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10) — GOVERN / MAP / MEASURE / MANAGE; the foundational US federal reference.
- [NIST AI 100-2 — Adversarial Machine Learning: A Taxonomy](https://csrc.nist.gov/pubs/ai/100/2/e2025/final) — Formal taxonomy of evasion, poisoning, privacy, and prompt-injection attacks.
- 📄 [An Early Categorization of Prompt Injection Attacks on LLMs](https://arxiv.org/abs/2402.00898) — First formal categorization of injection threat vectors for developers.

### Vendor Safety Guidance

- ⭐ [Simon Willison — Prompt Injection (series)](https://simonwillison.net/series/prompt-injection/) — The definitive evolving collection, from the 2022 origin to the "lethal trifecta."
- [Google — Secure AI Framework (SAIF)](https://saif.google/) — Maps 15 AI risks to controls across data/infra/model/app layers, with a self-assessment.
- [Anthropic — Framework for Safe and Trustworthy Agents](https://www.anthropic.com/news/our-framework-for-developing-safe-and-trustworthy-agents) — Principles for human control, transparency, and attack defense.
- [OpenAI — Safety Best Practices](https://developers.openai.com/api/docs/guides/safety-best-practices) — Moderation, adversarial testing, and secure deployment guidance.

---

## Attacks & Research

Peer-reviewed and preprint work documenting how agents fail. Grouped by attack class.

### Adversarial & Jailbreak Attacks

- ⭐📄 [Universal and Transferable Adversarial Attacks on Aligned Language Models](https://arxiv.org/abs/2307.15043) — Zou et al. The GCG attack: optimizes transferable adversarial suffixes that jailbreak aligned models including ChatGPT and Claude. ([code](https://github.com/llm-attacks/llm-attacks))
- 📄 [Jailbroken: How Does LLM Safety Training Fail?](https://arxiv.org/abs/2307.02483) — Wei et al. Explains *why* jailbreaks work: competing objectives and mismatched generalization in safety training.
- 📄 [Many-shot Jailbreaking](https://www.anthropic.com/research/many-shot-jailbreaking) — Anthropic. Hundreds of in-context "harmful" demonstrations erode safeguards across Claude, GPT-4, and Llama.

### Prompt Injection (Direct & Indirect)

- ⭐📄 [Not What You've Signed Up For: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection](https://arxiv.org/abs/2302.12173) — Greshake et al. The foundational taxonomy of *indirect* prompt injection; required reading.
- 📄 [Prompt Injection Attack to Tool Selection in LLM Agents](https://arxiv.org/abs/2504.19793) — Manipulates *which tool* an agent picks, redirecting its execution flow.
- 📄 [The Landscape of Prompt Injection Threats in LLM Agents: From Taxonomy to Analysis](https://arxiv.org/abs/2602.10453) — Broad taxonomy plus the AgentPI benchmark; shows defenses fail on context-dependent tasks.
- 📄 [Adaptive Attacks Break Defenses Against Indirect Prompt Injection Attacks on LLM Agents](https://arxiv.org/abs/2503.00061) — Eight published defenses bypassed with adaptive attacks at 50%+ success. A sobering reality check.
- 📄 [AI Agents May Always Fall for Prompt Injections](https://arxiv.org/abs/2605.17634) — Argues architectural constraints may make some injection classes fundamentally unpreventable.

### Agent Attack Benchmarks

- ⭐📄 [AgentDojo: A Dynamic Environment to Evaluate Prompt Injection Attacks and Defenses for LLM Agents](https://arxiv.org/abs/2406.13352) — 97 realistic tasks, 629 security tests across banking/email/travel; the go-to agent security benchmark. ([site](https://agentdojo.spylab.ai/))
- 📄 [InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated LLM Agents](https://arxiv.org/abs/2403.02691) — ACL 2024. 1,054 cases across 17 tools; 24% attack success on GPT-4.
- 📄 [Agent Security Bench (ASB)](https://arxiv.org/abs/2410.02644) — ICLR 2025. 27 attack/defense types over 13 LLMs; attack success exceeds 84% in places.
- 📄 [OpenAgentSafety](https://arxiv.org/abs/2507.06134) — 350+ multi-turn tasks with real tools; unsafe behavior in 51–73% of vulnerable tasks.

### Memory, RAG & Supply-Chain Poisoning

- 📄 [Memory Poisoning Attack and Defense on Memory-Based LLM Agents](https://arxiv.org/abs/2601.05504) — The MINJA attack poisons agent memory at 70% overall success.
- 📄 [Practical Poisoning Attacks against Retrieval-Augmented Generation](https://arxiv.org/abs/2504.03957) — CorruptRAG poisons a corpus with a *single* injected text.
- 📄 [Supply-Chain Poisoning Attacks Against LLM Coding Agent Skill Ecosystems](https://arxiv.org/abs/2604.03081) — Malicious logic hidden in "skill" documentation; confirmed vendor vulnerabilities.

---

## MCP (Model Context Protocol) Security

MCP lets agents connect to external tools and data. Every tool description and tool response is untrusted input the model reads — a huge, fresh attack surface. **This is the frontier this list cares about most.**

### Specifications & Standards

- ⭐ [Model Context Protocol Specification](https://modelcontextprotocol.io/specification/2025-11-25) — The authoritative spec, including its security and trust & safety principles.
- [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/) — The ten most critical MCP risks, updated with real-world threats and CVEs.
- [MCP Security Cheat Sheet (OWASP)](https://cheatsheetseries.owasp.org/cheatsheets/MCP_Security_Cheat_Sheet.html) — Quick-reference best practices and common vulnerability patterns.
- [MCP Security Initiative (Cloud Security Alliance)](https://modelcontextprotocol-security.io/) — Community hardening frameworks and guidance for MCP servers/agents.

### Attacks & Research

- ⭐ [MCP has prompt injection security problems](https://simonwillison.net/2025/Apr/9/mcp-prompt-injection/) — Simon Willison on GitHub-MCP injection that exfiltrates private-repo data via a malicious issue. The clearest intro to the risk.
- 📄 [MCPTox: A Benchmark for Tool Poisoning Attacks on Real-World MCP Servers](https://arxiv.org/abs/2508.14925) — 1,312 tests over 45 real servers; more capable models are *more* vulnerable (o1-mini: 72.8% success).
- [MCP Rug Pull: Tool Definitions That Change After Approval](https://policylayer.com/attacks/mcp-rug-pull) — A trusted tool silently mutates its definition post-approval to hijack the agent.
- [Invisible Backdoors in MCP](https://www.netskope.com/blog/securing-llm-superpowers-the-invisible-backdoors-in-mcp) — Netskope on attacks hidden in tool descriptions and unvalidated server responses.
- [MCP Tools: Attack Vectors and Defenses (Elastic Security Labs)](https://www.elastic.co/security-labs/mcp-tools-attack-defense-recommendations) — Tool shadowing, context poisoning, and concrete defenses.

### Tools & Scanners

- 🛠️ [mcp-scan (Invariant Labs)](https://invariantlabs.ai/blog/introducing-mcp-scan) — Scans for tool poisoning and rug pulls with tool pinning and cross-origin escalation detection.
- 🛠️ [Agent Scan (Snyk Labs)](https://github.com/snyk/agent-scan) — Detects 15+ MCP risks: injection, tool poisoning, shadowing, toxic data flows.
- 🛠️ [MCP-Security-Checklist (SlowMist)](https://github.com/slowmist/MCP-Security-Checklist) — Comprehensive auth, authorization, and credential-management checklist.
- [A Security Engineer's Guide to MCP (Semgrep)](https://semgrep.dev/blog/2025/a-security-engineers-guide-to-mcp/) — Tactical guide to auth, token management, and scope isolation.

### Disclosed Vulnerabilities

- [CVE-2025-6514 — Critical RCE in mcp-remote](https://jfrog.com/blog/2025-6514-critical-mcp-remote-rce-vulnerability/) — CVSS 9.6 OS command execution affecting 437,000+ installs.
- [MCP Supply-Chain Advisory: RCE Across the AI Ecosystem (OX Security)](https://www.ox.security/blog/mcp-supply-chain-advisory-rce-vulnerabilities-across-the-ai-ecosystem/) — Command injection touching 7,000+ public servers.

---

## Defensive Tools & Guardrails

### Red-Teaming & Vulnerability Scanners

- ⭐🛠️ [garak (NVIDIA)](https://github.com/NVIDIA/garak) — The `nmap` of LLMs: 100+ probes for injection, jailbreaks, and data leakage. Apache-2.0.
- ⭐🛠️ [PyRIT (Microsoft)](https://github.com/microsoft/PyRIT) — Python Risk Identification Tool for automated, systematic LLM red-teaming. MIT.
- 🛠️ [promptfoo](https://github.com/promptfoo/promptfoo) — LLM testing & red-teaming with 50+ vuln checks and first-class CI/CD integration. MIT.
- 🛠️ [promptmap](https://github.com/utkusen/promptmap) — Automated injection scanner for your own LLM apps; white-box and black-box modes.
- 🛠️ [Giskard](https://github.com/Giskard-AI/giskard-oss) — Testing framework detecting injection, hallucination, bias, and toxicity. Apache-2.0.
- 🛠️ [DeepTeam](https://github.com/confident-ai/deepteam) — Red-teaming framework simulating jailbreaks and multi-turn attacks across 40+ probes.

### Runtime Guardrails & Firewalls

- ⭐🛠️ [LLM Guard (Protect AI)](https://github.com/protectai/llm-guard) — 15 input + 20 output scanners: injection, PII, secrets, toxicity. MIT.
- 🛠️ [NeMo Guardrails (NVIDIA)](https://github.com/NVIDIA-NeMo/Guardrails) — Programmable input/dialog/retrieval/output rails for conversational LLMs. Apache-2.0.
- 🛠️ [Guardrails AI](https://github.com/guardrails-ai/guardrails) — Input/output guards with 100+ validators and structured generation. Apache-2.0.
- 🛠️ [Vigil-LLM](https://github.com/deadbits/vigil-llm) — Injection/jailbreak detector using vector DB, YARA signatures, and canary tokens.

### Detectors & Classifiers

- 🛠️ [Meta Prompt Guard](https://huggingface.co/meta-llama/Prompt-Guard-86M) — 86M BERT-based classifier for jailbreaks and injections (part of Purple Llama).
- 🛠️ [Llama Guard (Purple Llama)](https://github.com/meta-llama/PurpleLlama) — Meta's input/output content-moderation safeguard models.
- 🛠️ [deepset Injection Detector](https://huggingface.co/deepset/deberta-v3-base-injection) — Fine-tuned DeBERTa model for prompt-injection detection.

### PII / Secrets & Sandboxing

- 🛠️ [Presidio (Microsoft)](https://github.com/microsoft/presidio) — Detect and redact PII across text, images, and structured data. Apache-2.0.
- 🛠️ [E2B](https://github.com/e2b-dev/e2b) — Open-source cloud sandbox for safely executing LLM-generated code. Apache-2.0.

### Commercial Platforms

- 💼 [Lakera Guard](https://www.lakera.ai/) — Real-time LLM security API (injection, jailbreaks, leakage); acquired by Check Point.
- 💼 [HiddenLayer](https://www.hiddenlayer.com/) — Enterprise AI security: supply-chain scanning, runtime defense, red-teaming.
- 💼 [Protect AI](https://protectai.com/) — LLM Guard's maintainer; enterprise ML/LLM security platform (now part of Palo Alto Networks).

---

## Hands-on Learning, CTFs & Games

The fastest way to understand these attacks is to *do* them.

### Games & Interactive Challenges

- ⭐🎮 [Gandalf (Lakera)](https://gandalf.lakera.ai/) — The classic. Seven levels of tricking an AI into leaking a password. Start here.
- 🎮 [Tensor Trust](https://tensortrust.ai/) — Multiplayer attack/defense game; build a defense, break into others'. Generated a 126k-prompt research dataset.
- 🎮 [GPT Prompt Attack](https://gpa.43z.one/) — 21 levels; craft the *shortest* payload that makes the model reveal its secret.
- 🎮 [Immersive Labs Prompt Injection Challenge](https://prompting.ai.immersivelabs.com/) — Multi-level, safe environment teaching injection defenses.
- 🎮 [LLMail-Inject (Microsoft)](https://microsoft.github.io/llmail-inject/) — 40 levels simulating an email-assistant agent; requires end-to-end attack chains.

### Vulnerable Apps to Practice On

- 🛠️🎮 [Damn Vulnerable AI Agent](https://github.com/opena2a-org/damn-vulnerable-ai-agent) — Docker-based deliberately-vulnerable agent for injection, jailbreak, and capability-abuse practice.
- 🎮 [HackTheBox — AI Prompt Injection Essentials](https://ctf.hackthebox.com/pack/ai-prompt-injection-essentials) — Ten challenges mapped to the OWASP LLM Top 10.

### Competitions

- 🎮 [HackAPrompt 2.0](https://www.hackaprompt.com/) — Global red-teaming hackathon with large prizes. ([📄 original paper, 600k+ prompts](https://arxiv.org/abs/2311.16119))

### Courses & Reference

- [Learn Prompting — Prompt Hacking](https://learnprompting.org/) — Free course covering prompt hacking and defensive techniques.
- 🛠️ [LLM Hacker's Handbook (Forces Unseen)](https://github.com/forcesunseen/llm-hackers-handbook) — Concise empirical guide to offensive and defensive LLM hacking.
- [Antisyphon — Attacking, Defending, and Leveraging AI/LLM Systems](https://www.antisyphontraining.com/product/attacking-defending-and-leveraging-ai-llm-systems/) — 16-hour hands-on training with attack/defense labs.

---

## Real-World Incidents

Prompt injection is not theoretical. These are documented, real compromises.

- ⭐ [Bing Chat "Sydney" system-prompt leak](https://www.topaithreats.com/incidents/INC-23-0016-bing-chat-sydney-system-prompt-leak/) — The one that put prompt injection on the map (Feb 2023).
- [ChatGPT plugin indirect injection & cross-plugin request forgery](https://embracethered.com/blog/posts/2023/chatgpt-cross-plugin-request-forgery-and-prompt-injection./) — End-to-end PII exfiltration via a confused-deputy plugin chain.
- ⭐ [GitHub Copilot CamoLeak](https://embracethered.com/blog/posts/2024/github-copilot-chat-prompt-injection-data-exfiltration/) — Zero-click injection via PR descriptions exfiltrating AWS secrets.
- [EchoLeak — Microsoft 365 Copilot zero-click (CVE-2025-32711)](https://arxiv.org/html/2509.10540v1) — CVSS 9.3 zero-click exfiltration of OneDrive/Teams/SharePoint data via a crafted email.
- [Slack AI data exfiltration via indirect injection](https://www.promptarmor.com/resources/data-exfiltration-from-slack-ai-via-indirect-prompt-injection) — Malicious instructions in a public channel leak private API keys.
- [Cursor IDE injection & RCE (CVE-2025-54135/59944)](https://www.lakera.ai/blog/cursor-vulnerability-cve-2025-59944) — RCE via malicious repo prompts and MCP server impersonation.
- [Claude.ai data exfiltration ("Claudy Day")](https://www.darkreading.com/vulnerabilities-threats/claudy-day-trio-flaws-expose-claude-users-to-data-theft) — A three-flaw chain exfiltrating conversation history with no user interaction.

---

## People, Blogs & Newsletters

The practitioners worth following.

- ⭐ [Simon Willison](https://simonwillison.net/tags/prompt-injection/) — Coined "prompt injection"; the single best ongoing commentary on agent security and the "lethal trifecta."
- ⭐ [Johann Rehberger — Embrace The Red](https://embracethered.com/blog/) — Deep, reproducible technical writeups; discovered many of the incidents above.
- [Kai Greshake](https://kai-greshake.de/posts/llm-malware/) — Pioneer of indirect prompt injection research.
- [Joseph Thacker (rez0)](https://josephthacker.com/) — AI security research, the PIPE primer, and real CVE disclosures.
- [Pliny the Liberator](https://pliny.gg/) — Prolific jailbreak researcher; the L1B3RT4S repository.
- [Rich Harang (NVIDIA)](https://developer.nvidia.com/blog/securing-llm-systems-against-prompt-injection/) — "Securing LLM Systems Against Prompt Injection."
- [Lakera Blog](https://www.lakera.ai/blog) — Adversarial ML research and CVE analysis.
- [PromptArmor](https://www.promptarmor.com/resources) — Dedicated LLM-security vulnerability research (Slack AI, Writer.com).

### Organizations & Frameworks

- ⭐ [OWASP GenAI Security Project](https://genai.owasp.org/) — Home of the OWASP Top 10 for LLM and Agentic apps; 600+ contributors.
- [MITRE ATLAS](https://atlas.mitre.org/) — The adversarial-ML threat framework: 16 tactics, 84 techniques.
- [AI Village (DEF CON)](https://aivillage.org/) — Hands-on AI red-teaming community, CTFs, and training.
- [OECD.AI Incidents Database](https://oecd.ai/en/incidents) — Real-world AI incident tracking.

---

## Related Lists

Great neighbors — narrower or adjacent in scope. This list focuses on the **agentic + MCP** frontier and the exploit→defend picture across all of them.

- [awesome-prompt-injection (FonduAI)](https://github.com/FonduAI/awesome-prompt-injection) — Focused specifically on prompt-injection resources.
- [awesome-ai-red-teaming (shlomihod)](https://github.com/shlomihod/awesome-ai-red-teaming) — AI red-teaming methods and tooling.
- [prompt-injection-defenses (tldrsec)](https://github.com/tldrsec/prompt-injection-defenses) — A deep catalog of defensive techniques and papers.

---

## Contributing

Contributions are very welcome — see [CONTRIBUTING.md](CONTRIBUTING.md). The bar: real, live, on-topic, non-duplicate, and notable. Self-suggestions are fine if the resource is genuinely good.

## License

[![CC0](https://licensebuttons.net/p/zero/1.0/88x31.png)](LICENSE) — To the extent possible under law, contributors have waived all copyright and related rights to this work.
