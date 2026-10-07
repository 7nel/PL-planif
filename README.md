# PL-planif’

Outil de classe et minuteur — page web unique, sans compte ni serveur : tout est enregistré localement dans le navigateur (aucune donnée n'est envoyée où que ce soit).

Pensé pour un enseignant ou un·e éducateur·rice spécialisé·e qui prépare et anime le déroulé minuté d'une séance (individuelle, petit groupe ou classe) : étapes chronométrées, vue à projeter devant les élèves, tâches et devoirs, outils annexes (tirage au sort, feu de circulation, sonomètre, émotions, groupes d'élèves...).

## Utiliser l'application

**En ligne (recommandé)** : si ce dépôt est publié via GitHub Pages, ouvrez simplement l'adresse du site (voir en haut de la page GitHub, ou Settings → Pages une fois activé).

**Hors ligne et installable** : une fois le site ouvert une première fois en ligne, il fonctionne sans réseau et peut être ajouté à l'écran d'accueil (téléphone, tablette) ou installé comme une application (Chrome, Edge).

**En local** : téléchargez le dépôt et ouvrez `index.html` directement dans un navigateur (double-clic). Gardez le dossier `fonts/` à côté du fichier pour retrouver les polices ; sans lui, l'application fonctionne avec les polices du système. Aucune installation, aucun serveur nécessaire.

Un guide d'utilisation complet est intégré à l'application : bouton **« ? »** en haut de l'écran d'accueil.

## Données et confidentialité

Toutes les données (programmes, élèves, réglages) sont stockées uniquement dans le navigateur de l'appareil utilisé (`localStorage`), sans compte ni synchronisation en ligne. Changer d'appareil ou de navigateur repart donc de zéro — utilisez la fonction **Sauvegarder** (export `.json`) depuis l'écran d'accueil pour transporter ou sécuriser un programme, et **Importer un fichier de sauvegarde** pour le récupérer ailleurs.

## Polices

Les polices Atkinson Hyperlegible, Lexend et Andika sont fournies dans le dossier `fonts/` (licence libre SIL OFL) : aucune requête vers Google ni vers un autre service. Elles sont proposées comme options d'apparence dans l'éditeur.

## Licence

Ce projet est distribué sous licence **Creative Commons Attribution – Pas d'utilisation commerciale 4.0 International (CC BY-NC 4.0)**. Voir le fichier [LICENSE](LICENSE).

En résumé : vous pouvez utiliser, partager et adapter cet outil librement (par exemple pour votre propre classe ou votre établissement), à condition de créditer l'auteur·e d'origine et de ne pas en faire un usage commercial. Voir le texte complet de la licence pour les conditions exactes.
