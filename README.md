# Cartographie QA — Bibliothèque de tests RED

> **URL live :** https://zohratouazi.github.io/Cartographie-fonctionnelle-QA  
> **Maintenu par :** Zohra Touazi — Lead QA, Upply  
> **Dernière mise à jour :** Juillet 2026

---

## But du projet

Ce projet est une cartographie interactive de la bibliothèque de tests QA du projet RED (Upply DEV Front-Back). Il permet de :

- **Visualiser** l'ensemble des tests actifs organisés par groupe fonctionnel et sous-thème
- **Prioriser** les tests (P0 → P4) sans toucher à Jira
- **Suivre** la couverture d'automatisation (statut réel Jira : `AUTOMATED` vs `Ouvert`)
- **Partager** une vue claire avec les PM, le manager et l'équipe QA via une simple URL
- **Générer** un rapport de couverture en un clic

---

## Architecture du fichier

Le projet est un **fichier HTML autonome** (`index.html`) — aucun serveur, aucune dépendance externe. Il s'ouvre directement dans n'importe quel navigateur.

```
Cartographie-fonctionnelle-QA/
├── index.html        ← Application complète (HTML + CSS + JS en un seul fichier)
└── README.md         ← Ce fichier
```

---

## Structure des groupes fonctionnels

| Ordre | Groupe | Description |
|-------|--------|-------------|
| 1 | **Connect — Chargeur** | Lots disponibles, lots affrétés, offres reçues, affrètement, blacklist, poste du lot |
| 2 | **Connect — Transporteur** | Recherche de lots, envoi d'offres, offres envoyées, ordres d'affrètement |
| 3 | **SMART** | Benchmark (modes de transport, résultats, exports), Smartscan (unique, récurrent), UFI |
| 4 | **Authentification** | SSO, Inscription, Connexion & mot de passe |
| 5 | **Emails & Notifications** | Emails automatiques transactionnels, centre de notifications |
| 6 | **Grille tarifaire** | Rate cards, surcharges, calcul du prix, date d'effet, changelog |
| 7 | **Atlas** | Crédits individuels, sièges, requêtes, accès Atlas |
| 8 | **Mobile** | Lots live & affrétés sur application mobile (iOS & Android) |
| 9 | **Jarvis** | Facturation, billing, comptes shippers, dashboard interne |

---

## Règles de gestion

### Statut d'automatisation

Le statut d'automatisation est basé **uniquement** sur le statut Jira réel du ticket de test :

| Statut Jira | Signification | Affichage |
|-------------|---------------|-----------|
| `AUTOMATED` | Test automatisé (Cucumber/Playwright en CI) | Badge vert ⚙ Auto |
| `Ouvert` | Test manuel | Badge gris ✎ Manuel |
| `Abandoned` | Exclu — non affiché dans la cartographie | — |

> ⚠️ **Important :** Un test avec du Gherkin dans sa description n'est **pas** automatisé pour autant. Seul le statut Jira `AUTOMATED` fait foi.

**38 tests AUTOMATED** au moment de la dernière sync Jira (juillet 2026).

### Système de priorités

| Priorité | Couleur | Signification |
|----------|---------|---------------|
| **P0** | Rouge | Critique — bloquant, smoke test, flux principal |
| **P1** | Orange | Important — fonctionnalité clé |
| **P2** | Vert | Normal — cas nominaux secondaires |
| **P3** | Bleu | Mineur — cas limites, edge cases |
| **P4** | Gris | Régression — tri, filtres, affichages secondaires |
| **À définir** | Gris clair | Pas encore priorisé |

**Sources des priorités :**
- **Connect Chargeur, Connect Transporteur, SMART, Auth, Emails** → priorités issues du FigJam existant (`https://www.figma.com/board/yBVgWUz8RqyIfgp98ZNxi9/Cartographie-QA`)
- **Grille tarifaire, Atlas, Mobile, Jarvis** → à définir par l'équipe QA

### Règles d'édition dans l'outil

- Chaque ticket est **cliquable** → ouvre le ticket Jira dans un nouvel onglet
- La priorité se modifie en **cliquant sur le badge de priorité** du ticket → menu déroulant
- Le statut auto/manuel se modifie en **cliquant sur le badge** Auto ou Manuel
- Les tickets peuvent être **déplacés entre colonnes** par drag & drop
- Les modifications sont **sauvegardées dans le localStorage** du navigateur (persistant entre sessions)

---

## Workflow de mise à jour

### Mise à jour du contenu (nouvelles priorités, nouveaux tickets)

