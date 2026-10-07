# CLAUDE.md — PL-planif’

Ce fichier guide Claude Code pour travailler sur ce dépôt. Langue du projet : **français** (interface, commentaires, commits, README). Réponds et commente en français.

## Vue d'ensemble

**PL-planif’** est un outil de classe et minuteur : une **page web unique, sans serveur, sans compte, sans build**. Il sert à un·e enseignant·e ou éducateur·rice spécialisé·e à préparer et animer le déroulé minuté d'une séance (individuelle, petit groupe, classe) : étapes chronométrées, vue à projeter devant les élèves, tâches et devoirs, et outils annexes (tirage au sort, feu de circulation, sonomètre, émotions, dé, groupes d'élèves, mode de travail).

- Dépôt : `github.com/7nel/PL-planif` (branche principale, publiable via GitHub Pages)
- Licence : CC BY-NC 4.0 (usage non commercial, attribution)
- Confidentialité : **aucune donnée ne quitte le navigateur**. Tout est en `localStorage`. C'est une promesse faite aux utilisateurs (voir README) — ne jamais ajouter de réseau, télémétrie, analytics ou service tiers de données.

## Structure du dépôt

```
index.html   # TOUTE l'application (~5200 lignes, ~310 Ko)
README.md    # Présentation utilisateur, données, polices, licence
sw.js, manifest.json, icon-*.png, fonts/   # hors ligne, installation, polices
LICENSE      # CC BY-NC 4.0
CLAUDE.md    # ce fichier
```

Pas de `package.json`, pas de bundler, pas de tests, pas de linter. Un `.gitignore` écarte `.DS_Store`, `.claude/`, `.codex/`, `.impeccable/` et `impeccable-report.json`.

## Technologies

- HTML/CSS/JavaScript **vanilla**, aucun framework, aucune dépendance npm.
- JavaScript style **ES5** (`var`, `function`, concaténation de chaînes `"a" + b`), avec quelques template literals pour le CSS. `"use strict"` est actif. Reste dans ce style.
- Stockage : `localStorage` (toujours dans `try/catch`).
- Polices : fichiers `.woff2` autohébergés dans `fonts/` (Atkinson Hyperlegible, Lexend, Andika), déclarés par `@font-face` dans `CSS_TEXT`. Aucune ressource externe.
- Hors ligne : `sw.js` (cache local, aucun réseau tiers) et `manifest.json`, servis avec `index.html`. Changer `CACHE` dans `sw.js` à chaque publication qui modifie des fichiers mis en cache.
- Icônes : SVG inline (section « icons »). Icône PWA générée via `<canvas>` en data URL.
- Thème clair/sombre via variables CSS + `prefers-color-scheme` + attribut `data-theme` sur `:root`.

## Architecture de `index.html`

Le fichier est un squelette minimal (`<!DOCTYPE html><html><head>…</head><body><script>`) dont **tout le contenu est un unique `<script>`** :

1. `function boot(initialData) { "use strict"; … }` — toute l'application vit dans cette fermeture (lignes ~2 à ~5136).
2. `boot({ steps, theme, title, students })` en fin de fichier (~ligne 5164) — appelle l'app avec les données par défaut (« Notre après-midi », 4 étapes d'exemple).

Le CSS est une chaîne `CSS_TEXT` injectée dans un `<style>` par le JS ; le DOM est construit par le JS (pas de HTML statique).

Sections principales de `boot()` (repérées par des commentaires `/* ---------- … ---------- */`) :

