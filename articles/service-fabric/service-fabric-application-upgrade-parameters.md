
<properties
   pageTitle="Mise à niveau de l’application : mettre à niveau les paramètres | Microsoft Azure"
   description="Décrit les paramètres relatifs à la mise à niveau d’une application de Service Fabric, notamment pour effectuer des vérifications de fonctionnement et des stratégies automatiquement annuler la mise à niveau."
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



# <a name="application-upgrade-parameters"></a>Paramètres de mise à niveau d’application

Cet article décrit les différents paramètres qui s’appliquent au cours de la mise à niveau d’une application de Service de Azure Fabric. Les paramètres comprennent le nom et la version de l’application. Ils sont des boutons qui contrôlent les délais d’attente et les contrôles qui sont appliqués au cours de la mise à niveau, et définissent les stratégies doivent être appliquées en cas d’échec d’une mise à niveau.


<br>

| Paramètre | Description |
| --- | --- |
| ApplicationName | Nom de l’application en cours de mise à niveau. Exemples : fabric : / VisualObjects, fabric : / ClusterMonitor  |
| TargetApplicationTypeVersion | Type de la version de l’application les cibles de mise à niveau. |
| FailureAction | L’action entreprise par Fabric du Service en cas d’échec de la mise à niveau. L’application peut être restaurée vers la version mise à jour préalable (rollback), ou la mise à niveau peut être arrêté au niveau du domaine en cours de mise à niveau. Dans ce dernier cas, le mode de mise à niveau est également modifié sur manuel. Les valeurs autorisées sont manuel et la restauration. |
| HealthCheckWaitDurationSec | Le délai d’attente (en secondes) après que la mise à niveau terminée sur le domaine mis à niveau avant le Service Fabric évalue l’état de santé de l’application. Cette durée peut également être considérée comme le temps, qu'une application doit être en cours d’exécution avant qu’il peut être considéré comme sain. Si la vérification réussit, le processus de mise à niveau se poursuit dans le domaine de la mise à niveau suivant.  Si la vérification échoue, Service Fabric attend un intervalle (l’UpgradeHealthCheckInterval) avant une nouvelle tentative de la vérification d’état à nouveau jusqu'à ce que le HealthCheckRetryTimeout est atteinte. La valeur recommandée et par défaut est 0 secondes. |
| HealthCheckRetryTimeoutSec | La durée (en secondes) que Fabric Service continue d’effectuer l’évaluation d’intégrité avant de déclarer la mise à niveau comme ayant échoué. La valeur par défaut est de 600 secondes. Cette durée commence après que HealthCheckWaitDuration est atteinte. Dans cet HealthCheckRetryTimeout, Service Fabric peut effectuer plusieurs vérifications de la santé de la santé de l’application. La valeur par défaut est de 10 minutes et doit être personnalisée de manière appropriée pour votre application. |
| HealthCheckStableDurationSec | La durée (en secondes) pour vérifier que l’application est stable avant le déplacement vers le prochain domaine de mise à niveau ou à la fin de la mise à niveau. Cette durée d’attente est utilisée pour empêcher des modifications non détectées de santé droite après l’exécution de la vérification d’état. La valeur par défaut est de 120 secondes et doit être personnalisée de manière appropriée pour votre application. |
| UpgradeDomainTimeoutSec | Durée maximale (en secondes) de mise à niveau d’un seul domaine de mise à niveau. Si ce délai est atteint, la mise à niveau s’arrête et s’exécute en fonction du paramètre de UpgradeFailureAction. La valeur par défaut n’est jamais (infini) et doit être personnalisé de manière appropriée pour votre application. |
| UpgradeTimeout | Un délai d’attente (en secondes) qui s’applique à la mise à niveau complète. Si ce délai est atteint, la mise à niveau cesse et UpgradeFailureAction est déclenchée. La valeur par défaut n’est jamais (infini) et doit être personnalisé de manière appropriée pour votre application. |
| UpgradeHealthCheckInterval | La fréquence est que l’état de santé est activée. Ce paramètre est spécifié dans la section ClusterManager de la _cluster_ _manifeste_et n’est pas spécifié dans le cadre de l’applet de commande de mise à niveau. La valeur par défaut est de 60 secondes.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Évaluation d’intégrité au cours de la mise à niveau de l’application de service

<br>
Les critères d’évaluation de la santé sont facultatifs. Si les critères d’évaluation de santé ne sont pas spécifiés au démarrage d’une mise à niveau, Service Fabric utilise les stratégies de santé d’application spécifiés dans le ApplicationManifest.xml de l’instance de l’application.


<br>

