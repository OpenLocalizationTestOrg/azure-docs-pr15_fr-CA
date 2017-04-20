<properties
    pageTitle="Utiliser les jeux de données d’exemple dans un Studio d’apprentissage Machine | Microsoft Azure"
    description="Descriptions des ensembles de données utilisés dans les exemples de modèles inclus dans un Studio de ML. Vous pouvez utiliser ces jeux de données d’exemple pour vos expériences."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Utiliser les jeux de données d’exemple dans Azure Machine Learning Studio

[top]: #machine-learning-sample-datasets

Lorsque vous créez un nouvel espace de travail dans l’apprentissage automatique de Azure, un certain nombre de jeux de données d’exemple et les expériences est inclus par défaut. La plupart de ces jeux de données d’exemple sont utilisées par les exemples de modèles dans la [Galerie d’Intelligence Azure Cortana](http://gallery.cortanaintelligence.com/)et d’autres sont fournis comme des exemples des différents types de données généralement utilisées dans l’apprentissage automatique.

Certains de ces jeux de données sont disponibles dans le stockage des objets Blob Azure. Pour ces ensembles de données, le tableau ci-dessous fournit un lien direct. Vous pouvez utiliser ces ensembles de données dans vos expériences en utilisant les [Données d’importation] [ import-data] module.

Le reste de ces jeux de données d’exemple sont répertoriés sous **Des groupes de données enregistrées** dans la palette de module à gauche de la zone d’essai lorsque vous ouvrez ou créez une nouvelle expérience dans un Studio de ML.
Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en la faisant glisser à la zone de votre expérience.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nom du groupe de données</th>
  <th align=left>Description du groupe de données</th>
</tr>

<tr>
  <td valign=top>Dataset de Classification binaire de recensement revenu adulte</td>
  <td valign=top>
Un sous-ensemble de la base de recensement de 1994, à l’aide de travail adultes âgés de 16 à un indice du revenu ajusté de 100 >.<p> </p><b>Utilisation :</b> classer des personnes à l’aide des données démographiques pour prévoir si une personne reçoit plus de 50 Ko an.<p> </p><b>Recherche :</b> Kohavi, R., Becker, B. (1996). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Dataset de Codes d’aéroport</td>
  <td valign=top>
Codes d’aéroports américains.<p> </p>Ce groupe de données contient une ligne pour chaque aéroport américain, en fournissant le numéro d’identification de l’aéroport et le nom et la ville de l’emplacement et l’état.
  </td>
</tr>

<tr>
  <td valign=top>Données de prix automobile (Raw)</td>
  <td valign=top>
Informations sur les automobiles par marque et le modèle, y compris le prix, des fonctionnalités telles que le nombre de cylindres et MPG, ainsi un score de risque d’assurance.<p> </p>Le score de risque est initialement associé à prix automatique et réglé pour un risque réel d’un processus connu pour les actuaires comme symboling. Une valeur de + 3 indique que l’auto est une opération risquée, et une valeur de -3 qu’il est probablement assez fiable.<p> </p><b>Utilisation :</b> prévoir le niveau de risque par des fonctions, à l’aide de la classification de régression ou MULTIVARIABLES. <p> </p><b>Recherche :</b> Schlimmer, J.C. (1987). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Vélo location UCI dataset</td>
  <td valign=top>
Location de bicyclettes UCI dataset basé sur des données réelles à partir de la société Bikeshare de capitaux qui conserve un réseau de location de vélo à Washington DC.<p> </p>Le groupe de données comporte une ligne pour chaque heure de chaque jour en 2011 et 2012, pour un total de 17,379 lignes. La plage de toutes les heures des loyers de vélo est de 1 à 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Image RVB de Bill Gates</td>
  <td valign=top>
Fichier image accessible publiquement converti en données CSV.<p> </p>Le code pour la conversion de l’image est fourni dans la page de détails de modèle <strong>à l’aide de moyens K clustering de quantification des couleurs</strong> .
  </td>
</tr>

<tr>
  <td valign=top>Données de don de sang</td>
  <td valign=top>
Un sous-ensemble de données à partir de la base de données des donneurs de sang de la ville de Transfusion sanguine Service Center de Hsin-Chu, Taïwan.<p> </p>Données de donneurs incluent les mois dernier don), fréquence, ou le nombre total de dons, depuis le dernier don et le montant des dons de sang.<p> </p><b>Utilisation :</b> l’objectif est de prédire par classification si le donneur donated sang en mars 2007, où 1 indique un donneur au cours de la période cible et 0 un donneur-non. <p> </p><b>Recherche :</b> ' YA ', réputés, (2008). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique <p> </p>' YA ', I-Cheng, Yang, ROI-Jang et Ting, Tao-Ming, « découverte des connaissances sur le modèle de mode de fonctionnalités réduites à l’aide de la séquence de Bernoulli, « systèmes experts avec les Applications, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Critiques d’Amazon</td>
  <td valign=top>
