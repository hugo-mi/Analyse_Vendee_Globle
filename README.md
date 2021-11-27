# Analyse d'un jeu de données du Vendée Globle 2020-2021

_"Le Vendée Globe est à ce jour la plus grande course à la voile autour du monde, en solitaire, sans escale et sans assistance"_

**Objectifs principaux du projet**

* Extraction des caractéristiques techniques de chaque voilier (Web Scrapping)
* Pré-traitement des différents jeux de données
* Rapprochement des données des voiliers avec les données de classement de la course
* Corrélation et régression linéaire entre le classement (rank) et la vitesse utile (VMG) des bateaux.
* Analyse de l'impact de la présence d'un foil sur le classement et la vitesse des bateaux (test de significativité)
* Visualisation de la distance parcourue par les voiliers.
* Cartes avec les parcours d'un ou plusieurs voiliers.
* Analyse de séries temporelles : Suivi de la vitesse moyenne d'un skipper durant la course, Suivi de l'évolution du rang d'un skipper pendant la course pour prédire la position du skipper à la fin de la course (ARIMA)
* Sélection de variables (Lasso, Ridge, Feed Forward)
* Application de plusieurs algorithmes d'apprentissage automatique (Régression logistique, K-Means,...)

**Technologies :** Python, Scikit-Learn, Pandas, Matplotlib, Seaborn, Beautiful Soup (bs4)

Toutes les données utilisées pour ce projet sont à disposition sur le site officiel de la course :

* https://www.vendeeglobe.org/fr/classement
* https://www.vendeeglobe.org/fr/glossaire

## Import des données (Web Scrapping)

`def get_carac_boat(url):`

