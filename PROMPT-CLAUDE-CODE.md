# Prompt pour Claude Code — Schéma Build Minecraft en application de bureau

## 0. Comment travailler avec moi
- Je suis Romain. Je conçois l'architecture, je décide, je valide. Toi, tu écris le code, tu testes, tu proposes.
- Tutoie-moi. Phrases courtes, voix active, listes. Aucune analogie. Aucune question rhétorique.
- Chaque décision : écrite avec sa raison, dans `DECISIONS.md`.
- Les choix structurants (technologie, stockage, distribution) : tu proposes, tu attends mon accord avant de coder.
- Si le fil devient long (réponses plus lentes, contexte chargé) : finis l'étape en cours, fais un commit, écris `REPRISE.md` (fait, reste à faire, décisions ouvertes), puis dis-moi d'ouvrir une nouvelle session.

## 1. Origine du projet
- Projet né le 2026-11-19 sur claude.ai, dans un projet « Artefacts Schéma Build Minecraft ».
- Concept : un « Sweet Home 3D » pour Minecraft. On dessine des volumes (murs, sols, plafonds, portes, fenêtres, escaliers, objets, toits), pas des blocs un par un.
- Grille : 1 case = 1 bloc. Axes de Minecraft : `x` vers l'est, `z` vers le sud, `y` vers le haut. Les nombres du plan sont ceux de F3.
- Public : moi, le staff et les amis de mes serveurs Discord. Les schémas doivent se lire sans explication.
- But de l'import / export : échanger des projets entre nous, au format `.nbt` (structure vanilla Minecraft Java 1.21.1, DataVersion 3955).
- Manière de s'en servir reprise d'un autre artefact à moi, « Schémas d'usines Create » : catalogue à gauche, plan au centre, fiche à droite, bandeau d'outils, glisser-déposer, sélection multiple, copier / coller, onglets de projets.
- Construit par étapes avec Claude sur claude.ai, un chat par étape :
  1. Modèle de données, grille, murs.
  2. Plan 2D, étages, sols, plafonds.
  3. Portes, fenêtres, escaliers.
  3 bis. Interactions « comme Create ».
  4. Vue 3D, isométrique, coupes de côté, plan de coupe.
  5. Matériaux (105 blocs, escaliers, dalles), liste de ressources, export `.nbt`.
  6. Import `.nbt` (structure importée fixe + ajouts par-dessus ; un `.nbt` exporté par l'outil se recharge modifiable grâce à la clé `schemaBuildMinecraft`).
  7. Finitions : tuto, aides, glossaire, tailles de texte.
  8. Objets (lit, bibliothèque, tables), escalier en colimaçon, double porte, étage automatique, formes vraies des escaliers et dalles, toit automatique sur emprise rectangulaire. Puis refonte visuelle (thème Jour / Nuit, colonnes réglables, trames calmes).

## 2. État du code
- Fichier fourni : `app-source/index.html`. C'est la version publiée `1795628306-8efb` de l'artefact (2026-11-25).
- 13 975 lignes, 913 Ko. HTML, CSS et JavaScript en ligne. Aucune bibliothèque.
- Le commentaire « MODÈLE DE DONNÉES » dans `<head>`, juste après `<meta charset>`, fait foi pour le format. Lis-le en premier. Format `schema-build-minecraft`, version 1.
- Accès de test en lecture : `window.schemaBuild` (`lireProjet()`, `lireEtat()`, `toit(id)`…).
- Pas encore fait (à reprendre plus tard, dans ce dépôt) :
  - étape 8, lot 6 partie 2 : toit automatique sur emprises en L, T, U, croix (noues, jonctions d'ailes) ;
  - étape 8, lot 7 : finitions (tuto des nouveautés, glossaire, guide du staff, audit complet).
- Lis le code par morceaux. Cherche par nom de fonction (`grep -n`), pas par numéro de ligne.

## 3. Le but
Faire de cet artefact une application de bureau :
- installée et utilisée par moi ;
- téléchargeable et installable par d'autres (staff, amis), sans compte Claude, sans compétence technique ;
- complète hors ligne ;
- cible principale : Windows 10 et 11. Mac et Linux : dis-moi ce que ça coûte en plus, je déciderai.

À partir de ce travail, **ce dépôt devient la source du projet**. L'artefact claude.ai reste figé à `1795628306-8efb`. Hypothèse à me confirmer à l'étape 0.

## 4. Dépendances à claude.ai trouvées dans le code (à remplacer)
| Dépendance | Où (chercher) | Rôle actuel | À remplacer par |
|---|---|---|---|
| `window.claude.use('db')` et `window.claude.use('user')` | `Promise.all([window.claude.use('db')…` | « Copie en ligne » privée par personne sur le compte claude.ai, document `data/users/<id>/projet-courant`, 256 Kio max | sauvegarde dans des fichiers du dossier de données de l'application (à proposer) |
| Attente de `window.claude` | `typeof window.claude.use === 'function'` | texte « Programme pas encore démarré… » tant que l'hôte n'est pas prêt | démarrage direct ; garder le cadre « Erreur du programme » |
| `window.claude.use('downloads')` (3 endroits) | `window.claude.use('downloads')` | export `.nbt` (dans un `.zip` à cause de claude.ai), image PNG, sauvegarde texte | boîte « Enregistrer sous » du système ; `.nbt` direct, sans `.zip` |
| Police Google Fonts | `fonts.googleapis.com` | Atkinson Hyperlegible 400 et 700 | police embarquée dans l'application (vérifier la licence et la joindre) |
| `localStorage` | `sbm.projet.v1`, `sbm.interface.v1`, `sbm.projet.illisible` | plan, préférences, copie d'un plan illisible | à décider : fichiers, ou `localStorage` de l'application + fichiers |

Vérifie qu'il n'en existe pas d'autres (recherche complète de `window.claude`, `fetch(`, URLs externes).

## 5. Choix à me proposer (étape 0, avant tout code)
1. **Technologie de la coquille** : compare au moins Tauri 2 et Electron. Critères :
   - taille de l'installateur et de l'application ;
   - outils à installer sur mon PC (Rust, Node…) ;
   - accès aux fichiers et boîtes de dialogue natives ;
   - mises à jour ;
   - signature du code et avertissement Windows SmartScreen pour un `.exe` non signé ;
   - effort pour Mac et Linux ensuite.
   Donne ta recommandation et sa raison.
2. **Stockage** : où vont les projets (dossier, un fichier par projet ou un seul fichier), sauvegarde automatique, copie de secours, plan illisible gardé à part. Un projet doit pouvoir être copié à la main dans l'explorateur Windows.
3. **Distribution** : dépôt GitHub public ou privé, page de téléchargement (GitHub Releases ou autre), licence du code. Je décide ; donne-moi les conséquences de chaque option.
4. **Mises à jour** : automatiques, manuelles, ou rien au début.
5. **Association de fichiers** : ouvrir un `.nbt` par double-clic. Utile ou non, coût.
6. **Découpage de `index.html`** en modules : pas pendant le portage. Propose seulement un plan pour plus tard.

## 6. Exigence absolue : adapté TDAH et autisme (inchangée)
Toute nouveauté de l'application (installation, menus, mises à jour, messages, boîtes de dialogue) la respecte. Sinon : refusée ou redessinée.
- Aucune animation automatique, aucun son. Option « réduire les mouvements » cochée par défaut.
- Jamais la couleur seule : trame, symbole ou texte en plus.
- Interface prévisible : mêmes zones aux mêmes endroits.
- 20 boutons au plus par zone. Du texte sur chaque bouton. Cibles de 44 px au moins (seule exception : la croix ✕).
- Aucun message qui s'efface seul. Aucune fenêtre surgissante. Confirmations et dialogues dans le panneau, pas de `confirm()` ni de fenêtre système de confirmation.
  - Exception admise : la boîte « Enregistrer sous » / « Ouvrir » du système, parce qu'elle vient d'un clic de l'utilisateur.
- Mise à jour : jamais imposée, jamais surgissante. Un message écrit dans le panneau, l'utilisateur choisit quand.
- État courant toujours écrit. Sauvegarde automatique visible, avec son état.
- Tout annulable. Suppressions confirmées. Aucune perte de travail, même en cas d'erreur (disque plein, fichier illisible, fermeture brutale).
- Messages d'erreur précis : ce qui ne va pas, quoi faire.
- Vocabulaire : un mot = un sens. « Copie en ligne » n'existe plus : propose le nouveau libellé, à ajouter au glossaire.
- Pas de compte, pas de télémétrie, aucune donnée envoyée.

## 7. Ce qui ne doit pas changer
- Le modèle de données (version 1). Un plan de l'artefact s'ouvre dans l'application sans perte, et l'inverse.
- L'export `.nbt` : identique bloc par bloc à celui de l'artefact pour un même plan.
- L'import `.nbt`, la liste de ressources, les vues, le tuto, les raccourcis.
- L'agencement « comme Create » : en-tête, bandeau sur une rangée à 1280 px, sous-barre, catalogue, plan, panneau, séparateurs de colonnes.
- Le commentaire « MODÈLE DE DONNÉES » dans `<head>`.

## 8. Mes projets existants
Ils sont dans l'artefact (navigateur + copie en ligne claude.ai). L'application ne peut pas les lire directement.
- Prévois un chemin simple pour les récupérer : export depuis l'artefact (« Sauvegarde texte » ou « Export .nbt »), puis import dans l'application.
- Écris la procédure, en étapes courtes, dans `docs/recuperer-mes-projets.md`.

## 9. Étapes (un commit par étape, je valide entre chaque)
0. **Lecture et plan** : lis le commentaire du modèle, repère les dépendances du tableau de la section 4, réponds à la section 5. Aucune ligne de code. Attends mon accord.
1. **Dépôt** : structure, `index.html` d'origine gardé tel quel dans `reference/` ; tests de référence sur la version d'origine (Playwright, Chrome sans écran, fichier servi en local) : démarrage, mur, porte, sol, escalier, toit, Annuler, vues, ressources, export `.nbt` (fichier de référence gardé), import.
2. **Coquille** : la page s'ouvre dans l'application, hors ligne, police embarquée, sans « Programme pas encore démarré… » bloqué.
3. **Stockage** : remplacement de la copie en ligne. Sauvegarde automatique visible. Tests d'erreur (fichier illisible, disque plein simulé, deux fenêtres).
4. **Fichiers** : export `.nbt`, PNG et sauvegarde texte par « Enregistrer sous » ; import par « Ouvrir ».
5. **Récupération de mes projets** (section 8).
6. **Paquet** : installateur Windows, nom « Schéma Build Minecraft », icône, numéro de version. Guide d'installation pour le staff et les amis : `docs/installer.md` (avec l'avertissement SmartScreen s'il existe, et quoi faire).
7. **Mises à jour** : selon ma décision de l'étape 0.
8. **Contrôle final** : non-régression contre les tests de l'étape 1, export `.nbt` comparé bloc par bloc, audit « Exigence absolue » en tableau (règle, résultat, preuve).

## 10. Critères de réussite
- J'installe l'application avec un seul fichier téléchargé, je l'ouvre sans connexion internet.
- Un ami sans Claude fait pareil avec le guide `docs/installer.md`.
- Mes projets de l'artefact sont dans l'application, sans perte.
- Le `.nbt` exporté par l'application est identique à celui de l'artefact pour le même plan, et se charge dans Minecraft 1.21.1.
- Aucune erreur JavaScript. Aucune requête réseau, sauf la vérification de mise à jour si je l'ai choisie.
- L'audit « Exigence absolue » est conforme sur chaque ligne.

## 11. Documents à tenir à jour dans le dépôt
- `README.md` : ce que fait l'application, comment la construire.
- `DECISIONS.md` : chaque décision, sa raison, sa date.
- `REPRISE.md` : où on en est.
- `docs/installer.md`, `docs/recuperer-mes-projets.md`.
- Ce qui n'a pas pu être testé : écrit, avec une procédure courte pour moi.
