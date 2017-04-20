<properties
   pageTitle="Surveillance de l’état dans un Service de Fabric | Microsoft Azure"
   description="Introduction à la santé d’Azure Fabric de Service analyse le modèle, qui assure la surveillance des services du cluster et ses applications."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduction à l’analyse de health Service Fabric
Azure Fabric du Service présente un modèle de fonctionnement qui fournit une évaluation d’intégrité riche, flexible et extensible et de reporting. Le modèle permet de près en temps réel de surveillance de l’état du cluster et les services en cours d’exécution dans celui-ci. Vous pouvez facilement obtenir des informations de santé et corriger des problèmes potentiels avant qu’ils en cascade et provoquent des pannes massives. Dans le modèle classique, services envoyer des rapports en fonction de leurs vues locales, et que les informations sont rassemblées pour fournir un ensemble cluster au niveau du mode.

Composants de service Fabric utilisent ce modèle de santé riche pour signaler son état actuel. Vous pouvez utiliser le même mécanisme pour la santé de l’état à partir de vos applications. Si vous investissez dans le rapport d’état de santé de haute qualité qui capture des conditions personnalisées, vous pouvez détecter et résoudre des problèmes beaucoup plus facilement de votre application en cours d’exécution.

> [AZURE.NOTE] Nous avons commencé le sous-système de santé pour répondre à un besoin des mises à niveau contrôlées. Service de Fabric fournit surveillés application cluster mises à niveau et qui garantissent une disponibilité totale, sans interruption de service et il y a peu d’aucune intervention de l’utilisateur. Pour atteindre ces objectifs, la mise à niveau vérifie selon la santé configuré des stratégies de mise à niveau et permet une mise à niveau fonctionne uniquement lorsque la santé respecte les seuils de votre choix. Dans le cas contraire, la mise à niveau est automatiquement annulé ou suspendu pour donner aux administrateurs une chance de corriger les problèmes. Pour en savoir plus sur les mises à niveau de l’application, reportez-vous à [cet article](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Magasin de santé
Le magasin d’état de santé conserve des informations relatives à la santé sur les entités dans le cluster pour faciliter la récupération et l’évaluation. Il est implémenté comme un Service de Fabric persistant avec état service pour s’assurer de la disponibilité et l’évolutivité. Le magasin d’état de santé fait partie de la **fabric : / système** application et il est disponible lorsque le cluster est disponible et en cours d’exécution.

## <a name="health-entities-and-hierarchy"></a>Hiérarchie et les entités santé
Les entités santé sont organisées selon une hiérarchie logique qui capture des interactions et des dépendances entre les différentes entités. La hiérarchie et les entités sont créées automatiquement par le magasin de santé basé sur les rapports reçus à partir de composants de Service Fabric.

Les entités santé mettre en miroir les entités de Service Fabric. (Par exemple, **entité sanitaire d’application** correspond à une instance de l’application déployée dans le cluster, tandis que **l’entité de nœud de santé** correspond à un nœud de cluster Service Fabric.) La hiérarchie de santé capture les interactions entre les entités système, et il sert de base pour l’évaluation d’intégrité avancées. Vous pouvez découvrir les principaux concepts de Service Fabric de [présentation technique du Service de Fabric](service-fabric-technical-overview.md). Pour plus d’informations sur l’application, voir [modèle d’application de Service de Fabric](service-fabric-application-model.md).

La hiérarchie et des entités santé autorisent le cluster et les applications pour être signalé, de débogage et surveillés. Le modèle de fonctionnement offre une représentation précise, *granulaire* de la santé des éléments mobiles de nombreuses dans le cluster.

![Entités santé.][1]
Les entités santé, organisés dans une hiérarchie basée sur les relations parent-enfant.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Les entités santé sont les suivantes :

- **Cluster**. Représente l’état de santé d’un cluster de Service Fabric. Rapports d’intégrité cluster décrivent les conditions qui affectent l’ensemble du cluster et ne peut pas être limitées à un ou plusieurs enfants défectueux. Les exemples incluent le cerveau du cluster fractionnement en raison de problèmes de partitionnement ou de communication réseau.

- **Nœud**. Représente l’état de santé d’un nœud de Service Fabric. Rapports d’intégrité nœud décrivent les conditions qui affectent la fonctionnalité de nœud. En général, elles affectent toutes les entités déployées qu’il exécute. Lorsqu’un nœud de manque d’espace disque (ou une autre propriété de l’échelle de l’ordinateur, telles que la mémoire, connexions) et exemples lorsqu’un nœud est en panne. Le nœud est identifiée par le nom du nœud (chaîne).

- **Application**. Représente l’état d’une instance de l’application en cours d’exécution dans le cluster. Application des rapports de santé décrivent les conditions qui affectent la santé globale de l’application. Ils ne peuvent pas être limitées au enfants individuels (services ou applications déployées). Les exemples incluent l’interaction de bout en bout entre les différents services de l’application. L’application est identifiée par le nom de l’application (URI).

- **Service**. Représente l’état de santé d’un service en cours d’exécution dans le cluster. Rapports d’intégrité service décrivent les conditions qui affectent le fonctionnement global du service, et qu’ils ne peut pas être limitées à une partition ou d’un réplica. Une configuration de service (par exemple, le port ou le partage de fichiers externe) qui est à l’origine des problèmes pour toutes les partitions sont des exemples. Le service est identifiée par le nom du service (URI).

- **Partition**. Représente l’état de santé d’une partition de service. Rapports d’intégrité partition décrivent les conditions qui affectent le jeu de réplicas. Lorsque le nombre de réplicas est inférieur à nombre de cibles et exemples lorsqu’une partition est en perte de quorum. La partition est identifiée par l’ID (GUID) de la partition.

- **Réplica**. Représente l’état de santé d’un réplica avec état de service ou d’une instance de service sans état. La plus petite unité d’agents de surveillance et de composants système peuvent signaler pour une application. Pour les services de l’état, exemples incluent un réplica principal signalé lorsqu’il ne peut pas répliquer des opérations secondaires et lorsque la réplication n’est pas continuer à attendu rythme. En outre, une instance sans état peut signaler lorsqu’il manque de ressources ou a des problèmes de connectivité. Le réplica est identifiée par l’ID (GUID) de la partition et l’ID de réplica ou instance (long).

- **DeployedApplication**. Représente l’état de santé d’une *application en cours d’exécution sur un nœud*. Rapports d’intégrité application déployée décrivent les conditions spécifiques à l’application sur le nœud qui ne peut pas être limité aux packages de service déployés sur le même nœud. Lorsque le package d’application ne peut pas être téléchargé sur ce nœud et lorsqu’il y a un paramètre de problème des entités de sécurité d’application sur le nœud sont des exemples. L’application déployée est identifiée par le nom de l’application (URI) et le nom du nœud (chaîne).

- **DeployedServicePackage**. Représente l’état de santé d’un package de service en cours d’exécution sur un nœud dans le cluster. Il décrit les conditions spécifiques à un package de service qui n’affectent pas les autres packages de service sur le même nœud pour la même application. Un package de code des exemples dans le package de service qui ne peut pas être démarré et un colis de configuration qui ne peuvent pas être lues. Le package de service déployé est identifié par le nom de l’application (URI), le nom de nœud (chaîne) et nom de manifeste de service (chaîne).

La granularité du modèle santé facilite détecter et corriger les problèmes. Par exemple, si un service ne répond pas, il est possible d’indiquer que l’instance d’application est incorrecte. Reporting que niveau n’est pas idéal, cependant, étant donné que le problème ne risque pas d’affecter tous les services au sein de cette application. Le rapport doit être appliqué pour le service défectueux ou une partition enfant spécifique, si les points de plus d’informations sur cette partition. Les données automatiquement surfaces par le biais de la hiérarchie et une partition défectueuse est rendu visible à des niveaux de service et de l’applications. Cette agrégation permet de déceler et résoudre la cause première du problème plus rapidement.

La hiérarchie de la santé est composée de relations parent-enfant. Un cluster est composé de nœuds et d’applications. Les applications ont des services et des applications déploiement. Les packages de service ont déployé des applications déployées. Les services ont des partitions, et chaque partition possède un ou plusieurs réplicas. Il existe une relation entre les nœuds et les entités déployées. Si un nœud est défectueux, tel qu’indiqué par son composant de système d’autorité (service de gestionnaire de basculement), il a une incidence sur les applications déployées, packages de service et des duplications déployées sur celui-ci.

La hiérarchie de santé représente le dernier état du système basé sur les derniers rapports d’état, qui est presque en temps réel.
Agents de surveillance internes et externes peuvent signaler sur les mêmes entités basées sur la logique spécifique à l’application ou surveillés des conditions personnalisées. Rapports sur les utilisateurs coexistent avec les rapports du système.

Vous souhaitez investir dans comment signaler et de répondre à la santé lors de la conception d’un service en nuage de grande taille, pour faciliter la déboguer, de surveiller et de faire fonctionner le service.

## <a name="health-states"></a>États de fonctionnement
TISSU de service utilise les trois états de santé pour décrire si une entité est saine ou non : OK, avertissement et erreur. Tout rapport envoyé à la banque de santé doit spécifier un de ces États. Le résultat de l’évaluation de la santé est un de ces États.

[États de fonctionnement](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) possibles sont les suivantes :

- **OK**. L’entité est en bon état. Il n’y a pas de problèmes connus signalés ou ses enfants (le cas échéant).

- **Avertissement**. L’entité rencontre des problèmes, mais il n’est pas encore incorrecte (par exemple, il y a des retards, mais ils n’entraînent pas encore les problèmes fonctionnels). Dans certains cas, la condition d’avertissement peut résoudre lui-même sans aucune intervention spéciale, et il est utile de fournir une visibilité sur ce qui se passe. Dans les autres cas, la condition d’avertissement peut-être se dégrader dans un problème grave sans intervention de l’utilisateur.

- **Erreur**. L’entité est défectueux. Il convient de fixer l’état de l’entité, car elle ne peut pas fonctionner correctement.

- **Inconnu**. L’entité n’existe pas dans le magasin d’état de santé. Ce résultat peut être obtenu à partir des requêtes distribuées qui fusionner les résultats à partir de plusieurs composants. Par exemple, obtenir de requête de liste de nœuds est redirigé vers le **Gestionnaire de basculement** et de **HealthManager**, requêtes get application accède à **ClusterManager** et **HealthManager**. Ces requêtes fusionner les résultats à partir de plusieurs composants système. Si un autre composant système retourne une entité qui n’a pas été atteint, ou a été nettoyée à partir du magasin d’état de santé, le résultat fusionné a inconnu état d’intégrité.

## <a name="health-policies"></a>Stratégies d’intégrité
Le magasin d’état de santé s’applique des stratégies de santé afin de déterminer si une entité est saine en fonction de ses rapports et ses enfants.

> [AZURE.NOTE] Stratégies d’intégrité peuvent être spécifiés dans le manifeste du cluster (pour l’évaluation de la santé des clusters et les nœuds) ou dans le manifeste d’application (pour l’évaluation de l’application et un de ses enfants). Demandes d’évaluation de la santé peuvent également passer dans les stratégies d’évaluation d’état personnalisé qui est utilisé uniquement pour cette évaluation.

Par défaut, Service Fabric applique des règles strictes pour la relation hiérarchique parent-enfant (tout doit être en bon état). Si un seul des enfants a un événement défectueux, le parent est considéré comme défectueux.

### <a name="cluster-health-policy"></a>Stratégie de contrôle de cluster
La [stratégie de contrôle de cluster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) est utilisée pour évaluer l’état du cluster et les États de fonctionnement de nœud. La stratégie peut être définie dans le manifeste du cluster. Si elle n’est pas présent, la stratégie par défaut (zéro échecs tolérées) est utilisée.
La politique de la santé du cluster contient :

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Spécifie si à traiter la santé de l’avertissement indique comme des erreurs lors de l’évaluation de la santé. Par défaut : false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Spécifie le pourcentage maximal autorisé d’applications qui peuvent être défectueux avant que le cluster est considéré comme erreur.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Spécifie le pourcentage maximal autorisé de nœuds qui peuvent être défectueux avant que le cluster est considéré comme erreur. Dans les clusters de grande taille, certains nœuds sont toujours vers le bas ou out pour les réparations, afin que ce pourcentage doit être configuré pour faire face qui.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). Le mappage de stratégie application type santé peut servir lors de l’évaluation de la santé de cluster pour décrire les types d’application spéciale. Par défaut, toutes les applications sont placées dans un pool et évaluées avec MaxPercentUnhealthyApplications. Si certains types d’application doivent être traités différemment, ils peuvent être retirés du pool global. Au lieu de cela, elles sont évaluées contre les pourcentages associées à leur nom de type d’application dans le mappage. Par exemple, dans un cluster il y a des milliers d’applications de différents types et plusieurs instances d’applications de contrôle d’un type spécial d’application. Les applications de contrôle ne doivent jamais être dans l’erreur. Vous pouvez spécifier MaxPercentUnhealthyApplications globales à 20 % tolérer certaines défaillances, mais pour le type d’application « ControlApplicationType » définie des MaxPercentUnhealthyApplications à 0. Ainsi, si certaines des nombreuses applications sont défectueux, mais sous le pourcentage global de mauvais état, le cluster évaluation à avertissement. Un état d’avertissement n’influe pas sur la mise à niveau de cluster ou autres surveillance déclenchée par un état d’erreur. Mais l’application même pour un contrôle dans l’erreur rendrait le cluster défectueux, ce qui déclenche la restauration ou s’interrompt la mise à niveau de cluster, en fonction de la configuration de mise à niveau.
Pour les types d’application définies dans le plan, toutes les instances de l’application sont retirés de la liste globale des applications. Elles sont évaluées en fonction du nombre total d’applications du type d’application, à l’aide des MaxPercentUnhealthyApplications spécifiques à partir de la carte. Toutes les autres applications restent dans le pool global et sont évaluées avec MaxPercentUnhealthyApplications.

