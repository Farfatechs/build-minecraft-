# Décisions

## 2026-11-25 — Coquille : Electron
- Décidé par Romain.
- Raison : même moteur Chromium que les tests de référence ; Node déjà installé ; code identique pour Mac et Linux plus tard.
- Coût accepté : installateur d'environ 90 Mo.

## 2026-11-25 — Stockage : un fichier par projet
- Décidé par Romain.
- Dossier : `Documents\Schéma Build Minecraft\Projets\`, un fichier `<nom>.sbm.json` par projet (structure importée incluse).
- Préférences : `%APPDATA%\Schéma Build Minecraft\interface.json`.
- Écriture atomique, copie de secours `.bak`, plan illisible gardé à part dans `Projets\Illisibles\`.
- Nouveau libellé : « Sauvegarde automatique » (remplace « Copie en ligne »).

## 2026-11-25 — Distribution : dépôt GitHub public + Releases
- Décidé par Romain.
- Raison : téléchargement sans compte, gratuit.
- Licence : à décider.
