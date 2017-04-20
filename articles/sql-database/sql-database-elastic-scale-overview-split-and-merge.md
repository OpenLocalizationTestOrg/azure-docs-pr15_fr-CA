<properties 
    pageTitle="Déplacement de données entre les bases de données à grande échelle nuage | Microsoft Azure" 
    description="Explique comment manipuler le milieu des fragments et déplacer les données via un service auto-hébergé, à l’aide des API de bases de données élastique." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="moving-data-between-scaled-out-cloud-databases"></a>Déplacement de données entre les bases de données à grande échelle cloud

Si vous êtes un logiciel en tant que Service développeur et soudainement votre application subit une extraordinaire, vous devez tenir compte de la croissance. Vous ajoutez les bases de données plus (milieu des fragments). Comment redistribuer les données pour les nouvelles bases de données sans perturber le fonctionnement de l’intégrité des données ? Pour déplacer des données à partir de bases de données limitées pour les nouvelles bases de données, utilisez l' **outil de fusion et fractionnement** .  

L’outil de fusion et fractionnement s’exécute comme un service web Azure. Un développeur ou un administrateur utilise l’outil Déplacer shardlets (les données à partir d’un partagé) entre les différentes bases de données (milieu des fragments). L’outil utilise la gestion de mappage partagé pour mettre à jour de la base de données de métadonnées de service et garantir des mappages cohérentes.

![Vue d’ensemble][1]

## <a name="download"></a>Télécharger
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)