L’exemple suivant est un extrait d’un manifeste de cluster. Pour définir des entrées dans la table de types d’application, précéder le nom de paramètre de « ApplicationTypeMaxPercentUnhealthyApplications- », suivi du nom de type d’application.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Stratégie de contrôle d’application
La [stratégie de contrôle d’application](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) décrit comment l’évaluation des événements et agrégation des États de l’enfant est effectuée pour les applications et leurs enfants. Il peut être défini dans le manifeste d’application, **ApplicationManifest.xml**, dans le package d’application. Si aucune stratégie n’est spécifié, Fabric du Service part du principe que l’entité est incorrecte si elle a un état de santé ou d’un enfant à l’état avertissement ou erreur.
Les règles configurables sont les suivantes :

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Spécifie si à traiter la santé de l’avertissement indique comme des erreurs lors de l’évaluation de la santé. Par défaut : false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Spécifie le pourcentage maximal autorisé d’applications déployées pouvant être défectueux avant que l’application est considérée comme erreur. Ce pourcentage est calculé en divisant le nombre de défectueux applications déployées sur le nombre de nœuds que les applications actuellement déployées sur le cluster. Le calcul s’arrondit à tolérer une défaillance sur un petit nombre de nœuds. Valeur par défaut de pourcentage : zéro.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Spécifie la stratégie par défaut service type santé qui remplace la stratégie d’intégrité par défaut pour tous les types de service dans l’application.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Fournit un plan de service des politiques de santé par type de service. Ces stratégies remplacent les stratégies de santé de type de service par défaut pour chaque type de service spécifié. Par exemple, si une application a un type de service sans état passerelle et un type de service moteur avec état, vous pouvez configurer les stratégies d’intégrité pour leur évaluation différemment. Lorsque vous spécifiez des stratégies par type de service, vous pouvez obtenir un contrôle plus granulaire de l’état de santé du service.

