# 📊 block-blast-royal — Suivi

> Voir aussi : [CAHIER-CHARGES.md](CAHIER-CHARGES.md) (vision/spec complète)
> Mis à jour : **2026-04-23**

| | |
|---|---|
| **Stack** | Vanilla JS + CSS + HTML monolithique (2483 lignes dans `index.html`, 65 fonctions JS, ~950 lignes CSS inline) |
| **Statut** | 🔴 Prototype monolithique (jouable mais 0 modularité, 0 tests) |
| **Type** | Jeu puzzle de blocs arcade — 3 modes (Adventure / Classic / Dynamic) |
| **Tagline** | Place. Détruis. Monte sur le trône. |
| **URL** | (déployable via `clonex-studio/block-blast-royal.html`) |

---

## ✅ Fait

### Modes de jeu
- **Adventure** : carte zones + niveaux + objectifs + étoiles 0–3 + blocs glazed (cracking en 2 hits)
- **Classic** : mode infini, 3 pièces tray, replace après 3 utilisées
- **Dynamic** : ceinture défilante temps-réel + mur de danger + speed badge
- **Compétition** : annoncé (badge "Bientôt", `showComingSoon()`)

### Mécaniques core
- Grille **8×8**, **7 couleurs** de blocs
- **26 formes de pièces** (1×2 à 1×5, 2×2, 3×3, T×4, L 4-briques×4, L coin 5-briques×4, S, Z, etc.)
- Drag & drop tactile + souris unifié
- Ghost preview soulevé `LIFT = 100px` (visible au-dessus du doigt)
- Highlight valid drops + preview placement (cellules fantômes colorées)
- Détection lignes complètes (rangs + colonnes simultanées)
- Combo flash si 2+ lignes effacées
- Game over auto si aucun placement possible

### UI / écrans (12 sections .screen)
- Splash (logo 3D animé "BLOCK 👑 BLAST ROYAL" + loader bar 2.5s)
- Home (logo XL + win streak counter + 4 boutons mode 3D)
- Game (header score + couronne + objectif Adventure + grille + tray/belt)
- Adventure Map (zones empilées avec progress)
- Modals : Game Over + Level Complete (étoiles cubic-bezier)

### Effets visuels premium
- Shimmer infinite sur cells filled (4s avec delay aléatoire)
- 12 orbs flottants canvas en background (5 hues bleu/violet)
- 50 stars twinkle sur splash + home + map
- Background gradients radial + linear navy
- 3D buttons avec ombres + inset highlights + translateY active
- Particles colorées spawnées sur chaque cell d'une ligne effacée
- Wall pulse animation sur mur de danger Dynamic mode

### PWA
- `manifest.json` standalone portrait, theme `#0d1b4b`
- `sw.js` service worker enregistré
- 2 icons SVG (192/512)

### Persistance
- `localStorage.bbr_best` (best score Classic seulement)

### Documentation
- CAHIER-CHARGES.md rétrospectif (vision, personas, périmètre, architecture, charte UI, roadmap, risques)

## 🔄 En cours

- Aucun (état figé en prototype monolithique)

## 📋 À faire (priorité décroissante)

### Critique
1. ~~Créer `CAHIER-CHARGES.md`~~ ← fait dans cette session
2. **Décomposer `index.html` (2483 lignes)** en `src/` modulaire :
   - Extraire CSS (~950 lignes inline) en fichiers `.css` thématiques
   - Extraire JS (65 fonctions) en modules ES6 (state, pieces, grid, modes/, ui/, drag, render)
   - Garder `index.html` minimal (shell only)
3. **Setup Vite + TypeScript strict** (recette chrome-messenger)

### Important
4. **Tests Vitest** ≥ 70% sur logique pure : `canPlace`, `clearLines`, `hasAnyMove`, `randPiece`, dynamic belt physics
5. **Audio Web Audio API** : place piece, line clear, combo, game over, level up, win streak
6. **Persistance complète** :
   - Étoiles par niveau Adventure (`bbr_stars_<levelId>`)
   - Daily streak counter (actuellement affiché × 0 statique)
   - Settings utilisateur (sound on/off, vibrations)
7. **Leaderboard Firebase Firestore** :
   - Best score par mode (Classic + Dynamic)
   - Anti-cheat Cloud Function (cohérence score/temps)
   - Pseudo + emoji avatar
8. **i18n** : minimum FR + EN + ES + DE + PT
9. **30 niveaux Adventure** dans 3+ zones thématiques (Forêt / Volcan / Royaume)
10. **Audit Lighthouse ≥ 90** + accessibilité WCAG AA

### Nice to have
11. Mode **Compétition** complet (saisons mensuelles, ligues Bronze→Diamant, récompenses cosmétiques)
12. Daily challenge (1 niveau spécial/jour avec récompense)
13. Skins blocs déblocables + effets particules custom
14. Build APK Capacitor + soumission Play Store
15. iOS via Capacitor + soumission App Store
16. Achievements natifs Play Games / Game Center
17. Cloud save (Firebase Auth)
18. Mode 2-joueurs en local (split screen)
19. Mode coop asynchrone (un joueur place, l'autre nettoie)
20. Replay system (vidéo best run partageable)
21. Page promo dédiée dans clonex-studio (actuellement landing existe mais à enrichir)
