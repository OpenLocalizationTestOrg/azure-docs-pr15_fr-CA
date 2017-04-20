<properties
    pageTitle="Mise en route Azure flux Analytique pour traiter des données à partir de périphériques de IoT. | Microsoft Azure"
    description="Flux de balises et de données de capteur analytique de flux et de traitement des données en temps réel IoT"
    keywords="IOT solution, mise en route d’iot"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="10/19/2016"
    ms.author="jeffstok"
/>

# <a name="get-started-with-azure-stream-analytics-to-process-data-from-iot-devices"></a>Mise en route avec Azure flux Analytique pour traiter des données à partir de périphériques de IoT

Dans ce didacticiel, vous allez apprendre à créer la logique de traitement des flux de données pour collecter des données à partir de périphériques de l’Internet des objets (IoT). Un cas d’usage réel, Internet des objets (IoT) nous permet de vous montrer comment générer votre solution rapidement et à moindre coût.

## <a name="prerequisites"></a>Conditions préalables

-   [Abonnement Azure](https://azure.microsoft.com/pricing/free-trial/)
-   Données de requêtes et de fichiers de l’exemple téléchargeables à partir de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Scénario

Contoso, qui est une société dans l’espace de l’automatisation industrielle, son procédé de fabrication entièrement automatisées. La machine dans cette usine a capteurs capables d’émettre des flux de données en temps réel. Dans ce scénario, un gestionnaire de plancher de production souhaite des analyses en temps réel à partir des données de capteur pour rechercher des modèles et effectuer des opérations sur les. Nous allons utiliser le langage de requête Analytique flux (SAQL) sur les données des capteurs pour rechercher des modèles intéressants à partir de flux de données entrant.

Ici les données sont générées à partir d’un capteur de balise de Texas Instruments.

![Balise de capteur de Texas Instruments](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

La charge utile des données est au format JSON et ressemble à ceci :


    {
        "time": "2016-01-26T20:47:53.0000000",  
        "dspl": "sensorE",  
        "temp": 123,  
        "hmdt": 34  
    }  

Dans un scénario réel, vous pouvez avoir des centaines de ces capteurs générant des événements sous la forme d’un flux de données. Dans l’idéal, une passerelle exécuterait code pour distribuer ces événements pour [Azure événement concentrateurs](https://azure.microsoft.com/services/event-hubs/) ou [Les concentrateurs IoT Azure](https://azure.microsoft.com/services/iot-hub/). Votre tâche de flux de données Analytique serait réceptionner ces événements de concentrateurs d’événements et exécuter des requêtes de l’analytique en temps réel sur les flux de données. Ensuite, vous pouvez envoyer les résultats à l’un de la [prise en charge des sorties](stream-analytics-define-outputs.md).

Pour une facilité d’utilisation, ce guide de mise en route fournit un exemple de fichier de données ont été capturée à partir de périphériques de balise de capteur réel. Vous pouvez exécuter des requêtes sur les exemples de données et afficher les résultats. Dans les didacticiels suivants, vous apprendrez comment connecter votre travail aux entrées et sorties et les déployez sur le service Azure.

## <a name="create-a-stream-analytics-job"></a>Créer une tâche de flux de données Analytique

1. Dans le [portail Azure](http://portal.azure.com), cliquez sur le signe plus et puis tapez **Analyse de flux de données** dans la fenêtre de texte à droite. Puis sélectionnez **Analytique de flux de travail** dans la liste des résultats.

    ![Créer une nouvelle tâche de flux de données Analytique](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

2. Entrez un nom unique de la tâche et vérifiez que l’abonnement est correcte pour votre travail. Puis créez un nouveau groupe de ressources ou sélectionnez-en une existante sur votre abonnement.

3. Sélectionnez un emplacement pour votre projet. Vitesse de traitement et de réduction des coûts dans les données de transfert sélectionnant le même emplacement que le groupe de ressources et d’un compte de stockage prévu est recommandé.

    ![Créer une nouvelle tâche de flux de données Analytique des détails](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

    > [AZURE.NOTE] Vous devez créer ce compte de stockage qu’une seule fois par région. Ce système de stockage sera partagé pour tous les travaux d’Analytique de flux de données qui sont créées dans cette région.

4. Cochez la case pour placer votre travail sur votre tableau de bord, puis sur **créer**.

    ![Création de la tâche en cours](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03a.png)

5. Vous devez voir un « déploiement démarré... » affiché en haut à droite de la fenêtre de votre navigateur. Dès qu’il devient une fenêtre terminée comme indiqué ci-dessous.

    ![Création de la tâche en cours](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03b.png)

### <a name="create-an-azure-stream-analytics-query"></a>Créer une requête Analytique de flux de données Azure

Une fois votre projet créé, il est temps de les ouvrir et de créer une requête. Vous pouvez facilement accéder à votre projet en cliquant sur la mosaïque pour lui.

![Mosaïque de travail](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Dans le volet **Topology de travail** , cliquez sur la zone de la **requête** pour accéder à l’éditeur de requête. L’éditeur de **requête** vous permet d’entrer une requête T-SQL qui exécute la transformation sur les données d’événement entrant.

![Zone de requête](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

### <a name="query-archive-your-raw-data"></a>Requête : Archiver vos données brutes

La forme la plus simple de requête est une requête SQL directe qui archive toutes les données d’entrée à sa sortie désigné. Téléchargez l’exemple de fichier de données à partir de [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot) vers un emplacement sur votre ordinateur. 

1. Collez la requête à partir du fichier PassThrough.txt. 

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

2. Sur les trois points en regard de votre entrée et sélectionnez la zone de **téléchargement des exemples de données à partir du fichier** .

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06a.png)

3. Un volet s’ouvre sur la droite en conséquence, de sélectionner le fichier de données HelloWorldASA-InputStream.json à partir de votre emplacement de téléchargement et cliquez sur **OK** au bas du volet.

    ![Flux d’entrée de test](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06b.png)

4. Cliquez sur le rapport **de Test** dans le coin supérieur gauche de la fenêtre, puis traiter votre requête de test dans l’exemple de dataset. Une fenêtre de résultats s’ouvre sous votre requête que le traitement est terminé.

    ![Résultats des tests](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Requête : Filtrer les données en fonction d’une condition

Nous allons essayer de filtrer les résultats en fonction d’une condition. Nous souhaitons afficher les résultats pour les seuls événements qui proviennent de « sensorA ». La requête se trouve dans le fichier Filtering.txt.

![Filtrage d’un flux de données](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Notez que la requête sensible à la casse compare une valeur de chaîne. Cliquez sur le rapport de **Test** pour exécuter la requête. La requête doit retourner des 389 lignes événements de 1860.

![Résultats du deuxième test de requête](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Requête : Alerte pour déclencher un workflow métier

Créons notre requête plus détaillée. Pour chaque type de capteur, nous souhaitons surveiller la température moyenne par fenêtre de 30 secondes et d’afficher des résultats que si la température moyenne est supérieure à 100 degrés. Nous écrire la requête suivante et puis cliquez sur **Test** pour afficher les résultats. La requête se trouve dans le fichier ThresholdAlerting.txt.

![requête de filtre de 30 secondes](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Vous devez maintenant voir des résultats qui contiennent seulement 245 lignes et noms des capteurs dont la moyenne tempérée est supérieure à 100. Cette requête regroupe le flux d’événements en **dspl**, qui est le nom du capteur, via une **Fenêtre de distribution automatique** de 30 secondes. Requêtes temporelles doivent indiquer comment nous voulons au progrès. À l’aide de la clause **BY TIMESTAMP** , nous avons spécifié la colonne **OUTPUTTIME** pour associer tous les calculs temporelles des heures. Pour plus d’informations, lisez les articles MSDN sur la [Gestion du temps](https://msdn.microsoft.com/library/azure/mt582045.aspx) et des [fonctions multifenêtre](https://msdn.microsoft.com/library/azure/dn835019.aspx).

### <a name="query-detect-absence-of-events"></a>Requête : Détecter l’absence d’événements

Comment nous pouvons pour écrire une requête pour rechercher un manque d’événements d’entrée ? Nous allons rechercher la dernière fois qu’un capteur transmis des données et puis n’a pas envoyé les événements pour la prochaine minute. La requête se trouve dans le fichier AbsenseOfEvent.txt.

![Détecter l’absence d’événements](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Nous utilisons ici une jointure **Externe gauche** dans le même flux de données (jointure réflexive). Pour une jointure **interne** , un résultat est retourné uniquement si une correspondance est trouvée.  Pour une jointure **Externe gauche** , si un événement à partir de la gauche de la jointure qui ne correspondent pas, une ligne qui a la valeur NULL pour toutes les colonnes du côté droit est renvoyée. Cette technique est très utile pour vérifier l’absence d’événements. Consultez notre documentation MSDN pour plus d’informations sur la [jointure](https://msdn.microsoft.com/library/azure/dn835026.aspx).

## <a name="conclusion"></a>Conclusion

L’objectif de ce didacticiel est de montrer comment écrire des requêtes de langage de requête Analytique de flux de données différents et afficher les résultats dans le navigateur. Toutefois, il est simplement mise en route. Vous pouvez faire tellement plus avec les flux de données Analytique. Analytique de flux prend en charge une variété d’entrées et sorties et peut même utiliser fonctions dans Azure Machine Learning pour faciliter un outil puissant pour l’analyse des flux de données. Vous pouvez commencer à Découvrez Analytique de flux de données à l’aide de notre [plan de formation](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/). Pour plus d’informations sur la façon d’écrire des requêtes, lisez l’article sur les [modèles de requête commun](./stream-analytics-stream-analytics-query-patterns.md).
