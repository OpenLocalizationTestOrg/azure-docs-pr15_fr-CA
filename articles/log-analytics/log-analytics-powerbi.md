<properties
   pageTitle="Exporter des données d’Analytique de journal BI d’alimentation | Microsoft Azure"
   description="Alimentation BI est un service d’analytique de business de nuage de points basée à partir de Microsoft qui fournit une visualisation riche et rapports pour l’analyse des différents ensembles de données.  Analytique de journal peut en permanence exporter des données à partir du référentiel de l’OMS en puissance BI pour valoriser ses visualisations et les outils d’analyse.  Cet article décrit comment configurer des requêtes dans le journal Analytique d’exporter automatiquement à l’analyse Décisionnelle de puissance à intervalles réguliers."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="export-log-analytics-data-to-power-bi"></a>Exporter des données d’Analytique de journal BI de puissance

[Alimentation BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) est un service d’analytique de business de nuage de points basée à partir de Microsoft qui fournit une visualisation riche et rapports pour l’analyse des différents ensembles de données.  Analytique de journal peut exporter automatiquement des données à partir du référentiel de l’OMS en puissance BI pour valoriser ses visualisations et les outils d’analyse.

Lorsque vous configurez la BI de puissance avec journal Analytique, vous créez des requêtes de journal qui exportent leurs résultats aux groupes de données correspondant dans BI d’alimentation.  La requête et l’exportation se poursuit automatiquement selon un planning que vous définissez pour le groupe de données à jour avec les dernières données collectées par journal Analytique.