### <a name="service-type-health-policy"></a>Stratégie de contrôle de type de service
La [stratégie de contrôle de type de service](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) spécifie comment évaluer et regrouper les services et les enfants de services. La stratégie contient :

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Spécifie le pourcentage maximal autorisé des partitions défectueux avant un service est considéré comme défectueux. Valeur par défaut de pourcentage : zéro.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Spécifie le pourcentage maximal autorisé des duplications défectueux avant une partition est considérée comme incorrecte. Valeur par défaut de pourcentage : zéro.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Spécifie le pourcentage maximal autorisé des services défectueux avant que l’application est considérée comme incorrecte. Valeur par défaut de pourcentage : zéro.

L’exemple suivant est un extrait d’un manifeste d’application :

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Évaluation de l’intégrité
Les utilisateurs et les services d’information automatisés peuvent évaluer l’intégrité d’une entité à tout moment. Pour évaluer l’intégrité de l’entité, les agrégats de banque d’informations de santé santé de tous les rapports sur l’entité et évalue tous ses enfants (le cas échéant). L’algorithme d’agrégation de santé utilise des stratégies de santé qui spécifient comment évaluer les rapports sur la santé et le mode d’agrégation des États de santé des enfants (le cas échéant).

### <a name="health-report-aggregation"></a>Agrégation de rapport état de santé
Une entité peut avoir plusieurs rapports de santé envoyés par les journalistes différents (composants système ou des agents de surveillance) sur différentes propriétés. L’agrégation utilise les stratégies de la santé, en particulier le membre ConsiderWarningAsError de la stratégie de contrôle d’application ou de cluster. ConsiderWarningAsError spécifie comment évaluer les avertissements.

