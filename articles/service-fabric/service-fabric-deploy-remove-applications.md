<properties
   pageTitle="Déploiement d’applications Fabric de service | Microsoft Azure"
   description="Comment déployer et supprimer des applications dans un Service de Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# <a name="deploy-and-remove-applications-using-powershell"></a>Déployer et supprimer des applications à l’aide de PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

Une fois un [type d’application est empaqueté][10], il est prêt à être déployé dans un cluster d’Azure Fabric de Service. Le déploiement implique les trois étapes suivantes :

1. Télécharger le package d’application
2. Inscrire le type d’application
3. Créez l’instance d’application

>[AZURE.NOTE] Si vous utilisez Visual Studio pour le déploiement et le débogage d’applications sur votre cluster de développement local, toutes les étapes suivantes sont gérées automatiquement via un script PowerShell situé dans le dossier Scripts du projet d’application. Cet article fournit des informations générales sur ce que font ces scripts afin que vous pouvez effectuer les mêmes opérations en dehors de Visual Studio.

## <a name="upload-the-application-package"></a>Télécharger le package d’application

Télécharger le package d’application place dans un emplacement accessible par les composants internes du Service Fabric. Vous pouvez utiliser PowerShell pour effectuer le téléchargement. Avant d’exécuter des commandes PowerShell dans cet article, commencez toujours à l’aide de [ServiceFabricCluster de la connexion](https://msdn.microsoft.com/library/mt125938.aspx) pour la connexion au Fabric du Service cluster.

Supposons que vous ayez un dossier nommé *MyApplicationType* qui contient le manifeste d’application nécessaires, les manifestes de service et les packages de données/config/code. La commande de [Copie-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) charge le package dans le magasin d’Image du cluster. L’applet de commande **Get-ImageStoreConnectionStringFromClusterManifest** , qui fait partie du module PowerShell de SDK Service Fabric, est utilisée pour obtenir la chaîne de connexion du magasin image.  Pour importer le module du Kit de développement logiciel, exécutez :

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

Vous pouvez copier un package d’application à partir de la *C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug* à *c:\temp\MyApplicationType* (renommer le répertoire « debug » à « MyApplicationType »). L’exemple suivant télécharge le package :

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>Enregistrer le package d’application

Enregistrement du package d’application rend la version déclaré dans le manifeste d’application à utiliser et le type d’application. Le système lit le package téléchargé à l’étape précédente, vérifiez que le package (équivalent à l’exécution de [Test-ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) localement), traiter le contenu du package et copier le package transformé vers un emplacement de système interne.

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

La commande [Register-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) retourne uniquement une fois que le système a copié le package d’application. La durée de ce processus dépend du contenu du package d’application. Si nécessaire, le paramètre **- TimeoutSec** peut être utilisé pour fournir un délai plus long. (Le délai par défaut est 60 secondes).

La commande [Get-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx) répertorie toutes les versions de type application enregistrée avec succès.

## <a name="create-the-application"></a>Création de l’application

Vous pouvez instancier une application à l’aide de n’importe quelle version de type d’application qui a été enregistrée avec succès par le biais de la commande [New-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx) . Le nom de chaque application doit commencer par le *fabric :* schéma et être unique pour chaque instance de l’application. Tous les services par défaut définis dans le manifeste d’application du type d’application cible sont créés pour l’instant.

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

La commande [Get-ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx) répertorie toutes les instances d’application qui ont été créés, ainsi que de leur état général.

La commande [Get-ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx) répertorie toutes les instances de service qui ont été créés dans une instance de l’application donnée. Services par défaut (le cas échéant) sont répertoriés ici.

Plusieurs instances de l’application peuvent être créés pour une version donnée d’un type d’application enregistrée. Chaque instance de l’application s’exécute de manière isolée, avec son propre processus et le répertoire de travail.

## <a name="remove-an-application"></a>Supprimer une application

Lorsqu’une instance d’application n’est plus nécessaire, vous pouvez le supprimer définitivement à l’aide de la commande [Remove-ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) . Cette commande supprime automatiquement tous les services qui appartiennent à l’application, la suppression définitive de tous les États de service. Cette opération ne peut pas être annulée, et état de l’application ne peut pas être récupéré.

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

Lorsqu’une version particulière d’un type d’application n’est plus nécessaire, vous devez annuler son inscription à l’aide de la commande de [Désinscription-ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) . Annulation de l’inscription des types non utilisés libère l’espace de stockage utilisé par le contenu de package d’application de ce type sur le magasin d’image. Un type d’application peut être supprimée tant qu’aucune application n’est instanciées par rapport à elle et n’en attente de l’application mises à niveau sont faites référence.

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Demande de copie-ServiceFabricApplicationPackage pour une ImageStoreConnectionString

L’environnement de Service Fabric SDK doit déjà disposer les valeurs par défaut correctes. Mais, si nécessaire, le ImageStoreConnectionString pour toutes les commandes doit correspondre à la valeur le Fabric du Service de cluster utilise. Vous pouvez le trouver dans le manifeste de cluster récupéré par le biais de la commande [Get-ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx) :

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>Étapes suivantes

[Mise à niveau des applications de service Fabric](service-fabric-application-upgrade.md)

[Introduction de health service Fabric](service-fabric-health-introduction.md)

[Diagnostiquer et résoudre les problèmes liés à un service de Fabric du Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modèle d’une application dans le tissu de Service](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
