---
title: Choose technology for the companion entity
status: proposed
date: 2026-07-05
decision-makers: Eudicy
---

# 001. Choose technology for the companion entity

## Status

Proposed

## Context and Problem Statement

New Minecraft players struggle with a lonely, overwhelming solo experience because
the world has only NPC entities — no real companion to share fun, answer questions,
or ease their fear of enemies and mistakes. The intended solution is an AI companion
that plays alongside the player, reacting to what they're doing and answering
questions in the moment.

To build that companion, we need an entity that (a) appears in the Minecraft world,
(b) can run pre-programmed and later AI-driven behavior, and (c) reaches the target
audience (new players, many of them teens) with as little installation friction as
possible.

Which technology should we use to implement this companion entity?

## Decision Drivers

Ranked by priority, confirmed via stakeholder interview on 2026-07-05:

1. **Platform reach** — does the approach work on Java Edition, Bedrock Edition, or
   both?
2. **Real-time AI/LLM integration ease** — how easily can live chat and reactive
   behavior be wired to an LLM?
3. **Development effort & maintenance** — how much ongoing work for a solo builder,
   across how many mod-loader/platform variants?
4. **Depth of in-world presence** — does the companion show up as a true mob/creature
   (model, animations, natural behavior) or as something more limited (e.g. a
   player-shaped bot)?
5. **Player installation friction** — does using the companion require the player
   (or the world/server) to install a mod, plugin, or add-on pack?

## Considered Options

1. Do nothing
2. Manual workaround — point new players to human multiplayer communities
3. Adopt an existing AI companion product
4. Build custom: Java Edition mod (Forge / Fabric / NeoForge)
5. Build custom: Bedrock Add-on + Script API
6. Build custom: Server plugin (Paper/Spigot) + Citizens
7. Build custom: External bot via Mineflayer (Node.js)

## Decision Outcome

Not yet decided. This ADR is presented so stakeholders can weigh the options below
and make an informed choice.

## Pros and Cons of the Options

### 1. Do nothing

Accept the status quo; don't build a companion.

* Good, because zero cost or effort, no risk of breaking anything.
* Bad, because it leaves the core problem (loneliness, overwhelm, churn) unsolved.
* Bad, because competitors (see option 3) are already shipping this — missed
  opportunity.
* Mitigation: none needed unless resources appear; revisit later.

### 2. Manual workaround — point players to human multiplayer