L’état de santé agrégé est déclenchée par les *pire* rapports d’intégrité de l’entité. Si au moins une erreur santé rapport, l’état de santé agrégée est une erreur.

![Agrégation de rapport d’état avec le rapport d’erreurs.][2]

Une entité sanitaire qui possède un ou plusieurs rapports d’état d’erreur est évaluée en tant qu’erreur. Il en est de même pour un état de santé a expiré, indépendamment de son état de santé.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Si il n’y a aucun rapport d’erreurs et d’un ou plusieurs avertissements, l’état de santé agrégées est Avertissement ou erreur, en fonction de l’indicateur de stratégie ConsiderWarningAsError.

![Agrégation de rapport état de santé avec un état d’avertissement et ConsiderWarningAsError false.][3]

Agrégation de rapport d’état avec un état d’avertissement et ConsiderWarningAsError la valeur false (par défaut).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Agrégation de santé enfant
L’état de santé agrégées d’une entité reflète les États de santé enfant (le cas échéant). L’algorithme d’agrégation des États de santé enfant utilise les stratégies de santé applicables en fonction du type d’entité.

![Agrégation de santé entités enfant.][4]

Agrégation d’enfant en fonction des stratégies d’intégrité.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Une fois le magasin d’état de santé a évalué tous les enfants, il regroupe leurs États de santé en fonction du pourcentage maximal configuré des enfants non intègres. Ce pourcentage provient de la stratégie basée sur le type d’entité et les enfants.

