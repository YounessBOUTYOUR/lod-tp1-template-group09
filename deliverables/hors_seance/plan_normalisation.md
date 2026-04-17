# Plan de normalisation et d'identification

## 1. Entités prioritaires

| Type d'entité | Pourquoi est-il prioritaire ? | Identifiant actuel | Stratégie proposée |
| --- | --- | --- | --- |
| **Université** | Point d'ancrage administratif central. Regroupe tous les établissements et détermine le contexte budgétaire/géographique. | Texte libre avec variations (tirets finaux, espaces parasites, ex: Université Mohammed V - Rabat -) | Normaliser la dénomination officielle MESRSI, générer un slug stable, aligner sur Wikidata (wd:Qxxxx) |
| **Établissement** (Faculté/École/Institut) | Unité de granularité réelle du dataset. Porte les effectifs, adresses, contacts. Cœur de la future modélisation RDF. | Aucun identifiant natif. Nom long + acronyme souvent redondant avec la ville (FSJES Agdal Rabat) | Créer une clé composite normalisée (Université + Abrégé + Ville), générer un UUID ou hash persistant pour l'URI locale |
| **Ville** | Ancrage géographique indispensable pour désambiguïser les homonymes (ex: 10+ FSJES, 5+ EST). Point de lien vers GeoNames. | Texte libre avec incohérences orthographiques (Berkene, Fès/Fes, EL Kelâa des Sraghna) | Standardiser selon la nomenclature HCP/GeoNames, mapper directement sur les IDs GeoNames pour géolocalisation et hiérarchie administrative |

## 2. Champs à normaliser

| Champ | Problème observé | Règle de normalisation proposée | Impact attendu |
| --- | --- | --- | --- |
| **Ville** | Variations d'accents, majuscules/minuscules, translittération arabe/française, fautes de frappe (Berkene) | Nettoyage Unicode (NFC), mapping vers une liste de référence HCP/GeoNames, conservation de la forme francophone officielle | Évite les collisions lors du linking, permet un géocodage fiable et des agrégations régionales exactes |
| **Adresse** | Texte libre non structuré, mélange BP/Av./Rte, codes postaux manquants ou mal placés, données AR/FR | Extraction semi-automatique des composants (voie, BP, CP, ville), conservation du texte brut dans schema:streetAddress, ajout de schema:postalCode si détectable | Améliore la recherche, permet l'enrichissement via APIs de géocodage (Nominatim/GeoNames), structure les métadonnées de contact |
| **Téléphone / Fax** | Formats hétérogènes (537772732, 0537 7745 48/49/50, /, espaces, indicatif +212 absent) | Conversion en format E.164 (+212XXXXXXXX), remplacement de / par duplication sur lignes distinctes ou champ multivalué, marquage NA si vide | Données exploitables machine, compatibilité avec les standards téléphoniques internationaux, évite les erreurs de parsing |
| **Effectifs des Etudiants 2023-2024** | Valeurs textuelles (-) au lieu de numériques, risque de confusion entre 0 et donnée manquante | Remplacement de - par NA (ou 0 si contexte métier le valide), conversion en type xsd:integer, gestion des NULL explicites | Permet des requêtes SPARQL quantitatives fiables, évite les biais d'interprétation sémantique |

## 3. Stratégie d'identifiants

| Entité cible | Base de construction envisagée | Risque | Recommandation |
| --- | --- | --- | --- |
| **Université** | slug de la dénomination officielle normalisée : univ-{nom}-{ville} (ex: univ-mohammed-v-rabat) | Fusion/création d'universités, changements de nom officiels | Utiliser un URI persistant https://id.data.gov.ma/universite/{slug} + propriété schema:name + owl:sameAs Wikidata |
| **Établissement** | Hash SHA-256 ou UUID v4 généré à partir de la clé composite : normalize(Université || Abrégé || Ville) | Homonymie d'acronymes (FSJES, ENSA, EST dans plusieurs villes), changements de nom interne | Ne jamais utiliser l'acronyme seul comme ID. Conserver la clé composite comme skos:altLabel, privilégier l'UUID pour la persistance temporelle |
| **Ville** | Identifiant GeoNames (ex: 2548885 pour Fès, 2538475 pour Rabat) | Multiples entrées GeoNames pour une même ville, IDs dépréciés, limites administratives floues | Utiliser directement sws.geonames.org/{id} comme URI principale. Conserver le nom HCP comme rdfs:label officiel en français |

## 4. Risques et limites

**Quels champs sont trop faibles pour servir d'identifiant ?**
- Adresse : trop variable, non structuré, sujet aux erreurs de saisie et aux mises à jour fréquentes.
- Etablissement (Abr) : contient souvent la ville (FSJES Salé), ce qui en fait une clé composite implicite mais non normalisée. Seul, il génère des collisions massives.
- Effectifs : donnée quantitative fluctuante, ne peut en aucun cas jouer le rôle d'identifiant structurel.

**Quelles collisions ou ambiguïtés sont possibles ?**
- **Homonymie institutionnelle** : EST, ENSA, ENCG, FSJES existent dans presque chaque région. Sans la dimension Ville ou Université, l'appariement est impossible.
- **Variations orthographiques** : Berkene vs Berkane, Fès vs Fes, Marrakech vs Marrakesh. Ces écarts cassent les jointures automatiques (= ou LIKE).
- **Bilinguisme non aligné** : Les colonnes arabes (المؤسسة, الجامعة) sont précieuses pour le LOD multilingue, mais leur encodage ou translittération peut diverger des référentiels internationaux.

**Quelles informations supplémentaires seraient nécessaires ?**
- Un **code institutionnel officiel MESRSI** (type SIRET/RNA français) pour chaque établissement, garantissant l'unicité indépendante du nom.
- Les **coordonnées GPS précises** (geo:lat, geo:long) pour chaque campus, souvent absentes ou à approximer via l'adresse textuelle.
- Les **URL des sites web officiels** pour faciliter le rdfs:seeAlso et la validation automatique des entités.
- Une **colonne de versionnement temporel** (année_universitaire, date_ouverture, statut_juridique) pour gérer les créations/fusions dans un graphe LOD évolutif.