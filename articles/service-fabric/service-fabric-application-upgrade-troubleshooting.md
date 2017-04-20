<properties
   pageTitle="Résolution des problèmes de mises à niveau de l’application | Microsoft Azure"
   description="Cet article décrit certains problèmes courants autour de la mise à niveau d’une application de Service Fabric et comment les résoudre."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="troubleshoot-application-upgrades"></a>Résoudre les problèmes de mises à niveau de l’application

Cet article aborde certains des problèmes courants autour de la mise à niveau d’une application de Service de Azure Fabric et comment les résoudre.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Résoudre les problèmes liés à une mise à niveau de l’application qui a échoué

En cas d’échec d’une mise à niveau, la sortie de la commande **Get-ServiceFabricApplicationUpgrade** contient des informations supplémentaires pour le débogage de l’échec.  La liste suivante indique comment les informations supplémentaires peuvent être utilisées :

1. Identifier le type d’échec.
2. Identifiez la raison de l’échec.
3. Isoler un ou plusieurs composants défectueux pour examen ultérieur.

Cette information est disponible lorsque le Service Fabric détecte la panne, qu’il s’agisse du **FailureAction** , d’annuler ou de suspendre la mise à niveau.

### <a name="identify-the-failure-type"></a>Identifier le type d’échec

Dans la sortie de **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifie l’horodatage (en UTC) à laquelle un échec de mise à niveau a été détecté par le Service Fabric et **FailureAction** a été déclenchée. **FailureReason** identifie une des trois causes principales possibles de l’échec :

1. UpgradeDomainTimeout - indique qu’un domaine de mise à niveau particulier a pris trop de temps et que **UpgradeDomainTimeout** a expiré.
2. OverallUpgradeTimeout - indique que la mise à niveau globale a pris trop de temps et que **UpgradeTimeout** a expiré.
3. Bilan - indique qu’après la mise à niveau d’un domaine de mise à jour, l’application est restée défectueux en fonction des stratégies de santé spécifié et **HealthCheckRetryTimeout** a expiré.

Ces entrées s’afficher uniquement dans la sortie lors de la mise à niveau échoue et lance la restauration. Des informations complémentaires s’affiche en fonction du type de l’erreur.

### <a name="investigate-upgrade-timeouts"></a>Étudier les délais de mise à niveau

Délai d’attente échecs sont généralement provoquées par des problèmes de disponibilité de service de mise à niveau. La sortie ci-dessous est typique des mises à niveau où des réplicas de service ou des instances de ne pas démarrer dans la nouvelle version du code. Le champ **UpgradeDomainProgressAtFailure** capture un instantané de tout travail en attente de mise à niveau au moment de la défaillance.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

Dans cet exemple, la mise à niveau a échoué au domaine de mise à niveau *MYUD1* et deux partitions (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* et *4b43f4d8-b26b-424e-9307-7a7a62e79750*) ont été bloquées. Les partitions ont été bloquées, car le runtime n’a pas pu placer des réplicas principales (*WaitForPrimaryPlacement*) sur les nœuds cible *Node1* et *Node4*.

La commande **Get-ServiceFabricNode** peut être utilisée pour vérifier que ces deux nœuds sont dans le domaine de mise à niveau *MYUD1*. *UpgradePhase* indique que *PostUpgradeSafetyCheck*, qui signifie que ces contrôles de sécurité sont produisent une fois que tous les nœuds dans le domaine de mise à niveau ont terminé la mise à niveau. Toutes ces informations pointant sur un problème potentiel avec la nouvelle version du code d’application. Les problèmes les plus courants sont les erreurs de service dans la commande Ouvrir ou promotion pour les chemins de code principal.

Un *UpgradePhase* de *PreUpgradeSafetyCheck* signifie il existait des problèmes de préparation du domaine de mise à niveau avant qu’il a été effectuée. Dans ce cas, les problèmes les plus courants sont les erreurs de service dans la fermer ou la rétrogradation de chemins d’accès de code principal.

L' actuel **UpgradeState** est *RollingBackCompleted*, la mise à niveau d’origine doit avoir été effectuée avec une **FailureAction**, qui annule automatiquement la mise à niveau en cas d’échec de la restauration. Si la mise à jour d’origine a été effectuée avec un manuel **FailureAction**, puis la mise à niveau serait plutôt être dans un état suspendu à direct débogage de l’application.