- Si tous les enfants ont des États OK, l’état de santé des enfants agrégés est OK.

- Si les enfants ont à la fois OK et états d’avertissement, l’avertissement de l’état de santé des enfants agrégés.

- S’il existe des enfants avec les États d’erreur qui ne respectent pas la valeur maximale autorisée de pourcentage d’enfants incorrectes, l’état de santé agrégée est une erreur.

- Si les enfants avec les États d’erreur respectent le pourcentage maximal autorisé d’enfants incorrectes, l’état de santé agrégé est avertissement.

## <a name="health-reporting"></a>Rapports de santé
Composants du système, des applications de tissu du système et agents de surveillance interne/externe pouvant appartenir à des entités de Service Fabric. Les journalistes effectuer les déterminations *local* de la santé les entités surveillées, basée sur les conditions de que surveillance des. Il n’est pas nécessaire d’examiner n’importe quel état global ou les données d’agrégation. Le comportement souhaité est d’avoir des journalistes simples et des organismes non complexes qui ont besoin d’examiner de nombreux éléments pour déduire les informations à envoyer.

Pour envoyer des données de santé dans le magasin d’état de santé, un rapporteur doit identifier l’entité affectée et créer un état de santé. Le rapport peut ensuite être envoyé via l’API à l’aide de [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), PowerShell ou reste.

### <a name="health-reports"></a>Rapports d’intégrité
Les [rapports d’intégrité](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) pour chacune des entités du cluster contient les informations suivantes :

- **SourceId**. Chaîne qui identifie de manière unique le rapporteur de l’événement d’état.

