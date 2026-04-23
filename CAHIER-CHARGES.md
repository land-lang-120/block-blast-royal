# 📘 block-blast-royal — Cahier des charges

> Document rétrospectif. Source de vérité pour la vision, le périmètre et la roadmap.
> Lié à : [PROGRESS.md](PROGRESS.md) (suivi opérationnel)
> Mis à jour : **2026-04-23**

---

## 1. Vision

**Block Blast Royal** est un jeu **puzzle de blocs arcade** premium-feel, jouable instantanément dans le navigateur. Inspiré du genre "Block Blast" populaire sur mobile, il propose **3 modes de jeu** (Adventure narratif, Classic infini, Dynamic temps-réel), avec une **production visuelle 3D-feel** (shimmer sur blocs, orbs flottants, animations cubic-bezier) et une vraie courbe de progression (zones + niveaux + étoiles).

**Tagline** : « Place. Détruis. Monte sur le trône. »

**Promesse joueur** :
- Zéro friction : 0 install Play Store nécessaire (PWA)
- 3 modes pour 3 humeurs (chill / sportif / panique)
- Progression visible (zones débloquées, étoiles cumulées, daily streak)
- Effets visuels qui valent un jeu mobile premium

---

## 2. Personas

### Persona 1 — Le casual mobile (cible primaire)
- Joue 5–15 min en pause / transport
- Veut un puzzle facile à comprendre, dur à maîtriser
- Sensible aux **récompenses visuelles** (combos, particules, étoiles)
- KPI : sessions/semaine + temps moyen session

### Persona 2 — Le complétionniste
- Veut 3⭐ sur tous les niveaux Adventure
- Suit son daily win streak (fierté de la série)
- Compare avec amis (futur : leaderboard)
- KPI : % niveaux 3⭐ + longueur streak

### Persona 3 — Le speedrunner / sportif
- Préfère le mode Dynamic (panique temps-réel, ceinture qui défile)
- Cherche le high score absolu
- KPI : best score Dynamic + sessions Dynamic vs. Classic

### Persona 4 — Le joueur compétitif (futur)
- Mode Compétition annoncé (badge "Bientôt")
- Veut classement mondial, ligues, saisons
- KPI (futur) : ranking + ligue actuelle

---

## 3. Périmètre fonctionnel

### Modes de jeu
| Mode | Statut | Description |
|---|---|---|
| **Adventure** | ✅ Live | Carte avec zones thématiques + niveaux numérotés. Objectifs spéciaux (score cible, lignes à effacer, blocs glazed à détruire). Système d'étoiles 0–3. |
| **Classic** | ✅ Live | Mode infini sans contrainte de temps. 3 pièces tray, replace après usage des 3. Score = nombre de cellules placées + bonus combos. |
| **Dynamic** | ✅ Live | Pièces défilent sur une "ceinture" en temps réel vers un mur de danger. Speed badge (×1, ×1.5, ×2…). Game over si ceinture pleine. |
| **Compétition** | 🟠 Bientôt | Badge "Bientôt", `showComingSoon()` alert |

### Mécaniques core
- **Grille 8×8** (`GRID_SIZE = 8`)
- **7 couleurs** de blocs (`COLORS = 7`)
- **26 formes de pièces** : 1×2, 1×3, 1×4, 1×5 (h/v), 2×2, 3×3, 2×3, 3×2, T (4 orientations), L 4-briques (4 orientations), L coin 5-briques (4 orientations), S, Z
- **Drag & drop tactile + souris** avec ghost preview soulevé (`LIFT = 100px`)
- **Highlight valid drops** sur survol pièce
- **Preview placement** au moment du drag (cellules avec couleur transparente)
- **Détection lignes complètes** : rangs + colonnes
- **Combo flash** si 2+ lignes effacées simultanément
- **Animation clearing** : fade des cellules + spawn particules colorées par cellule
- **Game over** détecté quand `!hasAnyMove()` (aucune pièce du tray ne peut être placée)

### Adventure-spécifique
- **Carte zones** : multiples zones thématiques (`adv-zones`), 3 niveaux/zone par grille
- **3 états** par niveau : `unlocked` / `completed` / `locked`
- **Objectifs** affichés dans une bar `adv-objective` (label + target + étoiles)
- **Blocs glazed** : `color-glazed` (jamais effaçables sans cracking) → `color-glazed-cracked` après 1 hit → effaçables ensuite
- **Stars entry animation** cubic-bezier pour la modale level complete

