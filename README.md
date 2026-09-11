# LIGHT CYCLES

A neon TRON-inspired light-cycle arena battle — one page, no build step. Race your light trail around the grid, lock your opponents into your wall, and be the last rider standing.

Play it live at **https://thecodercomau.github.io/Light-Cycle-Tron/**

---

## Gameplay

- **Turn 90° only.** You can't reverse into your own trail.
- Leaving a wall — hitting your own or an opponent's trail, an arena edge, or the shrinking barrier — eliminates you.
- **Last rider standing** takes the round. First to the win score takes the match.
- **Boosts:** in Standard and Shrinking modes each player gets **3 boost charges per round** for a short speed burst. Classic mode bans boosts; Shrinking mode lets you toggle infinite boosts per player.
- Options include **team play (2v2)**, arena shrinking, variable board sizes and speeds.

## Getting Started

Open `index.html` in any modern browser. Everything runs client-side — no install needed.

### Controls

| Action | Keys |
|---|---|
| Move | WASD (Player 1), arrow keys (Player 2), remappable per player up to 8 local players |
| Boost | Shift by default (remappable) |
| Fullscreen | `F` (in-game) |

Every control scheme is fully remappable in the setup screens.

## Features

- **Local play** for 2–8 players on one keyboard, with teams
- **Online play** — true peer-to-peer (WebRTC) with room codes; hosts can fill seats with AI bots
  - Public room browser, spectator mode, in-lobby chat, ping display
  - Host controls: kick / mute, per-player infinite boosts, bot difficulty, room size
- **AI bots** — five difficulty tiers (Peaceful → Impossible); the Impossible bot glitches
- **Replays** — every match is recorded, playback bar with scrubbing, and downloadable as JSON
- **Career stats** — persistent win/loss leaderboard per player name
- **Settings screen** — graphics (CRT scanlines, animated glow, grid style), gameplay defaults, and online defaults, saved to `localStorage`

## Game Modes

| Mode | Rules |
|---|---|
| **CLASSIC** | No boosts. Pure positioning. |
| **STANDARD** | 3 boost charges per round. |
| **SHRINKING** | The arena walls close inward as the match goes on — the grid runs out of room. |

## Online Play & the Signaling Server

Browsers connect directly to each other over WebRTC. A tiny Node signaling server (`server/server.js`) brokers the initial handshake and hosts the public-room listing:

- `POST /rooms` / `GET /rooms` / `DELETE /rooms/:code` — public room registry (rooms expire after 30s unless refreshed)
- `GET /turn` — TURN relay credentials (cloudflare/Cloudflare-free fallback), rate-limited
- `GET /health` / `GET /metrics` — deployment health and metrics
- `/peerjs` — PeerJS (WebSocket) signaling endpoint

### Running the server locally

```bash
cd server
npm install
npm start            # listens on :9000
```

### Deploying the server

- **Docker:** `docker-compose up --build` (uses `server/Dockerfile`)
- Set `PORT`, `PEER_PATH`, `PEER_KEY`, and optional `METERED_API_KEY` / `TURN_API_KEY` env vars for TURN credentials.

The client's PeerServer URL lives in `index.html` (`DEFAULT_PEER_CONFIG`) — point it at wherever you host the server.

### Hosting the game itself

The game is a single static `index.html` — drop it on any static host (GitHub Pages included). Online play additionally requires the signaling server above.

## Project Structure

```
index.html            The entire game — UI, rendering, game logic, networking (single file)
server/
  server.js           PeerJS signaling server + room registry + health/metrics
  package.json        Dependencies (express, peer, cors, express-rate-limit)
  Dockerfile          Container image for deployment
docker-compose.yml    One-command server deployment
```

## Tech Stack

- Vanilla HTML / CSS / JavaScript (no frameworks, no build step)
- Canvas 2D rendering (offscreen canvases for the grid and trails)
- [PeerJS](https://peerjs.com/) for WebRTC peer-to-peer networking
- Node.js + Express for the signaling server
- Programmatic AI bots that plan paths through the live grid

## Browser Support

Any evergreen browser (Chrome, Firefox, Safari, Edge). Keyboard controls are required for local play.