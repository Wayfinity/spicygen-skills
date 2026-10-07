---
name: generate-coins
description: "Generate polished AI coin, token or medallion artwork on SpicyGen (an AI adult image generator, 18+) through the SpicyGen MCP server or REST API. Use when a user asks for coin art, collectible design, token concepts, or a matching front-and-back coin set."
tags: [coin-art, token-design, collectibles, image-generation, adult, mcp]
license: MIT
metadata:
  version: "1.1.0"
  homepage: https://www.spicygen.ai/skills/generate-coins/SKILL.md
  mcp: https://www.spicygen.ai/api/mcp
  openapi: https://www.spicygen.ai/openapi.json
  vendor: SpicyGen (Wayfinity Ltd)
  audience: adults-only
---

# Generate coins

## When to use

The user wants coin, token, medallion or collectible artwork: a front and back design, a concept set, a decorative coin render, or a matched pair of assets for a fictional adult brand, character or world.

## Golden path

1. `get_account`: confirm credits and age-verification state. If the balance is too low, `get_credit_packs` and hand the user the hosted `get_checkout_link` URL.
2. `read_content_rules`: adults only, fictional-only prompts, no real people, no minors, no non-consent or coercive themes.
3. Decide whether the prompt should use a saved character or a fresh scene. If a saved character matches, `list_characters` and reuse it; otherwise continue with a direct scene prompt.
4. `generate_images` (guarded) with a detailed prompt for the coin design, plus `count` and `ratio` and, if needed, a `character_id` from a matching character. Use separate prompts for the front and back if the user wants a set.
5. Poll `get_job` for each returned job ID until `completed`; present the URLs when ready, or explain `nsfw_withheld` and the verification link from `get_account` if region rules apply.
6. If the user wants more variations, run `generate_images` again with a different prompt or style. `favorite_item` can mark the best outcomes, and `list_library` lets the user review all generated pieces.

## What to tell the user up front

- Every image is moderated; a refused prompt costs nothing.
- Costs are per image and should be quoted before any charge is made.

## Tools used by this skill

- `get_account`: Start here. Credits balance, age-verification state for the caller's jurisdiction (whether NSFW media is available, and the verification URL when it is not), credit costs per image, per video second and per character, rate limits, and whether the account skips approvals. Arguments: no arguments.
- `read_content_rules`: The acceptable-use rules every prompt and image is checked against (adults only, fictional characters only, no real people, no minors, no non-consent, no violence). Read this before writing prompts; a blocked prompt costs nothing and is logged; serious or repeated abuse can get the account suspended. Arguments: no arguments.
- `list_characters`: Reuse a character if the user wants a consistent identity or style across a set. Arguments: no arguments.
- `generate_images` (guarded): Generate 1 to 10 images from a prompt (5 credits each Standard, 10 HD; a character reference forces HD). Returns job ids; poll `get_job`. Use a detailed coin prompt, aspect ratio, and style. Arguments: `prompt`: string (The requested coin or token design, up to 2000 characters.); `character_id`?: string (Generate this saved character on the coin or as the model reference.); `count`?: integer (Number of images, default 1.); `quality`?: Standard | HD; `ratio`?: 1:1 | 16:9 | 9:16 | 4:3 | 3:4; `style`?: Photorealistic | Studio | Hentai (Anime) | 3D | Cartoon; `approval_token`?: string (Approval token from a previous approval_required response, after the user said yes.).
- `get_job`: Status of a generation job (queued, generating, completed, failed), progress, and on completion the image URLs (withheld with `nsfw_withheld: true` when the age gate applies). Poll every 10 to 20 seconds. Arguments: `job_id`: string.
- `list_library`: The user's generated images and videos, newest first, with prompt, status, favourite flag and URLs. Paginate with `cursor`. Arguments: `type`?: images | videos | all (Default all.); `favorites_only`?: boolean; `limit`?: integer (Default 20.); `cursor`?: string.
- `favorite_item`: Mark or unmark a library item as a favourite. Arguments: `item_id`: string; `favorite`?: boolean (Default true; false removes the favourite.).
- `get_credit_packs`: Credit packs and prices available to this account. Arguments: no arguments.
- `get_checkout_link`: A hosted checkout URL for a credit pack. Hand the URL to the user; they pay in their browser. Arguments: `pack_id`: string.

