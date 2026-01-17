# Clue: Supernatural – Digital Game Design Specification

## Purpose of This Document

This document captures the **complete functional and thematic requirements** of the physical board game **Clue: Supernatural** and translates them into clear, actionable guidance for a development team tasked with creating a faithful digital adaptation. The goal is not to redesign the game, but to **digitally preserve its mechanics, tone, pacing, and fan-service details** while leveraging digital affordances where appropriate.

The intended audience includes:

- Game designers
- Gameplay engineers
- UI/UX designers
- Backend / multiplayer engineers
- QA testers

---

## 1. Source Material Overview

**Clue: Supernatural** is a licensed crossover between the classic *Clue* deduction board game and the TV series *Supernatural*. It combines standard Clue mechanics with characters, locations, and weapons drawn from the show’s lore.

### Core Deduction Premise

Players must determine:

- **WHO** committed the crime
- **WHAT WEAPON** was used
- **WHERE** it happened

All answers are concealed in a secret case file at the start of the game.

---

## 2. Physical Game Components (Canonical Reference)

### 2.1 Game Board

- Mansion-style board layout, reskinned to Supernatural locations
- Fixed rooms connected by hallways
- Rooms contain named spaces for weapons
- Board includes:
  - Room entrances
  - Hallway intersections
  - Starting locations for characters

**Digital Requirement**:

- Board must be **functionally identical** in layout and connectivity
- Movement distances and room adjacency must match exactly
- Visual styling should reference Supernatural aesthetics (dark wood, occult symbols, moody lighting)

---

### 2.2 Rooms (Crime Locations)

The rooms replace the classic Clue mansion rooms with Supernatural-themed locations.

Examples include:

- Bobby Singer’s House
- Men of Letters Bunker
- Abandoned Cabin
- Cemetery
- Roadhouse Bar
- Haunted Mansion / Chapel equivalents

**Digital Requirements**:

- Each room must:
  - Be a discrete game state
  - Support weapon presence
  - Allow suggestions when entered
- Rooms should have unique background art or subtle ambient audio

---

### 2.3 Characters (Suspects / Player Tokens)

Playable characters are drawn from *Supernatural* canon, such as:

- Sam Winchester
- Dean Winchester
- Castiel
- Bobby Singer
- Crowley
- Ruby (or similar depending on edition)

Each character has:

- A distinct token
- A fixed starting position on the board
- No asymmetric abilities (all play identically mechanically)

**Digital Requirements**:

- Character choice screen
- Character portrait + token representation
- Enforce starting positions automatically
- No gameplay bonuses or penalties per character

---

### 2.4 Weapons

Weapons are thematic replacements for classic Clue weapons.

Examples:

- Angel Blade
- Demon-Killing Knife
- Colt
- Rope (or equivalent)
- Wrench / blunt object equivalent
- Firearm or occult artifact

Weapons:

- Exist only conceptually (not items players hold)
- Are placed in rooms via suggestions

**Digital Requirements**:

- Weapons must be tracked as abstract entities
- Visually represented only during suggestions/accusations
- Automatically moved to rooms when suggested

---

### 2.5 Cards

There are **three decks**, shuffled separately:

1. **Character Cards**
2. **Weapon Cards**
3. **Room Cards**

Setup:

- One card from each deck is randomly selected and placed into a hidden case file
- Remaining cards are shuffled together and dealt evenly to players

**Digital Requirements**:

- Secure RNG shuffle
- Hidden case file object (server-authoritative in multiplayer)
- Private hand state per player
- Uneven card distribution handled correctly

---

### 2.6 Dice

- Single six-sided die
- Used for movement only

**Digital Requirements**:

- RNG dice roll
- Roll animation (optional but recommended)
- Prevent multiple rolls per turn

---

## 3. Core Gameplay Loop

### 3.1 Turn Structure

Each player’s turn consists of:

1. Dice roll
2. Movement
3. Optional suggestion (if in a room)
4. Optional accusation (instead of suggestion)
5. End turn

**Digital Enforcement**:

- Turn-based state machine
- UI should only expose valid actions at each phase

---

### 3.2 Movement Rules

- Players may move up to the number rolled
- Movement is orthogonal only
- Cannot pass through occupied spaces
- Entering a room ends movement

**Digital Requirements**:

- Path validation logic
- Highlight valid movement tiles
- Prevent illegal moves

---

### 3.3 Suggestions

When inside a room, a player may make a suggestion consisting of:

- One suspect
- One weapon
- The current room

Effects:

- Named suspect token is moved into the room
- Named weapon is moved into the room
- Other players, in turn order, may refute by revealing one matching card (privately)

**Digital Requirements**:

- Suggestion UI (dropdowns or card-style selection)
- Automatic piece movement
- Refutation handled privately and sequentially
- Log visible to all players showing **that** a refutation occurred, not which card

---

### 3.4 Accusations

- Can be made at any time during the player’s turn
- Player names suspect, weapon, and room

Outcomes:

- If correct: immediate win
- If incorrect: player is eliminated from making further accusations, but remains in the game to refute others

**Digital Requirements**:

- Confirmation dialog (accusations are irreversible)
- Reveal case file on success
- Track eliminated-but-active players

---

## 4. Multiplayer & Game Modes

### Second-Screen Multiplayer Model (Required)

This digital version uses an **asymmetric screen model**:

- **Shared Display** (TV / monitor / projector): shows the full board, table, room states, turn order, animations, and public game log.
- **Personal Mobile Devices** (one per player): used for **all private interactions**.

Players **must not** see private information on the shared display.

This mirrors the physical table experience: everyone sees the board, but only you see your cards and notes.

---

### 4.1 Player Counts

- Minimum: 3 players
- Maximum: 6 players