## <a name="documentation"></a>Documentation
1. [Didacticiel d’outil de base de données élastique fusion et fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
* [Configuration de la sécurité de la fusion et fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Considérations sur la sécurité de la fusion et fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)
* [Gestion de mappage partagé](sql-database-elastic-scale-shard-map-management.md)
* [Migration des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md)
* [Outils de base de données élastique](sql-database-elastic-scale-introduction.md)
* [Glossaire d’outils de base de données élastique](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Pourquoi utiliser l’outil de fusion et fractionnement ?

**Flexibilité**

Les applications doivent étendre flexible au-delà des limites d’une seule base de données de la base de données SQL Azure. Utilisez l’outil pour déplacer des données selon les besoins pour les nouvelles bases de données tout en conservant l’intégrité.

**Fractionnement de croître** 

Vous devez augmenter la capacité globale de gérer la croissance explosive. Pour ce faire, créez une capacité supplémentaire par ont les données et en le distribuant dans plus de façon incrémentielle des bases de données jusqu'à ce que les besoins en capacité sont remplies. Il s’agit d’un bon exemple de la fonction « fractionné ». 

**Fusionner pour réduire**

Capacité doit réduire la nature saisonnière de l’entreprise. L’outil vous permet de mettre à l’échelle vers le bas pour moins d’unités d’échelle lorsque le ralentit de l’activité. La fonctionnalité de « fusion » dans le Service de fusion et fractionnement évolutivité élastique couvre cette exigence. 

**Gérer des zones réactives en déplaçant des shardlets**

Avec plusieurs des locataires par base de données, l’allocation de shardlets au milieu des fragments peut entraîner des goulets d’étranglement de capacité sur certains milieu des fragments. Cela nécessite la réaffectation shardlets ou le déplacement de shardlets occupé au milieu des fragments nouvelles ou moins utilisées. 

## <a name="concepts--key-features"></a>Concepts et fonctionnalités clés

**Services hébergés par le client**

La fusion de fractionnement est rendue sous la forme d’un service hébergé par le client. Vous devez déployer et héberger le service dans votre abonnement Microsoft Azure. Le package que vous téléchargez à partir de NuGet contient un modèle de configuration et les informations relatives à votre déploiement spécifique. Consultez le [didacticiel de fusion et fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md) pour plus de détails. Étant donné que le service s’exécute dans votre abonnement Azure, vous pouvez contrôler et configurer la plupart des aspects de sécurité du service. Le modèle par défaut comprend les options de configuration de SSL, authentification client basée sur les certificats, le chiffrement pour les informations d’identification stockées, protégeant un déni de service et les restrictions IP. Vous trouverez plus d’informations sur la sécurité dans la [configuration de la sécurité de la fusion et fractionnement](sql-database-elastic-scale-split-merge-security-configuration.md)de document suivante.

La valeur par défaut de déployer le service s’exécute avec un travailleur et un rôle web. Chacun utilise la taille de la mémoire virtuelle de A1 dans Azure Cloud Services. Bien que vous ne pouvez pas modifier ces paramètres lors du déploiement du package, vous pouvez décider de les après un déploiement réussi dans le service cloud en cours d’exécution (via le portail Azure). Notez que le rôle de travail ne doit pas être configuré qu’à une seule instance pour des raisons techniques. 

**Intégration de mappage partagé**

Le service de fusion et fractionnement interagit avec le mappage partagé de l’application. Lorsque vous utilisez le service de fusion et fractionnement pour fractionner ou fusionner les plages ou shardlets entre le milieu des fragments, le service conserve automatiquement le mappage éclater à jour. Pour ce faire, le service se connecte à la base de données de gestionnaire mappage partagé de l’application et gère les plages et les mappages en progression des demandes de fusion/fractionnement/déplacer. Cela garantit que le mappage partagé présente toujours une vue à jour lorsque vous allant les opérations de fusion et fractionnement. Fractionner, les opérations de déplacement de fusion et shardlet sont implémentées en transférant un lot de shardlets partagé de la source vers la partagé cible. Au cours de la shardlet l’opération de déplacement le shardlets au lot en cours sont marquées comme étant en mode hors connexion dans le mappage partagé et ne sont pas disponible pour les connexions de routage dépendant des données à l’aide de l’API **OpenConnectionForKey** . 

**Connexions de shardlet cohérente**

Au démarrage de déplacement des données pour un nouveau lot de shardlets, les connexions de routage dépendant des données partagé-map fournie à l’éclater le shardlet le stockage sont mis à mort et les connexions de la carte d’éclater API à ces shardlets sont bloqués pendant le mouvement des données afin d’éviter des incohérences. Connexions à d’autres shardlets sur le même partagé sera également obtenir mis à mort, mais il réussira à nouveau immédiatement sur Réessayer. Une fois le traitement par lots est déplacé, les shardlets sont marqués en ligne pour éclater de la cible et la source de données est supprimé de l’éclater source. Le service passe par ces étapes pour chaque lot jusqu'à ce que tous les shardlets ont été déplacés. Ceci entraîne plusieurs opérations de kill de connexion au cours de l’opération de fusion/fractionnement/déplacement terminé.  

**Gestion de la disponibilité de shardlet**

Limitant la connexion de mise à mort au lot en cours de shardlets comme indiqué ci-dessus limite la portée de l’indisponibilité d’un lot de shardlets à la fois. Il s’agit par défaut sur une approche où l’éclater complète reste en mode hors connexion pour tous ses shardlets au cours d’une opération de fractionnement ou de fusion. La taille d’un lot, défini comme le nombre de shardlets distinct pour déplacer à la fois, est un paramètre de configuration. Elle peut être définie pour chaque opération de fractionnement et de fusion en fonction des besoins de disponibilité et les performances de l’application. Notez que la plage est verrouillée dans la table partagé peut être supérieure à la taille de lot spécifiée. C’est parce que le service renvoie la taille de la plage dont le nombre réel ont des valeurs de clé dans les données correspond environ à la taille de lot. Il est important de se rappeler en particulier pour les clés ont peuplées. 

**Stockage des métadonnées**

Le service de fusion et fractionnement utilise une base de données pour maintenir son état et de conserver les journaux pendant le traitement de la demande. L’utilisateur crée cette base de données dans leur abonnement et lui fournit la chaîne de connexion dans le fichier de configuration pour le déploiement du service. Les administrateurs de l’organisation de l’utilisateur peuvent également se connecter à cette base de données pour examiner la progression de la demande et d’examiner des informations détaillées sur les défaillances potentielles.

**Sensibilisation ont**

Le service de fusion et fractionnement fait la distinction entre les tables (1) sharded, les tables de référence (2) et tables (3) normales. La sémantique d’une opération de fusion/fractionnement/déplacement dépendre du type de la table utilisée et est définie comme suit : 

* **Sharded tables**: fractionnement, fusion et opérations de déplacement atteindre shardlets à partir du code source partagé de la cible. Après l’exécution réussie de la demande globale, les shardlets ne sont plus présents sur la source. Notez que les tables cibles doivent figurer sur le partagé cible et qu’il ne doivent pas contenir de données dans la plage cible avant le traitement de l’opération. 

* **Tables de référence**: pour les tables de références, le fractionnement, fusionner et déplacer des opérations copier les données de la source à la partagé cible. Notez, toutefois, qu’aucune modification n’intervient dans le partagé cible pour une table donnée si une ligne est déjà présente dans le tableau de la cible. La table doit être vide pour toute opération de copie de table de référence soient traitées.

* **Autres Tables**: autres tables peuvent être présents sur la source ou la cible d’une opération de fusion et fractionnement. Le service de fusion et fractionnement ne tient pas compte de ces tables pour les opérations de copie ou de déplacement de données. Notez, cependant, que ceux-ci peuvent interférer avec les opérations en cas de contraintes.

Les informations de référence et des tables sharded sont fournies par l’API **SchemaInfo** sur la carte partagé. L’exemple suivant illustre l’utilisation de ces API sur un smm d’objet partagé donné carte manager : 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Les tables 'région' et le 'nation' sont définis comme des tables de référence et sont copiées avec les opérations de fusion/fractionnement/déplacement. « client » et les « ordres » à son tour sont définis en tant que tables sharded. C_CUSTKEY et O_CUSTKEY servent la clé ont. 

**Intégrité référentielle**

Le service de fusion et fractionnement analyse les dépendances entre les tables et utilise les relations de clé primaire-clé étrangère pour préparer les opérations de déplacement de shardlets et tables de références. En général, les tables de référence sont d’abord copiées dans l’ordre de dépendance, puis shardlets sont copiées dans l’ordre de leurs dépendances au sein de chaque lot. Ceci est nécessaire afin que les contraintes FK-CP partagé de la cible sont pris en compte que les nouvelles données arrivent. 

**Cohérence de mappage partagé et son achèvement éventuel**

En présence de défaillances, le service de fusion et fractionnement reprend les opérations après une panne et vise à compléter les demandes de progression. Toutefois, il peut arriver irrécupérable, par exemple, lorsque l’éclater cible est perdue ou compromise irréparable. Dans ces circonstances, certaines shardlets supposés être déplacée peut continuent de résider sur partagé de la source. Le service garantit que les mappages de shardlet sont uniquement mis à jour après que les données nécessaires a été copiées avec succès à la cible. Shardlets ne sont supprimés de la source une fois que toutes les données de leurs a été copié vers la cible et les mappages correspondants ont été mis à jour avec succès. L’opération de suppression se produit en arrière-plan tandis que la plage est déjà en ligne sur l’éclater cible. Le service de fusion et fractionnement garantit toujours la cohérence des mises en correspondance stockées dans la table partagé.


## <a name="the-split-merge-user-interface"></a>L’interface utilisateur de la fusion et fractionnement

Le package de la fusion et fractionnement service inclut un rôle worker et un rôle web. Le rôle web est utilisé pour soumettre des demandes de fusion et fractionnement de manière interactive. Les principaux composants de l’interface utilisateur sont les suivantes :

-    Type d’opération : Le type d’opération est une case d’option qui contrôle le type d’opération effectuée par le service pour cette demande. Vous pouvez choisir entre le fractionnement, fusionner et déplacer les scénarios. Vous pouvez également annuler une opération soumise précédemment. Vous pouvez utiliser répartition, fusionner et déplacer les demandes pour les mappages d’éclater gamme. Partagé de la liste correspond à la prise en charge uniquement les opérations de déplacement.

-    Mappage d’éclater : La section suivante de paramètres de demande couvrir d’informations sur le mappage partagé et la base de données hébergeant votre mappage partagé. En particulier, vous devez fournir le nom du serveur de base de données de SQL Azure et hébergeant des informations d’identification pour se connecter à la base de données partagé et enfin le nom de la carte d’éclater la shardmap, de la base de données. Actuellement, l’opération n’accepte qu’un seul jeu d’informations d’identification. Ces informations d’identification doivent disposer des autorisations suffisantes pour effectuer des modifications au mappage partagé ainsi que pour les données utilisateur sur les milieu des fragments.

-    Plage source (fractionnement et fusion) : une opération de fusion et fractionnement traite une plage à l’aide de sa clé haute et basse. Pour spécifier une opération avec une valeur de clé haute unbounded, cochez la case « clé haute est max » et ne renseignez pas le champ de clé élevé. Les valeurs de clé de plage que vous spécifiez n’avez pas besoin d’épouser précisément un mappage et ses limites dans le mappage d’éclater. Si vous ne spécifiez pas les limites de la plage à tout le service déduit la plage la plus proche pour vous automatiquement. Vous pouvez utiliser le script GetMappings.ps1 PowerShell pour récupérer les mappages en cours dans un mappage partagé donné.

-    Comportement de la Source séparation (split) : pour les opérations de division, définissez le point pour fractionner la plage source. Pour cela, en fournissant la clé ont où vous souhaitez effectuer le fractionnement. Utilisez le bouton radio spécifiez si vous souhaitez la partie inférieure de la plage (à l’exception de la touche de division) déplacer, ou si vous souhaitez que la partie supérieure pour déplacer (y compris la clé de fractionnement).

-    Source de Shardlet (déplacer) : déplacer des opérations sont différentes des opérations de fractionnement ou de fusion car ils ne nécessitent pas une plage pour décrire la source. Une source de déplacement est simplement identifiée par la valeur de clé ont que vous prévoyez de déplacer.

-    Cible partagé (split) : une fois que vous avez fourni les informations sur la source de votre opération de fractionnement, vous devez définir où vous souhaitez que les données à copier dans en fournissant le nom de serveur et de base de données de base de données SQL Azure pour la cible.

-    Plage de cible (fusion) : les opérations de fusion shardlets atteindre un partagé existant. Vous identifiez le partagé existant en fournissant les limites de la plage de la plage existante que vous souhaitez fusionner avec.

-    Taille du lot : La taille de lot contrôle le nombre de shardlets qui sera affichée à la fois pendant le déplacement de données hors connexion. Il s’agit d’une valeur entière où vous pouvez utiliser des valeurs plus petites lorsque vous êtes sensible aux longues périodes d’indisponibilité pour shardlets. Des valeurs plus grandes augmentera le temps qui est d’un shardlet donné mais en mode hors connexion peut améliorer les performances.

-    Id de l’opération (Annuler) : Si vous avez une opération en cours qui n’est plus nécessaire, vous pouvez annuler l’opération en fournissant son ID d’opération dans ce champ. Vous pouvez extraire l’ID de l’opération à partir de la table d’état de demande (voir la Section 8.1) ou de la sortie dans le navigateur web dans laquelle vous avez envoyé la demande.


## <a name="requirements-and-limitations"></a>Configurations requises et restrictions 

L’implémentation actuelle de la fusion et fractionnement de service dépend de la configuration requise et les restrictions suivantes : 

* Au milieu des fragments doivent exister et être enregistré dans la table partagé avant de pouvoir effectuer une opération de fusion et fractionnement sur ces milieu des fragments. 

* Le service ne crée pas de tables ou autres objets de base de données automatiquement dans le cadre de ses opérations. Cela signifie que le schéma pour les tables de tous les sharded et des tables de référence doivent figurer sur le partagé cible avant toute opération de fusion/fractionnement/déplacer. Sharded les tables en particulier doivent être vides dans la plage où shardlets nouveau doivent être ajoutés par une opération de fusion/fractionnement/déplacement. Dans le cas contraire, l’opération échoue la vérification de la cohérence initiale sur partagé de la cible. Notez également que les données sont copiées uniquement si la référence de la table est vide et qu’il n’y a aucune garantie de la cohérence en ce qui concerne les autre simultanée des opérations d’écriture sur les tables de référence de référence. Nous vous recommandons de cela : lors de l’exécution des opérations de fractionnement et de fusion, aucune autre opération d’écriture n’apporter des modifications aux tables de références.

* Le service repose sur l’identité de ligne établie par un index unique ou une clé qui inclut la clé ont pour améliorer les performances et la fiabilité de grande shardlets. Ainsi, le service déplacer des données à une granularité encore plus fine que simplement la valeur de clé ont. Cela permet de réduire la quantité maximale d’espace du journal et les verrous sont nécessaires lors de l’opération. Envisagez de créer un index unique ou une clé primaire, y compris la clé ont sur une table si vous souhaitez utiliser cette table avec des demandes de fusion/fractionnement/déplacer. Pour des raisons de performances, la clé ont doit être la colonne de gauche de la clé ou l’index.

* Au cours du traitement de la demande, certaines données shardlet peuvent être présentes à la fois sur la source et partagé de la cible. Cela est nécessaire pour protéger des échecs lors de la circulation de shardlet. L’intégration de la fusion et fractionnement avec le mappage partagé garantit que les connexions via les données dépendantes d’API à l’aide de la méthode **OpenConnectionForKey** sur la carte d’éclater le routage ne voient pas les états intermédiaires incohérentes. Toutefois, lors de la connexion à la source ou au milieu des fragments cible sans l’aide de la méthode **OpenConnectionForKey** , des états intermédiaires incohérentes peuvent être visibles lorsque vous allant des demandes de fusion/fractionnement/déplacement. Ces connexions peuvent présenter des résultats partiels ou en doublons en fonction de la durée ou l’éclater la connexion sous-jacente. Actuellement, cette limitation inclut les connexions effectuées par les évolutivité élastique Multi-Shard-requêtes.

* La base de données de métadonnées pour le service de fusion et fractionnement ne doit pas être partagé entre les différents rôles. Par exemple, un rôle du service fusion et fractionnement en cours d’exécution dans la zone de transit doit pointer sur une base de données de métadonnées différents que le rôle de la production.
 

## <a name="billing"></a>Facturation 

Le service de fusion et fractionnement s’exécute comme un service en nuage dans votre abonnement Microsoft Azure. Par conséquent les frais des services en nuage s’appliquent à votre instance du service. Sauf si vous effectuez fréquemment des opérations de fusion/fractionnement/déplacement, nous vous recommandons de que supprimer de votre service cloud de fusion et fractionnement. Qui enregistre les coûts pour l’exécution ou déployé des instances de service cloud. Vous pouvez redéployer et démarrer votre exécutable facilement configuration chaque fois que vous avez besoin effectuer des opérations de fractionnement ou de fusion. 
 
## <a name="monitoring"></a>Surveillance 
### <a name="status-tables"></a>Tableaux d’état 

Le Service de fusion et fractionnement offre la table **d’état de la demande** dans les métadonnées de stocker la base de données pour l’analyse de requêtes terminées et en cours. La table affiche une ligne pour chaque demande de fusion et fractionnement qui a été soumis à cette instance du service fusion et fractionnement. Il fournit les informations suivantes pour chaque requête :

* **Horodatage**: l’heure et la date à laquelle la demande a été lancée.

* **OperationId**: un GUID qui identifie de manière unique la demande. Cette demande peut également servir à annuler l’opération, alors qu’il est encore en cours.

* **État**: l’état actuel de la demande. Pour les demandes en cours, il répertorie également la phase en cours dans lequel la demande est.

* **CancelRequest**: un indicateur qui indique si la demande a été annulée.

* **Progression**: une estimation du pourcentage d’achèvement de l’opération. Une valeur de 50 indique que l’opération est achevée à 50 % environ.

* **Détails**: valeur XML qui fournit un rapport de progression plus détaillé. Le rapport de progression est régulièrement mis à jour des jeux de lignes sont copiées à partir de la source vers la cible. En cas de défaillances ou des exceptions, cette colonne inclut également des informations plus détaillées sur l’échec.


### <a name="azure-diagnostics"></a>Diagnostics de Windows Azure

Le service de fusion et fractionnement utilise des Diagnostics Azure basé sur Azure SDK 2.5 pour la surveillance et de diagnostic. Vous contrôlez la configuration de diagnostics comme expliqué ici : [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md). Le package de téléchargement comprend deux configurations de tests de diagnostic – un pour le rôle web et un pour le rôle de travail. Ces configurations de diagnostics pour le service de suivent les conseils de [Principes de base de Service de Cloud de Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Il inclut les définitions pour enregistrer les compteurs de Performance, des journaux IIS, journaux des événements Windows et les journaux d’événements application de fusion et fractionnement. 

## <a name="deploy-diagnostics"></a>Déployer des Diagnostics 

Pour activer la surveillance et diagnostic à l’aide de la configuration de diagnostic pour les rôles web et worker fournis par le package NuGet, exécutez les commandes suivantes à l’aide de PowerShell d’Azure : 

    $storage_name = "<YourAzureStorageAccount>" 
    
    $key = "<YourAzureStorageAccountKey" 
    
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    
    
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 
    
    
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 
    
    $service_name = "<YourCloudServiceName>" 
    
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Vous trouverez plus d’informations sur la façon de configurer et de déployer les paramètres de diagnostic : [Activation des Diagnostics dans Azure Services en nuage et les Machines virtuelles](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Récupérer les tests de diagnostic 

Vous pouvez facilement accéder à vos tests de diagnostic à partir de l’Explorateur de serveurs Visual Studio dans la partie Azure de l’arborescence de l’Explorateur de serveurs. Ouvrez une instance de Visual Studio et dans la barre de menus cliquez sur Affichage, Explorateur de serveurs. Cliquez sur l’icône Azure pour vous connecter à votre abonnement Azure. Puis accédez à Azure -> stockage -> <your storage account> -> Tables -> WADLogsTable. Pour plus d’informations, consultez [Exploration des ressources de stockage avec l’Explorateur de serveurs](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

Le WADLogsTable mis en surbrillance dans la figure ci-dessus contient les événements détaillés dans le journal d’application de service de la fusion et fractionnement. Notez que la configuration par défaut du package téléchargé est adaptée à un déploiement de production. Par conséquent, l’intervalle auquel les compteurs et les journaux sont extraites par les instances de service est volumineux (5 minutes). Pour les tests et le développement, réduire l’intervalle en réglant les paramètres de tests de diagnostic du web ou travailleur à vos besoins. Avec le bouton droit sur le rôle dans l’Explorateur de serveurs Visual Studio (voir ci-dessus), puis réglez la période de transfert dans la boîte de dialogue pour les paramètres de configuration des tests de diagnostic : 

![Configuration de][3]


## <a name="performance"></a>Performances

En général, les meilleures performances sont à prévoir à partir d’est élevée, plus performant niveaux de service dans la base de données de SQL Azure. Des allocations de mémoire, l’UC et d’e/s plus élevées pour les niveaux de service plus élevés bénéficient de la copie en bloc et les opérations qu’utilise le service de fusion et fractionnement de suppression. Pour cette raison, augmenter le niveau de service pour ces bases de données pour une période limitée, définie.

Le service effectue également les requêtes de validation dans le cadre de ses opérations. Ces requêtes de validation vérification de la présence inattendue de données dans la plage cible et assurez-vous que toute opération de fusion/fractionnement/déplacement démarre à partir d’un état cohérent. Ces requêtes toutes les fonctionnent sur les plages de clés ont défini par la portée de l’opération et la taille de lot fourni dans le cadre de la définition de requête. Ces requêtes fonctionnent mieux si un index est présent qui possède la clé ont comme la colonne de gauche. 

En outre, une propriété d’unicité avec la clé ont comme la colonne de gauche permet au service d’utiliser une approche optimisée qui limite la consommation de ressources en termes de mémoire et d’espace du journal. Cette propriété d’unicité est requis pour déplacer les tailles de données volumineuses (généralement au-dessus de 1 Go). 

## <a name="how-to-upgrade"></a>La mise à niveau

1. Suivez les étapes de [déploiement d’un service de fusion et fractionnement](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Modifier votre fichier de configuration de service cloud pour votre fusion et fractionnement de déploiement afin de refléter les nouveaux paramètres de configuration. Un nouveau paramètre obligatoire est les informations relatives au certificat utilisé pour le cryptage. Un moyen simple pour cela est de comparer le nouveau fichier de modèle de configuration à partir du téléchargement par rapport à la configuration existante. Assurez-vous de qu'ajouter les paramètres pour les « DataEncryptionPrimaryCertificateThumbprint » et « DataEncryptionPrimary » pour le web et le rôle de travail.
3. Avant de déployer la mise à jour vers Azure, assurez-vous que toutes les opérations de fusion en cours d’exécution de fractionnement ont terminé. Vous pouvez facilement le faire en interrogeant les tables d’état de la demande et PendingWorkflows dans la base de données de métadonnées de fusion et fractionnement des demandes en cours.
4. Mise à jour de votre déploiement de service cloud existant pour la fusion et fractionnement de votre abonnement Azure avec le nouveau package et votre fichier de configuration du service mises à jour.

Vous n’avez pas besoin de mettre en service une nouvelle base de données de métadonnées de fusion et fractionnement mettre à niveau. La nouvelle version met automatiquement à niveau votre base de données de métadonnées existantes vers la nouvelle version. 

## <a name="best-practices--troubleshooting"></a>Meilleures pratiques et résolution des problèmes
 
-    Définir un client de test et exercer vos opérations de fusion/fractionnement/déplacement plus importantes avec les clients de test sur plusieurs milieu des fragments. Assurez-vous que toutes les métadonnées sont définie correctement dans le mappage partagé et que les opérations ne violent pas les contraintes ou les clés étrangères.
-    Conserver le locataire test taille de données au-dessus de la taille maximale des données de vos clients plus grande afin de garantir la taille des données ne s’agit pas de problèmes. Cela vous permet d’évaluer une limite supérieure sur le temps que nécessaire pour déplacer un client unique. 
-    Assurez-vous que votre schéma autorise les suppressions. Le service de fusion et fractionnement nécessite la possibilité de supprimer les données de l’éclater source une fois les données a été copiées avec succès à la cible. Par exemple, **Supprimer des déclencheurs** peut empêcher le service suppression sur la source de données et peut provoquer l’échec des opérations.
-    La clé ont doit être la colonne de gauche de votre clé primaire ou d’une définition d’index unique. Qui garantit les meilleures performances pour les requêtes de validation de fractionnement ou de fusion et pour les opérations de déplacement et la suppression de données réelles qui fonctionnent toujours sur les plages de clés ont.
-    Colocaliser votre fusion et fractionnement de service dans le centre de données et de la région où vos bases de données résident. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]



<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
 
