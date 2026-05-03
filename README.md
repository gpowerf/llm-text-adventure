# 8-Bit Island

*A tiny text adventure from the Commodore 64 era, now running entirely inside an LLM chat.*

---

## What is this?

8-Bit Island is a classic parser-based adventure game I originally wrote in BASIC for the Commodore 64. This repo contains the entire game converted into a format that a large language model (LLM) can “run” — as a deterministic piece of software.

No Python, no interpreter, no custom engine. Just two Markdown files and a prompt. If you paste them into a chat like LeChat, ChatGPT, or Claude (with the right settings), the AI becomes the game engine. It reads room descriptions, tracks your inventory, updates your health, and moves you through the world — one user command at a time.

## Why?

LLMs are non-deterministic and stateless. Their “memory” is only the conversation so far. After a few dozen turns, they’ll forget you picked up the rusty key, or that the drawbridge is already down. Even worse, most chat interfaces trim the oldest messages when the context window fills up, deleting early game history.

That’s a problem for a piece of software that must remember every single variable at all times.

The experiment: **force the LLM to output the entire game state after every single move, as a compact line at the end of its response.** That line lives in the most recent message, so context trimming never touches it. The state is always fresh, always complete, always exactly where the model looks first.

Does it work? Yes — surprisingly well.

## How it works

Every time the LLM responds to a player command (e.g., `GO NORTH`, `GET LAMP`), it must end its output with:

```
[STATE: room=guard_room; inv=key; health=9; flags=dark]
```

The state line encodes:
- `room` — the player’s current location
- `inv` — inventory items (comma-separated, empty if none)
- `health` — hit points
- `flags` — any permanent world changes (door unlocked, bridge lowered, etc.)

The prompt (in `game_rules.md`) instructs the LLM:
1. Read the current state from the last `[STATE:` line in the conversation.
2. Process the player’s command against the game’s map, object logic, and script (defined in `game_data.md`).
3. Generate the appropriate narrative response.
4. Append the **new** state line, reflecting all changes.

Because the state line is always near the end of the conversation, even if the chat interface drops the first 20 messages, the latest state survives. The game continues with zero memory loss.

## Playing the game

1. **Open an LLM chat** that supports long contexts. (Tested with Mistral’s LeChat; should work with GPT-4/Claude, preferably with temperature set to 0 for best determinism.)
2. **Upload or paste** the two Markdown files from this repo into the chat:
   - `game_rules.md` — instructions that teach the AI how to run a text adventure and use the state line.
   - `game_data.md` — the full map, object, and story data of 8-Bit Island.
3. **Type commands** like `LOOK`, `INVENTORY`, `GO EAST`, `TAKE SWORD`, `EAT BANANA`.  
   The LLM will act as the parser and game engine.

## What’s in the repo

| File | Purpose |
|------|---------|
| `game_rules.md` | Detailed system prompt: explains the role of the LLM, the state-line mechanism, game loop logic, and formatting rules. |
| `game_data.md` | Complete definition of every room, connection, object, variable, flag, and scripted event from the original C64 game. |
| `README.md` | You’re reading it. |

## Limitations & quirks

It is a text adventure running in an online AI chat! This is a bad idea and I know it!

## But … why?

Because it’s fun. Because it’s ridiculous. Because we can.

And because it's a neat demonstration that with a bit of prompt engineering, you can embed a fully functional, deterministic machine inside a probabilistic language model. No code execution environment. Just words.

## Original game

8-Bit Island was written by me in Commodore 64 BASIC. 

## License

MIT — do whatever you like. If you port it to run inside a toaster, let me know.

---

*“You are standing in a dusty guard room. Moonlight slants through a barred window. Exits are: NORTH, EAST.”*
