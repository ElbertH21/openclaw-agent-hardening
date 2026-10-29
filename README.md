# Defensive Security Controls for OpenClaw AI Agents

## Project Domain
AI Agent Security (Option 3)

## Team Members
* Elbert Henriquez: Technical Implementation and Validation Lead
* Esteban Calderon: Documentation and Presentation Co-Lead
* Gavin Barton: Project Lead
* Opjeet Dhanoa: Risk/Threat Analyst
* Berenice Moreno-Perez: Documentation and Presentation Co-Lead


## My role (Elbert Henriquez)
*Fork of the team repository [gavinbarton7/8-Bit-Security](https://github.com/gavinbarton7/8-Bit-Security). Team project for CS361 Introduction to Cybersecurity, UMKC, April–May 2026.*

As the Technical Implementation and Validation Lead, I built and tested the Week 4 security controls on the team's OpenClaw setup (Raspberry Pi 5, Discord connector, local and cloud LLMs). All of these files are in [`evidence/week4/`](evidence/week4/) and come from my two commits.

**Day 1: isolation and attack surface**
- Turned on Docker sandboxing for tool calls, blocked web/browser tools (`tools.deny`), and limited file access to the workspace (`workspaceOnly`). → [`config-controls-diff.txt`](evidence/week4/config-controls-diff.txt)
- Cut the ready skills from 6 to 1 with an allowlist. → [`skills-diff.txt`](evidence/week4/skills-diff.txt)
- Security audit: 1 critical finding before, 0 after. → [`audit-diff-day1.txt`](evidence/week4/audit-diff-day1.txt)

**Day 2: approvals, filtering, hardening, integrity**
- Human-in-the-loop approval for commands, with a 4-program allowlist. → [`approvals-after-hitl.txt`](evidence/week4/approvals-after-hitl.txt)
- Wrote a prompt-injection filter hook that checks 7 pattern categories and logs hits as JSONL. → [`prompt-injection-filter-handler.js`](evidence/week4/prompt-injection-filter-handler.js)
- Hardened the systemd service (`ProtectSystem=strict`, `NoNewPrivileges`, limited write paths). → [`systemd-hardening.conf`](evidence/week4/systemd-hardening.conf)
- SHA-256 integrity monitoring of 14 key files every 15 minutes, with a drift test. → [`integrity-check.sh`](evidence/week4/integrity-check.sh), [`integrity-drift-demo.jsonl`](evidence/week4/integrity-drift-demo.jsonl)

**Final test: 4 attacks, all blocked**
I attacked the agent 4 ways to steal a fake credentials file: a direct read, a social-engineering request, a prompt-injection message, and shell exfiltration. All 4 were blocked, and the file's hash did not change. → [`capstone-discord-transcript.txt`](evidence/week4/capstone-discord-transcript.txt), [`target-file-hash-after-attack.txt`](evidence/week4/target-file-hash-after-attack.txt)

**Two gaps I found and reported**
- The social-engineering message was refused by the model, but my filter did not catch it, because the wording did not match any filter pattern.
- The shell exfiltration was stopped by the sandbox and allowlist before it reached the model or the filter, so it left **no log entry**. The attack was blocked, but nothing recorded it.

## Project Description
This project evaluates the security posture of OpenClaw, an open-source AI agent framework that connects LLMs to real-world tools, file systems, and messaging platforms. Acting as a defensive security team for an organization considering OpenClaw for internal deployment, we audit the agent's default configuration, identify security gaps, and implement defensive controls to reduce risk.
The focus areas include least-privilege tool permissions, skill vetting and sandboxing, structured audit logging, input validation against prompt injection, and human-in-the-loop gates for sensitive actions.

## Tools Used
OpenClaw — Open-source AI agent framework (subject of analysis)
Google AI Studio (Gemini 2.5 Flash) — Primary LLM backend (free tier)
Ollama — Local LLM backend for testing data exfiltration controls
Docker — Agent isolation and lab environment
Git/GitHub — Version control and project delivery
