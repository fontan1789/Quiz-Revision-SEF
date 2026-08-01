# Quiz-Revision-SEF
Un outil de révision du SEF très complet,
doté d’une interface claire, d’explications pédagogiques détaillées 
et d’un système intelligent de suivi des progrès qui cible les thèmes à renforcer.
[README.md](https://github.com/user-attachments/files/30613882/README.md)
# Révise le SEF avec ClaudIA

Application web d'entraînement aux enchères de bridge, couvrant le **SEF 2024** (Système d'Enchères Français). 453 donnes réparties en 29 thèmes, de l'ouverture de 1 en majeure au Lebensohl.

Un fichier HTML autonome, sans dépendance ni serveur. Installable sur téléphone, utilisable hors ligne.

## Utilisation

L'application présente une main, la vulnérabilité et la séquence d'enchères déjà prononcée. Vous annoncez ce que vous diriez à la table, puis vous comparez avec la réponse du SEF et son commentaire.

Trois modes :

| Mode | Réservoir | Score |
|---|---|---|
| 🎯 **Révision guidée** | 420 donnes, 24 thèmes du SEF strict | oui |
| 📚 **Par thème** | le thème choisi, sans répétition | non |
| 🎲 **Jouer en situation** | toute la banque, annexes hors SEF comprises | non |

Les séries font cinq donnes. En révision guidée, passé trente réponses, les thèmes où vos scores sont les plus faibles ressortent plus souvent.

**Le score n'est tenu qu'en révision guidée**, et c'est délibéré : un chiffre ne vaut que si l'échantillon reste comparable d'une fois sur l'autre. En mode par thème, c'est vous qui choisissez — cinq manches sur votre meilleure convention flatteraient le résultat sans rien dire de votre niveau. En jeu en situation, le tirage mêle des conventions hors SEF que vous ne jouez peut-être pas. Les deux autres modes servent à apprendre, pas à se noter ; rien n'y est enregistré.

### Notation des cartes

Les mains s'affichent au choix en français (A R D V 10) ou en anglais (A K Q J 10) — un bouton bascule d'un système à l'autre. Les commentaires restent en français. Pour la saisie des réponses, la lecture française l'emporte sur les caractères ambigus : `C` désigne Cœur et non Clubs, `P` désigne Pique et non Pass.

### Les deux signalements

**Bandeau ⚠️ hors SEF.** Certaines réponses relèvent d'un usage largement répandu plutôt que de la lettre du SEF 2024 — l'ouverture de 2SA avec une majeure cinquième, par exemple. Ces donnes portent un bandeau qui explique donne par donne en quoi la réponse s'écarte du texte.

**Réponse défendable.** 44 donnes admettent une seconde annonce sérieuse. Quand vous la trouvez, l'application le signale et explique ce que chaque voie privilégie et ce qu'elle concède. La donne reste comptée comme ratée : le but est de faire connaître la réponse du SEF, pas de valider toutes les écoles.

## Les 29 thèmes

**Enchères compétitives** — Contre d'appel · Contre punitif · Barrages · Interventions simples · Sacrifice et loi des levées totales · Réponse après contre d'appel · Bicolore d'intervention (Unusual 2SA / Michaels) · Deuxième tour après intervention · Deuxième tour : deux réponses défendables

**Ouvertures et réponses** — Ouverture 5 cartes majeure · Réponses à l'ouverture majeure · Ouverture 1SA · Ouverture 2SA et développements · Ouvertures faibles 2/3 · Faible majeure et OGUST · Fort 2♣ · Réponses à l'ouverture de 2♦ par As · Réponse au barrage

**Conventions** — Stayman · Texas / Jacoby · Splinters · RKCB 1430 et Gerber · Lebensohl · Check-back Stayman et nouvelle mineure forcing · Rubensohl

**Développements** — Redemande de l'ouvreur · Deuxième tour en silence adverse · Réveil et réponse en quatrième position

