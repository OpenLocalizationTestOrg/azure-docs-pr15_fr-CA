<properties 
    pageTitle="Instructions d’installation de modèle de solution analytique véhicule télémétrie PowerBI le tableau de bord | Microsoft Azure" 
    description="Les capacités d’Intelligence de Cortana permet d’obtenir des analyses prédictives et en temps réel sur la santé du véhicule et à la conduite habitudes." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="bradsev" />


# <a name="vehicle-telemetry-analytics-solution-template-powerbi-dashboard-setup-instructions"></a>Instructions de configuration du tableau de bord PowerBI de véhicule télémétrie analytique solution modèle

Cette **menu** contient des liens vers les chapitres de ce manuel. 

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


La solution Analytique de télémétrie de véhicule présente comment les concessionnaires de voitures, les constructeurs automobiles et les compagnies d’assurance peuvent exploiter les capacités d’Intelligence de Cortana pour obtenir des analyses prédictives et en temps réel sur la santé du véhicule et rencontrez des habitudes de conduite à apporter des améliorations dans le domaine du client, R & D et campagnes marketing. Ce document contient des instructions étape par étape sur comment vous pouvez configurer le tableau de bord et les rapports de PowerBI une fois que la solution est déployée dans votre abonnement. 


## <a name="prerequisites"></a>Conditions préalables
1.  Déployer la solution de véhicule télémétrie Analytique en accédant à [https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)  
2.  [Installer Microsoft Power BI bureau](http://www.microsoft.com/download/details.aspx?id=45331)
3.  Un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous n’avez pas un abonnement Azure, mise en route avec l’abonnement gratuit d’Azure
4.  Compte de Microsoft PowerBI
    

## <a name="cortana-intelligence-suite-components"></a>Composants de la Suite Intelligence Cortana
Dans le cadre du modèle de solution Analytique de télémétrie de véhicule, les services Cortana Intelligence suivants sont déployés dans votre abonnement.

- **Événement concentrateurs** pour traiter des millions d’événements de télémesure de véhicule dans Azure.
- **Les flux**s pour obtenir des informations en temps réel sur la santé du véhicule et l’enregistre ces données dans le stockage à long terme pour l’analytique de lot plus riche.
- L' **Apprentissage automatique** pour détecter une anomalie en temps réel et de traitement par lots pour obtenir les analyses prédictives.
- **HDInsight** est utilisé pour transformer des données à grande échelle
- **Data Factory** gère d’orchestration, de gestion des ressources de planification et de surveillance du pipeline de traitement par lots.

**Alimentation BI** donne à cette solution un tableau de bord riche pour les données en temps réel et les visualisations analytique prédictive. 

La solution utilise deux sources de données différentes : **les signaux simulés de véhicule et groupe de données de diagnostic** et **catalogue du véhicule**.

Un simulateur de télématique du véhicule est inclus dans le cadre de cette solution. Il émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et d’un cycle d’essai en un point donné dans le temps. 

Le catalogue de véhicule est un VIN contenant de dataset de référence à un mappage de modèle


## <a name="powerbi-dashboard-preparation"></a>Préparation de la PowerBI le tableau de bord

### <a name="deployment"></a>Déploiement

Une fois le déploiement terminé, vous devriez voir le diagramme suivant avec tous ces composants marqués en vert. 

- Pour accéder à des services correspondants permettant de valider si tous ces éléments ont déployé avec succès, cliquez sur la flèche dans le coin supérieur droit des nœuds verts.
- Pour télécharger le package de simulateur de données, cliquez sur la flèche dans le coin supérieur droit sur le nœud de **Simulateur de VEHICULE télématiques** . Enregistrer et extraire les fichiers localement sur votre ordinateur. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

Maintenant, vous êtes prêt à configurer le tableau de bord de PowerBI avec des visualisations enrichies pour obtenir en temps réel et des analyses prédictives sur la santé du véhicule et à la conduite habituelles. Il prend environ 45 minutes à une heure à créer tous les rapports et configurer le tableau de bord. 


### <a name="setup-power-bi-real-time-dashboard"></a>Le programme d’installation d’alimentation en temps réel tableau de bord

**Générer des données simulées**

1. Sur votre ordinateur local, accédez au dossier où vous avez extrait le package de simulateur de VEHICULE télématique![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)
2.  Exécutez l’application ***CarEventGenerator.exe***.
3.  Il émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et d’un cycle d’essai en un point donné dans le temps. Il est publié vers une instance d’Azure événement concentrateur qui est configurée dans le cadre de votre déploiement.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
     
**Démarrez l’application de tableau de bord en temps réel**

La solution comprend une application qui génère un tableau de bord en temps réel dans les PowerBI. Cette application est à l’écoute à une instance de concentrateur d’événements, à partir de laquelle Analytique de flux publie les événements en permanence. Pour chaque événement qui reçoit de cette application, il traite les données à l’aide d’un point de terminaison de notation de formation de Machine demande-réponse. Le groupe de données résultant est publié sur la diffusion PowerBI API pour visualisation. 

Pour télécharger l’application :

1.  Cliquez sur le nœud PowerBI sur la vue de diagramme et cliquez sur l' **Application de tableau de bord en temps réel de télécharger**« lien dans le volet Propriétés.![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard-new1.png)
2.  Extraire et enregistrer l’application localement![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.  Exécutez l’application **RealtimeDashboardApp.exe**
4.  Fournir des informations d’identification valides de puissance BI, connectez-vous et cliquez sur **Accepter**
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
    
    ![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)


### <a name="configure-powerbi-reports"></a>Configurer les rapports de PowerBI
Tableau de bord et les rapports en temps réel prennent environ 30 à 45 minutes. Accédez à [http://powerbi.com](http://powerbi.com) et de la connexion.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-1-powerbi-signin.png)

Un nouveau groupe de données est généré dans BI d’alimentation. Cliquez sur le groupe de données **ConnectedCarsRealtime** .

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

Enregistrer le rapport vide à l’aide de **Ctrl + s**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

Fournir *des rapports en temps réel de véhicule télémétrie Analytique -*nom de l’état.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)

## <a name="real-time-reports"></a>Rapports en temps réel
Il existe trois rapports en temps réel dans cette solution :

1.  Véhicules de l’opération
2.  Véhicules exigeant une Maintenance
3.  Statistiques de la santé de véhicules

Vous pouvez choisir de configurer tous les trois rapports en temps réel ou après n’importe quelle étape et passez à la section suivante de la configuration de le rapports en lot. Nous vous recommandons de créer les trois rapports permet de visualiser les analyses complètes du chemin d’accès en temps réel de la solution.  

### <a name="1-vehicles-in-operation"></a>1. les véhicules dans l’opération
  
Double-cliquez sur la **Page 1** et renommez-le « Véhicules dans l’opération »  
    ![Connecté voitures - véhicules de l’opération](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)  

Champ de **vin** à partir de **champs** , sélectionnez type de visualisation comme **« Carte »**.  

Visualisation de la carte est créée comme illustré à la figure.  
    ![Voitures connectées - sélection de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez la **Ville** et le **numéro d’identification** à partir des champs. Modifier la visualisation à **« Mappage »**. Dans la zone valeurs, faites glisser **vin** . Faites glisser la **Ville** à partir des champs à la zone de **légende** .   
    ![Connecté voitures - visualisation de la carte](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
Sélectionnez la section **format** de **visualisations**, cliquez sur **titre** et modifier le **texte** pour **« Véhicules dans l’opération par ville »**.  
    ![Connecté voitures - véhicules dans l’opération par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)   

Visualisation finale se présente comme illustré à la figure.    
    ![Voitures connectées - visualisation finale](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez la **Ville** et le **numéro d’identification**, modifier le type de visualisation **d’Histogramme groupé**. Assurez-vous que le champ **Ville** de la **zone de l’axe** et de **vin** dans la **zone de valeur**  

Trier le diagramme par le **« Nombre de vin »**  
    ![Voitures connectées - nombre de vin](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)  

Modifier le **titre** de graphique **«** véhicules dans l’opération par ville »  

Cliquez sur la section de **Format** , puis sélectionnez les **Couleurs de données**, cliquez sur **« On »** pour **Afficher tout**  
    ![Voitures connectées - afficher toutes les données de couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)  

Modifier la couleur de chaque ville en cliquant sur l’icône de couleur.  
    ![Connecté voitures - modifier les couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Histogramme groupé** visualisation de visualisations, faites glisser les champs **ville** dans la zone **axe** , le **modèle** dans la zone de **légende** et le **numéro d’identification** dans la zone **valeur** .  
    ![Voitures connectées - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png)  
    ![Voitures connectées - rendu](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
Réorganiser toutes les visualisation sur cette page comme indiqué dans la figure.  
    ![Voitures connectées - visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

Vous avez correctement configuré le rapport en temps réel « Véhicules dans l’opération ». Vous pouvez procéder à créer le prochain rapport en temps réel ou arrêter ici et configurer le tableau de bord. 

### <a name="2-vehicles-requiring-maintenance"></a>2. les véhicules nécessitant une Maintenance
  
Cliquez sur ![ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport, renommez-le **« véhicules nécessitant une Maintenance »**

![Voitures connectées - véhicules exigeant une Maintenance](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)  

Sélectionnez champ de **vin** et de modifier le type de visualisation à la **carte**.  
    ![Voitures connectées - visualisation de carte d’identification du véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)  

Nous avons un champ nommé « MaintenanceLabel » dans le dataset. Ce champ peut avoir une valeur de « 0 » ou « 1 ». » Il est défini par le modèle d’apprentissage automatique de Azure mis en service dans le cadre de la solution et intégré avec le chemin d’accès en temps réel. La valeur « 1 » indique un véhicule requiert une maintenance. 

Pour ajouter un filtre de **Niveau de Page** pour afficher des données de véhicules, qui nécessitent la maintenance : 

1. Dans **Filtres de niveau de Page**, faites glisser le champ **« MaintenanceLabel »** .  
![Voitures connectées - filtres au niveau de la Page](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. Cliquez sur le menu de **Filtrage de base** présente au bas du filtre de niveau Page MaintenanceLabel.  
![Connecté voitures - filtrage de base](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)  

3.  Définissez sa valeur de filtre sur **« 1 »**    
![Voitures connectées - valeur de filtre](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)  


Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Histogramme groupé** de visualisations  
![Voitures connectées - visualisation de la carte de Vind](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png)  
![Voitures connectées - Histogramme groupé](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

Faites glisser le champ **modèle** dans la zone **axe** , **Vin** à la zone de **valeur** . Trier ensuite la visualisation en fonction **du nombre de vin**.  Modifier le **titre** de graphique **«** véhicules nécessitant une maintenance par modèle »  

Faites glisser les champs de **vin** dans la **Saturation des couleurs** présent dans les **champs** ![champs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) section de l’onglet de **visualisation**  
![Voitures connectées - Saturation des couleurs](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)  

Modifier **Les couleurs de données** de visualisation à partir de la section **Format**  
Changer de couleur Minimum à : **F2C812**  
Modifier couleur maximale : **FF6300**  
![Connecté de voitures - et les modifications de couleur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png)  
![Connecté voitures - nouvelles couleurs de visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez le **graphique en histogrammes Clustered** de visualisations, faites glisser le champ de **numéro d’identification** dans la zone **valeur** , glisser le champ **Ville** dans la zone de **l’axe** . Trier le diagramme par le **« Nombre de vin »**. Modifier le **titre** de graphique à **« Véhicules nécessitant une maintenance par ville »**   
![Voitures connectées - véhicules exigeant une maintenance par ville](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

Sélectionnez **Plusieurs lignes carte** visualisation de visualisations, faites glisser le **modèle** et le **numéro d’identification** dans la zone **champs** .  
![Voitures connectées - carte de plusieurs ligne](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)    

Réorganisation de l’ensemble de la visualisation, l’état final se présente comme suit :  
![Voitures connectées - carte de plusieurs ligne](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)  

Vous avez correctement configuré le rapport en temps réel « Véhicules nécessitant une Maintenance ». Vous pouvez procéder à créer le prochain rapport en temps réel ou arrêter ici et configurer le tableau de bord. 

### <a name="3-vehicles-health-statistics"></a>3. statistiques de la santé des véhicules
  
Cliquez sur ![ajouter](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png) pour ajouter un nouveau rapport, renommez-le **« statistiques de la santé de véhicules »**  

Sélectionnez **indicateur de** visualisation de visualisations, puis faites glisser le champ de **vitesse** dans des zones de **valeur, valeur minimale, valeur maximale** .  
![Voitures connectées - carte de plusieurs ligne](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)  

Modifier l’agrégation par défaut de la **vitesse** dans la **zone valeur** à **moyenne** 

Modification de l’agrégation par défaut de la **vitesse** dans la **zone du Minimum** au **Minimum**

Modifier l’agrégation par défaut de la **vitesse** dans la **zone de Maximum** sur **Maximum**

![Voitures connectées - carte de plusieurs ligne](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)  

Renommer le **Titre de la jauge** à **« Average speed »** 
 
![Voitures connectées - indicateur](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)  

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.  

De la même façon ajouter une **jauge** **d’huile de moteur moyen**, **moyenne de carburant**et **moteur moyenne tempérée**.  

Indicateur de la modification des étapes de l’agrégation par défaut des champs de chaque jauge comme indiqué ci-dessus dans **« Average speed »** .

![Voitures connectées - jauges](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **ligne et histogramme groupé** de visualisations, puis faites-la glisser champ **ville** dans l' **Axe de partagés**, glisser, **vitesse**, **champs tirepressure et engineoil** dans la zone des **Valeurs de colonne** , modifier leur type d’agrégation à **moyen**. 

Faites glisser le champ **engineTemperature** dans la zone **Valeurs de la ligne** , modifiez le type d’agrégation à la **moyenne**. 

![Voitures connectées - champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

Modifier le **titre** du graphique à la **« vitesse moyenne, la pression des pneus, huile moteur et température du moteur »**.  

![Voitures connectées - champs de visualisations](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez **Treemap** visualisation de visualisations, faites glisser le champ de **modèle** dans la zone de **groupe** et faites glisser le champ **MaintenanceProbability** dans la zone des **valeurs** .

Modifier le **titre** du graphique **«**modèles de véhicule nécessitant une maintenance ».

![Connecté voitures - modifier le titre de graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez le **graphique à barres empilées de 100 %** à partir de visualisation, faites glisser le champ **ville** dans la zone **axe** et faites glisser de la **MaintenanceProbability**, **RecallProbability** des champs dans la zone **valeur** .

![Voitures connectées - ajouter nouvelle visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

Cliquez sur **Format**, sélectionnez les **Couleurs de données**et définir la couleur du **MaintenanceProbability** la valeur **« F2C80F »**.

Modifier le **titre** du graphique **« probabilité de véhicule Maintenance & rappeler par**la ville ».

![Voitures connectées - ajouter nouvelle visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation.

Sélectionnez la **Zone graphique** de visualisation de visualisations, faites glisser le champ de **modèle** dans la zone **axe** et faites glisser les champs **engineOil, tirepressure, vitesse et MaintenanceProbability** dans la zone des **valeurs** . Modifier leur type d’agrégation à **« Moyen »**. 

![Connecté voitures - modifier le Type d’agrégation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

Modifier le titre du graphique **« moyenne d’huile de moteur**, pneu probabilité de pression, de vitesse et de maintenance par modèle ».

![Connecté voitures - modifier le titre de graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

Cliquez sur la zone vide pour ajouter la nouvelle visualisation :

1. Sélectionnez la visualisation du **Graphique de nuages de points** de visualisations.
2. Faites glisser le champ de **modèle** dans la zone de **Détails** et de la **légende** .
3. Faites glisser le champ de **carburant** dans la zone **axe des abscisses** , modifier le groupement à **moyen**.
4. Faites glisser **engineTemparature** dans la **zone de l’axe y**, l’agrégation au **moyen**
5. Dans la zone **taille** , faites glisser le champ de **numéro d’identification** .


![Voitures connectées - ajouter nouvelle visualisation](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

Modifier le **titre** du graphique **« moyennes de carburant**, température du moteur par modèle ».

![Connecté voitures - modifier le titre de graphique](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

Le rapport final ressemblera comme indiqué ci-dessous.

![Rapport de voitures-Final connecté](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### <a name="pin-visualizations-from-the-reports-to-the-real-time-dashboard"></a>Visualisations de broche de le rapports au tableau de bord en temps réel
  
Créer un tableau de bord vide en cliquant sur l’icône de signe plu en regard de tableaux de bord. Vous pouvez la nommer « Véhicule télémétrie Analytique tableau de bord »

![Voitures connecté-tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.5.png)

Code PIN de la visualisation de le rapports ci-dessus au tableau de bord. 
 
![Voitures connecté-tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.6.png)

Le tableau de bord doit se présenter comme suit lorsque les trois rapports sont créées et les visualisations correspondantes sont ancrées au tableau de bord. Si vous n’avez pas créé tous les rapports, votre tableau de bord peut différer. 

![Voitures connecté-tableau de bord](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)

Félicitations ! Vous avez créé le tableau de bord en temps réel. Lorsque vous continuez à exécuter CarEventGenerator.exe et RealtimeDashboardApp.exe, vous devez voir les mises à jour direct sur le tableau de bord. Il doit prendre environ 10 à 15 minutes pour effectuer les étapes suivantes.

 
##  <a name="setup-power-bi-batch-processing-dashboard"></a>Le programme d’installation de puissance lot traitement de tableau de bord

>[AZURE.NOTE] Il faut environ deux heures (à partir de la réussite du déploiement) pour le pipeline de traitement par lots de bout en bout terminer l’exécution et de traiter une année de données générées. Par conséquent, attendez que le traitement se termine avant de passer aux étapes suivantes. 

**Téléchargez le fichier de concepteur PowerBI**

-   Un fichier de concepteur PowerBI préconfiguré est inclus dans le cadre du déploiement
-   Cliquez sur le nœud PowerBI sur la vue de diagramme et cliquez sur **Télécharger le fichier du concepteur PowerBI** le lien dans le volet des propriétés![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9.5-download-powerbi-designer.png)

-   Enregistrer localement

**Configurer les rapports de PowerBI**

-   Ouvrez le fichier du concepteur « VehicleTelemetryAnalytics - Report.pbix de bureau » à l’aide de PowerBI bureau. Si vous n’avez pas encore, installer PowerBI Desktop à partir de [l’installation du bureau de PowerBI](http://www.microsoft.com/download/details.aspx?id=45331). 

-   Cliquez sur la **modification des requêtes**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

- Double-cliquez sur la **Source**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- Mettre à jour la chaîne de connexion serveur avec le serveur SQL d’Azure qui a été mis en service dans le cadre du déploiement. Cliquez sur le nœud Azure SQL sur le diagramme et afficher le nom du serveur du volet Propriétés.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11.5-view-server-name.png)

- Laisser la **base de données** en tant que *connectedcar*.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- Cliquez sur **OK**.
- Vous verrez onglet **d’informations d’identification Windows** sélectionné par défaut, modifier la base de données **d’informations d’identification** en cliquant sur l’onglet **base de données** à droite.
- Fournir le **nom d’utilisateur** et le **mot de passe** de votre base de données SQL Azure qui a été spécifié lors de l’installation de son déploiement.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- Cliquez sur **se connecter**
- Répétez les étapes ci-dessus pour chacun des trois requêtes restantes présents au volet de droite, puis mettre à jour les détails de connexion de source de données.
- Cliquez sur **Fermer et la charger**. Fichier des groupes de données BI bureau d’alimentation sont connectés à des tables de base de données de SQL Azure.
- **Fermer** Fichier de bureau de BI d’alimentation.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- Cliquez sur le bouton **Enregistrer** pour enregistrer les modifications. 
 
Vous avez configuré tous les rapports correspondant pour le chemin d’accès de traitement par lots dans la solution. 


## <a name="upload-to-powerbicom"></a>Téléchargement de *powerbi.com*
 
1.  Accédez au portail web de PowerBI à http://powerbi.com et de la connexion.
2.  Cliquez sur **obtenir les données**  
3.  Téléchargez le fichier du bureau de BI d’alimentation.  
4.  Pour télécharger, cliquez sur **obtenir les données -> obtenir des fichiers -> fichier Local**  
5.  Accédez à la **« VehicleTelemetryAnalytics – Report.pbix bureau »**  
6.  Une fois que le fichier est téléchargé, vous allez accéder à votre espace de travail de puissance BI.  

Un groupe de données, rapport et un tableau de bord vide seront créés pour vous.  
 

Diagrammes de PIN au tableau de bord existant **Du tableau de bord de véhicule télémétrie Analytique** **D’alimentation**BI. Cliquez sur le tableau de bord vide créé ci-dessus et accédez à le **rapports** cliquez ensuite le rapport qui vient d’être téléchargé.  

![PowerBI.com de télémétrie de véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png) 


**Notez que l’état comporte six pages :**  
Page 1 : Densité véhicule  
Page 2 : Santé du véhicule en temps réel  
Page 3 : Véhicules à moteur agressive   
Page 4 : Véhicules de rappeler  
Page 5 : Carburant véhicules à moteur efficace  
6 de la page : Logo de Contoso  

![PowerBI.com de véhicules connectés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**À partir de Page 3**, code pin suivants :  

1.  Nombre de VIN  
    ![PowerBI.com de véhicules connectés](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 

2.  Véhicules à moteur activement par le modèle de graphique de la chute d’eau  
    ![Véhicule télémétrie - graphiques de broche 4](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**À partir de Page 5**, code pin suivants : 
 
1.  Nombre de vin    
    ![Véhicule télémétrie - graphiques de broche 5](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png)  
2.  Véhicules efficaces de carburant par modèle : histogramme  
    ![Véhicule télémétrie - graphiques de broche 6](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**À partir de Page 4**broches suivantes :  

1.  Nombre de vin  
    ![Véhicule télémétrie - graphiques de broche 7](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.  Les véhicules rappelés par ville, modèle : Treemap  
    ![Véhicule télémétrie - graphiques de broche 8](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)  

**À partir de la Page 6**, code pin suivants :  

1.  Logo de Contoso auto  
    ![Véhicule télémétrie - graphiques de broche 9](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**Organiser le tableau de bord**  

1.  Accédez au tableau de bord
2.  Survolez chaque graphique et les renommer selon l’attribution de noms fournies dans l’image du tableau de bord complet ci-dessous. Également déplacer les graphiques pour se présenter comme le tableau de bord ci-dessous.  
    ![TELEMETRIE de véhicule - organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)  
    ![PowerBI.com de télémétrie de véhicule](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)
3.  Si vous avez créé tous les rapports comme mentionné dans ce document, le tableau de bord terminée final doit ressembler à la figure suivante. 

![TELEMETRIE de véhicule - organiser le tableau de bord 2](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard3.png)

Félicitations ! Vous avez créé le rapports et le tableau de bord pour accéder en temps réel, prédictive et conseils sur le fonctionnement du véhicule et à la conduite de traitement par lots habituelles.  
