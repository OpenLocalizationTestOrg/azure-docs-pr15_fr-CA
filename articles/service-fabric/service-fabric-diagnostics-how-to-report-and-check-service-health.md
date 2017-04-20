<properties
   pageTitle="Rapport et vérifier l’intégrité du tissu de Service Azure | Microsoft Azure"
   description="Apprenez à envoyer des rapports d’état à partir de votre code de service et vérifier l’état de santé de votre service en utilisant les outils de surveillance de la santé qui fournit des services de Azure Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Rapport et vérifier l’intégrité du service
Lorsque vos services de rencontrer des problèmes, votre capacité à répondre à et de résoudre les incidents et pannes dépend de votre capacité à détecter les problèmes rapidement. Si vous signalez des problèmes et des échecs pour le Gestionnaire d’état Azure Fabric de Service à partir de votre code de service, vous pouvez utiliser les outils tissu de Service fournit pour vérifier l’état de santé de surveillance d’état standard.

Il existe deux méthodes que vous pouvez signaler la santé du service :

- Utiliser des objets [Partition](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) ou [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
Vous pouvez utiliser la `Partition` et `CodePackageActivationContext` objets de signaler l’état de santé des éléments qui font partie du contexte actuel. Par exemple, le code qui s’exécute dans le cadre d’un réplica peut signaler la santé uniquement sur ce réplica, la partition auquel il appartient et l’application dont il fait partie de.

- Utilisez `FabricClient`.   
Vous pouvez utiliser `FabricClient` pour la santé de rapport à partir du code de service, si le cluster n’est pas [sécurisée](service-fabric-cluster-security.md) ou si le service est exécuté avec des privilèges d’administrateur. Ce n’est pas vrai dans la plupart des scénarios du monde réel. Avec `FabricClient`, vous pouvez signaler la santé à toute entité qui fait partie du cluster. Dans l’idéal, cependant, code service doit uniquement envoyer des rapports qui sont liés à son propre état.

Cet article vous guide à travers un exemple qui signale l’état à partir du code de service. L’exemple montre également comment les outils assurant la Fabric du Service peuvent être utilisés pour vérifier l’état de santé. Cet article constitue une introduction rapide à la fonctionnalités de Fabric du Service de contrôle d’état. Pour des informations plus détaillées, vous pouvez lire la série d’articles détaillés à propos de la santé qui commencent par le lien à la fin de cet article.

## <a name="prerequisites"></a>Conditions préalables
Vous devez avoir les éléments suivants :

   * Visual Studio 2015
   * TISSU de service SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Pour créer un cluster local de développement sécurisé
- Ouvrez PowerShell avec privilèges administrateur et exécutez les commandes suivantes.

![Commandes qui montrent comment créer un cluster de développement sécurisé](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Pour déployer une application et vérifier son état de santé

1. Ouvrez Visual Studio en tant qu’administrateur.

2. Créez un projet à l’aide du modèle de **Service d’état** .

    ![Créer une application de Service Fabric avec le Service d’état](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Appuyez sur **F5** pour exécuter l’application en mode débogage. L’application sera déployée vers le cluster local.

4. Après l’exécution de l’application, cliquez sur l’icône Gestionnaire de Cluster Local dans la zone de notification et sélectionnez **Gérer le Cluster Local** dans le menu contextuel pour ouvrir le Service Fabric Explorer.

    ![Ouvrez l’Explorateur de tissu de Service à partir de la zone de notification](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. La santé de l’application doit être affichée comme dans cette image. À ce stade, l’application doit être en bon état sans erreurs.

    ![Application correcte dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. Vous pouvez également vérifier l’état de santé à l’aide de PowerShell. Vous pouvez utiliser ```Get-ServiceFabricApplicationHealth``` pour vérifier l’état de santé d’une application et vous pouvez utiliser ```Get-ServiceFabricServiceHealth``` pour vérifier l’état de santé d’un service. L’état de santé de la même application dans PowerShell est dans cette image.

    ![Application saine dans PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Pour ajouter des événements d’état personnalisé à votre code de service
Les modèles de projet de Service Fabric dans Visual Studio contiennent des exemples de code. Les étapes suivantes montrent comment vous pouvez signaler des événements d’état personnalisé à partir de votre code de service. Ces rapports apparaît automatiquement dans les outils standard pour la surveillance de l’intégrité que Service Fabric fournit, tels que le Service Fabric Explorer, la vue de la santé de portail Azure et PowerShell.

1. Rouvrez l’application que vous avez créée précédemment dans Visual Studio, ou créez une nouvelle application à l’aide du modèle de Visual Studio **Avec état Service** .

2. Ouvrez le fichier Stateful1.cs et recherchez la `myDictionary.TryGetValueAsync` appeler dans le `RunAsync` méthode. Vous pouvez voir que cette méthode retourne un `result` qui comporte la valeur actuelle du compteur parce que la logique de clé de cette application est à conserver un décompte en cours d’exécution. S’il s’agissait d’une application réelle, et si l’absence de résultat représenté une défaillance, vous pouvez signaler cet événement.

3. Pour signaler un événement d’état lorsque l’absence de résultat représente un échec, ajoutez les étapes suivantes.

    une barre d’outils. Ajouter le `System.Fabric.Health` le fichier Stateful1.cs de l’espace de noms.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Ajoutez le code suivant après le `myDictionary.TryGetValueAsync` appeler

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Nous signaler la santé réplica car elle est signalée par un service dynamique. Le `HealthInformation` paramètre stocke des informations sur le problème de santé qui est signalée.

    Si vous aviez créé un service sans état, utilisez le code suivant

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Si votre service s’exécute avec des privilèges d’administrateur ou si le cluster n’est pas [sécurisé](service-fabric-cluster-security.md), vous pouvez également utiliser `FabricClient` pour la santé du rapport comme indiqué dans les étapes suivantes.  

    une barre d’outils. Créer le `FabricClient` d’instance après la `var myDictionary` déclaration.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Ajoutez le code suivant après le `myDictionary.TryGetValueAsync` appeler.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Nous allons simuler cette panne et voir apparaissent dans les outils de surveillance de la santé. Pour simuler une panne, commentez la première ligne dans le code de déclaration d’intégrité que vous avez ajouté précédemment. Une fois que vous mettez en Commentez la première ligne, le code ressemblera à l’exemple suivant.

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Ce code se déclenche désormais cet état de santé de chaque fois `RunAsync` s’exécute. Après avoir apporté la modification, appuyez sur **F5** pour exécuter l’application.

6. Une fois que l’application s’exécute, ouvrez l’Explorateur de tissu de Service pour vérifier l’état de l’application. Cette fois, Service Fabric Explorer indiquera que l’application ne fonctionne pas correctement. C’est en raison de l’erreur a été signalée par le code que nous avons ajouté précédemment.

    ![Application incorrecte dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Si vous sélectionnez le réplica principal dans l’arborescence de l’Explorateur de tissu de Service, vous verrez que **État de fonctionnement** indique une erreur, trop. Service de Fabric Explorer affiche également les détails du rapport état de santé qui ont été ajoutés à la `HealthInformation` paramètre dans le code. Vous pouvez voir les mêmes rapports de santé dans PowerShell et le portail Azure.

    ![Santé des réplicas dans l’Explorateur de tissu de Service](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Ce rapport restera dans le Gestionnaire d’état jusqu'à ce qu’il est remplacé par un autre rapport, ou jusqu'à ce que ce réplica est supprimé. Étant donné que nous n’avez pas défini `TimeToLive` pour cet état de santé dans les `HealthInformation` objet, le rapport n’expirera jamais.

Il est recommandé que la santé doit être signalée sur le niveau plus granulaire, qui dans ce cas est le réplica. Vous pouvez également signaler l’état de santé sur `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

À l’état du rapport sur `Application`, `DeployedApplication`, et `DeployedServicePackage`, utilisez `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Étapes suivantes
[Plongée sur la santé du Service Fabric](service-fabric-health-introduction.md)