### Dynamic-spécifique
- **Belt track** horizontal qui défile (pièces apparaissent côté gauche, voyagent vers droite)
- **Mur de danger** rouge à droite avec animation `wallPulse`
- **Speed badge** indique multiplicateur courant
- **Danger flash** : ceinture devient rouge clignotante quand pièce proche du mur

### UI / écrans
- **Splash** : logo 3D animé "BLOCK 👑 BLAST ROYAL" + loader bar (2.5s minimum)
- **Home** :
  - Logo XL + couronne O
  - Win streak counter (consecutive daily victories)
  - 4 boutons mode 3D (Adventure / Classic / Dynamic / Compétition)
  - Medal button top-left
- **Game** : score header avec couronne + best score + objectif (Adventure) + grille + tray ou belt
- **Adventure Map** : zones empilées avec progress (`X/Y`)
- **Modals** : Game Over + Level Complete (avec étoiles animées)

### Persistance
- `localStorage.bbr_best` : meilleur score classic
- (à étendre) : étoiles par niveau Adventure, streak counter, mode preferences

### Effets visuels
- **Shimmer** sur cellules filled (animation 4s avec delay aléatoire)
- **Floating orbs canvas** : 12 orbs colorés (hue 190/210/240/260/280) flottants en background
- **Stars twinkle** : 50 étoiles aléatoires sur splash + home + map
- **Background gradients** : radial ellipse + linear navy/blue
- **3D buttons** avec ombres + inset highlights
- **Logo "3D text"** avec gradient + crown overlay sur le O

### Audio
- ❌ Pas d'audio actuellement (à ajouter)

### PWA
- `manifest.json` : standalone portrait, theme `#0d1b4b`, icons SVG 192/512
- `sw.js` : service worker enregistré
- Installable depuis browser

---

## 4. Architecture & stack

### Stack actuelle
- **Vanilla JS** entièrement bundlé dans `index.html` (2483 lignes)
- **CSS inline** (~950 lignes de style dans `<style>`)
- **HTML** : 12 sections `.screen` (splash, home, game, adventureMap, modals, drag ghost)
- **65 fonctions JS** : navigation, jeu state, drag&drop, drawing, particles, modes Adventure/Dynamic, persistance
- **PWA** : manifest + service worker + 2 icons SVG
- **0 dépendance** npm
- **Polices** : Fredoka One + Nunito (Google Fonts CDN)

### Pattern actuel
- **Multi-screen SPA** via `.screen.active` toggle
- **State global** : `grid`, `score`, `bestScore`, `pieces`, `usedSlots`, `dragPiece`, `currentMode`
- **Game loop Adventure/Classic** : event-driven (pas de RAF)
- **Game loop Dynamic** : RAF + intervals pour ceinture mouvante
- **Drag & drop** : touchstart/touchmove/touchend + mousedown/mousemove/mouseup unifiés via `getGridCoords()`

### Cible (post-refactoring)
```
block-blast-royal/
├── src/
│   ├── main.ts                 # Entry point
│   ├── state.ts                # Game state (Zustand-like ou pur object)
│   ├── pieces.ts               # 26 piece shapes + randPiece
│   ├── grid.ts                 # canPlace, placePiece, clearLines
│   ├── modes/
│   │   ├── classic.ts
│   │   ├── dynamic.ts
│   │   └── adventure.ts
│   ├── adventure/
│   │   ├── levels.ts           # 30+ levels JSON
│   │   ├── zones.ts            # Métadata zones
│   │   └── progression.ts      # Stars, unlock chain
│   ├── ui/
│   │   ├── screens/
│   │   │   ├── Splash.ts
│   │   │   ├── Home.ts
│   │   │   ├── Game.ts
│   │   │   ├── AdventureMap.ts
│   │   │   └── modals/
│   │   ├── render.ts           # buildGrid, renderPieces
│   │   ├── drag.ts             # touch+mouse drag system
│   │   └── effects.ts          # particles, combo flash, shimmer
│   ├── render/
│   │   ├── orbs.ts             # Floating orbs canvas
│   │   └── stars.ts            # Twinkle stars
│   ├── audio/
│   │   └── sfx.ts              # (nouveau) Web Audio API
│   ├── storage.ts              # localStorage abstraction
│   └── i18n/
│       └── strings.ts          # Multilingue
├── assets/
│   ├── sounds/
│   └── icons/
├── public/
│   ├── manifest.json
│   ├── sw.js
│   └── icons/
├── tests/
│   ├── grid.test.ts
│   ├── pieces.test.ts
│   └── dynamic.test.ts
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## 5. Charte UI / design

### Couleurs (variables CSS root)
| Token | Valeur | Usage |
|---|---|---|
| `--navy` | `#0d1b4b` | Fond principal |
| `--navy2` | `#112060` | Fond alterné |
| `--blue` | `#1a3a8f` | Surfaces actives |
| `--bright-blue` | `#2563eb` | Boutons primary accent |
| `--cyan` | `#06b6d4` | Accent secondary |
| `--gold` | `#f59e0b` | Score, étoiles, objectifs Adventure |
| `--gold-light` | `#fcd34d` | Highlights gold |
| `--green` | `#22c55e` | Validation, win streak check |
| `--purple` | `#a855f7` | Bouton Adventure |
| `--red` | `#ef4444` | Mur de danger, alertes |
| `--white` | `#ffffff` | Texte principal |