Each player must connect via:

- One mobile device (phone or tablet)
- One shared display per session

No split-screen or single-device mode is supported.

**Digital Requirements**:

- Enforce player limits
- Scale UI cleanly from 3–6 players

---

### 4.2 Multiplayer Architecture

#### Network Topology

- Central authoritative game server (local or cloud)
- Shared display acts as a **spectator + controller client**
- Mobile devices act as **player clients**

#### Connectivity

Players join a session via:

- QR code displayed on the shared screen
- Short alphanumeric room code (backup)

#### Security & Privacy

- Player hands, notebooks, and refutations are **never** sent to the shared display
- Server enforces visibility rules strictly
- Mobile clients only receive data they are entitled to see

This architecture is mandatory to preserve deduction integrity.

---

Recommended:

- Server-authoritative logic
- Clients only receive permitted information

Must support:

- Online multiplayer
- Hot-seat local multiplayer (optional but ideal)
- AI players (optional stretch goal)

---

## 5. User Interface & UX

### 5.1 Shared Display (Main Screen)

The shared display is the digital game table.

Shows:

- Full board with tokens, rooms, and weapons
- Turn order and active player highlight
- Dice roll animations
- Public log of suggestions and refutations (without card details)
- Game phase indicators

Never shows:

- Player hands
- Notebook markings
- Accusation choices before confirmation

---

### 5.2 Mobile Player Interface

Each player’s mobile device is their **private control surface**.

Core screens:

- Hand view (cards dealt to the player)
- Detective notebook
- Movement controls (confirm path)
- Suggestion builder
- Accusation builder
- Refutation prompt (private)

Mobile UI principles:

- One primary action per screen
- Thumb-friendly controls
- Haptic feedback for key moments (dice roll, refutation request)

---

### 5.3 Core Screens

- Main Menu
- Lobby / Invite Screen
- Character Select
- Game Board View
- Suggestion / Accusation Modals
- Notebook / Deduction Sheet

---

### 5.4 Detective Notebook (Mobile Only)

The notebook exists **only on mobile devices**.

Features:

- Grid of suspects, weapons, and rooms
- Manual checkmarks, Xs, and notes
- Optional personal annotations

Rules:

- No automatic deductions
- No shared visibility
- Notebook state persists across reconnects

This preserves the tactile reasoning experience of the physical game.

---

A digital replacement for the paper Clue checklist.

Features:

- Grid of suspects, weapons, rooms
- Manual player marking
- No automatic deductions (to preserve player agency)

---

## 6. Audio & Theme

### Audio Routing

- Ambient music and board SFX play from the shared display
- Private sounds (card reveal, refutation notification) play on mobile devices

### Visual Theme

Because full licensed rights are available:

- Use original **Clue: Supernatural** board art
- Use official character portraits and tokens
- Use authentic room illustrations
- Use canonical weapon artwork

No visual abstraction is required; fidelity is preferred.

---

- Subtle ambient music inspired by Supernatural tone
- Dice roll sounds
- Card reveal sounds
- Optional voice lines or stingers (non-dialogue)

No jump scares. This is mystery, not horror.

---

## 7. Accessibility & Quality of Life

- Colorblind-safe markers
- Clear iconography
- Text scaling
- Turn reminders / AFK timers

---

## 8. Win Conditions & Endgame

Game ends when:

- A correct accusation is made

End screen should show:

- Full case file
- Timeline of suggestions
- Optional replay / rematch

---

## 9. Non-Goals (Important)

To preserve the spirit of Clue:

- No hidden abilities
- No RNG beyond dice and shuffle
- No power-ups
- No real-time mechanics
- No automatic deduction assistance

Digital convenience must not replace player reasoning.

---

To preserve the spirit of Clue:

- No hidden abilities
- No RNG beyond dice and shuffle
- No power-ups
- No real-time mechanics

This is deduction, not chaos.

---

## 10. Open Questions for Stakeholders

- Exact licensed character list per edition
- Final room naming consistency
- Online vs offline priority
- Monetization (if any)

---

## Final Design Philosophy

This digital adaptation should feel like **sitting at a table with fans of Supernatural**, not like an arcade game wearing a trench coat. Faithfulness beats flash. Mystery beats speed. Let players think, accuse, and be gloriously wrong.

The devil is in the details. Conveniently, so is this game.

---

## MVP Implementation Plan

### MVP Scope (Must Have)

- Confirm final licensed content list (characters, rooms, weapons) and align naming conventions.
- Translate the physical board into a digital grid graph with exact adjacency and movement rules.
- Define data models for cards, case file, hands, notebook state, and turn phase state machine.
- Establish authoritative server rules for shuffling, dealing, dice rolls, and action validation.
- Implement movement validation (orthogonal paths, no passing through occupied tiles).
- Build suggestion and accusation flows with private refutations and public logs.
- Enforce elimination rules (no further accusations, still refutes).
- Implement win/lose resolution and case file reveal.
- Implement lobby, join via QR + room code, and player limit enforcement.
- Build shared display client (board, turn order, animations, public log).
- Build mobile client (hand, notebook, movement confirm, suggestion/accusation builders).
- Ensure strict visibility rules between shared display and mobile clients.
- Add baseline accessibility options (colorblind-safe markers, text scaling).

### Post-MVP Enhancements (Nice to Have)

- Add ambient audio routing and SFX cues to appropriate devices.
- Integrate official art assets and finalize thematic styling.
- Add quality-of-life features (AFK timers, turn reminders, improved animations).
- Create automated tests for rules enforcement and refutation flow.
- Run multiplayer soak tests with 3–6 players and reconnection scenarios.
- Validate against the physical ruleset with a full playthrough checklist.
