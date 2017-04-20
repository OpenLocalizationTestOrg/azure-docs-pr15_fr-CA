<properties 
    pageTitle="Introduction aux flux de données Analytique | Microsoft Azure" 
    description="Obtenir des informations sur les flux de données Analytique, un service géré qui vous aide à analyser les données de transmission en continu à partir de l’Internet des objets (IoT) en temps réel." 
    keywords="analytique en tant que service, managed services, le traitement de flux, analytique de diffusion en continu, ce qui est analytique de flux"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>


# <a name="what-is-stream-analytics"></a>Quel est le flux Analytique ?

Analytique de flux Azure est un moteur de traitement des événements en temps réel entièrement managé et économique qui permet de déverrouiller des informations fouillées à partir des données. Analytique de flux facilite le paramétrage des calculs analytiques en temps réel sur les données de diffusion en continu à partir de périphériques, capteurs, sites web, sociaux, applications, systèmes d’infrastructure et bien plus encore.

En quelques clics dans Azure portal, vous pouvez créer une tâche de flux Analytique spécifiant la source d’entrée des données en continu, le récepteur de sortie pour les résultats de votre travail, et une transformation de données exprimées dans un langage de type SQL. Vous pouvez surveiller et ajuster l’échelle/la vitesse de votre travail dans le portail Azure mise à l’échelle à partir de quelques kilo-octets à un gigaoctet ou plus d’événements traités par seconde.

Analytique de flux s’appuie sur des années de travail de Microsoft Research dans le développement de hautement optimisée en flux continu de moteurs pour le traitement de la durée de vie critique, ainsi que les intégrations de langue pour les spécifications intuitives de ces.

## <a name="what-can-i-use-stream-analytics-for"></a>Que puis-je utiliser Analytique de flux pour ?
Importants volumes de données sont transmis à haute vitesse sur le réseau dès aujourd'hui. Les organisations qui peuvent traiter et agir sur ces données en flux continu en temps réel peuvent considérablement améliorer l’efficacité et se différencier dans le marché. Scénarios d’analytique de transmission en continu en temps réel, vous pouvez trouver dans tous les secteurs d’activité : personnalisée, en temps réel analyse boursières et alertes proposées par les sociétés de services financiers ; détection des fraudes en temps réel ; services de protection des données et des identités ; réception fiable et l’analyse des données générées par les capteurs et actionneurs incorporés dans des objets physiques (Internet des objets, ou IoT) ; analytique de parcours Web ; et les applications de gestion (CRM) de relation client émettant des alertes lorsque l’expérience client dans un intervalle de temps est dégradé. Les entreprises recherchent la façon plus flexible, fiable et économique effectuer cette analyse des données en temps réel de flux d’événements pour réussir dans le monde hautement compétitif moderne.

## <a name="key-capabilities-and-benefits"></a>Fonctionnalités clés et avantages
-   **Simplicité d’utilisation :** Analytique de flux prend en charge un modèle de requête simple et déclaratif pour décrire les transformations. Afin d’optimiser la facilité d’utilisation, flux Analytique utilise une variante de T-SQL et supprime la nécessité pour les clients à gérer les complexités techniques des systèmes de traitement de flux. À l’aide du [langage de requête Analytique de flux](https://msdn.microsoft.com/library/azure/dn834998.aspx) dans l’éditeur de requête de navigateur, vous obtenez grandement semi-automatique pour vous aider à rapidement et facilement mettre en œuvre des requêtes de série heure, y compris la base temporelle des jointures, les agrégats avec fenêtres, filtres temporelles et autres opérations courantes telles que les jointures, les agrégats, projections et filtres. En outre, le test contre un exemple de fichier de données de la requête dans le navigateur permet un développement itératif rapide.  

-   **L’évolutivité :** Flux de données Analytique est capable de gérer le débit élevé d’événements jusqu'à 1 Go/s. Intégration avec les [Concentrateurs d’événement Azure](https://azure.microsoft.com/services/event-hubs/) et [d’Azure IoT concentrateurs](https://azure.microsoft.com/services/iot-hub/) autoriser la solution à des millions d’événements par seconde provenant de périphériques connectés, de visites, d’acquisition et de fichiers journaux, pour ne citer. Pour ce faire, flux Analytique tire parti de la fonctionnalité de partitionnement de concentrateurs d’événements, qui peuvent produire 1 Mo/s par partition. Les utilisateurs peuvent partitionner le calcul dans un certain nombre d’étapes logiques au sein de la définition de requête, chacun avec la possibilité d’être partitionnées pour accroître l’évolutivité.  

-   **Répétabilité, la fiabilité et la restauration rapide :** Un service géré dans le nuage, les flux de données Analytique permet d’éviter la perte de données et offre une continuité d’activité en cas de panne grâce à des capacités de récupération intégrées. Avec la possibilité de conserver l’état en interne, le service fournit des résultats répétables, garantissant ainsi qu’il est possible d’archiver des événements et de réappliquer le traitement à l’avenir, toujours obtenir les mêmes résultats. Cela permet aux clients de revenir en arrière et examinez les calculs lors d’une analyse des causes profondes, analyse, etc..  

-   **Économique :** Sous la forme d’un service en nuage, flux Analytique est optimisé pour fournir aux utilisateurs un très faible coût pour commencer et tenir à jour les solutions d’analytique en temps réel. Le service est conçu pour vous pour le paiement en fonction de l’utilisation de l’unité de transmission en continu et de la quantité de données traitées par le système. L’utilisation est dérivée en fonction du volume d’événements traités et de la quantité de puissance de calcul mis en service au sein du cluster pour gérer les tâches de flux Analytique respectifs.  

-   **Données de référence :** Analytique de flux offre aux utilisateurs la possibilité de spécifier et d’utiliser les données de référence. Il peut s’agir de données historiques ou simplement non-diffusion en continu les données qui changent moins souvent au fil du temps. Le système simplifie l’utilisation des données de référence pour être traité comme n’importe quel flux d’événements autres entrants pour joindre avec les autres flux d’événement ingérées en temps réel pour effectuer des transformations.  

-   **Fonctions définies par l’utilisateur :** Analytique de flux offre une intégration avec formation de Machine Azure permet de définir des appels de fonction dans le service d’apprentissage automatique dans le cadre d’une requête Analytique de flux de données. Il étend les fonctionnalités de flux Analytique pour tirer parti des solutions de formation de Machine Azure existantes. Pour plus d’informations, veuillez consulter le [didacticiel d’intégration apprentissage automatique](stream-analytics-machine-learning-integration-tutorial.md).

-   **Connectivité :** Analytique de flux se connecte directement aux concentrateurs d’événement Azure concentrateurs de IoT Azure pour l’ingestion de flux et le service Azure Blob à l’acquisition de données historiques. Des résultats peuvent être écrites à partir du flux de données Analytique pour Azure stockage BLOB ou Tables, base de données SQL Azure, magasins de LAC de données Azure, DocumentDB, concentrateurs d’événement, thèmes du Bus des services Azure ou files d’attente et d’alimentation BI, où il peut puis être affiché, plus traitée par des workflows, utilisé dans analytique de lot via [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) ou nouveau traité sous la forme d’une série d’événements. Lors de l’utilisation de concentrateurs de l’événement, il est possible de composer plusieurs Analytique de flux de données avec d’autres sources de données et les moteurs de traitement sans perdre la nature en continu des calculs.  

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez été introduit pour l’Analytique des flux de données, un service managé pour la diffusion analytique des données à partir de l’Internet des objets. Pour en savoir plus sur ce service, voir :

- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)

