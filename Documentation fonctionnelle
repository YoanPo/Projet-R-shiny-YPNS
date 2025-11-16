1. Présentation de l’application
Nom : Exploration DPE – ADEME
Objectif : Permettre l’analyse, la visualisation et la cartographie des Diagnostics de Performance Énergétique (DPE) pour un ou plusieurs codes postaux et périodes définies.

Fonctionnalités principales :

1)Recherche et filtrage des DPE
2)Indicateurs clés (KPI)
3)Analyses statistiques
4)Cartographie interactive
5)Export de données et graphiques

L’accès est sécurisé via identifiant et mot de passe.

2. Connexion à l’application
1)Ouvrir l’application Shiny.
2)Sur la page de login :
  - Identifiant : admin_dpe
  - Mot de passe : rshiny1

3)Cliquer sur Se connecter.
4)En cas d’erreur, un message rouge s’affiche : “Identifiant ou mot de passe incorrect”.



3. Page Accueil & Recherche
3.1 Paramètres de recherche :

  - Plage de dates : Sélectionner la période de réception des DPE
  - Code postal : Filtrer par commune
  - Bouton Rechercher : Lance la requête auprès de l’API ADEME
  - Bouton Rafraîchir : Recharge les données si elles ont été mises à jour
  - Afficher toutes les colonnes : Cochez pour voir les données détaillées

3.2 Résultats et KPI :

  - Nombre de DPE : Total de DPE récupérés
  - Conso. Moyenne (kWh/m²) : Moyenne des consommations énergétiques
  - Surface Moyenne (m²) : Moyenne des surfaces habitables

3.3 Tableau des DPE :

  - Affiche les résultats filtrés
  - Possibilité d’exporter CSV via le bouton “Exporter les données du tableau (.csv)”



4. Page Analyses statistiques
4.1 Visualisations :
  - Histogramme de consommation : Répartition des consommations énergétiques
  - Boxplot par étiquette DPE : Consommation par catégorie A → G
  - Diagramme à barres : Répartition des étiquettes DPE
  - Nuage de points & régression : Corrélation entre variables (ex : surface vs consommation)

4.2 Export :
  - Chaque graphique peut être exporté en PNG via le bouton correspondant



5. Page Cartographie :
  - Carte interactive avec les DPE géolocalisés
  - Popups : Numéro DPE, étiquette, consommation
  - Zoom et déplacement : Utilisez la souris pour naviguer
  - Remarque : Si aucune coordonnée disponible, un message d’alerte s’affiche



6. Page Contexte et sources
  - Logo ADEME et mention des données publiques
  - Limites de l’API : 5000 DPE maximum par requête
  - Champs disponibles :
    - Essentiels : numero_dpe, etiquette_dpe, surface_habitable_logement, date_reception_dpe, code_postal_ban
    - Analyse : conso_5_usages_ep, emission_ges_5_usages, annee_construction
    - Localisation : coordonnee_cartographique_x_ban, coordonnee_cartographique_y_ban



7. Bonnes pratiques utilisateur :
1)Limiter la période ou le code postal pour ne pas dépasser la limite API (5000 DPE)
2)Vérifier les données manquantes : certaines valeurs peuvent être NA
3)Exporter régulièrement les graphiques et données pour archivage
4)Utiliser les filtres pour se concentrer sur un quartier ou une étiquette DPE spécifique