### 7 couleurs blocs
- Couleurs `color-1` à `color-7` avec gradients 145deg + box-shadow 3D + inset highlights/shadows
- Glazed : gradient bleu pâle sky → cyan
- Glazed-cracked : gradient gris

### Typographie
- **Display** : Fredoka One (logos, scores, modales, boutons mode)
- **Body** : Nunito 700/800/900 (texte courant)
- **Letter-spacing** légèrement positif sur titres

### Ergonomie tactile
- Tap target minimum : 44×44 px (boutons mode 3D plus larges)
- Drag ghost soulevé `LIFT = 100px` pour ne pas être caché par le doigt
- `FINGER_NUDGE_Y` sur le ghost pour centrage fin
- `-webkit-tap-highlight-color: transparent` partout
- `user-scalable=no` viewport

### Animations clés
- `twinkle` 3s alternate sur stars
- `wallPulse` 1s alternate sur danger wall (+ 0.3s en mode "danger")
- `shimmer` 4s infinite sur blocs filled (delay aléatoire)
- `starsEntry` cubic-bezier(0.34,1.56,0.64,1) sur level complete
- Button active : `transform: translateY(3px)` (effet 3D press)

---

## 6. Roadmap

### v0.1 — Présent ✅
- 3 modes jouables (Adventure, Classic, Dynamic)
- 26 formes de pièces, 7 couleurs
- Drag & drop tactile + souris fluide
- Effets visuels premium (shimmer, orbs, particules, combo flash)
- PWA installable
- Daily streak (UI seulement, persistance partielle)

### v0.2 — Refactoring
- [ ] **Décomposer `index.html` en `src/`** (priorité absolue) :
  - Extraire CSS dans fichiers `.css` modulaires
  - Extraire JS en modules ES6 (state, pieces, grid, modes, ui, drag)
  - Garder `index.html` minimal (shell only)
- [ ] Setup **Vite + TypeScript**
- [ ] Strict mode TS + tsconfig propre

### v1.0 — Backend & social
- [ ] **Audio** : Web Audio API (place piece, line clear, combo, game over, level up, win streak)
- [ ] **Persistance complète** : étoiles par niveau, streak counter, settings (sound on/off)
- [ ] **Leaderboard Firebase** (Firestore) :
  - Best score par mode (Classic + Dynamic)
  - Anti-cheat Cloud Function
  - Pseudo + emoji avatar
- [ ] **Tests Vitest** ≥ 70% sur logique pure (`grid`, `pieces`, `clearLines`, `dynamic` belt physics)
- [ ] **i18n** : minimum FR + EN + ES + DE + PT
- [ ] **Daily challenge** : niveau spécial 1×/jour avec récompense

### v1.1 — Adventure étendu
- [ ] **30 niveaux Adventure** dans 3+ zones (Forêt / Volcan / Royaume)
- [ ] Mécaniques par zone : glazed (déjà), gemmes bonus, blocs explosifs, etc.
- [ ] Cinematics inter-zones (transition narrative)
- [ ] Système de couronnes (méta-récompense au-delà des étoiles)

### v1.2 — Compétition
- [ ] Mode **Compétition** activé (déblocage du badge "Bientôt")
- [ ] Saisons mensuelles avec ligues (Bronze → Diamant)
- [ ] Récompenses cosmétiques (skins blocs, effets particules custom)