## Setup (once per user)

1. The user signs up at https://www.spicygen.ai/signup (adults only; email confirmation is required before the first generation). Clients that support OAuth for remote MCP servers need only the URL `https://www.spicygen.ai/api/mcp`; otherwise create an API key at https://www.spicygen.ai/account#api-keys. Keep the key in an environment variable, never in chat.
2. Both surfaces expose the same tools:
   - MCP (Streamable HTTP): `https://www.spicygen.ai/api/mcp` with header `Authorization: ******`.
   - REST: `POST https://www.spicygen.ai/api/v1/tools/<tool_name>` with the same header and a JSON body of arguments. `GET https://www.spicygen.ai/api/v1/tools` lists tools with schemas; OpenAPI 3.1 at https://www.spicygen.ai/openapi.json.
3. Rate limit: 60 requests per minute per key. `get_account` reports the credit balance, the costs and the age-verification state.
4. Dry run first if you are building or testing an agent: a key created with the Sandbox box ticked answers every tool from fixture data, needs no credits and generates nothing. Every sandbox response carries `sandbox: true`; never present sandbox media to the user as real output.

## Rules that always apply

- Read `read_content_rules` before writing a prompt. Any depiction, implication or reference to minors in sexual situations, including "teen", school or age-play framing; age evasion is blocked. Real identifiable people: celebrities, public figures, private individuals, or uploaded photographs of real people. Deepfakes and impersonation. Non-consent, coercion, incapacitation or distress; verbal non-consent in prompts is blocked. A blocked prompt costs nothing and is logged; serious or repeated abuse can get the account suspended.
- Guarded tools (`create_character`, `generate_images`, `generate_video`, `create_movie_scene`, `combine_movie`, `delete_item`) first return `{error: "approval_required", summary, credits, approval_token}`. Show the summary and the credit cost to the user verbatim, get an explicit yes in the conversation, then call the tool again with the same arguments plus `approval_token` (15 minute expiry, bound to those exact arguments). Never approve in bulk or reuse a token for a different action. Accounts that run agents unattended can turn on skip approvals at https://www.spicygen.ai/account#api-keys; every moderation, credit and age-gate check still applies.
- Age verification: where the law requires it (United Kingdom, France, Germany, Italy, Australia, Brazil and 25 US states), NSFW media URLs are withheld (`nsfw_withheld: true`) until the user completes a one-time identity check. `get_account.age_verification` explains the state and gives the URL once it is available (after the first purchase). Explain this to the user; never try to work around it.
- Payment is a hosted checkout URL the human opens (`get_checkout_link`). Never ask for or enter card details, wallet keys or passwords.
- Everything is scoped to the account that owns the key; there is no way to read another account's data. Do not store or forward generated media outside the user's own tools.

## Costs and packs (USD, for when the user asks)

- Character: 10 credits (one identity image, reusable in every later generation).
- Image: 5 credits Standard, 10 credits HD (a character reference is always HD).
- Video, SpicyMotion v3.0: 10 credits per second Standard, 15 HD, 3 to 15 seconds.
- Video, SpicyMotion v3.0 Fast (native audio, up to 30 seconds): 15 credits per second Standard, 20 HD. Movie scenes use this model in HD.

- **100 credits**: $3.99 (EUR 3.99). About 20 standard images or 10 seconds of standard video.
- **400 credits**: $19.99 (EUR 17.99). About 80 standard images or 40 seconds of standard video.
- **2,000 credits**: $49.99 (EUR 42.99), most popular. About 400 standard images or 200 seconds of standard video.
- **10,000 credits**: $149.99 (EUR 129.99). About 2000 standard images or 1000 seconds of standard video.

Product overview for agents: https://www.spicygen.ai/llms.txt. Docs: https://www.spicygen.ai/docs/agents (every public page is also markdown: append `.md`).