| Paramètre | Description |
| --- | --- |
| ConsiderWarningAsError | Valeur par défaut est False. Traiter les événements d’état avertissement d’application comme des erreurs lors de l’évaluation de l’état de santé de l’application au cours de la mise à niveau. Par défaut, Service Fabric n’évalue pas les événements d’état avertissement pour être des échecs (erreurs), afin que la mise à niveau peut se poursuivre même si des événements d’avertissement.   |
| MaxPercentUnhealthyDeployedApplications | Par défaut et la valeur recommandée est 0. Spécifier le nombre maximal d’applications déployées (voir la [section d’état](service-fabric-health-introduction.md)) qui peut être défectueux avant que l’application est considérée comme étant défectueuse et l’échec de la mise à niveau. Ce paramètre définit l’état de l’application sur le nœud et vous aide à détecter les problèmes de mise à jour. En règle générale, les réplicas de l’application d’obtenir avec équilibrage de charge vers l’autre nœud, ce qui permet à l’application semble en bon état, ce qui permet la mise à niveau continuer. En spécifiant un contrôle d’intégrité stricte MaxPercentUnhealthyDeployedApplications, Service Fabric peut détecter un problème avec le package d’application rapidement et aider à produire un message d’échec rapide mise à niveau. |
| MaxPercentUnhealthyServices | Par défaut et la valeur recommandée est 0. Spécifier le nombre maximal de services dans l’instance d’application pouvant être défectueux avant que l’application est considérée comme étant défectueuse et l’échec de la mise à niveau. |
| MaxPercentUnhealthyPartitionsPerService | Par défaut et la valeur recommandée est 0. Spécifier le nombre maximal de partitions dans un service qui peut être défectueux avant que le service est considéré comme défectueux. |
| MaxPercentUnhealthyReplicasPerPartition | Par défaut et la valeur recommandée est 0. Spécifier le nombre maximal de réplicas de partition pouvant être défectueuse avant que la partition est considérée comme incorrecte. |
| UpgradeReplicaSetCheckTimeout | **Service sans état**--dans un seul domaine de mise à niveau, Service Fabric essaie de garantir que des instances supplémentaires du service sont disponibles. Si le nombre d’instances cible est supérieur à un, Service Fabric attend plus d’une instance disponible, jusqu'à une valeur maximale de délai d’attente. Ce délai est spécifié à l’aide de la propriété UpgradeReplicaSetCheckTimeout. Si le délai d’attente expire, Service Fabric procède à la mise à niveau, quel que soit le nombre d’instances de service. Si le nombre d’instances cible, Service Fabric n’attend pas et procède immédiatement la mise à niveau. **Service dynamique**--dans un seul domaine de mise à niveau, Service Fabric essaie de garantir que le jeu de réplicas a un quorum. Service de Fabric attend un quorum soit disponible, jusqu'à une valeur de délai d’attente maximal (spécifiée par la propriété UpgradeReplicaSetCheckTimeout). Si le délai d’attente expire, Service Fabric procède à la mise à niveau, quel que soit le quorum. Ce paramètre est que jamais ensemble (infini) lors de la restauration et 900 secondes lors de la restauration. |
| ForceRestart | Si vous mettez à jour une configuration ou un ensemble de données sans mettre à jour le code de service, le service est redémarré uniquement si la propriété ForceRestart est définie sur true. Lors de la mise à jour est terminée, le Service Fabric avertit le service qu’un nouveau package de configuration ou d’un paquet de données est disponible. Le service est responsable de l’application des modifications. Si nécessaire, le service peut redémarrer automatiquement. |



<br>
<br>
Les critères de MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService et MaxPercentUnhealthyReplicasPerPartition peuvent être spécifiés par type de service d’une instance de l’application. Permet de définir ces paramètres par service d’une application contenir des types de services différents avec des stratégies d’évaluation différentes. Par exemple, un type de service passerelle sans état peut avoir un MaxPercentUnhealthyPartitionsPerService qui est différent d’un type de service moteur dynamique pour une instance d’application particulière.

## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau de votre Application à l’aide de Visual Studio](service-fabric-application-upgrade-tutorial.md) vous guide à travers une mise à niveau de l’application à l’aide de Visual Studio.

[Mise à niveau de votre Powershell à l’aide d’Application](service-fabric-application-upgrade-tutorial-powershell.md) vous présente une mise à niveau de l’application à l’aide de PowerShell.

Assurer la compatibilité de vos mises à niveau de l’application en apprenant comment utiliser la [Sérialisation des données](service-fabric-application-upgrade-data-serialization.md).

Apprenez à utiliser les fonctionnalités avancées lors de la mise à niveau de votre application en vous reportant aux [Rubriques avancées](service-fabric-application-upgrade-advanced.md).

Résoudre les problèmes courants dans les mises à niveau de l’application en vous reportant aux étapes de [Dépannage les mises à niveau de l’Application](service-fabric-application-upgrade-troubleshooting.md).
 