### <a name="investigate-health-check-failures"></a>Examinez les erreurs de contrôle de la santé

Erreurs de contrôle de la santé peuvent être déclenchés par différents problèmes qui peuvent se produire une fois tous les nœuds dans un domaine de mise à niveau mise à niveau et en passant toutes les vérifications de sécurité. La sortie ci-dessous est typique d’un échec de mise à niveau en raison de l’échec des contrôles. Le champ **UnhealthyEvaluations** capture un instantané de contrôles qui a échoué au moment de la mise à niveau selon la [stratégie d’intégrité](service-fabric-health-introduction.md)de spécifié.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

Rechercher les erreurs de contrôle de la santé nécessite tout d’abord la compréhension du modèle health Service Fabric. Mais même sans une telle compréhension approfondie, nous pouvons voir que les deux services sont défectueux : *fabric : DemoApp/Svc3* et *fabric : DemoApp/Svc2*, ainsi que les rapports d’erreurs de la santé (« InjectedFault » dans le cas présent). Dans cet exemple, deux des quatre services sont défectueux, ce qui est inférieur à la cible par défaut de 0 % incorrecte (*MaxPercentUnhealthyServices*).

La mise à niveau a été suspendu lors de l’échec en spécifiant un **FailureAction** du manuel lors du démarrage de la mise à niveau. Ce mode permet d’étudier le système en état d’échec avant d’effectuer toute autre action.

### <a name="recover-from-a-suspended-upgrade"></a>Récupérer à partir d’une mise à jour interrompue

Avec une restauration **FailureAction**, il n’y a aucune récupération nécessaire dans la mesure où la mise à niveau annule automatiquement lors de la défaillance. Un manuel **FailureAction**, il existe plusieurs options de restauration :

1. Déclencher manuellement une annulation
2. Suivez les instructions de la mise à niveau manuellement
3. Reprendre la mise à niveau surveillé

La commande **Start-ServiceFabricApplicationRollback** peut être utilisée à tout moment pour démarrer la restauration de l’application. Une fois que la commande a été retournée avec succès, la demande d’annulation a été enregistrée dans le système et démarre peu de temps après.

La commande **Resume-ServiceFabricApplicationUpgrade** peut être utilisée pour passer à la suite de la mise à niveau manuellement, un domaine de mise à niveau à la fois. Dans ce mode, les vérifications de sécurité uniquement sont effectuées par le système. Les vérifications de fonctionnement plus aucune sont exécutées. Cette commande peut uniquement être utilisée lorsque *UpgradeState* illustre les *RollingForwardPending*, qui signifie que le domaine en cours de mise à niveau a terminé la mise à niveau, mais suivant n’a pas démarré (en attente).

La commande de **ServiceFabricApplicationUpgrade de la mise à jour** peut être utilisée pour reprendre la mise à niveau surveillé avec à la fois la sécurité et les vérifications en cours d’exécution.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

La mise à niveau continue à partir de la mise à niveau de domaine dans lequel il a été suspendu en dernier et l’utilisation du même mettre à niveau les paramètres et les stratégies d’intégrité comme avant. Si nécessaire, un des paramètres de mise à niveau et des stratégies d’intégrité indiquées dans la sortie précédente peut être modifié dans la même commande de lors de la mise à niveau reprend. Dans cet exemple, la mise à niveau a été repris en mode surveillés, avec les paramètres et les stratégies d’intégrité inchangées.

## <a name="further-troubleshooting"></a>Procédures de dépannage supplémentaires

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Fabric de service n’est pas suivant les stratégies d’état spécifié

1 des causes possibles :

TISSU de service tous les pourcentages se traduit par des chiffres réels des entités (par exemple, les réplicas, les partitions et les services) pour l’évaluation de la santé et arrondit toujours à des entités entières. Par exemple, si le maximum _MaxPercentUnhealthyReplicasPerPartition_ est de 21 %, et il y a cinq réplicas, Service Fabric permet jusqu'à deux réplicas défectueux (que is,'Math.Ceiling (5\*0,21)). Par conséquent, les stratégies d’intégrité doivent être définies en conséquence.

2 des causes possibles :

