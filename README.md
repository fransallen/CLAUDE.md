# CLAUDE.md

My global instructions for [Claude Code](https://claude.com/claude-code). It reads `~/.claude/CLAUDE.md` in every session, in every project.

## What's in it

- **How I like to work:** clear recommendations, honest estimates, faithful reports
- **Writing style:** plain, short sentences
- **Product and UX:** plain errors, no flashing UI, quiet scripts, docs with every change
- **Releases:** release candidates first, clear "what you must do" notes
- **Code:** follow existing patterns, find the cause of CI failures, ask before sudo
- **Memory:** record decisions with dates
- **Git and GitHub:** Conventional Commits, one worktree per task, no pushing without asking, no AI attribution
- **Safety:** read-only on live systems, throwaway test resources, secrets never printed
- **Dependencies:** exact pins, verified checksums, CVE checks
- **Telegram:** a short message when a task is done or needs me
- **Agents and machine resources:** spawn agents, but keep memory and disk healthy

## Use it

Copy `CLAUDE.md` to `~/.claude/CLAUDE.md`, then change what doesn't fit you.

For Telegram, put your bot token in `~/.claude/telegram/bot-token` and your chat id in `~/.claude/telegram/chat-id`, both with mode 600. Or delete that section.

A project's own `CLAUDE.md` adds to these rules. Where they conflict, the project's rule wins.

## License

MIT