**Annexe hors SEF** — 2♦ Multi

## Installation sur téléphone

L'application est une PWA : elle s'installe depuis le navigateur et fonctionne ensuite hors ligne.

- **Android** : un bouton « Installer sur mon téléphone » apparaît sur la page de garde.
- **iOS** : Partager → Sur l'écran d'accueil.

## Déploiement

Quatre fichiers à la racine du dépôt, servis en statique (GitHub Pages convient) :

```
index.html              l'application entière — banque, moteur, interface, styles
manifest.webmanifest    nom, icônes, couleur de thème, mode standalone
sw.js                   service worker — mise en cache pour le hors-ligne
icon-180.png            icône d'accueil iOS
```

Aucune étape de construction, aucune dépendance externe, aucun appel réseau. Ouvrir `index.html` en local suffit à faire tourner l'application.

Après une mise à jour du contenu, pensez à faire évoluer la version du cache dans `sw.js`, faute de quoi les appareils déjà installés continueront de servir l'ancienne copie.

## Sauvegarde des données

Prénom du joueur, score cumulé et historique des sessions guidées sont conservés **sur l'appareil** — `window.storage` quand l'application tourne dans un artifact Claude, `localStorage` préfixé `sef363:` partout ailleurs. Rien n'est transmis, aucun compte n'est nécessaire, aucune donnée ne quitte le navigateur.

Les statistiques de cette application sont indépendantes de celles du quiz « Le deuxième tour d'enchères avec ClaudIA », qui vit dans son propre dépôt et tient son propre score.

## Contrôle de la banque

Un validateur s'exécute au chargement de la page et vérifie chaque donne : 13 cartes réparties en quatre couleurs, caractères de rang admis, réponse et catégorie renseignées, thème connu, identifiant unique, séquence d'enchères croissante, réponse supérieure à la dernière annonce, alternative distincte de la réponse retenue, et pas d'ouverture de 2 en majeure faible avec quatre cartes dans l'autre majeure. Les anomalies bloquantes s'affichent en bandeau rouge en tête de page ; les redondances de contenu — deux donnes de même main et même séquence — sortent en avertissement dans la console, sans gêner le fonctionnement.

Ce filet existe parce que la banque est saisie à la main : une main à douze cartes est vite arrivée et se remarque mal à la relecture.

## Structure de la banque

Chaque donne est un objet JavaScript dans le tableau `bank` :

```js
{
  p: "Sud",                       // votre position
  v: "Personne",                  // vulnérabilité
  h: ["AK852","AQ7","K93","4"],   // main : ♠ ♥ ♦ ♣
  a: "Partenaire 1♥ - ?",         // séquence déjà prononcée
  q: "Que répondez-vous ?",
  c: "2SA",                       // réponse SEF
  cat: "other",                   // "comp" (compétitif) ou "other"
  theme: "rep-majeure",           // clé de thème
  id: "rep-majeure-14",           // identifiant stable
  alt: [{ c: "3♥", e: "..." }],   // facultatif — seconde réponse défendable
  hors: "...",                    // facultatif — écart au SEF strict
  e: ["..."]                      // commentaire
}
```

Les thèmes sont déclarés à part dans `themeDefs`, avec leur clé, leur libellé, leur catégorie et l'indicateur `classic` qui désigne le réservoir de la révision guidée. Les comptes de donnes sont dérivés au chargement, jamais écrits en dur.

Pour ajouter des donnes, complétez `bank` en donnant à chacune un `theme` existant et un `id` libre : rien d'autre n'est à mettre à jour. Le validateur signalera au chargement ce qui manque.

## Licence et statut

Projet personnel d'entraînement, écrit avec l'aide de Claude (Anthropic). Le contenu suit le SEF 2024 tel qu'il est enseigné en club ; les écarts assumés sont signalés dans l'application. Les corrections de fond sont bienvenues.