- **Identificateur de l’entité**. Identifie l’entité à laquelle le rapport est appliqué. Il diffère en fonction de l' [entité de type](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Aucun.

  - Nœud. Nom du nœud (chaîne).

  - Application. Nom de l’application (URI). Représente le nom de l’instance de l’application déployée dans le cluster.

  - Service. Nom du service (URI). Représente le nom de l’instance de service déployée dans le cluster.

  - Partition. ID (GUID) de la partition. Représente l’identificateur de partition unique.

  - Réplica. L’ID de réplica avec état de service ou l’ID d’instance de service sans état (INT64).

  - DeployedApplication. Nom de l’application (URI) et le nom du nœud (chaîne).

  - DeployedServicePackage. Nom de l’application (URI), le nom de nœud (chaîne) et service manifeste name (chaîne).

- **Propriété**. *Chaîne* (pas une énumération fixe) qui permet du reporter à classer l’événement d’état pour une propriété spécifique de l’entité. Par exemple, reporter A peut signaler que l’état de la propriété Node01 « stockage » et reporter B peut signaler l’état de la propriété Node01 « connectivité ». Dans le magasin d’état de santé, ces rapports sont traités comme des événements d’état distinct pour l’entité Node01.

- **Description**. Chaîne qui autorise un reporter fournir des informations détaillées sur l’événement d’état. **SourceId**, **propriété**et **HealthState** doivent décrire complètement le rapport. La description ajoute des informations lisibles par l’homme sur le rapport. Le texte rend plus facile pour les administrateurs et les utilisateurs à comprendre l’état de santé.

