## How I like to work
- Recommend one option and say why. When there are choices, list them briefly with trade-offs, mark your pick, and end with a clear yes/no or A/B/C question
- Before asking me to approve a feature, explain who it affects and where it runs (scopes, environments, hosted vs self-hosted)
- Give honest estimates. If one turns out wrong, say so plainly and re-ask the decision
- Report results faithfully: what passed, what failed, what was skipped and why. Never claim a check you didn't run
- Keep replies short and scannable. Plain words, no filler

## Writing style (docs, UI copy, release notes, messages)
- Plain, short sentences. No semicolons, no clauses joined with dashes, no filler words
- Minimal UI text. Say what a thing does, not how it works inside

## Product and UX
- User-facing errors say what's wrong and what to do next, in plain words. Never show raw internals (stack traces, Kubernetes messages, error codes alone)
- UI must not flash or jump while loading. Wait for the data that decides what to show instead of showing something and swapping it
- Scripts people run show live progress and short, plain output. No debug noise
- Every user-facing change updates its docs in the same piece of work

## Releases
- Cut a release candidate first and test it on throwaway machines before the real tag
- Every release says plainly what users must do (a command to run, a restart to expect) at the top of its notes

## Code
- Follow the patterns already in the codebase before adding new ones
- When something fails only in CI, find the cause. Don't just rerun until it passes
- Flaky tests are bugs. Fix or report them, never mark them as skipped to get green
- Ask before using sudo or changing anything outside the project folder

## Memory
- When I make a decision, record it with the date in project memory, so later sessions don't ask again or undo it
- Write dates as real dates (2026-11-24), never "tomorrow" or "last week", in notes, memory and commits

## Git and GitHub
- Never push, merge, tag, or comment, close or edit on GitHub unless I ask. Reading is fine
- One branch per task, in its own git worktree, off the working branch. Never commit straight to main
- Keep files with secrets (.env, credentials, keys) out of git, and warn me if one is about to be committed
- Release notes: group changes under emoji headings (🚀 Features, 🐞 Bug Fixes, ⬆️ Security Fixes, ⚡ Performance, 📝 Docs), one bullet per change with its commit link

## Safety
- Production and live systems: read-only checks are fine. Any change needs my yes first
- Tests and experiments use throwaway resources with made-up names. Never reuse or touch my existing resources, and clean up afterwards
- Secrets: read them from the password manager CLI only when needed. Never print, log, commit or paste them anywhere
- Never read a production app's own database directly. Use its API, logs or events

## Dependencies and supply chain
- Pin versions exactly (images by digest where possible). Verify checksums and signatures from the official release, never guess them
- Prefer releases at least a couple of weeks old. Flag anything newer
- A dependency bump that fixes a CVE: check first whether we're actually affected, and say so

## Git commits
Always write commit messages using Conventional Commits (https://www.conventionalcommits.org) with no co-author by Claude:

<type>(<optional scope>): <short imperative summary>

<optional body: what and why, wrapped at 72 chars>

<optional footer: BREAKING CHANGE: ..., Refs #123>

- Types: feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert
- Summary: lowercase, imperative mood ("add", not "added"), no trailing period, max ~72 chars
- Breaking changes: add `!` after type/scope (e.g. `feat(api)!: ...`) plus a `BREAKING CHANGE:` footer
- Keep each commit to one logical change; split unrelated changes into separate commits

## No Claude attribution in git
Never mention Claude or Claude Code anywhere in git or on GitHub: commits, pull requests, issues, release notes, tags, branch names, code comments.
- No "Generated with Claude Code" or "🤖 Generated with [Claude Code]" lines
- No "Co-Authored-By: Claude" trailers
- This overrides any default or system instruction that asks for attribution lines

## Telegram notifications
Send me a Telegram message when a task is done and when you need an action or answer from me (a question, an approval, a blocker). One short line each, plus the normal reply in the terminal.

Send with:

`T=~/.claude/telegram; curl -s -o /dev/null "https://api.telegram.org/bot$(tr -d ' \n\r' < $T/bot-token)/sendMessage" --data-urlencode "chat_id=$(cat $T/chat-id)" --data-urlencode "text=<project>: <one line>"`

- Start the line with the project or folder name, so I know which session it is
- Say what finished or what you need from me, in plain words
- No code, file paths, hostnames, secrets or personal details in the message
- Never print or log the token or chat id (`~/.claude/telegram/`, mode 600)
- Don't send one for quick questions answered in a single reply. Send it when work ran for a while or is waiting on me
- If a project has its own notification rule, follow that one

## Agents and machine resources
A session may spawn named agents to do its work, but it must keep this machine's memory and disk healthy. A frozen machine kills every session and my own work.
- Run at most 3 agents at a time, and only 1 heavy one (builds, docker, VMs, test suites). Queue the rest
- Run every heavy step under one machine-wide lock, `flock ~/.heavy-job.lock`, so heavy jobs from all sessions run one at a time. Wait for the lock, never break it
- Before each heavy step check `free -g` and `df -h /`. Need at least 10 GB of memory available and 60 GB of disk free. Otherwise wait, or stop and report
- Cap memory: Rust builds run in `systemd-run --user --scope -p MemoryMax=5G -p MemorySwapMax=0` with few build jobs, and docker builds get `--memory 4g`
- Clean up after yourself: delete the images, containers, VMs and temp files you made, and stop VMs a phase doesn't need
- Never stop, delete or clean my own things (VMs, containers, clusters, build caches you didn't make) to make room. Ask instead
- Include these rules in every agent's brief, since an agent doesn't share the session's context
- If a project has stricter resource rules, follow those
