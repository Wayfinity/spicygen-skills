# SpicyGen agent skills

Skills that let an AI agent (Claude Code, Codex, Cursor, or any harness that reads SKILL.md files) create characters, generate images and videos, build movies and manage credits on SpicyGen (an AI adult image, character and movie generator, adults only) through the SpicyGen MCP server or REST API. Generated from the live tool catalog; regenerate with `npm run skills:build` in the product repository.

| Skill | Use it when |
|---|---|
| [create-a-character-and-generate-a-set](./skills/create-a-character-and-generate-a-set/SKILL.md) | a user asks for an AI character, a consistent AI model or persona, AI adult images of the same person, or an NSFW AI image generator |
| [generate-coins](./skills/generate-coins/SKILL.md) | a user asks for AI coin art, collectible token designs, a matching front-and-back coin set, or minted coin-style imagery |
| [turn-an-image-into-a-video](./skills/turn-an-image-into-a-video/SKILL.md) | a user asks to turn an image into a video, animate a picture, make an NSFW AI video, or generate an AI adult video clip |
| [build-a-movie-from-scenes](./skills/build-a-movie-from-scenes/SKILL.md) | a user asks for a longer AI video, a storyboard, a multi-scene clip, or an adult AI movie |
| [manage-credits-and-spend](./skills/manage-credits-and-spend/SKILL.md) | a user asks how much SpicyGen costs, to top up credits, buy a pack, check their balance, or tidy their library |

Install for Claude Code: copy a skill folder into `.claude/skills/` (project) or `~/.claude/skills/` (user). Other harnesses: point them at the folder or at the raw URL `https://www.spicygen.ai/skills/<name>/SKILL.md`.

MCP endpoint: `https://www.spicygen.ai/api/mcp` (OAuth, or header `Authorization: Bearer sg_live_…` with a key from https://www.spicygen.ai/account#api-keys; sandbox keys spend nothing). Docs: https://www.spicygen.ai/docs/agents. Product overview for agents: https://www.spicygen.ai/llms.txt.

Adults only. Every prompt is moderated: fictional consenting adults, no real people, no minors. Full rules: https://www.spicygen.ai/ai-policy.
