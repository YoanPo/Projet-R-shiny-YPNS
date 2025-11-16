# Exploration DPE – ADEME

## Description

Cette application R/Shiny permet de rechercher, analyser et visualiser les Diagnostics de Performance Énergétique (DPE) à partir des données publiques de l’ADEME.  
Elle offre une interface intuitive pour filtrer par code postal et période, visualiser des graphiques statistiques, et localiser les DPE sur une carte interactive.

---

## Fonctionnalités principales

- Recherche DPE : par date et code postal  
- Tableau interactif : visualisation et export CSV  
- KPI (Indicateurs clés) : nombre de DPE, consommation moyenne, surface moyenne  
- Analyses statistiques :
  - Histogrammes de consommation  
  - Boxplots par étiquette DPE  
  - Diagrammes à barres  
  - Nuages de points et régressions linéaires  
- Cartographie interactive : localisation des DPE avec popups informatifs  
- Exports : graphiques PNG et données CSV  

---

## Authentification

- **Identifiant :** `admin_dpe`  
- **Mot de passe :** `rshiny1`  

---

## Installation et lancement

1. Cloner le dépôt ou télécharger le projet :
   ```bash
   git clone https://github.com/votre-utilisateur/exploration-dpe.git
Installer les packages R nécessaires :

r
Copier le code
install.packages(c(
  "shiny", "shinydashboard", "shinyWidgets", "shinyjs",
  "httr", "jsonlite", "DT", "ggplot2", "plotly", "leaflet", "sf"
))
Lancer l’application :

r
Copier le code
shiny::runApp("app.R")
Accéder à l’interface via le navigateur à l’URL indiquée par RStudio.

 Structure du projet :
/MonProjet/
├── app.R                    # Script principal Shiny
├── rapport.Rmd              # Rapport d’analyse statistique
├── www/                     # Ressources CSS/images
├── doc/
│   ├── documentation_technique.md  # Documentation développeur
│   └── schema.drawio         # Schéma architecture Draw.io
└── README.md                 # Ce fichier

 ## Données et limites :
Source : Données publiques ADEME – DPE existant

Limite maximale : 5000 DPE par requête API

Colonnes disponibles :
  - Essentielles : numero_dpe, etiquette_dpe, surface_habitable_logement, date_reception_dpe, code_postal_ban
  - Analyse : conso_5_usages_ep, emission_ges_5_usages, annee_construction
  - Localisation : coordonnee_cartographique_x_ban, coordonnee_cartographique_y_ban

## Bonnes pratiques :
  - Limiter les périodes ou codes postaux pour ne pas dépasser 5000 DPE
  - Vérifier les valeurs manquantes (NA) avant analyse
  - Exporter régulièrement les graphiques et CSV pour archivage

## Support :
Documentation technique disponible dans le dossier doc/ pour les développeurs

## Licence :
Données publiques ADEME
