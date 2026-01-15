# Rapport de Projet : Pipeline de Prédiction de Performance Académique sur Google Cloud Platform

## Introduction
Ce projet implémente une architecture Big Data complète pour l'analyse et la prédiction de la performance des étudiants. L'objectif est de traiter un volume important de données pour identifier les facteurs de réussite scolaire et fournir des prédictions précises via un modèle d'apprentissage automatique distribué. L'ensemble du flux de données est orchestré sur Google Cloud Platform (GCP), utilisant Spark pour le traitement et BigQuery pour l'analyse décisionnelle.

## Architecture Technique
L'infrastructure repose sur le couplage de plusieurs services Cloud pour garantir la scalabilité et la persistance des données :
- Le stockage brut des fichiers CSV est assuré par Google Cloud Storage (GCS).
- Le traitement, le nettoyage et l'entraînement du modèle sont réalisés avec Apache Spark (PySpark) sur un cluster Dataproc.
- L'entrepôt de données BigQuery est utilisé pour stocker les résultats des prédictions et les analyses de performance.
- Looker Studio sert d'interface de Business Intelligence pour la visualisation finale des indicateurs clés.



## Méthodologie et Pipeline ML
Le cycle de développement du modèle suit les étapes rigoureuses du Data Engineering et du Machine Learning :
- Initialisation et normalisation : Chargement des données avec inférence de schéma et renommage systématique des colonnes pour assurer la compatibilité avec les systèmes SQL (remplacement des espaces par des underscores).
- Analyse de qualité : Vérification de l'absence de valeurs nulles et détection des valeurs aberrantes par la méthode de l'écart interquartile (IQR).
- Ingénierie des caractéristiques : Transformation des variables catégorielles en variables binaires (0/1) via StringIndexer et assemblage des vecteurs de caractéristiques avec VectorAssembler.
- Modélisation : Utilisation de l'algorithme Random Forest Regressor, choisi pour sa capacité à gérer des relations non-linéaires et sa robustesse.
- Évaluation : Mesure de la performance via les métriques R2, RMSE et MAE pour garantir la fiabilité des prédictions avant l'exportation.

## Problèmes Rencontrés et Solutions Appliquées

### Problème de compatibilité des noms de champs
Lors de l'exportation des données de Spark vers BigQuery et leur visualisation dans Looker Studio, des erreurs de type "Nom de champ non valide" sont apparues. Cela était dû à la présence d'espaces dans les noms de colonnes d'origine (ex: "Hours Studied").
- Solution : Implémentation d'une fonction de nettoyage dynamique dès l'étape d'ingestion pour transformer tous les noms de colonnes au format "snake_case" (ex: "Hours_Studied") de manière automatisée.

### Limitation de la mémoire locale pour les visualisations
L'utilisation de bibliothèques de visualisation standard comme Matplotlib ou Pandas présentait un risque de saturation de la mémoire du noeud maître lors du traitement de volumes de données importants.
- Solution : Externalisation complète de la couche de visualisation. Les calculs lourds (corrélations, importances des variables) ont été réalisés de manière distribuée dans Spark, puis exportés vers BigQuery pour être visualisés de façon fluide dans Looker Studio sans contrainte de mémoire.

### Intégration des données catégorielles
La variable "Activités extra-scolaires" était initialement au format texte (Yes/No), ce qui empêchait son intégration directe dans la matrice de corrélation de Pearson et dans l'entraînement du modèle.
- Solution : Conversion systématique via StringIndexer pour obtenir un format binaire (0,1). Cette solution a permis d'inclure cette variable dans l'analyse de corrélation globale et d'évaluer son importance réelle sur la performance finale.



## Résultats et Conclusions
Le modèle final affiche une précision remarquable avec un score R2 r à 0.91, indiquant que les variables d'entrée expliquent la quasi-totalité de l'index de performance. L'analyse de l'importance des variables confirme que les scores précédents et le temps d'étude sont les deux piliers de la réussite académique. Le pipeline est désormais automatisé et capable de traiter des volumes de données croissants tout en offrant une interface de monitoring claire via le tableau de bord décisionnel Looker Studio.