- **HealthState**. [Énumération](service-fabric-health-introduction.md#health-states) décrivant l’état de santé de l’état. Les valeurs acceptées sont OK, avertissement et erreur.

- **La propriété TimeToLive**. Valeur de type timespan qui indique la durée pendant laquelle l’état de santé est valide. Associé à **RemoveWhenExpired**, il permet le magasin d’état de santé savoir comment évaluer les événements. Par défaut, la valeur est infinie, et le rapport est toujours valide.

- **RemoveWhenExpired**. Une valeur booléenne. Si la valeur true, l’état de santé expiré est automatiquement supprimée de la banque de la santé et l’état n’affecte pas évaluation intégrité de l’entité. Utilisé lorsque le rapport est valide pour une période donnée uniquement, et n’est pas nécessaire que le rapporteur désactivez de manière explicite. Il est également utilisé pour supprimer des rapports à partir du magasin de santé (par exemple, un chien de garde est modifié et cesse d’envoyer des rapports avec source précédente et la propriété). Il peut envoyer un rapport avec une brève TimeToLive avec RemoveWhenExpired pour supprimer n’importe quel état précédent à partir du magasin d’état de santé. Si la valeur est définie sur false, l’état expiré est traité comme une erreur sur l’évaluation de la santé. Les signaux de la valeur false à la banque de santé qui la source devrait faire régulièrement rapport sur cette propriété. Si elle n’est pas le cas, il doit y avoir un problème avec l’agent de surveillance. La santé de l’agent de surveillance est capturée en tenant compte de l’événement comme une erreur.

- **Numéro de séquence**. Entier positif qui doit être de plus en plus, elle représente l’ordre des rapports. Il est utilisé par la banque de santé pour détecter le rapports périmés qui arrivent en retard en raison des retards de réseau ou d’autres problèmes. Un rapport est rejeté si le numéro de séquence est qu'inférieure ou égale à la plus récemment appliquée numéro pour la même entité, la source et la propriété. S’il n’est pas spécifié, le numéro de séquence est généré automatiquement. Il est nécessaire de placer dans le numéro de séquence uniquement lors de la création de rapports sur les transitions d’état. Dans ce cas, la source doit retenir les rapports qu’il envoyé et de conserver les informations de récupération lors d’un basculement.

Ces quatre éléments d’information--SourceId, identificateur de l’entité, propriété et HealthState--sont requis pour chaque état de santé. L’ID de source string n’est pas autorisé à démarrer avec le préfixe «**système.**», qui est réservé à un système de rapports. Pour la même entité, c’est le rapport uniquement pour la même source et la propriété. Rapports multiples pour la même source et la propriété substituent mutuellement, sur le côté client de la santé (si elles sont groupées en batch) ou sur l’état de santé côté banque. Le remplacement est basé sur les numéros de séquence ; des rapports plus récents (avec des numéros de séquence plus élevés) remplacent les anciens rapports.

### <a name="health-events"></a>Événements d’état
En interne, la banque de santé conserve des [événements d’état](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), qui contiennent toutes les informations des rapports et des métadonnées supplémentaires. Les métadonnées incluent le temps que le rapport a été fourni au client de santé et l’heure, qu'il a été modifié sur le serveur. Les événements d’état sont retournés par les [requêtes d’état de santé](service-fabric-view-entities-aggregated-health.md#health-queries).

Les métadonnées ajoutée contient :

- **SourceUtcTimestamp**. Le temps de l’état a été donné au client de santé (temps universel).

- **LastModifiedUtcTimestamp**. L’heure de que la dernière modification de l’état côté serveur (temps universel).

- **IsExpired**. Un indicateur pour indiquer si le rapport a expiré lorsque la requête a été exécutée par le magasin d’état de santé. Un événement peut être expiré uniquement si RemoveWhenExpired a la valeur false. Dans le cas contraire, l’événement n’est pas retourné par la requête et est supprimé de la banque.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**. La dernière fois pour les transitions de OK/erreur/avertissement. Ces champs fournissent des transitions d’état pour les événements de l’historique de l’état de santé.

Les champs de transition d’état peuvent servir pour les alertes plus efficacement ou des informations sur les événements de santé « historiques ». Ils activent des scénarios tels que :

- Alerte lorsqu’une propriété a été au niveau d’avertissement/erreur de plus de X minutes. Vérification de la condition pour une période de temps permet d’éviter les alertes sur les conditions temporaires. Par exemple, une alerte si l’état a été avertissement pendant plus de cinq minutes peut être traduite (HealthState == avertissement et maintenant - LastWarningTransitionTime > 5 minutes).

- Alerte que dans des conditions qui ont changé dans la dernière X minutes. Si un rapport était déjà d’un erreur avant l’heure spécifiée, il peut être ignoré, car il a été déjà signalé précédemment.

- Si une propriété est basculer entre l’avertissement et d’erreur, déterminer la durée pendant laquelle il a été défectueux (c'est-à-dire, pas OK). Par exemple, une alerte si la propriété n’a pas été saine pendant plus de cinq minutes peut être traduite (HealthState ! = Ok et maintenant - LastOkTransitionTime > 5 minutes).

## <a name="example-report-and-evaluate-application-health"></a>Exemple : Rapport et évaluer l’intégrité de l’application
L’exemple suivant envoie un rapport de santé via PowerShell sur l’application **fabric : / WordCount** à partir de la source **MyWatchdog**. L’état de santé contient des informations sur la propriété de l’état « disponibilité » dans l’état de santé d’erreur, avec la propriété TimeToLive infinie. Il interroge ensuite l’état d’application, qui retourne agrégées des erreurs d’état d’intégrité et les événements d’état signalés dans la liste des événements d’état.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Utilisation du modèle d’état de santé
Le modèle de fonctionnement permet de services en nuage et la plate-forme sous-jacente de la Fabric de Service à l’échelle, car les déterminations de surveillance et de la santé sont réparties sur les moniteurs différents au sein du cluster.
Autres systèmes ont un service unique et centralisé au niveau du cluster qui analyse toutes les *potentiellement* utiles informations émises par les services. Cette approche empêche leur évolutivité. Il ne les autorisez également à collecter des informations très spécifiques pour vous aider à identifier les problèmes et les problèmes potentiels que près de la cause possible.

Le modèle de fonctionnement est utilisé pour la surveillance et de diagnostic, pour évaluer la santé de cluster et des applications et des mises à niveau contrôlées. Autres services utilisent des données de santé à effectuer des réparations automatiques, générer des antécédents médicaux de cluster et émettre des alertes sous certaines conditions.

## <a name="next-steps"></a>Étapes suivantes
[Afficher les rapports health Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utiliser les rapports d’intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Comment signaler et vérifier l’intégrité du service](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Ajouter des rapports personnalisés de health Service Fabric](service-fabric-report-health.md)

[Surveiller et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications de service Fabric](service-fabric-application-upgrade.md)
