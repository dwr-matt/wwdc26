# WWDC26 Sessions — Learning Guide

This repo contains transcripts and summaries for all WWDC 2026 sessions.

## Directory Structure

Each session folder contains:
- `README.md` — structured summary of the session
- `transcript.json` — full word-by-word transcript with timestamps
- `CLAUDE.md` — (if present) personal study notes from prior sessions

## Learning Workflow

When the user asks about a topic from a specific session:

1. **Read `README.md`** in that session's folder to get the structured overview
2. **Read `transcript.json`** to get the full detail and exact wording from the speakers
3. **Fetch any Apple documentation URLs** listed in the README's Resources section using WebFetch — these contain the official API reference and code examples
4. **Explain in Traditional Chinese (繁體中文)** with clear structure: what it is, why it exists, how to use it, code examples, and key caveats
5. **After explaining**, save an English summary to that session's `CLAUDE.md` for future reference

## Notes

- Always combine README + transcript + official docs for the most complete explanation
- If a `CLAUDE.md` already exists in the session folder, read it first — it contains prior study notes
- The root `README.md` has a categorized index of all 111 sessions with links