1. Ouvrir la cartographie sur GitHub Pages
2. Faire les modifications directement dans l'interface (changement de prio, ajout de ticket)
3. Cliquer **"Exporter JSON"** → télécharger le fichier
4. Envoyer le JSON à l'IA (Claude) avec la demande de mise à jour
5. Récupérer le nouveau `index.html`
6. Glisser le fichier dans le dossier local du repo (remplace l'ancien)
7. GitHub Desktop → **Commit to main** → **Push origin**
8. Attendre 2 minutes → **Ctrl+Shift+R** sur la page pour vider le cache

### Sync depuis Jira (nouveaux tests créés dans Xray)

1. Exporter le JSON actuel depuis la cartographie
2. Envoyer à Claude : *"Sync Jira — ajoute les nouveaux tests du projet RED sans écraser l'existant"*
3. Claude fait la requête JQL, identifie les tickets absents, les ajoute en "À définir"
4. Même workflow push GitHub que ci-dessus

### Mise à jour des tests AUTOMATED

Le statut AUTOMATED est récupéré depuis l'API Jira via le connecteur Atlassian Rovo.
- **CloudId Upply :** `51add53d-1cd1-459d-92c6-6ae235a00cad`
- **JQL de référence :** `project = RED AND issuetype = Test AND status = AUTOMATED`

---

## Fonctionnalités de l'outil

| Fonctionnalité | Description |
|----------------|-------------|
| 🔍 Recherche | Recherche par clé RED-xxxx ou mot-clé dans les titres |
| 🎯 Filtre priorité | Filtrer par P0, P1, P2, P3, P4 ou À définir |
| ⚙ Filtre automation | Afficher uniquement les tests auto ou manuels |
| 📁 Filtre groupe | Filtrer par groupe fonctionnel |
| ⚠ À définir seulement | Bouton rapide pour voir tous les tickets sans priorité |
| 📊 Rapport | Tableau récapitulatif avec répartition par priorité + liste des tests automatisés |
| ➕ Ajouter un ticket | Bouton en bas de chaque colonne |
| ➕ Ajouter un sous-thème | Bouton en bas de chaque groupe |
| ➕ Ajouter un groupe | Bouton "+ Groupe" dans le header |
| 🗑 Supprimer un ticket | Bouton ✕ au survol du ticket |
| ↕ Déplacer un ticket | Drag & drop entre colonnes de priorité |
| 💾 Exporter JSON | Sauvegarde de l'état complet |
| 📥 Importer JSON | Restauration depuis un JSON exporté |
| 🖨 Imprimer | Rendu optimisé pour impression / export PDF |

---

## KPIs de référence (juillet 2026)

| Métrique | Valeur |
|----------|--------|
| Total tests actifs | ~297 (dans la carto) / ~1 585 (bibliothèque complète Jira) |
| Tests automatisés | 38 |
| Tests manuels | ~259 (carto) / ~1 547 (bibliothèque complète) |
| Groupes fonctionnels | 9 |
| Priorités définies | Connect, Transporteur, SMART, Auth, Emails |
| Priorités à définir | Grille tarifaire, Atlas, Mobile, Jarvis |

> **Note :** La cartographie contient une sélection représentative des tests. La bibliothèque complète Jira contient ~1 585 tests actifs. La sync complète est à faire groupe par groupe.

---

## Outils & intégrations

| Outil | Usage |
|-------|-------|
| **Jira Cloud** | `upply1.atlassian.net` — source de vérité des tickets de test |
| **Xray** | Plugin Jira pour la gestion des tests (Test Repository, Test Execution) |
| **Atlassian Rovo** | Connecteur API pour requêtes JQL depuis Claude |
| **FigJam** | Source des priorités P0→P4 pour Connect, SMART, Auth, Emails |
| **GitHub Pages** | Hébergement de la cartographie HTML |
| **GitHub Desktop** | Synchronisation locale → remote |
| **Claude (Anthropic)** | Assistant pour la génération et mise à jour du fichier HTML |

---

## Limitations connues

- **Pas de sync temps réel** — les modifications faites dans la carto ne remontent pas dans Jira, et vice versa. La carto est un outil de visualisation et de priorisation, pas un outil de gestion.
- **localStorage** — les modifications sont sauvegardées localement dans le navigateur. Si plusieurs personnes modifient en même temps, seule la version exportée/importée fait foi.
- **Cache navigateur** — après un push GitHub, faire **Ctrl+Shift+R** pour vider le cache et voir la dernière version.
- **Confluence non disponible** — l'instance `upply1.atlassian.net` n'a pas Confluence activé (erreur 404 via Rovo).

---

## Contacts

- **Lead QA / Maintainer :** Zohra Touazi (@ZohraTouazi)
- **Projet Jira :** RED (Upply DEV Front-Back)
- **FigJam original :** https://www.figma.com/board/yBVgWUz8RqyIfgp98ZNxi9/Cartographie-QA
