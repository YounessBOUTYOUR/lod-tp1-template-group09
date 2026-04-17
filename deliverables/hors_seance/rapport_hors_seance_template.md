# Rapport hors séance - TP1

## Page de garde

- **Module** : Linked Open Data
- **TP** : TP1 - Introduction aux données ouvertes liées
- **Groupe** : Groupe-09
- **Étudiants** : Amraoui Aymane, Draidi Oualid
- **Date** : 2026

---

## 1. Introduction

Dans le cadre du TP1 sur les Linked Open Data, nous avons étudié un jeu de données portant sur les établissements de l’enseignement supérieur public au Maroc (2024-2025), publié par le MESRSI.

L’objectif de ce travail hors séance est d’analyser le potentiel de transformation de ce dataset en données liées, en identifiant les entités principales, les possibilités d’appariement avec des référentiels externes, ainsi que les contraintes techniques liées à la qualité et à la structuration des données.

Nous nous concentrons principalement sur trois types d’entités : les universités, les établissements (facultés, écoles, instituts) et les villes, qui constituent les éléments centraux pour une future modélisation RDF.

---

## 2. Types d'entités étudiés

Les entités retenues sont :

- **Université**
- **Établissement (faculté, école, institut)**
- **Ville**

### Justification du choix

- L’**université** constitue une entité administrative centrale, qui regroupe plusieurs établissements.
- L’**établissement** représente l’unité réelle d’observation du dataset et porte la majorité des informations (adresse, effectifs, contacts).
- La **ville** permet de désambiguïser les entités et constitue un point d’ancrage géographique essentiel.

### Indices d’appariement disponibles

Les principaux champs utilisés pour l’appariement sont :

- Nom de l’université
- Nom de l’établissement
- Acronyme (ex : ENSIAS, EMI, ENCG)
- Ville
- Adresse

Ces indices permettent de construire des correspondances avec des référentiels externes, bien que leur qualité varie selon les cas.

---

## 3. Benchmark des référentiels externes

### Référentiels comparés

| Référentiel | URL | Types d'entités couverts | Intérêt potentiel | Limites observées |
|-------------|-----|--------------------------|-------------------|-------------------|
| Wikidata | https://www.wikidata.org | Universités, Villes, Établissements | Identifiants stables, multilingue, riche sémantique | Couverture incomplète |
| GeoNames | https://www.geonames.org | Villes, Régions | Identifiants géographiques, coordonnées | Pas d’établissements |
| data.gov.ma | https://data.gov.ma | Universités, Établissements | Source officielle nationale | Pas d’identifiants stables |

### Analyse comparative

- **Couverture** : Wikidata couvre bien les universités, GeoNames couvre les villes, data.gov.ma est exhaustif mais peu structuré.
- **Qualité des identifiants** : Wikidata et GeoNames offrent des identifiants stables, contrairement à data.gov.ma.
- **Richesse sémantique** : Wikidata est le plus riche.
- **Facilité d’appariement** : nécessite souvent une normalisation préalable.
- **Réutilisabilité** : Wikidata et GeoNames sont pleinement compatibles LOD.

---

## 4. Cas d'appariement manuel

| Entité locale | Type | Référentiel | Correspondance | Confiance | Décision |
|--------------|------|------------|---------------|----------|----------|
| Université Mohammed V - Rabat | Université | Wikidata | Q2915243 | Très élevé | Validé |
| Université Hassan II - Casablanca | Université | Wikidata | Q1569037 | Très élevé | Validé |
| Rabat | Ville | GeoNames | 2538475 | Très élevé | Validé |
| Fès | Ville | GeoNames | 2548885 | Élevé | Validé |
| FSJES Agdal Rabat | Établissement | Wikidata | Aucun | Moyen | Non apparié |
| EMI Rabat | Établissement | Wikidata | Q3048450 | Élevé | Validé |

### Analyse

Les appariements sont fiables pour les universités et les villes, mais plus difficiles pour les établissements en raison de l’absence d’identifiants et des ambiguïtés liées aux noms.

---

## 5. Plan de normalisation et d'identification

### Entités prioritaires

- Université
- Établissement
- Ville

### Normalisation nécessaire

- Standardisation des noms (suppression des accents, uniformisation)
- Structuration des adresses
- Conversion des numéros de téléphone en format standard
- Nettoyage des valeurs numériques

### Stratégie d’identifiants

- Université : slug basé sur nom + ville
- Établissement : clé composite (université + acronyme + ville)
- Ville : identifiant GeoNames


---

## 6. Analyse des risques

- Risque de faux appariement (homonymes)
- Variations orthographiques
- Données non normalisées
- Couverture partielle des référentiels

 Exemple :
"FSJES" existe dans plusieurs villes → ambigu sans contexte

---

## 7. Difficultés rencontrées

- Absence d’identifiants uniques dans le dataset
- Données textuelles non normalisées
- Difficulté de correspondance avec Wikidata pour certains établissements
- Variabilité linguistique (français / arabe / anglais)

---

## 8. Conclusion

Cette étude a permis de mettre en évidence le potentiel du dataset pour une transformation en Linked Open Data, tout en identifiant les principales limitations techniques.

Les référentiels comme Wikidata et GeoNames offrent des opportunités d’enrichissement significatives, mais nécessitent un travail préalable de normalisation et de désambiguïsation.

Ce travail constitue une étape essentielle pour préparer la modélisation RDF lors du TP2, en définissant une stratégie claire d’identification et d’alignement des entités.

---

## Annexes éventuelles

- Schéma conceptuel Mermaid
- Tableau des correspondances externes