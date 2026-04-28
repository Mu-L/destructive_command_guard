# Codex Integration

Last updated: 2026-04-28

Status: stub. Phase 5 follow-ups will expand troubleshooting and CI guidance as
the real Codex e2e harness lands.

## How dcg Disambiguates Codex From Claude Code

Codex CLI 0.125.0+ sends a `turn_id` field in hook stdin. Claude Code does not.
dcg uses that field in `src/hook.rs:detect_protocol` to select the Codex hook
path only for shell-command tools.

## Why Exit 2 And Stderr Instead Of JSON Deny

Codex's hook output parser is strict about unknown JSON fields, while dcg's
Claude-compatible deny payload includes fields such as `hookSpecificOutput`.
For Codex, `src/hook.rs:output_denial_for_protocol` writes no stdout JSON,
prints the block reason to stderr, and the deny branch in `src/main.rs` exits
with code 2 so the command is rejected.

## Troubleshooting "PreToolUse Failed"

Detailed troubleshooting will be filled in after the Phase 3 real-Codex e2e
tests produce failure artifacts. Initial checks:

- Confirm Codex is 0.125.0 or newer.
- Confirm `~/.codex/hooks.json` points at the intended `dcg` binary.
- Run a known destructive command in a throwaway repository and check for exit
  code 2 with a non-empty stderr reason.
- If stdout contains a Claude-style JSON deny payload, protocol detection did
  not classify the hook input as Codex.

## Reference Layout

- Protocol detection: `src/hook.rs:detect_protocol`
- Deny output dispatch: `src/hook.rs:output_denial_for_protocol`
- Codex deny exit: `src/main.rs` deny branch
- Install: `install.sh:configure_codex`
- Uninstall: `uninstall.sh:unconfigure_codex`
- Installer tests: `tests/install/agent_config_test.bats`
- Real-Codex harness: `scripts/e2e_codex.sh`
