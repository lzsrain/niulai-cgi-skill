---
name: niulai-cgi-skill
description: 'This skill should be used when a user wants to restyle images, game maps, characters, UI, or any visual asset into the "牛来" (NiuLai) aesthetic — the deliberately cheap, early-2000s mainland-China CGI look seen in low-budget 3D animated films (think 猪猪侠 / 果宝特攻 first seasons: flat-shaded low-poly geometry, broken proportions, visible clipping/poke-through, hard baked shadows, flat plastic pastel colors, no anti-aliasing). Triggers include "牛来化", "make it look like a trash-tier 2005 CGI render", "restyle as cheap bootleg 3D", or applying this style to a specific project such as pixel-city-walk. Contains battle-tested ImageGen prompts, the sunlit_game_map preset for architecture/maps, tool-environment workarounds (image-to-image schema bug, watermark removal), and a full pixel-city-walk engine-modification case study.'
agent_created: true
---

# 牛来 CGI Stylizer (niulai-cgi-skill)

## Purpose

Restyle any visual into the **"牛来" (NiuLai) cheap-CGI aesthetic**: a
deliberately under-produced early-2000s Chinese 3D-animation look. The low
quality must come from the *production pipeline* itself — modeling, proportions,
rigging, materials, lighting, rendering — **not** from post filters (pixelation,
blur, VHS, noise, dark grading, JPEG artifacts).

Concept inspired by `TanShilongMario/NiuLai-Skill` (that repository ships no
license; it is used here only as a conceptual reference). All prompts,
presets, and workflows in this skill are independently written and
battle-tested against real generated outputs.

## When to use

- User says "牛来化", "做成牛来风格", or wants the 猪猪侠 / 果宝特攻 cheap-CGI look.
- Restyling a map, illustration, character sprite, or UI into flat-shaded
  low-poly cheap 3D.
- Applying the look to the `pixel-city-walk` engine (map / UI / character /
  postcard) or any similar 2D-canvas game.

## Core aesthetic (the non-negotiable "破味" signals)

If any of these are missing, the result falls back to *clean low-poly* and is
**not** 牛来. Read `references/style-system.md` for full detail and the named
presets.

1. **Flat shading, one solid color per face** — NO gradient, NO specular, NO
   smooth shading, NO PBR, NO normal maps, NO reflections, NO ambient occlusion.
2. **NO anti-aliasing** — polygon edges are jagged and stepped.
3. **Water is a flat colored sheet** — NO transparency, NO ripples, NO specular
   highlights. A lake is just a flat colored rectangle.
4. **Countable 16×16 tiled textures** on walls — you can literally COUNT the
   repeating bricks.
5. **Single hard directional light** (upper-right) casting **solid black blob
   shadows** — no soft penumbra.
6. **Broken proportions, visible clipping / poke-through, reused stock assets.**
7. **Bright flat pastel plastic colors** (cobalt blue, faded brick red, ochre,
   pale teal, peachy beige); flat pale-grey sky band with NO gradient.

## Workflow

1. Pick a preset: `sunlit_game_map` (architecture / city maps / scenes) or
   `character` (figures). See `references/style-system.md`.
2. Use the battle-tested ImageGen prompts in `references/prompt-blueprint.md`.
   **Critical tool note:** image-to-image is currently unreliable in this
   environment (the `image` param rejects arrays even when correctly formed) —
   prefer **text-to-image with layout locked via numbered `(a)(b)(c)` positional
   anchors**. See the "Tool environment" section there.
3. For character sprites: generate on a pure `#00ff00` green screen, then key it
   to transparent in-engine (see the pixel-city-walk case).
4. Remove the ImageGen "AI生成 WORKBUDDY" corner watermark with `ffmpeg
   drawbox` (see `references/prompt-blueprint.md`).
5. Resize / normalize with `sips` (macOS) — there is no ImageMagick and ffmpeg
   lacks `posterize`.

## pixel-city-walk case study

`references/pixel-city-walk-case.md` documents the four-block 牛来化 (map / UI /
character / postcard), the `uiMode` isolation pattern, the TDZ white-screen
gotcha, green-screen sprite + `npc.scale`, and postcard-scene generation.

## Quality bar

Reject outputs that are too clean, too coordinated, or that achieve cheapness
through post-filters (pixelation / blur / VHS / heavy noise). The result should
look like a **first unpolished render keyframe before any cleanup pass**, made
by a poor studio.
