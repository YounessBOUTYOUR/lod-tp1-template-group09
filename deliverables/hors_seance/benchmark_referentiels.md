## Benchmark des référentiels externes
## Identification
**Groupe :** Groupe-09  
**Jeu de données étudié :** Etablissements de l'enseignement supérieur universitaire public au Maroc (2024-2025) - MESRSI  

**Types d'entités retenus :**
1. Université
2. Ville
3. Établissement (faculté/école)

## 1. Référentiels comparés

| Référentiel | URL | Types d'entités couverts | Intérêt potentiel | Limites observées |
|-------------|-----|--------------------------|-------------------|-------------------|
| **Wikidata** | https://www.wikidata.org | Universités, Villes, Établissements | Identifiants stables, multilingue, liens vers d'autres bases | Couverture incomplète pour certaines facultés marocaines |
| **GeoNames** | https://www.geonames.org | Villes, Régions, Coordonnées géo | Identifiants géographiques standards, hiérarchie administrative | Ne couvre pas les entités "établissement" |
| **Data.gov.ma (Référentiel MESRSI)** | https://data.gov.ma | Universités, Établissements publics | Source officielle, données à jour, contexte national | Pas d'identifiants stables, format peu structuré |

## 2. Critères de comparaison

Nous avons utilisé les critères suivants pour comparer les référentiels :

1. **Couverture** : Le référentiel contient-il les entités marocaines pertinentes ?
2. **Qualité des identifiants** : Les URI/IDs sont-ils stables, persistants et dereferenceables ?
3. **Précision sémantique** : Les labels et descriptions permettent-ils une désambiguïsation fiable ?
4. **Facilité d'appariement** : Peut-on matcher automatiquement ou faut-il un travail manuel ?
5. **Richesse des informations** : Le référentiel apporte-t-il des attributs complémentaires utiles (coordonnées, liens, types) ?
6. **Licence et réutilisabilité** : Les données sont-elles ouvertes et compatibles avec notre projet LOD ?

## 3. Cas d'appariement manuel

Documentez au moins `6` cas.

| Entité locale | Type | Référentiel cible | Correspondance candidate | Indices utilisés | Niveau de confiance | Décision |
|--------------|------|-------------------|-------------------------|------------------|---------------------|----------|
| Université Mohammed V - Rabat | Université | Wikidata | Q2915243 (Mohammed V University) | Nom exact + ville + domaine | Très élevé |  Appariement validé |
| Université Hassan II - Casablanca | Université | Wikidata | Q1569037 (Hassan II University of Casablanca) | Nom + ville + site web | Très élevé |  Appariement validé |
| Rabat | Ville | GeoNames | 2538475 (Rabat, Morocco) | Nom exact + pays + coordonnées | Très élevé |  Appariement validé |
| Fès | Ville | GeoNames | 2548885 (Fès, Morocco) | Orthographe avec/sans accent | élevé |  Appariement validé (normalisation nécessaire) |
| FSJES Agdal Rabat | Établissement | Wikidata | *Aucune correspondance directe* | Recherche par nom complet + acronyme | moyen | Pas d'appariement possible actuellement |
| Ecole Mohammadia d'Ingénieurs Rabat | Établissement | Wikidata | Q3048450 (Mohammadia School of Engineering) | Nom anglais + ville + type | élevé |  Appariement validé après traduction |

**Justification des indices utilisés :**
- **Nom exact** : correspondance string-to-string après normalisation (minuscules, suppression des accents)
- **Ville** : attribut discriminant pour lever les ambiguïtés (ex: plusieurs "FSJES" dans différentes villes)
- **Acronyme** : utile pour les établissements connus (ex: "EMI", "ENSIAS", "ENCG")
- **Type d'établissement** : filtre sémantique (faculté vs école d'ingénieurs vs institut)

**Ambiguïtés rencontrées :**
- Orthographe variable : "Fès" vs "Fes", "Marrakech" vs "Marrakesh"
- Homonymes : "FSJES" existe dans 10+ villes → la ville est un critère obligatoire
- Traductions : certains établissements n'existent en Wikidata qu'en anglais/arabe

## 4. Synthèse

### Référentiel le plus utile par type d'entité

| Type d'entité | Référentiel recommandé | Pourquoi ? |
|--------------|------------------------|------------|
| Université | Wikidata | Couverture quasi-complète des 12 universités marocaines, identifiants stables |
| Ville | GeoNames | Standards géographiques, hiérarchie administrative, coordonnées GPS |
| Établissement | Wikidata + MESRSI | Wikidata pour les écoles prestigieuses, MESRSI/data.gov.ma pour exhaustivité nationale |

### Cas ambigus restants
- Les instituts de recherche (ex: Institut Scientifique Rabat) n'ont pas toujours d'équivalent dans Wikidata
- Les nouvelles écoles (ex: ENSIAD Berkane, 2024) ne sont pas encore référencées
- Les facultés avec noms génériques ("Faculté des Sciences") nécessitent la ville comme clé composite

### Champs les plus utiles pour l'appariement
1. `Université` + `Ville` → clé composite fiable pour désambiguïser
2. `Etablissement (Abr)` → utile pour les acronymes connus (EMI, ENSIAS, ENCG...)
3. `Adresse` → permet une géolocalisation fine via GeoNames/Nominatim