![Analytique de journal à la veille stratégique de puissance](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Planifications d’analyse Décisionnelle d’alimentation

Une *Planification d’analyse Décisionnelle d’alimentation* inclut une recherche de journal qui exporte un ensemble de données à partir du référentiel de l’OMS, à un groupe de données correspondant dans BI de puissance et une planification qui définit la fréquence d’exécution de cette recherche pour conserver le groupe de données en cours.

Les champs du groupe de données seront correspondent aux propriétés d’enregistrements renvoyés par la recherche du journal.  Si la recherche renvoie les enregistrements des différents types puis le dataset inclut toutes les propriétés de chacun des types d’enregistrements inclus.  

> [AZURE.NOTE] Il est recommandé d’utiliser une requête de recherche de journal qui renvoie des données brutes et non d’une consolidation à l’aide des commandes de [mesure](log-analytics-search-reference.md#measure).  Vous pouvez effectuer tous les calculs et agrégation d’alimentation BI à partir des données brutes.

## <a name="connecting-oms-workspace-to-power-bi"></a>Connexion d’espace de travail de l’OMS pour alimentation BI

Avant d’exporter à partir du journal Analytique à BI d’alimentation, vous devez vous connecter à votre espace de travail de l’OMS pour votre compte BI d’alimentation à l’aide de la procédure suivante.  

1. Dans la console de l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Sélectionnez des **comptes**.
3. Dans la section **Informations d’espace de travail** , cliquez sur **se connecter à un compte d’analyse Décisionnelle d’alimentation**.
4. Entrez les informations d’identification de votre compte courant BI.

## <a name="create-a-power-bi-schedule"></a>Créer une planification de BI d’alimentation

Créer une planification de BI de puissance pour chaque jeu de données à l’aide de la procédure suivante.

1. Dans la console de l’OMS sur la case de **Recherche des journaux** .
2. Entrez une nouvelle requête ou sélectionnez une recherche enregistrée qui renvoie les données que vous voulez exporter à **Alimentation BI**.  
3. Cliquez sur le bouton **D’alimentation BI** en haut de la page pour ouvrir la boîte de dialogue **Alimentation BI** .
4. Les informations contenues dans le tableau suivant et cliquez sur **Enregistrer**.

| Propriété | Description |
|:--|:--|
| Nom | Nom permettant d’identifier la planification lorsque vous affichez la liste des planifications de puissance BI. |
| Recherche enregistrée | La recherche du journal à s’exécuter.  Vous pouvez sélectionner la requête en cours ou sélectionnez une recherche enregistrée existante à partir de la zone de liste déroulante. |
| Planification | La fréquence d’exécution de la recherche enregistrée et exporter vers le groupe de données BI d’alimentation.  La valeur doit être entre 15 minutes et 24 heures. |
| Nom du groupe de données | Le nom du groupe de données BI d’alimentation.  Il sera créé s’il n’existe pas et mis à jour si elle n’existe pas. |

## <a name="viewing-and-removing-power-bi-schedules"></a>Affichage et suppression des planifications d’analyse Décisionnelle d’alimentation

Permet d’afficher la liste des planifications de BI d’alimentation existant avec la procédure suivante.

1. Dans la console de l’OMS, cliquez sur la fenêtre de **paramètres** .
2. Sélectionnez **alimentation BI**.

Outre les détails de la planification, le nombre de fois que la planification a été exécuté dans la semaine et l’état de la dernière synchronisation sont affichés.  Si la synchronisation a rencontré des erreurs, vous pouvez cliquer sur le lien pour exécuter une recherche de journaux d’enregistrements avec les détails de l’erreur.

Vous pouvez supprimer une planification en cliquant sur le **X** dans la **colonne de supprimer**.  Vous pouvez désactiver une planification en sélectionnant **désactiver**.  Pour modifier une planification, vous devez le supprimer et le recréer en utilisant les nouveaux paramètres.

![Planifications d’analyse Décisionnelle d’alimentation](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>Procédure d’exemple
La section suivante vous guide à travers un exemple de création d’une planification d’analyse Décisionnelle d’alimentation et à l’aide de son groupe de données pour créer un rapport simple.  Dans cet exemple, toutes les données de performances pour un ensemble d’ordinateurs est exporté à l’analyse Décisionnelle de puissance et un graphique en courbes est ensuite créé pour afficher l’utilisation du processeur.

### <a name="create-log-search"></a>Créer des journaux
Nous allons commencer par la création d’une recherche de journal pour les données à envoyer au groupe de données.  Dans cet exemple, nous allons utiliser une requête qui retourne toutes les données de performances pour les ordinateurs dont le nom commence par *srv*.  

![Planifications d’analyse Décisionnelle d’alimentation](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>Créer d’alimentation BI recherche
Cliquez sur le bouton **D’alimentation BI** pour ouvrir la boîte de dialogue alimentation BI et fournissent les informations requises.  Nous souhaitons que cette recherche à exécuter une fois par heure et de créer un groupe de données appelé *Contoso Perf*.  Puisque nous avons déjà la recherche ouvrir qui crée les données nous souhaitons, nous conservons la valeur par défaut de la *requête de recherche en cours d’utilisation* de la **Recherche enregistrée**.

![Recherche de BI de puissance](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>Vérifiez l’alimentation BI recherche
Pour vérifier que nous avons créé la planification correctement, nous permet d’afficher la liste des recherches de BI de puissance dans la fenêtre **paramètres** dans le tableau de bord OMS.  Nous attendez quelques minutes et actualisez cette vue jusqu'à ce qu’il indique que la synchronisation a été exécutée.

![Recherche de BI de puissance](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>Vérifiez que le groupe de données BI de puissance
Nous allons nous connecter à notre compte [powerbi.microsoft.com](http://powerbi.microsoft.com/) et faites défiler **les groupes de données** en bas du volet gauche.  Nous pouvons voir que le groupe de données de *Performance de Contoso* est répertorié indiquant que notre exportation a exécuté avec succès.

![Groupe de données BI d’alimentation](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>Créez le rapport basé sur le groupe de données
Sélectionnez le groupe de données de **Performance de Contoso** , puis puis cliquez sur **résultats** dans le volet **champs** sur la droite pour afficher les champs qui font partie de ce groupe de données.  Pour créer un graphique en courbes affichant l’utilisation du processeur pour chaque ordinateur, nous effectuer les actions suivantes.

1. Sélectionnez la visualisation graphique de ligne.
2. Faites glisser **NomObjet** à **filtre au niveau du rapport** et vérifier le **processeur**.
3. Faites glisser **CounterName** à **filtre au niveau du rapport** et vérifiez le **% temps processeur**.
4. Faites glisser des **contre-valeurs** aux **valeurs**.
5. Faites glisser **ordinateur** à la **légende**.
6. Faites glisser **TimeGenerated** vers **l’axe**.

Nous pouvons voir que la courbe résultante est affichée avec les données de notre dataset.

![Courbe de puissance BI](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>Enregistrer le rapport
Nous enregistrer le rapport en cliquant sur le bouton Enregistrer en haut de l’écran et valider qu’il est maintenant répertorié dans la section rapports dans le volet gauche.

![Alimentation des rapports BI](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) à créer des requêtes qui peuvent être exportés à l’analyse Décisionnelle de puissance.
- Pour en savoir plus sur [Alimentation BI](http://powerbi.microsoft.com) pour créer des visualisations basées sur les exportations d’Analytique de journal.
