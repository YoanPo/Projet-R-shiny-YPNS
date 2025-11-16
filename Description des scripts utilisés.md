## 1. Chargement des bibliothèques :

library(dplyr)   # Manipulation et nettoyage des données

library(ggplot2) # Visualisations statistiques

library(plotly)  # Graphiques interactifs (optionnel)

library(knitr)   # Mise en forme des tableaux

library(sf)      # Gestion des données géospatiales

library(leaflet) # Cartographie interactive

library(httr)    # Requêtes HTTP (API ADEME)

library(jsonlite)# Conversion JSON → R

library(scales)  # Formats et échelles (log, pourcentage, etc.)



Explication :

Ces bibliothèques permettent :

d’interroger l’API ADEME (httr, jsonlite),

de nettoyer et structurer les données (dplyr),

de produire des graphiques (ggplot2),

de générer une carte interactive (leaflet + sf).



## 2. Paramètres et construction de la requête API ADEME :

code_postal <- "69008"     # Zone d’étude (modifiable)

date_debut  <- "2023-06-01" # Date minimale du DPE

date_fin    <- "2023-08-30" # Date maximale du DPE

API_SIZE_LIMIT <- 5000      # Limite de résultats par requête

fields <- c(
  "numero_dpe","etiquette_dpe","surface_habitable_logement",
  "date_reception_dpe","code_postal_ban","conso_5_usages_ep",
  "coordonnee_cartographique_x_ban","coordonnee_cartographique_y_ban",
  "emission_ges_5_usages","annee_construction"
)

base_url <- "https://data.ademe.fr/data-fair/api/v1/datasets/dpe03existant/lines"
query <- list(
  page = 1,
  size = API_SIZE_LIMIT,
  select = paste(fields, collapse=","),
  qs = paste0('code_postal_ban:"', code_postal,
              '" AND date_reception_dpe:[', date_debut, " TO ", date_fin, "]")
)



Explication :

On définit ici :
le périmètre géographique (code postal),

la période d’étude,

les champs à récupérer depuis l’API,

les paramètres de pagination.

La variable query constitue la requête finale transmise au service web.


## 3. Interrogation de l’API et récupération des données :


resp <- GET(modify_url(base_url, query = query))

if(status_code(resp) != 200){
  stop(paste("Erreur API, code :", status_code(resp)))
}

content <- fromJSON(rawToChar(resp$content), flatten = TRUE)
df_raw <- content$results

if(is.null(df_raw) || nrow(df_raw) == 0){
  stop("Aucune donnée récupérée pour les filtres choisis.")
}


Explication :

GET() envoie la requête HTTP à ADEME.

Si le serveur renvoie un code ≠ 200, le script s’arrête avec un message d’erreur.

Les données JSON reçues sont converties en data.frame.



## 4. Mise en forme et conversion des colonnes :

df <- df_raw %>%
  mutate(
    consommation_energie = as.numeric(conso_5_usages_ep),
    emission_ges = as.numeric(emission_ges_5_usages),
    surface_habitable_logement = as.numeric(surface_habitable_logement),
    annee_construction = as.numeric(annee_construction),
    date_reception_dpe = as.Date(date_reception_dpe)
  )


Explication :

Les valeurs provenant de l’API sont souvent envoyées comme chaînes de caractères.

On les convertit en :

numérique → pour les calculs,

date → pour les analyses temporelles.



## 5. Nettoyage des données

df <- df %>%
  mutate(
    consommation_energie = ifelse(consommation_energie < 0, NA, consommation_energie),
    emission_ges = ifelse(emission_ges < 0, NA, emission_ges),
    etiquette_dpe = ifelse(etiquette_dpe == "" | is.na(etiquette_dpe), NA, etiquette_dpe)
  )

niveau <- c("A","B","C","D","E","F","G")
df$etiquette_dpe <- factor(df$etiquette_dpe, levels = niveau, ordered = TRUE)


Explication :
  
Suppression des valeurs aberrantes (par ex. énergie < 0).

Remplacement des champs vides par NA.

Conversion de l’étiquette DPE en facteur ordonné (A → G).



## 6. Visualisation : Histogramme des consommations :

ggplot(df, aes(x = consommation_energie)) +
  geom_histogram(bins = 30, color = "white") +
  labs(title = "Distribution des consommations énergétiques",
       x = "Consommation (kWh/m²/an)", y = "Nombre de DPE") +
  theme_minimal()



Explication :

Montre la répartition des consommations sur l’ensemble du parc étudié.



## 7. Visualisation : Répartition des étiquettes DPE :

ggplot(df, aes(x = etiquette_dpe)) +
  geom_bar() +
  coord_flip() +
  labs(title = "Répartition des étiquettes DPE",
       x = "Étiquette", y = "Nombre") +
  theme_minimal()



Explication :

Permet d’identifier si la zone est plutôt performante (A-B-C) ou non (E-F-G).



## 8. Boxplot : Consommation par étiquette :

ggplot(df, aes(x = etiquette_dpe, y = consommation_energie)) +
  geom_boxplot() +
  labs(title = "Consommation par étiquette DPE",
       x = "Étiquette", y = "kWh/m²/an") +
  theme_minimal()



Explication :

Montre la variabilité interne dans chaque classe DPE.


## 9. Matrice de corrélation :

vars <- df %>% select(surface_habitable_logement, consommation_energie, emission_ges)
corr_matrix <- cor(vars, use = "complete.obs")


Explication :

Analyse des relations :

surface ↔ consommation,

consommation ↔ émissions de GES.



## 10. Régression linéaire : consommation ~ surface :

df_reg <- df %>% select(consommation_energie, surface_habitable_logement) %>% na.omit()
model <- lm(consommation_energie ~ surface_habitable_logement, data = df_reg)
summary(model)




Explication :
  
Mesure si la surface explique la performance énergétique (souvent très faible).



## 11. Scatter plot + droite de régression :

ggplot(df, aes(surface_habitable_logement, consommation_energie)) +
  geom_point(alpha=0.5) +
  geom_smooth(method="lm", color="red") +
  theme_minimal() +
  labs(title="Relation entre surface et consommation énergétique")




Explication :
 
Visualisation directe de la relation entre surface et consommation par m².



## 12. Cartographie interactive Leaflet :

df_geo <- df %>%
  filter(!is.na(coordonnee_cartographique_x_ban),
         !is.na(coordonnee_cartographique_y_ban),
         is.finite(coordonnee_cartographique_x_ban),
         is.finite(coordonnee_cartographique_y_ban))

if(nrow(df_geo) == 0){
  cat("Aucune donnée géolocalisée disponible pour ces filtres.\n")
} else {
  pts <- st_as_sf(df_geo,
                  coords = c("coordonnee_cartographique_x_ban", "coordonnee_cartographique_y_ban"),
                  crs = 2154, agr = "constant") %>%
    st_transform(4326)

  leaflet(pts) %>%
    addTiles() %>%
    addCircleMarkers(
      lng = ~st_coordinates(pts)[,1],
      lat = ~st_coordinates(pts)[,2],
      radius = 4,
      popup = ~paste0("N° DPE: ", numero_dpe, "<br>",
                      "Étiquette: ", etiquette_dpe, "<br>",
                      "Conso: ", round(consommation_energie,0), " kWh/m²/an")
    )
}



Explication :

Filtrage des enregistrements ayant des coordonnées valides,

Transformation du système de projection (Lambert 2154 → WGS84),

Création d’une carte interactive où chaque point représente un DPE,

Les popups affichent : numéro, étiquette, consommation.
