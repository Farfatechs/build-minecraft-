# Étape 0 — Lecture et propositions (2026-11-25)

Aucun code écrit. Ce document répond à la section 5 du prompt. Une seule décision est demandée à la fois (voir la fin).

## A. Dépendances trouvées dans `app-source/index.html`

Recherche complète de `window.claude`, `fetch(`, URLs externes, stockage.

| Dépendance | Lignes (approx.) | Remplacement |
|---|---|---|
| `window.claude.use('db')` + `'user'` | 4207 | Fichiers dans le dossier de données |
| Attente de `window.claude` | 4252-4255 | Démarrage direct |
| `window.claude.use('downloads')` | 12536 (export `.nbt`, dans un `.zip`), 13249, 13906 (PNG) | « Enregistrer sous » du système, `.nbt` direct |
| Google Fonts (3 balises `<link>`) | 523-525 | Police embarquée (Atkinson Hyperlegible : licence SIL OFL, à joindre) |
| `localStorage` | `sbm.projets.index.v1`, `sbm.projet.<id>`, `sbm.projet.illisible.<id>`, `sbm.interface.v1`, structure importée par projet (`cleStructureLocale`, ~13526) | Fichiers pour les projets et la structure importée ; préférences d'interface en fichier aussi |
| `sessionStorage` | `sbm.historique.v1` (Annuler / Rétablir) | Peut rester tel quel (propre à la fenêtre, jamais en ligne) |
| `navigator.clipboard` | 10841, 12250, 12573, 13194 | Fonctionne dans l'application ; à tester |

Point à noter : le stockage réel est **multi-projets** (index + un plan par projet), pas un seul « projet-courant ». La « copie en ligne » n'est que la copie claude.ai.

Aucun `fetch`, aucun `XMLHttpRequest`, aucun `confirm()`/`alert()`/`prompt()`.

## B. Technologie : Electron (recommandé) ou Tauri 2

| Critère | Electron | Tauri 2 |
|---|---|---|
| Taille installateur | ~90 Mo | ~5-10 Mo |
| Outils sur ton PC | Node (déjà installé, v24) | Rust + Build Tools Visual Studio (absents) |
| Moteur d'affichage | Chromium embarqué, identique partout | WebView2 sur Windows, WebKit sur Mac/Linux |
| Tests de référence (Chrome) | Même moteur : résultats valables | Moteur différent sur Mac/Linux |
| Fichiers, boîtes natives | Oui, simple | Oui, simple |
| Mises à jour | `electron-updater` + GitHub Releases | Plugin updater + GitHub Releases |
| SmartScreen (.exe non signé) | Avertissement | Avertissement |
| Mac / Linux ensuite | Même code, même rendu ; Mac exige un compte Apple Developer (99 $/an) pour éviter le blocage | Même code, mais rendu WebKit à retester |

Recommandation : **Electron**. Raison : l'application fait 14 000 lignes testées sur Chrome ; un moteur identique partout évite les régressions, et Node est déjà installé.

Signature du code : un certificat Windows coûte ~100-400 €/an. Sans lui, SmartScreen affiche « Windows a protégé votre PC » ; le guide `docs/installer.md` expliquera « Informations complémentaires » puis « Exécuter quand même ».

## C. Stockage (proposition)

- Dossier : `Documents\Schéma Build Minecraft\Projets\`.
- Un fichier par projet : `<nom>.sbm.json` (le plan, format version 1, lisible, copiable à la main). La structure importée est incluse dans le même fichier.
- Préférences : `%APPDATA%\Schéma Build Minecraft\interface.json`.
- Sauvegarde automatique visible, écriture atomique (fichier temporaire puis renommage). Copie de secours `.bak` du fichier précédent.
- Plan illisible : renommé `<nom>.illisible.json` dans `Projets\Illisibles\`, jamais écrasé.
- Libellé proposé pour remplacer « Copie en ligne » : « Sauvegarde automatique ».

## D. Distribution

| Option | Conséquence |
|---|---|
| Dépôt public + GitHub Releases (recommandé) | Gratuit, téléchargement en un clic, mises à jour automatiques possibles, code visible par tous |
| Dépôt privé | Les amis ne peuvent pas télécharger sans compte GitHub ; mises à jour compliquées |
| Page de téléchargement à part | Hébergement et maintenance en plus |

Licence du code : MIT (libre, simple) ou « tous droits réservés » (personne ne peut réutiliser). À toi de choisir.

## E. Mises à jour

Recommandation : **manuelles au début** (lien vers la dernière version dans un message du panneau, rien d'envoyé). Les mises à jour automatiques demandent une vérification réseau et sont mieux si le code est signé. Reportable à l'étape 7.

## F. Association de fichiers `.nbt`

Utile : double-clic sur un `.nbt` ouvre l'import. Coût : faible (réglage de l'installateur + gestion de l'argument de lancement). Risque : `.nbt` est aussi utilisé par d'autres outils (conflit possible). Proposition : **non au début**, ajout possible plus tard.

## G. Découpage de `index.html` (plus tard)

Ordre proposé : (1) CSS en fichiers ; (2) données du catalogue (blocs, matériaux) ; (3) modèle + stockage ; (4) `.nbt` import/export ; (5) vues 2D/3D ; (6) interface. Un module à la fois, avec les tests de l'étape 1 comme filet. Pas pendant le portage.

## H. Hypothèse à confirmer

Ce dépôt devient la source du projet. L'artefact claude.ai reste figé à `1795628306-8efb`.