Stratégies d’intégrité sont spécifiés en termes de pourcentages de services total et les instances de service non spécifiques. Par exemple, avant une mise à niveau, si une application a quatre instances A, B, C et D, du service en cas de service D est défectueux, mais avec peu d’impact sur l’application. Nous voulons ignorer le service défectueux connu D au cours de la mise à niveau et de définir le paramètre *MaxPercentUnhealthyServices* à 25 %, en supposant seulement A, B et C doivent être en bon état.

Toutefois, au cours de la mise à niveau, D peut devenir en bon état alors que C est incorrecte. La mise à niveau peut toujours aboutir, car seulement 25 % des services sont défectueux. Toutefois, cela peut entraîner des erreurs imprévues en raison de C étant défectueux de façon inattendue au lieu de D. Dans ce cas, D doit être modélisée sous la forme d’un type de service différent de A, B et C. Dans la mesure où les stratégies d’intégrité sont spécifiés par le type de service, les seuils d’autre pourcentage incorrecte peuvent être appliqués à différents services. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Je ne spécifiait pas une stratégie de contrôle de mise à niveau de l’application, mais la mise à niveau échoue pour certains délais jamais spécifiée

Lorsque les stratégies d’intégrité ne sont pas fournies à la demande de mise à niveau, elles sont prises de l' *ApplicationManifest.xml* de la version actuelle de l’application. Par exemple, si vous mettez à niveau Application X à partir de la version 1.0 à la version 2.0, les stratégies d’intégrité application spécifiée pour la version 1.0 sont utilisés. Si une stratégie d’intégrité différent doit être utilisée pour la mise à niveau, la stratégie doit être spécifié dans le cadre de l’appel de l’API application mise à niveau. Les stratégies spécifiés comme faisant partie de l’appel API s’appliquent uniquement au cours de la mise à niveau. Une fois la mise à niveau est terminée, les stratégies spécifiés dans *ApplicationManifest.xml* sont utilisés.

### <a name="incorrect-time-outs-are-specified"></a>Les délais d’attente incorrectes sont spécifiés.

Vous pouvez avoir demandé sur ce qui se passe lorsque les délais d’expiration sont définies de façon incohérente. Par exemple, avoir un *UpgradeTimeout* qui est inférieure à *UpgradeDomainTimeout*. La réponse est qu’une erreur est renvoyée. Les erreurs sont renvoyées si *UpgradeDomainTimeout* est inférieure à la somme de *HealthCheckWaitDuration* et *HealthCheckRetryTimeout*, ou si *UpgradeDomainTimeout* est inférieure à la somme de *HealthCheckWaitDuration* et *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Mes mises à niveau prennent trop de temps

Le temps pour effectuer une mise à niveau dépend sur les vérifications de fonctionnement et les délais d’attente spécifiés. Vérifications de fonctionnement et les délais d’attente dépendant de la durée de copier, de déployer et de stabilisation de l’application. Être trop agressifs avec les délais d’attente peut signifier plus échecs mises à niveau, nous vous recommandons de démarrage plus restrictives avec les délais d’attente plus longs.

Voici un rappel rapide sur l’interagissent entre les délais d’attente et les temps de mise à niveau :

Mises à niveau d’un domaine de mise à niveau ne peut pas terminer plus rapidement que les *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Échec de la mise à niveau ne peut pas se produire plus rapidement que les *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

L’heure de mise à niveau d’un domaine de mise à niveau est limitée par *UpgradeDomainTimeout*.  Si *HealthCheckRetryTimeout* et *HealthCheckStableDuration* sont toutes deux nulles et l’état de santé de l’application conserve des changements, puis la mise à niveau peut finir par expire sur *UpgradeDomainTimeout*. *UpgradeDomainTimeout* démarre le compte à rebours une fois la mise à niveau pour le domaine en cours de mise à niveau commence.

## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau de votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[Mise à niveau de votre Powershell à l’aide d’Application](service-fabric-application-upgrade-tutorial-powershell.md) vous présente une mise à niveau de l’application à l’aide de PowerShell.

Contrôler comment votre application de mises à niveau à l’aide des [Paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md).

Assurer la compatibilité de vos mises à niveau de l’application en apprenant comment utiliser la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en vous reportant aux [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en vous reportant aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 