`    
    request = requests.get(url)
    
    # On crée l'objet bs4 
    soup = BeautifulSoup(request.content)
    
    # On initialise le dictionnaire "boats"
    boats = {}
    
    soup2 = soup.findAll('div', attrs = {'class': f'boats-list__popup mfp-hide'})
    
    for div in soup2:
        
        sub_dic_boats = {}
        
        soup3 = div.find('div', attrs = {'class': f'boats-list__popup-infos'})
        
        # On récupère le nom du bateau
        title_boat = soup3.find('h3').text
        boats[title_boat] = sub_dic_boats
        
        # On récupère le nom du skipper
        skipper_name = soup3.find('a').attrs['href'].split("/")[-1]
        sub_dic_boats["Skipper"] = skipper_name.replace("-", " ")
        
        # On récupère toutes les caractéristiques du bateau
        
        soup4 = div.find('ul', attrs = {'class': 'boats-list__popup-specs-list'}).findAll('li')
        
        id_boat = soup4[0].text.split(":")[1].strip()
        
        list_special_boat = ["FRA 79", "08", "FRA 85", "JPN 11", "4", "2", "FRA 6", "GBR 99"]
        linkedout_boat = "Verdier"
        corum_lepargne = "FRA 6"
        
        if id_boat == linkedout_boat:
            
            sub_dic_boats["Numéro de voile"] = "FRA 59"
            
            sub_dic_boats["Anciens noms du bateau"] = np.nan

            sub_dic_boats["Architecte"] = soup4[0].text.split(":")[1].strip()

            sub_dic_boats["Chantier"] = soup4[1].text.split(":")[1].strip()

            sub_dic_boats["Date de lancement"] = soup4[2].text.split(":")[1].strip()

            sub_dic_boats["Longueur"] = soup4[3].text.split(":")[1].strip()

            sub_dic_boats["Largeur"] = soup4[4].text.split(":")[1].strip()

            sub_dic_boats["Tirant d'eau"] = soup4[5].text.split(":")[1].strip()

            sub_dic_boats["Déplacement (poids)"] = soup4[6].text.split(":")[1].strip()

            sub_dic_boats["Nombre de dérives"] = soup4[7].text.split(":")[1].strip()

            sub_dic_boats["Hauteur mât"] = soup4[8].text.split(":")[1].strip()

            sub_dic_boats["Voile quille"] = soup4[9].text.split(":")[1].strip()
            
            sub_dic_boats["Surface de voiles au près"] = soup4[10].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au portant"] = soup4[11].text.split(":")[1].strip()
            
        if id_boat == corum_lepargne:
            
            sub_dic_boats["Numéro de voile"] = soup4[0].text.split(":")[1].strip()
            
            sub_dic_boats["Anciens noms du bateau"] = np.nan

            sub_dic_boats["Architecte"] = soup4[1].text.split(":")[1].strip()

            sub_dic_boats["Chantier"] = soup4[2].text.split(":")[1].strip()

            sub_dic_boats["Date de lancement"] = soup4[3].text.split(":")[1].strip()

            sub_dic_boats["Longueur"] = soup4[4].text.split(":")[1].strip()

            sub_dic_boats["Largeur"] = soup4[5].text.split(":")[1].strip()

            sub_dic_boats["Tirant d'eau"] = soup4[6].text.split(":")[1].strip()

            sub_dic_boats["Déplacement (poids)"] = soup4[7].text.split(":")[1].strip()

            sub_dic_boats["Nombre de dérives"] = soup4[8].text.split(":")[1].strip()

            sub_dic_boats["Hauteur mât"] = soup4[9].text.split(":")[1].strip()
            
            sub_dic_boats["Voile quille"] = np.nan
            
            sub_dic_boats["Surface de voiles au près"] = soup4[10].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au portant"] = soup4[11].text.split(":")[1].strip()

        if id_boat not in list_special_boat and id_boat != linkedout_boat and id_boat != corum_lepargne:
        
            sub_dic_boats["Numéro de voile"] = soup4[0].text.split(":")[1].strip()

            sub_dic_boats["Anciens noms du bateau"] = soup4[1].text.split(":")[1].strip()

            sub_dic_boats["Architecte"] = soup4[2].text.split(":")[1].strip()

            sub_dic_boats["Chantier"] = soup4[3].text.split(":")[1].strip()

            sub_dic_boats["Date de lancement"] = soup4[4].text.split(":")[1].strip()

            sub_dic_boats["Longueur"] = soup4[5].text.split(":")[1].strip()

            sub_dic_boats["Largeur"] = soup4[6].text.split(":")[1].strip()

            sub_dic_boats["Tirant d'eau"] = soup4[7].text.split(":")[1].strip()

            sub_dic_boats["Déplacement (poids)"] = soup4[8].text.split(":")[1].strip()

            sub_dic_boats["Nombre de dérives"] = soup4[9].text.split(":")[1].strip()

            sub_dic_boats["Hauteur mât"] = soup4[10].text.split(":")[1].strip()

            sub_dic_boats["Voile quille"] = soup4[11].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au près"] = soup4[12].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au portant"] = soup4[13].text.split(":")[1].strip()
            
        if id_boat in list_special_boat and id_boat != linkedout_boat and id_boat != corum_lepargne:
            
            sub_dic_boats["Numéro de voile"] = soup4[0].text.split(":")[1].strip()
            
            sub_dic_boats["Anciens noms du bateau"] = np.nan

            sub_dic_boats["Architecte"] = soup4[1].text.split(":")[1].strip()

            sub_dic_boats["Chantier"] = soup4[2].text.split(":")[1].strip()

            sub_dic_boats["Date de lancement"] = soup4[3].text.split(":")[1].strip()

            sub_dic_boats["Longueur"] = soup4[4].text.split(":")[1].strip()

            sub_dic_boats["Largeur"] = soup4[5].text.split(":")[1].strip()

            sub_dic_boats["Tirant d'eau"] = soup4[6].text.split(":")[1].strip()

            sub_dic_boats["Déplacement (poids)"] = soup4[7].text.split(":")[1].strip()

            sub_dic_boats["Nombre de dérives"] = soup4[8].text.split(":")[1].strip()

            sub_dic_boats["Hauteur mât"] = soup4[9].text.split(":")[1].strip()

            sub_dic_boats["Voile quille"] = soup4[10].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au près"] = soup4[11].text.split(":")[1].strip()

            sub_dic_boats["Surface de voiles au portant"] = soup4[12].text.split(":")[1].strip()


    return boats`

## Visualisation de l'évolution du rang pendant tout le long de la course pour les skippers

**Skipper Arnaud Boissieres**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Rank_Skipper1.png)

**Skipper Arnaud Boissieres**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Rank_Skipper2.png)

## Analyse de série temporelle

### Visualisation de l'évolution du rang des skipers

**_Arnaud Boissieres_**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Rank_Skipper1.png)