### v2.0 — APK + stores
- [ ] **Build APK Capacitor** + soumission Play Store
- [ ] iOS via Capacitor + soumission App Store
- [ ] Achievements natifs Play Games / Game Center
- [ ] Cloud save (Firebase Auth)

### Nice to have
- Mode 2-joueurs en local (split screen)
- Mode coopération asynchrone (un joueur place, l'autre nettoie)
- Replay system (vidéo de la meilleure run partageable)
- Tournois communautaires
- Page promo dans clonex-studio (landing dédiée)

---

## 7. Risques & dépendances

### Risques techniques
- **Tout en `index.html` (2483 lignes)** : maintenance difficile, conflits merge probable, impossible de tester unitairement → **dette technique #1**, refactoring critique avant toute nouvelle feature majeure
- **Pas de TypeScript** : régressions silencieuses sur drag&drop ou state mutations
- **Pas de tests** : risque de casser `canPlace`, `clearLines`, `hasAnyMove` lors de toute évolution
- **Adventure niveaux hardcodés** dans le JS → impossible de modifier sans rebuild
- **Win streak persistance** incomplète (UI affiche `× 0` toujours, à brancher)
- **Pas de service worker offline-first** (cache strategy à confirmer)

### Risques performance
- **65 fonctions globales** dans le scope window → risque de collisions
- **Particles spawnés** sur chaque cell d'une ligne effacée → potentiellement 16+ cells × N particules sur combo = lag possible sur entrée de gamme
- **Shimmer animations infinite** sur toutes les cells filled = compositing GPU permanent

### Risques business
- **Pas de monétisation** : pas de pubs, pas d'IAP, pas de freemium
- **Pas de leaderboard** : pas de viralité naturelle
- **Genre saturé sur stores** : Block Blast original a des dizaines de clones, différenciation par production value (orbs, shimmer, 3 modes) + Adventure narratif

### Dépendances externes
- Google Fonts CDN (Fredoka One + Nunito)
- Aucune API externe actuellement
- Hébergement GitHub Pages

---

## 8. Conventions

### Naming
- **Functions** : camelCase (`startGame`, `canPlace`, `renderPieces`)
- **Constants** : UPPER_SNAKE (`GRID_SIZE`, `COLORS`, `LIFT`, `FINGER_NUDGE_Y`)
- **CSS classes** : kebab-case + BEM-light (`.adv-level-btn.unlocked`, `.modal-score-val`)
- **IDs HTML** : camelCase (`bestScore`, `gameModeLabel`, `dynamicBelt`)
- **Storage keys** : `bbr_<scope>` (`bbr_best`, futur `bbr_streak`, `bbr_stars_<levelId>`)

### Couleurs blocs
- Index 0 = empty (`grid[r][c] === 0`)
- Index 1–7 = couleurs jouables
- String `'glazed'` / `'glazed-cracked'` = blocs spéciaux Adventure

### Modes string ID
- `'classic'` / `'dynamic'` / `'adventure'` (pas d'enum encore, à typer en TS)

### Tests (à créer)
- Unit : `grid.test.ts`, `pieces.test.ts`, `dynamic.test.ts`
- E2E Playwright : flow start → place 3 pieces → game over

### Versioning
- Pas encore de cache busting (HTML statique sans bundler)
- À ajouter avec migration Vite (hash auto)

---

## 9. Résumé exécutif

Block Blast Royal est un **jeu puzzle de blocs** premium-feel actuellement entièrement bundlé dans un seul `index.html` (2483 lignes, 65 fonctions JS, 950 lignes CSS). **3 modes jouables** : Adventure (carte zones + niveaux étoilés + blocs glazed), Classic (infini), Dynamic (ceinture défilante avec mur de danger). **Production visuelle premium** : 26 formes de pièces, 7 couleurs avec gradients 3D, shimmer permanent, orbs flottants canvas, particules sur clear, animations cubic-bezier. **PWA installable** avec manifest + service worker. **Dette technique majeure** : monolithe HTML à décomposer en `src/` modulaire avec Vite + TypeScript avant toute nouvelle feature. **Roadmap** : refactoring → audio + persistance complète → leaderboard Firebase → mode Compétition → APK Play Store. **Différenciation** : 3 modes vs. concurrence mono-mode + Adventure narratif + production value.