Critiques de livres d’Amazon, prélevés sur le site Web de amazon.com par les chercheurs de l’Université de Pennsylvanie (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">sentiment</a>). Le document de recherche, reportez-vous à la section « Biographies, films indiens, boîtes de bras et mélangeurs : Adaptation de domaine pour la Classification de Sentiment » par John Blitzer, Mark Dredze et les Fernando Pereira ; Association de calcul linguistique (ACL), 2007.<p> </p>Le dataset d’origine a des révisions de 975K avec des classements de 1, 2, 3, 4 ou 5. Les évaluations ont été rédigées en anglais et sont de la période 1997-2007. Ce groupe de données a été sous-échantillonnée aux réexamens de 10K.
  </td>
</tr>

<tr>
  <td valign=top>Données sur le cancer du sein</td>
  <td valign=top>
L’une des trois liées au cancer des groupes de données fournies par l’institut oncologie qui apparaît fréquemment dans la documentation de formation de machine. Combine les informations de diagnostic avec les fonctionnalités d’analyse en laboratoire des échantillons de tissus environ 300.<p> </p><b>Utilisation :</b> classer le type du cancer, basés sur 9 attributs, dont certaines sont linéaires et d’autres par catégorie. <p> </p><b>Recherche :</b> O.L. Wohlberg, W.H., rue, W.N. et Mangasarian, (1995). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Fonctionnalités de Cancer du sein <td valign=top>
Le groupe de données contient des informations pour les zones suspectes de 102K (candidats) de radiographies, chacun décrit par les fonctionnalités de 117. Les fonctionnalités sont propriétaires et leur signification n’est pas révélée par les créateurs de dataset (Siemens de soins de santé). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Info de Cancer du sein</td>
  <td valign=top>
Le groupe de données contient des informations supplémentaires pour chaque région suspecte d’image de rayons x. Chaque exemple fournit des informations (par exemple, étiquette, patient ID, coordonnées du correctif par rapport à l’image entière) sur le nombre de lignes correspondantes dans le dataset de fonctionnalités de Cancer du sein. Chaque patient possède un certain nombre d’exemples. Pour des patients ayant un cancer, certains exemples sont positives, mais négatives. Pour les patients qui n’ont pas un cancer, tous les exemples sont négatives. Le dataset a 102K exemples. Le groupe de données est biaisé, 0,6 % des points sont positifs, le reste est négatif. Le groupe de données a été mis à disposition par les soins de santé Siemens.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Étiquettes de Appetency CRM partagés</td>
  <td valign=top>
Étiquettes de KDD Cup 2009 relation prévision défis posés au client (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Étiquettes d’évolution du CRM partagés</td>
  <td valign=top>
Étiquettes de KDD Cup 2009 relation prévision défis posés au client (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Groupe de données CRM partagé</td>
  <td valign=top>
Ces données proviennent de la stimulation de prédiction de relation KDD Cup 2009 client (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Le groupe de données contient 50K des clients de la société de télécommunications du Français Orange. Chaque client a 230 fonctionnalités rendues anonymes, 190 dont sont numériques et 40 sont par catégorie. Les fonctionnalités sont très fragmentées.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Étiquettes de vente incitative CRM partagés</td>
  <td valign=top>
Étiquettes de KDD Cup 2009 relation prévision défis posés au client (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Données de régression de l’efficacité énergétique</td>
  <td valign=top>
Une collection de profils d’énergie simulé, basée sur 12 construction différentes formes. Les bâtiments diffèrent par rapport à différenciés par 8 fonctionnalités, telles que les vitrages de zone, la distribution de zone vitrages et l’orientation.<p> </p><b>Utilisation :</b> utiliser régression ou classification pour prédire l’efficacité énergétique est évaluée selon comme l’une des deux réponses à valeurs réelles. Pour la classification de classe multiple, est arrondi à l’entier le plus proche, la variable de réponse. <p> </p><b>Recherche :</b> Xifara, A. & Tsanas, A. (2012). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Données des retards de vol</td>
  <td valign=top>
Passagers sur-temps de vol des données de performances provenant de la collection de données de TranStats du ministère américain du transport (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">À temps</a>).<p> </p>Le groupe de données couvre la période avril-octobre 2013. Avant de le télécharger vers Azure ML Studio, le groupe de données a été traité comme suit :<ul><li>Le groupe de données a été filtrée pour couvrir uniquement les plus actifs 70 aéroports dans la continental des États-Unis.</li><li>Les vols annulés ont été étiquetés comme retardé de plus de 15 minutes</li><li>Vols transférés ont été filtrés.</li><li>Les colonnes suivantes ont été sélectionnées : année, mois, DayofMonth, DayOfWeek, support, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, annulé</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Performances à temps de vol (Raw)</td>
  <td valign=top>
Enregistrements des arrivées de vol d’un avion et des dérogations au sein des États-Unis depuis octobre 2011.<p> </p><b>Utilisation :</b> prévoir des retards. <p> </p><b>Recherche :</b> du département américain de transport <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Forêt déclenche des données</td>
  <td valign=top>
Contient les données météorologiques, tels que les indices de température et d’humidité et de la vitesse du vent, d’une zone du Portugal nord-est, combinée avec les enregistrements des incendies de forêt.<p> </p><b>Utilisation :</b> il s’agit d’une tâche difficile de régression, dont l’objectif est de prédire la zone gravée des incendies de forêt. <p> </p><b>Recherche :</b> Cortez, P., & Morais, A. (2008). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique <p> </p>[Cortez et Morais, 2007] P. Cortez et A. Morais. Une approche exploration de données pour prévoir les incendies de forêt à l’aide des données météorologiques. Dans J. Neves, M. f Santos et éditeurs de J. Machado, nouvelles tendances dans l’Intelligence artificielle, procédure de 2007 EPIA 13 - portugaise conférence sur l’Intelligence artificielle, décembre, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponible à l’adresse : <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Groupe de données UCI de carte de crédit allemand</td>
  <td valign=top>
Le dataset UCI Statlog (carte de crédit allemand) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Données + crédit + allemand + Statlog</a>), en utilisant le fichier german.data.<p> </p>Le groupe de données classe les personnes, décrites par un ensemble d’attributs, comme les risques de crédit faible ou élevé. Chaque exemple représente une personne. Il y a 20 fonctions, à la fois numériques et par catégorie et une étiquette binaire (la valeur de risque de crédit). Écritures de risque de crédit élevées ont l’étiquette = 2, entrées de risque de crédit faible ont étiquette = 1. Le coût d’un exemple de faible risque élevé de classer par erreur entrées est 1, alors que le coût d’un exemple de risque comme faible de classer par erreur entrées est 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titres de films IMDB</td>
  <td valign=top>
Le groupe de données contient des informations sur les films qui ont été classés dans les tweet Twitter : ID de film IMDB, nom du film, genre et année de production. Il existe des films de 17K dans le dataset. Le groupe de données a été introduite dans le livre « S. Dooms, T. De Pessemier et L. Martens. MovieTweetings : un film évaluation Dataset prélevé sur Twitter. Atelier de marketing participatif et calcul humaine pour les systèmes de conseiller, CrowdRec à RecSys 2013. »
  </td>
</tr>

<tr>
  <td valign=top>Données de classe IRIS deux</td>
  <td valign=top>
Cela est sans doute la base de données plus connu dans la documentation de reconnaissance de motif. Le jeu de données est relativement petit, contenant 50 chacun des exemples de mesures pétale de trois variétés d’iris.<p> </p><b>Utilisation :</b> prévoir le type d’iris dans les mesures.  <p> </p><b>Recherche :</b> Fisher, autorité (1988). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>FILM tweet</td>
  <td valign=top>
Le dataset est une version étendue du groupe de données Tweetings de film. Le groupe de données a des évaluations de 170K pour les films, extraits de tweet bien structurée sur Twitter. Chaque instance représente un tweet et est un tuple : ID de l’utilisateur, ID de film IMDB, évaluation, timestamp, nombre de favoris pour ce tweet, nombre de retweets de ce tweet. Le groupe de données a été mis à disposition par a dit S. Dooms, B. Loni et d Tikk pour des conseils sur les systèmes de stimulation 2014.
  </td>
</tr>

<tr>
  <td valign=top>Données MPG pour automobiles divers</td>
  <td valign=top>
Ce groupe de données est une version légèrement modifiée du groupe de données fourni par la bibliothèque StatLib de Carnegie Mellon University. Le groupe de données a été utilisé dans les spécifications statistique American Association 1983.<p> </p>Les listes de données la consommation de carburant pour automobiles différents en miles par gallon, ainsi que des informations, notamment le nombre de cylindres, cylindrée du moteur, la puissance, poids total et l’accélération.<p> </p><b>Utilisation :</b> prévoir la consommation de carburant en fonction des attributs discrets à valeurs multiples 3 et 5 attributs continus. <p> </p><b>Recherche :</b> StatLib, l’université Carnegie Mellon University, (1993). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr>
  <td valign=top>Classement binaire du diabète indiens PIMA le groupe de données</td>
  <td valign=top>
Un sous-ensemble de données à partir de l’Institut National du diabète et de la base de données de digestif et les maladies rénales. Le groupe de données a été filtré de se concentrer sur les patients femelles du patrimoine indien de Pima. Les données incluent des données médicales comme glucose et niveaux d’INSULINE, ainsi que les facteurs de style de vie.<p> </p><b>Utilisation :</b> prévoir si le sujet a DIABETE (classification binaire). <p> </p><b>Recherche :</b> Sigillito, V. (1990). Machine UCI formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml »</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique </td>
</tr>

<tr>
  <td valign=top>Données client de restaurant</td>
  <td valign=top>
Un jeu de métadonnées sur le client, y compris les données démographiques et les préférences.<p> </p><b>Utilisation :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, à former et tester un système de recommandation. <p> </p><b>Recherche :</b> Lichman, M. et Bache, K. (2013). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Données de fonctionnalité de restaurant</td>
  <td valign=top>
Un jeu de métadonnées sur les restaurants et leurs fonctionnalités, telles que le type d’aliments, dîner de style et l’emplacement.<p> </p><b>Utilisation :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, à former et tester un système de recommandation. <p> </p><b>Recherche :</b> Lichman, M. et Bache, K. (2013). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Évaluations de restaurant</td>
  <td valign=top>
Contient des indices donnés par des utilisateurs pour les restaurants sur une échelle de 0 à 2.<p> </p><b>Utilisation :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, à former et tester un système de recommandation. <p> </p><b>Recherche :</b> Lichman, M. et Bache, K. (2013). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Dataset de classe multiples de recuit en acier</td>
  <td valign=top>
Ce groupe de données contient une série d’enregistrements à partir d’acier RECUIT des essais avec les attributs physiques (largeur, épaisseur, types de type (bobine, feuille, etc.) de résultant en acier.<p> </p><b>Utilisation :</b> prévoir un des deux attributs de classe numérique ; dureté ou force. Vous pourriez également analyser des corrélations entre les attributs.<p> </p>Nuances d’acier suivent un jeu standard défini par SAE et d’autres organisations. Vous recherchez une « classe » (la variable de classe) de spécifique et que vous voulez comprendre les valeurs nécessaires. <p> </p><b>Recherche :</b> Sterling, D. et Buntine, w., (NA). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information et d’informatique <p> </p>Un guide utile pour des nuances d’acier se trouve ici : <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Données de télescope</td>
  <td valign=top>
Enregistrements de rafales de particules de gamma de haute énergie avec bruit de fond, à la fois simulé à l’aide d’un processus de Monte Carlo.<p> </p>L’objectif de la simulation était pour améliorer la précision au sol atmosphérique Cherenkov gamma spéciaux, à l’aide de méthodes statistiques pour faire la différence entre le signal de votre choix (douches de rayonnement de Cherenkov) et le bruit de fond (hadronic douches initiées par les rayons cosmiques dans l’atmosphère supérieur).<p> </p>Les données ont été préalablement traitées pour créer un cluster de forme allongé avec le long axe est orienté vers le centre de l’appareil photo. Les caractéristiques de cette ellipse, (souvent appelé Hillas paramètres) sont parmi les paramètres de l’image qui peuvent être utilisés pour la discrimination.<p> </p><b>Utilisation :</b> prévoir si image de douche représente le bruit du signal ou d’arrière-plan.<p> </p><b>Notes :</b> précision de classification Simple n’est pas significative pour ce type de données, depuis un événement en arrière-plan la classification comme signal est pire que la classification d’un événement de signal en tant qu’arrière-plan. Pour la comparaison de classifieurs différents, le graphique ROC doit être utilisé. La probabilité d’accepter un événement en arrière-plan en tant que signal doit être inférieure de l’un des deux seuils suivants : 0,01, 0,02, 0,05, 0,1 ou 0,2.<p> </p>En outre, notez que le nombre d’événements d’arrière-plan (h, de douches hadronic) est sous-estimé, alors que dans les mesures réelles, la classe h ou bruit représente la majorité des événements. <p> </p><b>Recherche :</b> Bock, R.K. (1995). UCI Machine de formation de référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>. Irvine, Californie : Université de Californie, l’établissement de l’Information </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Groupe de données météo</td>
  <td valign=top>
Observations de météo de terre horaire à partir de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">données fusionnées à partir de 201304 à 201310</a>).<p> </p>Les données météorologiques couvrent des observations effectuées à partir de la station météo de l’aéroport, couvrant la période avril-octobre 2013. Avant de le télécharger vers Azure ML Studio, le groupe de données a été traité comme suit :<ul><li>ID de la station météorologique ont été mappés à l’aéroport correspondant ID</li><li>Station météo n’est ne pas associée à l’aéroport le plus occupé 70 ont été filtrées.</li><li>La colonne de Date a été divisée en colonnes d’année, mois et jour séparées</li><li>Les colonnes suivantes ont été sélectionnées : AirportID, année, mois, jour, heure, fuseau horaire, SkyCondition, visibilité, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, vitesse du vent, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimètre</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipedia SP 500 Dataset</td>
  <td valign=top>
Données sont dérivées de Wikipedia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) en fonction des articles de chaque société S & P 500, stockées en tant que données XML.<p> </p>Avant de le télécharger vers Azure ML Studio, le groupe de données a été traité comme suit :<ul><li>Extraire le contenu de texte pour chaque société</li><li>Supprimer la mise en forme du wiki</li><li>Supprimez les caractères non alphanumériques</li><li>Convertir tout le texte en minuscules</li><li>Catégories de société connus ont été ajoutés.</li></ul><p> </p>Notez que, pour certaines entreprises d’un article est introuvable, le nombre d’enregistrements est inférieur à 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Le groupe de données contient les données des clients et des indications sur leur réponse à une campagne de publipostage direct. Chaque ligne représente un client. Le groupe de données contient des 9 fonctionnalités sur les données démographiques des utilisateurs et au-delà de comportement et 3 colonnes étiquette (visitez, conversion et dépenser).  Visite est une colonne binaire qui indique qu’un client visite après la campagne de commercialisation, de conversion indique un client acheté quelque chose et dépenses est le montant qui a été passé.  Le groupe de données a été mis à disposition par Kevin Hillstrom pour MineThatData électronique Analytique et données Mining défi.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Fonctionnalités des exemples de test dans le dataset d’actualités Reuters de RCV1-V2. Le groupe de données a des articles de news 781K avec leurs numéros d’identification (première colonne du groupe de données). Chaque article est sous forme de jetons, stopworded et tige. Le groupe de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Fonctionnalités des exemples de formation dans le dataset d’actualités Reuters de RCV1-V2. Le groupe de données a des articles de news 23K avec leurs numéros d’identification (première colonne du groupe de données). Chaque article est sous forme de jetons, stopworded et tige. Le groupe de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Groupe de données à partir de la découverte de connaissances Cup 1999 KDD et concurrence d’outils (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) d’exploration de données.<p> </p>Le groupe de données a été téléchargée et stockées dans le stockage Azure Blob (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) et comprend des groupes de données de test et de formation. Le groupe de données de formation comporte environ 126 Ko lignes et 43 colonnes, y compris les étiquettes ; 3 colonnes font partie de l’étiquette des informations et 40 colonnes, constituée de fonctions numériques et de chaîne/catégorique, sont disponibles pour la formation du modèle. Les données de test ont environ 22,5 K tester exemples avec les mêmes 43 colonnes dans les données d’apprentissage.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top>
Affectations de rubrique pour les articles de news dans le dataset d’actualités Reuters de RCV1-V2. Un article peut être affecté à plusieurs rubriques. Le format de chaque ligne est "<topic name>  <document id> 1". Le groupe de données contient les affectations de rubrique 2.6M. Le groupe de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Ces données proviennent de la stimulation de d’évaluation de performances KDD Cup 2010 étudiant (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">évaluation des performances de stagiaire</a>). Les données utilisées sont le jeu de formation de Algebra_2008_2009 (matrice de pressage, J., Niculescu-Mizil, S. A., Ritter, Gordon, G.J. et Koedinger, K.R. (2010). Algèbre I 2008-2009. Défi le jeu de données à partir de KDD Cup 2010 éducatif données Mining défi. Venez la trouver dans <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Le groupe de données a été téléchargée et stockées dans le stockage Azure Blob (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) et contient les fichiers journaux à partir d’un étudiant cours particuliers du système. Les fonctionnalités fournies incluent l’étudiant avant la résolution de ce problème dans la bonne façon de problème ID et sa description brève, ID de l’étudiant, timestamp et le nombre de tentatives. Le dataset d’origine a des enregistrements de 8,9 M, ce groupe de données a été sous-échantillonnée pour les premières lignes de 100 Ko. Le dataset a 23 colonnes séparées par des tabulations de divers types : numérique, par catégorie et timestamp.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