Direct new players to Realms, LAN play, or community Discords/servers instead of
building software.
[Playing on servers](https://minecraft.wiki/w/Tutorial:Playing_on_servers)

* Good, because it needs no development — uses Minecraft's existing Realms/LAN/
  Discord ecosystem.
* Good, because it's available immediately.
* Bad, because it still requires the new player to find and trust human
  companions, and often a paid Realms subscription or server setup.
* Bad, because it doesn't provide an always-available companion that answers
  questions instantly — it depends on other people being online.
* Bad, because it doesn't fit true single-player use at all.
* Mitigation: offer this as a documented stop-gap tip alongside the real product,
  not as a replacement for it.

### 3. Adopt an existing AI companion product

Several products already exist: [MinePal](https://minepal.net/) (free, hosted,
crossplay), [VoxelMind](https://voxel-mind.com/blog/best-ai-companion-mods-minecraft)
(Fabric mod, AI hosted via tunnel), [Player2 AI NPC](https://modrinth.com/mod/player2npc)
(embodied agents with personality/voice), and the open-source
["AI Companion" mod](https://www.curseforge.com/minecraft/mc-mods/ai-companion)
(bring-your-own API key).

* Good, because it's the fastest time-to-value — these are already live and
  maintained.
* Good, because some are free (MinePal) or open-source (AI Companion mod, could be
  forked instead of built from scratch).
* Bad, because we have no control over roadmap, branding, or monetization of a
  third-party product.
* Bad, because hosted options (MinePal, VoxelMind) route player traffic through
  their infrastructure — privacy, latency, and uptime depend on someone else.
* Bad, because none of them are tailored to the specific new-player onboarding
  journey described in our problem statement.
* Mitigation: use one as a stopgap/benchmark while building a differentiated
  product, or fork the open-source option instead of starting from zero.

### 4. Build custom: Java Edition mod (Forge / Fabric / NeoForge)

Implement the companion as a real custom mob using Minecraft's Goal or Brain AI
system. [SmartBrainLib](https://www.curseforge.com/minecraft/mc-mods/smartbrainlib)
wraps the Brain system and supports Forge, Fabric, and NeoForge.

* Good, because it gives the deepest in-world integration — a true mob with model,
  animations, natural spawning, and full compatibility with vanilla mechanics.
* Good, because it works fully offline in singleplayer.
* Bad, because every world/server needs the matching mod installed — real
  installation friction for the target audience (new players).
* Bad, because the Java modding ecosystem is fragmented across loaders (Forge vs.
  Fabric vs. NeoForge), multiplying maintenance work.
* Bad, because wiring live LLM calls out of a JVM mod is more work than from a
  scripting environment.
* Mitigation: pick one loader (Fabric has the largest, lightest ecosystem) and
  accept the reduced reach; ship a one-click installer to lower friction.

### 5. Build custom: Bedrock Add-on + Script API

Define the entity via a behavior pack and drive its logic with the
`@minecraft/server` [Script API](https://jaylydev.github.io/scriptapi-docs/latest/)
(TypeScript/JavaScript).

* Good, because add-ons need no separate install beyond the pack itself — easy
  distribution via world or Realm.
* Good, because it reaches the mobile/console Bedrock audience, which skews toward
  the "new/young player" segment we're targeting.
* Good, because TypeScript/JS is an easier entry point than Java, and the API is
  actively developed and officially supported.
* Bad, because it's Bedrock-only — excludes all Java Edition players.
* Bad, because some scripting capabilities still lag behind what Java modding can
  do.
* Mitigation: scope v1 to Bedrock only if the audience is mostly mobile/console;
  revisit Java support later if needed.

### 6. Build custom: Server plugin (Paper/Spigot) + Citizens

Run the companion as a server-side NPC using [Citizens](https://citizensnpcs.co/),
optionally scripted with Denizen.

* Good, because it's a mature, 14+ year ecosystem, and players join with a
  completely vanilla client — no player-side install at all.
* Good, because it fits a hosted multiplayer/community-server model well.
* Bad, because it requires running and maintaining a server — it doesn't fit
  singleplayer/offline use, which is central to our problem statement.
* Bad, because Citizens has an external dependency (Vault) and is built for
  scripted NPCs, not live LLM-driven reasoning.
* Mitigation: pair with an LLM webhook via Denizen scripting; still won't solve
  the pure-singleplayer case without also running a local server.

### 7. Build custom: External bot via Mineflayer (Node.js)

Connect a bot to the world via the Minecraft protocol using
[Mineflayer](https://github.com/PrismarineJS/mineflayer), a high-level JS API for
movement, world/entity awareness, and interaction.

* Good, because it needs zero install on the player's side — the bot just joins as
  a regular player (e.g. over "Open to LAN" in singleplayer).
* Good, because it's the easiest to wire to an LLM — it's just a Node.js process
  the builder fully controls.
* Good, because it's fastest to prototype and iterate on companion personality and
  reactions.
* Bad, because the companion appears as a player skin, not a distinct mob/creature
  — this weakens the "creature" framing from the concept summary.
* Bad, because it's limited to Java Edition, and some interactions (custom dialog
  UI, particle effects) are harder without custom client-side rendering.
* Bad, because singleplayer use still requires enabling "Open to LAN" or similar
  server access.
* Mitigation: use a custom skin/display name to brand it as a companion; combine
  with a lightweight companion mod later for visual polish once the concept is
  validated.

## More Information

* [SmartBrainLib](https://www.curseforge.com/minecraft/mc-mods/smartbrainlib)
* [NeoForge](https://github.com/neoforged/neoforge)
* [Bedrock Script API docs](https://jaylydev.github.io/scriptapi-docs/latest/)
* [Bedrock Wiki — scripting intro](https://wiki.bedrock.dev/scripting/scripting-intro)
* [Citizens](https://citizensnpcs.co/) · [Citizens Wiki](https://wiki.citizensnpcs.co/Citizens_Wiki)
* [Mineflayer](https://github.com/PrismarineJS/mineflayer) ·
  [API docs](https://github.com/PrismarineJS/mineflayer/blob/master/docs/api.md)
* [MinePal](https://minepal.net/) ·
  [VoxelMind](https://voxel-mind.com/blog/best-ai-companion-mods-minecraft) ·
  [Player2 AI NPC](https://modrinth.com/mod/player2npc) ·
  [AI Companion mod](https://www.curseforge.com/minecraft/mc-mods/ai-companion)
* [Minecraft Wiki — playing on servers](https://minecraft.wiki/w/Tutorial:Playing_on_servers)
