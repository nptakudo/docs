---
title: "Cortex Code CLI keyboard shortcuts"
url: "https://docs.snowflake.com/en/user-guide/cortex-code/keyboard-shortcuts"
---

# Cortex Code CLI keyboard shortcuts

Master Cortex Code CLI with these keyboard shortcuts for efficient navigation.

## Input shortcuts

| Shortcut | Action |
| --- | --- |
| Enter | Submit message |
| Ctrl-J | Insert newline (multiline input) |
| Ctrl/Cmd-V | Paste from clipboard |
| Ctrl-C | Cancel/interrupt (double-tap to exit) |
| Esc | Dismiss suggestions (double-tap to clear input) |
| Ctrl-K | Kill line (clear input) |
| Ctrl-Y | Yank (paste killed text) |
| Ctrl-A | Move to line start |
| Ctrl-E | Move to line end |

See moreShow less

Expand

## View shortcuts

| Shortcut | Action |
| --- | --- |
| Ctrl-T | Open table viewer (cycle forward) |
| Ctrl-Shift-T | Cycle table viewer backward |
| Ctrl-P | Toggle compact/expanded mode |
| Ctrl-O | Open full transcript viewer |
| Ctrl-B | View background bash processes |
| Ctrl-D | Open todo/task viewer |
| Ctrl-G | Open web search results |
| ? | Toggle help overlay |

See moreShow less

Expand

## Mode Shortcuts

| Shortcut | Action |
| --- | --- |
| Shift-Tab | Cycle operational modes |

See moreShow less

Expand

Modes cycle in this order:

* Confirm actions
* Plan mode
* Bypass safeguards

## History Navigation

| Shortcut | Action |
| --- | --- |
| Ctrl-R | Reverse search history (Emacs-style) |
| Ctrl-S | Forward search history |
| Ctrl-G | Cancel history search |
| Up / Down | Navigate history (when at top/bottom of input) |
| Option-Up | Previous history entry |
| Option-Down | Next history entry |

See moreShow less

Expand

### Table viewer shortcuts

Additional shortcuts for the table viewer (Ctrl-T):

| Shortcut | Action |
| --- | --- |
| Tab | Cycle to next table |
| Shift-Tab | Cycle to previous table |
| c | Copy query to clipboard |

See moreShow less

Expand

## Auto-complete triggers

Typing one of the trigger characters below begins auto-completion for commands, file paths, skills, or Snowflake tables.

| Trigger | Action |
| --- | --- |
| / | Slash command completion |
| @ | File path completion |
| $ | Skill completion |
| # | Snowflake table completion |
| Tab | Accept suggestion |
| Up / Down | Navigate suggestions |

See moreShow less

Expand

## Display modes

Press Ctrl-P to toggle between Compact and Expanded display modes for tool execution details.

Compact mode (Default)
:   * Minimal tool execution display
    * Shows summary of operations

Expanded mode
:   * Full tool execution details
    * Complete input/output display

## Quick reference card

CopyExpand

```
┌──────────────────────────────────────────────────────────┐
│                  CORTEX CODE SHORTCUTS                   │
├──────────────────────────────────────────────────────────┤
│  INPUT                  │  VIEW                          │
│  Enter      Submit      │  Ctrl-T    Table viewer        │
│  Ctrl-J     Newline     │  Ctrl-P    Toggle compact      │
│  Ctrl-C     Cancel      │  Ctrl-O    Transcript          │
│  Ctrl-R     History     │  Ctrl-B    Background bash     │
│  Esc Esc    Clear       │  Ctrl-D    Todo viewer         │
│                         │  ?         Help overlay        │
├─────────────────────────┼────────────────────────────────┤
│  NAVIGATION             │  AUTOCOMPLETE                  │
│  Up/k       Up          │  /         Commands            │
│  Down/j     Down        │  @         Files               │
│  g          Top         │  $         Skills              │
│  G          Bottom      │  #         Tables              │
│  q/Esc      Exit        │  Tab       Accept              │
├─────────────────────────┴────────────────────────────────┤
│  Shift-Tab  Cycle modes (Confirm → Plan → Bypass)        │
└──────────────────────────────────────────────────────────┘
```

Show lessSee more

Scroll to top

## Tips

* **Use Ctrl-P often**: Switch between compact and expanded mode based on what you need to see.
* **Master Ctrl-R**: History search is powerful for repeating complex prompts.
* **Vim-style navigation**: The h/j/k/l keys work everywhere for cursor movement.
* **Double-tap patterns**: For example, Esc Esc clears input, Ctrl-C Ctrl-C exits.
