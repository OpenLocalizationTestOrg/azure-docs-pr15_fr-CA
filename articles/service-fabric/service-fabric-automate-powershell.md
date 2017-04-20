<properties
    pageTitle="Automatiser la gestion des applications Service Fabric à l’aide de PowerShell | Microsoft Azure"
    description="Déployer, mettre à niveau, tester et supprimer les applications Fabric de Service à l’aide de PowerShell."
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>Automatiser le cycle des applications à l’aide de PowerShell

De nombreux aspects du [cycle de vie des applications Fabric de Service](service-fabric-application-lifecycle.md) peuvent être automatisées.  Cet article explique comment utiliser PowerShell pour automatiser les tâches courantes de déploiement, mise à niveau, supprimer et tester des applications Azure Fabric de Service.  Géré et HTTP APIs pour la gestion de l’application sont également disponibles. Consultez le [cycle de vie](service-fabric-application-lifecycle.md) pour plus d’informations.  

## <a name="prerequisites"></a>Conditions préalables
Avant de passer aux tâches dans l’article, veillez à :

+ Familiarisez-vous avec les concepts de Service Fabric décrits dans [présentation technique du Service de Fabric](service-fabric-technical-overview.md).
+ [Installez le runtime, SDK et les outils](service-fabric-get-started.md), qui installe également le module PowerShell de **ServiceFabric** .
+ [L’exécution du script PowerShell d’activer](service-fabric-get-started.md#enable-powershell-script-execution).
+ Démarrer un cluster local.  Ouvrir une nouvelle fenêtre PowerShell en tant qu’administrateur et exécutez le script de configuration du cluster à partir du dossier du Kit de développement logiciel :`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ Avant d’exécuter des commandes PowerShell dans cet article, d’abord vous connecter au cluster Service Fabric local à l’aide de [**ServiceFabricCluster de la connexion**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ Les tâches suivantes nécessitent un package d’application pour déployer v1 et v2 d’un package d’application pour la mise à niveau. Télécharger l' [ **WordCount** , exemple d’application](http://aka.ms/servicefabricsamples) (qui se trouve dans les exemples de mise en route). Générer et empaqueter l’application dans Visual Studio (avec le bouton droit sur **WordCount** dans l’Explorateur de solutions et sélectionnez **Package**). Copier le package v1 dans `C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug` à `C:\Temp\WordCount`. Copie `C:\Temp\WordCount` à `C:\Temp\WordCountV2`, créez le package d’application v2 pour mise à niveau. Ouvrir `C:\Temp\WordCountV2\ApplicationManifest.xml` dans un éditeur de texte. Dans l’élément **ApplicationManifest** , modifiez l’attribut **ApplicationTypeVersion** à partir de « 1.0.0 » « 2.0.0 » mettre à jour le numéro de version d’application. Enregistrez le fichier modifié de la ApplicationManifest.xml.

## <a name="task-deploy-a-service-fabric-application"></a>Tâche : Déployer une application de Service Fabric

Après avoir généré et empaqueté l’application (ou téléchargé le package d’application), vous pouvez déployer l’application dans un cluster à structure de Service local. Le déploiement implique le téléchargement du package d’application, l’enregistrement du type d’application et la création de l’instance d’application. Suivez les instructions de cette section pour déployer une application sur un cluster.

### <a name="step-1-upload-the-application-package"></a>Étape 1 : Téléchargement du package d’application
Télécharger le package d’application pour le magasin d’image place celui-ci dans un emplacement accessible aux composants de Fabric de Service internes.  Le package d’application contient la configuration de code, manifeste d’application et les manifestes de service nécessaires et les packages de données à créer l’application et les instances de service.  La commande de [**Copie-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) télécharge le package. Par exemple :

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>Étape 2 : Enregistrer le type d’application
Enregistrement du package d’application rend la version déclaré dans le manifeste d’application à utiliser et le type d’application. Le système lit le package téléchargé à l’étape 1, vérifiez que le package (équivalent à l’exécution de [**Test-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) localement), traiter le contenu du package et copier le package transformé vers un emplacement de système interne.  Exécutez l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCount
```
Pour afficher tous les types d’application enregistrés dans le cluster, exécutez l’applet de commande [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx) :

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>Étape 3 : Créez l’instance d’application
Une application peut être instanciée à l’aide de n’importe quelle version de type d’application qui a été enregistrée avec succès à l’aide de la commande [**New-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) . Le nom de chaque application est déclaré au niveau du déploiement et doit commencer par le **fabric :** schéma et être unique pour chaque instance de l’application. Le nom de type d’application et la version de l’application sont déclarées dans le fichier **ApplicationManifest.xml** du package d’application. Si tous les services par défaut ont été définies dans le manifeste d’application du type d’application cible, puis celles sont créés pour l’instant.

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

La commande [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx) répertorie toutes les instances d’application qui ont été créés, ainsi que de leur état général. La commande [**Get-ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx) répertorie toutes les instances de service qui ont été créés dans une instance de l’application donnée. Services par défaut (le cas échéant) sont répertoriés.

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>Tâche : Mettre à niveau une application de Service Fabric
Vous pouvez mettre à niveau une application précédemment déployée de tissu de Service avec un package d’application mis à jour. Cette tâche met à niveau l’application WordCount qui a été déployée dans « tâche : déployer une application de Service Fabric. » Pour plus d’informations, lire via la [mise à niveau de l’application de Service de Fabric](service-fabric-application-upgrade.md) .

Pour simplifier les choses pour cet exemple, seul le numéro de version d’application a été mis à jour dans le package d’application WordCountV2 créé dans les conditions préalables. Un scénario plus réaliste impliquerait la mise à jour de vos fichiers de données, de configuration ou de code de service reconstruction et empaquetage de votre application avec les numéros de version mis à jour.  

### <a name="step-1-upload-the-updated-application-package"></a>Étape 1 : Téléchargez le package de mise à jour d’application
L’application v1 WordCount est prête à être mis à niveau. Si vous ouvrez une fenêtre PowerShell en tant qu’administrateur et tapez [**Get-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx), vous voyez que la version 1.0.0 du type application WordCount est déployée.  

Maintenant copier le package d’application mis à jour dans le magasin d’image Service Fabric (où les packages d’application sont stockées par le Service Fabric). Le paramètre **ApplicationPackagePathInImageStore** indique le Fabric du Service où il peut trouver le package d’application. La commande suivante copie le package d’application pour **WordCountV2** dans le magasin d’image :  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>Étape 2 : Enregistrer le type d’application mis à jour
L’étape suivante consiste à enregistrer la nouvelle version de l’application avec le Service de Fabric, qui peut être effectuée à l’aide de l’applet de commande [**Register-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) :

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>Étape 3 : Démarrer la mise à niveau
Différents paramètres de mise à niveau, des délais et des critères de police sanitaire peuvent être appliqués aux mises à niveau de l’application. Lisez les documents [d’application des paramètres de mise à niveau](service-fabric-application-upgrade-parameters.md) et le [processus de mise à niveau](service-fabric-application-upgrade.md) pour en savoir plus. Tous les services et les instances doivent être _en bon état_ après la mise à niveau.  Définir le **HealthCheckStableDuration** de 60 secondes (de sorte que les services sont en bon état pendant au moins 20 secondes avant la mise à niveau se poursuit sur le domaine de la mise à niveau suivant).  Également définir **UpgradeDomainTimeout** à 1 200 secondes et **UpgradeTimeout** à 3000 secondes. Enfin, la valeur du **UpgradeFailureAction** **rollback**, qui demande que Service Fabric annule l’application à la version précédente si vous rencontrez des erreurs lors de la mise à niveau.

Vous pouvez maintenant démarrer la mise à niveau de l’application à l’aide de l’applet de commande [**Start-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx) :

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

Notez que le nom de l’application est le même que le nom de l’application déployée précédemment v1.0.0 (fabric : / WordCount). Service de Fabric utilise ce nom pour identifier l’application qui est mise en route de mise à niveau. Si vous définissez les délais d’attente trop courte, vous pouvez rencontrer un message d’échec de délai d’expiration qui indique le problème. Reportez-vous aux [mises à niveau des applications de dépannage](service-fabric-application-upgrade-troubleshooting.md)ou augmenter les délais d’attente.

### <a name="step-4-check-upgrade-progress"></a>Étape 4 : Progression de mise à jour vérification
Vous pouvez surveiller la progression de mise à niveau application en utilisant [l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md)ou à l’aide de l’applet de commande [**Get-ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx) :

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

Dans quelques minutes, l’applet de commande [Get-ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx) indique que tous les domaines de mise à niveau ont été mis à niveau (terminé).

## <a name="task-test-a-service-fabric-application"></a>Tâche : Test d’une application de Service Fabric

Pour écrire des services de haute qualité, les développeurs doivent être capable d’induire des erreurs d’infrastructure fiable pour tester la stabilité de leurs services. TISSU de service offre aux développeurs la possibilité d’induire des actions de panne et de tester les services en présence de défaillances à l’aide de scénarios de test chaos et le basculement.  Lisez la [vue d’ensemble de la testabilité](service-fabric-testability-overview.md) pour plus d’informations.

### <a name="step-1-run-the-chaos-test-scenario"></a>Étape 1 : Exécutez le scénario de test de chaos
Le scénario de test de chaos génère des erreurs sur l’ensemble du cluster Service Fabric. Le scénario compresse les erreurs généralement observées pendant des mois ou des années à quelques heures. La combinaison de défauts entrelacées avec un taux de défaillance élevé de recherche des cas qui passe inaperçues dans le cas contraire. L’exemple suivant montre le scénario de test de chaos pendant 60 minutes.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>Étape 2 : Exécutez le scénario de test de basculement
Le basculement des cibles de scénario de test une partition de service spécifique au lieu de l’ensemble du cluster, et il laisse les autres services affectés. Le scénario effectue une itération dans une séquence de défaillances simulées lors de la validation de service pendant l’exécution de votre logique métier. Un échec de validation du service indique un problème qui nécessite des enquête. Le test de basculement n'induit qu’une seule erreur à la fois, et non le scénario de test de chaos, qui peut amener à plusieurs erreurs. L’exemple suivant exécute le test de basculement sur incident pendant 60 minutes par rapport à la fabric : WordCount/service de WordCountService.

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>Tâche : Supprimer une application de Service Fabric
Vous pouvez supprimer une instance d’une application déployée, supprimer le type d’application de mise en service du cluster et supprimer le package d’application à partir de la ImageStore.

### <a name="step-1-remove-an-application-instance"></a>Étape 1 : Supprimer une instance d’application
Lorsqu’une instance d’application n’est plus nécessaire, vous pouvez le supprimer définitivement à l’aide de l’applet de commande [**Remove-ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx) . Cette opération supprime automatiquement tous les services appartenant à l’application, la suppression définitive de tous les États de service. Cette opération ne peut pas être annulée et l’état de l’application ne peut pas être récupéré.

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>Étape 2 : Annuler l’enregistrement du type d’application
Lorsque vous n’avez plus besoin une version particulière d’un type d’application, annuler son inscription à l’aide de l’applet de commande [**Unregister-ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx) . Annulation de l’inscription des types non utilisés libère l’espace de stockage utilisé par le package d’application dans le magasin d’image. Un type d’application peut être supprimée tant qu’aucune application instancié sur lui ou en attente de mises à niveau de l’application référençant.

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>Étape 3 : Supprimer le package d’application
Une fois le type d’application est annulé, l’application peut être supprimé du magasin d’image à l’aide de l’applet de commande [**Remove-ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx) .

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>Étapes suivantes
[Cycle de vie des applications Fabric de service](service-fabric-application-lifecycle.md)

[Déploiement d’une application](service-fabric-deploy-remove-applications.md)

[Mise à niveau de l’application](service-fabric-application-upgrade.md)

[Applets de commande de Service Fabric Azure](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Cmdlets de testabilité Azure Fabric du Service](https://msdn.microsoft.com/library/azure/mt125844.aspx)