| Section | Rôle |
|---|---|
| `style` | `CSS_TEXT` : variables, thèmes, mise en page |
| `PWA metadata` | manifest/icône best-effort (ne marche que sur un vrai serveur) |
| `icons` | bibliothèque d'icônes SVG |
| `local state` | clés `localStorage`, variables d'état globales, `esc()`, `catchUp()` |
| `DOM skeleton` | construction unique du DOM |
| `home / profils` | écran d'accueil, gestion des profils, export/import de sauvegarde |
| `editor` | éditeur de déroulé (étapes, tâches, apparence, blocs personnalisés) |
| `persistence` | `persist()`, sauvegarde par profil |
| copier devoirs / bloc « Devoirs et infos » | texte brut à coller, panneau vue projetée |
| éditeur de « leçon » | profils de type `lecon` (pas de déroulé, seulement tâches) |
| outils annexes | feu, mode de travail, tirage, dé, sonomètre, émotions |
| `go` | boucle `setInterval` à 250 ms (rendu, avance d'étape, ding, minuteurs) |

### Modèle de données

- **Profils** : liste dans `PROFILES_KEY`. Chaque profil a un `kind` : `"deroule"` (minuté ou checklist, avec vue projetée) ou `"lecon"` (vue projetée sans déroulé). Données d'un profil : `STORE_KEY_BASE + ":" + id`.
- **Profil** : `steps[]` (`label`, `seconds`, `icon`, `tasks[]`), `theme` (`accent`, `pause`, `font`), `title`, `students[]`, `studentGroups`, blocs personnalisés, etc.
- **Tâche** : `{ name, text, mandatory, homework, dueDate, groupId }`.
- **Clés `localStorage`** (préfixe historique `apres-midi-`, ne pas renommer) :
  `apres-midi-data-v2` (ancien cache mono-profil, **migration seulement**), `apres-midi-profiles-v1`, `apres-midi-timer-v1[:id]`, `-feu`, `-mode-travail`, `-tirage`, `-de`, `-sonometre`, `-emotions`, `-theme-mode`, `-last-backup`, `-transition-sound`, `-actions-visible`.
- **Sauvegarde utilisateur** : export/import `.json` (bouton « Sauvegarder » / « Importer un fichier de sauvegarde »). Le format est un contrat avec les fichiers déjà exportés par les utilisateurs.

## Commandes

Aucun build ni installation.

```bash
# Lancer l'app
open index.html                      # macOS, directement en file://
python3 -m http.server 8000          # optionnel, pour tester PWA / comportement en HTTP
# puis http://localhost:8000

# Vérification de syntaxe JS (node n'est PAS installé ; on utilise jsc, fourni avec macOS)
python3 -c "import json;s=open('index.html',encoding='utf8').read();print('new Function('+json.dumps(s[s.index('<script>')+8:s.rindex('</script>')])+');print(\"OK\");')" > /tmp/check.js \
  && /System/Library/Frameworks/JavaScriptCore.framework/Versions/A/Helpers/jsc /tmp/check.js
# (dans une session Claude Code, écrire check.js dans le scratchpad plutôt que /tmp)

# Repérer une zone
grep -n "function nomDeFonction" index.html
grep -n "/\* ---------- " index.html

# Git
git status && git log --oneline
```

Test manuel = ouvrir la page dans un navigateur et exercer la fonctionnalité modifiée (voir « Vérification »). Le skill `/run` ou l'extension Chrome peuvent piloter la page.

## Conventions de code

- **Un seul fichier** : ne pas découper `index.html` en plusieurs fichiers, ne pas introduire de build, de module ES, de npm ou de framework, sauf demande explicite.
- **ES5 cohérent** : `var`, fonctions nommées, pas de flèches/`let`/`const` dans le code applicatif, sauf si le voisinage en utilise déjà. Imiter l'indentation (2 espaces), les guillemets doubles et le style des lignes voisines.
- **Échappement** : tout texte saisi par l'utilisateur inséré via `innerHTML` doit passer par `esc()`. Préférer `textContent` quand possible. (Le fichier utilise `innerHTML` ~43 fois : rester vigilant.)
- **`localStorage`** : toujours `try { … } catch (e) {}` (navigation privée, quota, `file://`). Lecture avec valeur par défaut ; jamais de plantage si la clé manque ou est corrompue (`JSON.parse` protégé).
- **Rétrocompatibilité des données** : toute nouvelle propriété de profil doit avoir une valeur par défaut à la lecture/import (les anciennes sauvegardes n'ont pas le champ). Ne jamais renommer ni changer le sens d'une clé `localStorage` ou d'un champ exporté sans migration.
- **Nouvel état persistant** : ajouter une constante `*_KEY` dans la section « local state » avec le préfixe `apres-midi-`, et une paire `load…/save…` protégée par `try/catch`.
- **CSS** : utiliser les variables (`--bg`, `--surface`, `--ink`, `--muted`, `--accent`, `--pause-accent`, `--red`, `--border`…) ; toute nouvelle couleur doit fonctionner en **clair et sombre** (définir les deux). Attention à la règle `[hidden] { display:none !important }`.
- **Rendu** : la boucle de 250 ms appelle `render(false)` ; `render(true)` reconstruit la structure. Ne pas alourdir la boucle ; ne pas reconstruire le DOM à chaque tick (perte de focus dans les champs).
- **Commentaires** : en français, expliquant le *pourquoi* (le fichier en contient de longs et utiles — les conserver et les mettre à jour).
- **Textes d'interface** : français, tutoiement/vouvoiement comme l'existant (vouvoiement dans l'aide), apostrophe typographique `’` dans le nom **PL-planif’** (unifiée dans le commit `71bd1c7`). Le guide intégré (`HELP_GUIDE_HTML`, bouton « ? ») doit être mis à jour quand une fonctionnalité visible change.
- **Accessibilité** : public incluant des élèves à besoins spécifiques — polices lisibles (Atkinson, Lexend, Andika), bons contrastes, cibles tactiles larges, vue projetée lisible de loin. Ne pas dégrader.

## Règles de travail pour Claude

1. **Lire avant d'éditer.** Le fichier a des lignes de plus de 17 000 caractères : ne jamais le lire en entier. Localiser avec `grep -n`, puis `Read` avec `offset`/`limit`. Attention aux lignes très longues dans les résultats (`cut -c1-200`).
2. **Édits chirurgicaux** avec `Edit` sur des chaînes uniques ; ne pas réécrire le fichier ni reformater/réindenter du code non concerné (diffs lisibles).
3. **Après chaque modification de JS**, vérifier la syntaxe avec la commande `jsc` ci-dessus. Une erreur de syntaxe casse toute l'application (page blanche).
4. **Vérifier dans un navigateur** dès que possible (fonction touchée, écran d'accueil, éditeur, vue projetée, clair/sombre). Si tu ne peux pas tester, dis-le clairement.
5. **Ne jamais casser les données existantes** des utilisateurs (voir rétrocompatibilité). En cas de doute sur un changement de format, demande.
6. **Pas de réseau ni de tracking.** Ne pas ajouter de CDN de scripts, d'API, d'analytics. Le service worker local (`sw.js`) ne met en cache que les fichiers du dépôt. Toute nouvelle police s'ajoute en fichier dans `fonts/`, jamais depuis un CDN.
7. **Ne pas élargir le périmètre** : pas de refactor, de renommage ou de « nettoyage » non demandé. Signale plutôt les problèmes repérés.
8. **Documentation** : si le comportement visible change, mettre à jour `HELP_GUIDE_HTML` ; si le mode d'usage, les données ou la licence changent, mettre à jour `README.md`.
9. **Licence** : respecter CC BY-NC 4.0 ; ne pas retirer les mentions d'attribution ni introduire de code incompatible avec un usage non commercial.
10. **Git** : ne commiter/pousser que sur demande explicite. Messages de commit **en français**, courts et à l'impératif ou au descriptif (ex. « Cases … déplacées dans le panneau Blocs personnalisés »). Ajouter la ligne de co-auteur demandée par l'environnement. Ne jamais `push --force` ni réécrire l'historique sans accord.
11. **Confirmer avant** toute action difficile à annuler (suppression, réinitialisation d'historique, publication).

## Vérification (checklist rapide)

- [ ] Syntaxe JS valide (commande `jsc`)
- [ ] La page s'ouvre sans erreur dans la console (`file://` inclus)
- [ ] Création / ouverture d'un profil, ajout d'étapes, lancement du minuteur
- [ ] Vue projetée OK ; thème clair et sombre OK
- [ ] Rechargement de la page : les données persistent
- [ ] Export puis import d'une sauvegarde `.json` OK (anciennes sauvegardes comprises)
- [ ] Aide intégrée et README à jour si nécessaire
