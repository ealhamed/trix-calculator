# Trix Calculator (تركس)

Score tracker for the Levantine/Gulf Trix card game — sibling to baloot/sibeet/konkan in the الديوانية suite.

## Stack
- Single-file PWA (`index.html`)
- Service Worker (`sw.js`, cache `trix-v1`) for offline caching
- Al Dewaniah visual identity (burgundy #722F37 / gold #C9A227 / navy #1A2744 / cream #F5F0E6, Cairo font)
- **No P2P/Firebase yet** — v1 is single-device only. Rooms planned for v2.

## Commands
```bash
npx http-server . -p 8083    # Local dev server
```

## Live URL
https://ealhamed.github.io/trix-calculator/

## Files
| File | What |
|------|------|
| `index.html` | Calculator app (UI + game logic) |
| `sw.js` | Service worker (cache-first) |
| `manifest.json` | PWA manifest |
| `logo.png`, `icon-192.png`, `icon-512.png` | Branding |

## Game Rules (as implemented)

### Players & Session
- **4 players, fixed.** No add/remove mid-session.
- Session = each player completes their sequence of "kingdoms" (ممالك).
- Session ends when every player has completed their chosen path.
- **Winner = lowest total** (or lowest team sum in teams mode).

### Contracts
| Contract | Base scoring | Doubled variant |
|---|---|---|
| أكلات (tricks) | −15 per trick taken | — |
| ديمن (diamonds) | −10 per diamond taken | — |
| بنات (queens) | −25 per queen taken | taker −50, doubler +25 |
| شايب الهاص (king of hearts) | −75 | taker −150, doubler +75 |
| تركس (trix) | +200 / +150 / +100 / +50 by finish order | — |
| كمبلكس (complex) | sum of all 4 non-trix contracts, played as one hand | queens/king doubling works normally |

### Doubling
- Only **queens** and **king of hearts** can be doubled.
- Queens can be doubled during بنات contract or كمبلكس.
- King can be doubled during شايب الهاص contract or كمبلكس.
- UI: for each doubled card, pick the "downside" player (takes −2×base) and the "upside" player (takes +base).
  - Normal case: downside = taker, upside = doubler.
  - Self-forced case (doubler forced to take their own card): downside = doubler, upside = the forcer.

### Path Logic (per player)
Each player chooses one of two paths on their first call:

- **2-call path:** Complex + Trix (in either order, but Complex locks the path).
  - If a player picks Complex first, they are locked to 2-call and must next play Trix.
  - If a player picks Trix first, path stays undecided; their next pick determines the path.
- **5-call path:** Trix + 4 individual non-trix contracts (أكلات / ديمن / بنات / شايب الهاص).
  - Locked in as soon as they pick any individual contract.

### Teams Mode
- Pairs of 2 (always 2 teams, same as konkan).
- User picks pairs via team assignment modal.
- Each player still takes their own calls; team total = sum of the pair.
- Game-over ranks teams instead of individuals.
- Doubling a teammate's card is allowed and scores normally — the +25/−50 math nets to −25 for the team (same as undoubled), so it's a pure risk hedge against the opponent team.

## UI Layout
- **Header:** burger menu + logo + title
- **Progress row:** "المملكة N / M" pill + "الدور: <caller>" gold pill
- **Scoreboard:** 2×2 grid of player cards (or team cards in teams mode)
  - Current caller highlighted with gold border
  - Completed players dimmed
  - Each card shows name, running total (green if negative, burgundy if positive), and calls progress (e.g. "٢/٥ ممالك")
- **Call area:** either the call picker (contract selection) or the active contract form
- **Contract forms:** stepper inputs for أكلات/ديمن; card slots with doubling UI for بنات/شايب; drag-to-order for تركس; combined form for كمبلكس. Live preview row shows per-player delta.
- **Bottom actions:** تراجع (undo) + جلسة جديدة
- **History:** list of completed kingdoms with contract name, caller, and per-player score chips

## Shared Features (parity with siblings)
- `appConfirm()` / `appPrompt()` custom dialogs
- `pushOverlay()` + popstate for back-button integration
- Auto-dim overlay after 15s idle
- Dark mode with SVG moon/sun toggle
- Haptic feedback (`navigator.vibrate`) on submit, selection, undo
- iOS Safari bottom-strip fix (`html { background: var(--bg); min-height: 100% }`)
- `prefers-reduced-motion` respected via CSS animations that only activate on visible state changes
- PWA: offline-first, installable
- Menu links to all 4 sibling calculators

## Not Yet Implemented (v2 scope)
- P2P rooms via PeerJS
- Firebase RTDB fallback for scaling viewers
- Firebase project `trix-calculator-al-dew`
- Menu links from sibeet/baloot/konkan to trix (added on deploy)

## Repos
- **trix-calculator**: https://github.com/ealhamed/trix-calculator
- **Siblings**: [baloot-calculator](https://github.com/ealhamed/baloot-calculator), [sibeet-calculator](https://github.com/ealhamed/sibeet-calculator), [konkan-calculator](https://github.com/ealhamed/konkan-calculator)