**_Kevin Excoffier_**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Rank_Skipper2.png)

### Prédiction du rang des skippers

**Application du modèle ARIMA**

**_Kevin Excoffier_**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/ARIMA_Rank_Fitted_Value.png)

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/ARIMA_Rank_Predict_Skipper1.png)

**Rang prédit = 7 (rang réel = Abondon)**
**Si le skiper Florian Excoffier n'avait pas abandonné la course on peut s'attendre que ce dernier occupe la 7ème place du classement final**

**_Yannick Bestaven_**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/ARIMA_Rank_Fitted_Value1.png)

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/ARIMA_Rank_Predict_Skipper2.png)

**Rang prédit = 4 (rang réel = 1)**

## Visualisation des trajectoires des skippers

**Conversion des coordonnées "Longitude et "Latitude""**

`def conversion_gps(coordonnee):`
        
    if isinstance(coordonnee, str):
        coordonnee_ = coordonnee.replace("°", ",").replace(".", ",").replace("'", ",").split(",")

        degre = int(coordonnee_[0])
        minute = int(coordonnee_[1])
        seconde = int(coordonnee_[2])
        orientation = coordonnee_[3]

        if orientation == "N":
            orientation = 1
        elif orientation == "S":
            orientation = -1
        elif orientation == "E":
            orientation = 1
        elif orientation == "W":
            orientation = -1
        
        position = (degre + minute / 60.0 + seconde / 3600.0) * orientation
        
    else:
        position = np.nan

    return position`

### Trajet du skipper _Arnaud Boissieres_

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Trajet_Skipper1_SkipperLast.png)

## Analyse du rang en fonction de la vitesse moyenne "vmg''

**Application d'une regression linéaire simple**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/RegLin_Rank_VMG.png)

## Analyse entre le gagnant du vendée globle (_Yannick Bestaven_) et le dernier (_Ari Huusela_)

### Visualisation de la Distance To Finish (DTF)

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/DTF_Skipper1_Skipper2.png)

### Visualisation des trajets

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Trajet_Skipper1_SkipperLast.png)

### Visualisation de la vitesse utile (VMG)

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/VMG_Skipper1_SkipperLast.png)

## Analyse de l'implact d'un foils sur la VMG et le classement 

### Visualisation de l'impact du foils sur la VMG

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/BarChart_Foils_VMG.png)

### Visualisation de l'impact du foils sur le classement final

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/BarChart_Foils_Rank.png)

## Visualisation de la distance totale parcourue de course de chaque skipper

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Distance_Rank.png)

### Application d'une regression linéaire 

**Test de significativité sur la présence d'un foils**

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Summary_RegLin.png)

`### Interprétation des résultats de la regressions

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Reg_Lin_Interpreation1.png)

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Reg_Lin_Interpreation2.png)

## Analyse des caractéristiques techniques du bâteau pour intégrer le TOP 5

### Application d'une regression logistique

![](https://github.com/hugo-mi/Analyse_Vendee_Globle/blob/main/Images/Output_RegLogit.png)

Selon les résultats de la regression logistique, les coefficient des variables "Largeur_m", "Surface_voiles_au_pres_m2" et "Voile_quille_acier" sont positifs. Cela signifie que ces 3 paramètres possèdent une influence positive sur le fait d'être dans les 5 premiers du classement. On en déduit donc que les caractéristiques techniques "Largeur_m", "Surface_voiles_au_pres_m2" et "Voile_quille_acier", sont deux paramètres auxquels il est nécéssaire d'apporter un attention particulière lors de la conception du bâteau car si ces paramètres sont bien optimisés il permettront au skipper d'accrocher plus facilement les 5 premières places du classement ainsi obtenir un avantage par rapport aux bâteaux concurrents. Cependant, on remarque que la largeur du bâteau à une plus grande influence sur le fait d'être dans les 5 premiers du classement que les 2 autres paramètres dont le coefficient est positif. De plus, la regression logistique semble confirmer les résultats obtenu par le modèle de regression linéaire et le test de significativé sur la présence du foils car ici encore le coefficient de la variable "Presence_foils" et négatif ce qui signifie qu'il à un influence négative sur la variable cible (finir dans les 5 premiers). Ainsi cette caractéristique technique ne semble pas avoir d'influence sur l'obtention d'un bon classement.
