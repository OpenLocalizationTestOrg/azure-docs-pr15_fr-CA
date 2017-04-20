<properties
   pageTitle="Modèle d’application Fabric du service | Microsoft Azure"
   description="Comment modéliser et décrire les applications et services de Fabric de Service."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>Modèle d’une application dans le tissu de Service

Cet article fournit une vue d’ensemble du modèle d’application Azure Fabric de Service. Il décrit également comment définir une application et un service via des fichiers manifeste et obtenir l’application emballé et prêt pour le déploiement.

## <a name="understand-the-application-model"></a>Comprendre le modèle d’application

Une application est un ensemble de services constitutifs qui effectuent une certaine fonction ou des fonctions. Un service exécute une fonction complète et autonome (il peut démarrer et s’exécuter indépendamment des autres services) et est composé de code et données de configuration. Pour chaque service, code se compose des fichiers binaires exécutables et configuration se compose de paramètres de service qui peuvent être chargées au moment de l’exécution de données se composent de données statiques arbitraires pour être consommés par le service. Chaque composant dans ce modèle d’application hiérarchique peut être gérées et mis à jour indépendamment.

![Le modèle d’application Fabric du Service][appmodel-diagram]


Un type d’application est une catégorisation d’une application et se compose d’un ensemble de types de services. Un type de service est une catégorisation d’un service. La catégorisation peut avoir différents paramètres et configurations, mais la fonctionnalité principale reste la même. Les instances de service sont les variations de configuration de service différent du même type de service.  

Classes (ou « types ») des applications et services sont décrits dans les fichiers XML (les manifestes d’application et les manifestes de service) qui sont des modèles par rapport à laquelle les applications peuvent être instanciées à partir du magasin d’image du cluster. La définition de schéma pour le fichier ServiceManifest.xml et ApplicationManifest.xml est installée avec le Kit de développement logiciel de Service de Fabric et des outils à *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

Le code pour les instances de l’autre application s’exécute en tant que processus distincts même si hébergé par le même nœud de Service Fabric. En outre, le cycle de vie de chaque instance de l’application peut être gérée (c'est-à-dire mise à niveau) indépendamment. Le diagramme suivant illustre la façon dont les types d’applications sont composés des types de services, qui à son tour sont composent de code, de configuration et de packages. Pour simplifier le diagramme, uniquement le code/config/data packages pour `ServiceType4` sont affichés, même si chaque type de service inclut certains ou tous les types de packages.

![Types d’applications Fabric de service et les types de services][cluster-imagestore-apptypes]

Deux fichiers manifeste différents sont utilisés pour décrire les applications et les services : le manifeste de service et un manifeste d’application. Celles-ci sont traitées en détail dans les sections qui en découlent.

Une ou plusieurs instances d’un type de service peut être actif dans le cluster. Par exemple, les instances de service dynamique, ou réplicas, assurer une grande fiabilité grâce à la réplication d’état entre des réplicas sur différents nœuds dans le cluster. Cette réplication fournit essentiellement une redondance pour le service soit disponible même si la défaillance d’un nœud dans un cluster. Plus un [service partitionnée](service-fabric-concepts-partitioning.md) divise son état (et les modèles d’accès à cet état) sur les nœuds du cluster.

Le diagramme suivant montre la relation entre les applications et les instances de service, les partitions et les réplicas.

![Partitions et duplications au sein d’un service][cluster-application-instances]


>[AZURE.TIP] Vous pouvez afficher la disposition des applications dans un cluster à l’aide de l’outil Explorateur de tissu de Service disponible à http://&lt;yourclusteraddress&gt;: 19080/Explorer. Pour plus d’informations, consultez [visualiser votre cluster Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="describe-a-service"></a>Décrire un service

Le manifeste de service définit de manière déclarative le type de service et la version. Il spécifie les métadonnées de service comme type de service, propriétés de l’état de santé, mesures d’équilibrage de charge, des binaires de service et les fichiers de configuration.  Autrement dit, il décrit les packages de code, de configuration et de données qui composent un package de service pour prendre en charge d’un ou plusieurs types de service. Voici un exemple simple de manifeste de service :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Les attributs de **version** sont des chaînes non structurées et n’a pas analysé par le système. Ceux-ci sont utilisés pour la version de chaque composant des mises à niveau.

**ServiceTypes** déclare quels types de service sont prises en charge par **CodePackages** dans ce manifeste. Lorsqu’un service est instancié sur l’un de ces types de services, tous les packages de code déclarés dans ce manifeste sont activés en exécutant leurs points d’entrée. Les processus qui en résulte doivent enregistrer les types de service pris en charge au moment de l’exécution. Notez que les types de service sont déclarés au niveau de manifeste et non au niveau du package de code. Ainsi lorsqu’il y a plusieurs packages de code, ils sont tous activés chaque fois que le système de recherche de l’un des types de service déclarés.

**SetupEntryPoint** est un point d’entrée privilégié qui s’exécute avec les mêmes informations d’identification en tant que Service de Fabric (généralement le compte *LocalSystem* ) avant tout autre point d’entrée. Le fichier exécutable spécifié par le **point d’entrée** est généralement l’hôte de service de durée d’exécution longue. La présence d’un point d’entrée le programme d’installation distinct évite d’avoir à exécuter l’hôte de service avec des privilèges élevés pour de longues périodes. Le fichier exécutable spécifié par le **point d’entrée** est exécuté après que **SetupEntryPoint** a été fermé avec succès. Le processus résultant est surveillé et redémarré (commençant à nouveau **SetupEntryPoint**) s’arrête ou se bloque jamais.

**DataPackage** déclare un dossier appelé par l’attribut **Name** , qui contient des données statiques arbitraires pour être consommés par le processus en cours d’exécution.

**ConfigPackage** déclare un dossier appelé par l’attribut **Name** , qui contient le fichier *Settings.xml* . Ce fichier contient des sections de paramètres de paire clé / valeur définies par l’utilisateur que le processus peut lire au moment de l’exécution. Au cours d’une mise à niveau, si seule la **version** de **ConfigPackage** a été modifié, puis le processus en cours d’exécution n’est pas redémarré. Au lieu de cela, un rappel notifie le processus de configuration ont été modifiées afin qu’elles puissent être rechargées dynamiquement. Voici un exemple de fichier *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Un manifeste de service peut contenir plusieurs code, la configuration et les packages de données. Chacune d'entre elles peut être créée séparément.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>Décrire une application


Le manifeste d’application décrit de façon déclarative le type d’application et la version. Il spécifie les métadonnées de composition de service tels que les noms stables, facteur du nombre/réplication instance, stratégie d’isolation de sécurité /, les contraintes de placement, substitutions de configuration et que les types de services constitutifs. Les domaines de l’équilibrage de charge dans lequel l’application est placée sont également décrites.

Par conséquent, un manifeste d’application décrit les éléments au niveau de l’application et fait référence à un ou plusieurs manifestes de service pour composer un type d’application. Voici un manifeste de l’application exemple simple :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Comme service manifestes, les attributs de **Version** sont des chaînes non structurées et ne sont pas analysés par le système. Ils sont également utilisés pour la version de chaque composant des mises à niveau.

**ServiceManifestImport** contient des références à des manifestes de service qui composent ce type d’application. Les manifestes de service importé déterminent quels types de service sont valides dans ce type d’application.

**DefaultServices** déclare des instances de service qui sont automatiquement créées lorsqu’une application est instanciée par rapport à ce type d’application. Services par défaut sont simplement plus pratique et se comportent comme des services normales à tous égards, après que qu’ils ont été créés. Ils sont mis à niveau en même temps que d’autres services dans l’instance d’application et peuvent être également supprimés.

> [AZURE.NOTE] Un manifeste d’application peut contenir plusieurs importations manifeste de service et les services par défaut. Chaque importation de manifeste de service peut être créée séparément.

Pour savoir comment mettre à jour d’application et les paramètres de service pour des environnements individuels, consultez [Gestion des paramètres de l’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>Une application du package

### <a name="package-layout"></a>Disposition de lot

Le manifeste d’application, manifest(s) de service et d’autres fichiers de package nécessaires doivent être organisés selon une disposition spécifique pour le déploiement dans un cluster de Service Fabric. Les manifestes d’exemple dans cet article devra être organisés dans la structure de répertoire suivante :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Les dossiers sont nommés pour faire correspondre les attributs **nom** de chaque élément correspondant. Par exemple, si le manifeste de service contenue deux packages de code avec les noms **MyCodeA** et **MyCodeB**, puis de deux dossiers portant le même nom contiendrait les binaires nécessaires pour chaque package de code.

### <a name="use-setupentrypoint"></a>Utiliser SetupEntryPoint

Scénarios courants d’utilisation de **SetupEntryPoint** sont lorsque vous avez besoin exécuter un fichier exécutable avant le démarrage du service, ou vous devez effectuer une opération avec des privilèges élevés. Par exemple :

- Configuration et l’initialisation des variables d’environnement qui a besoin de l’exécutable du service. Ce n’est pas limité à des exécutables qu’écrits via les modèles de programmation de Service Fabric. Par exemple, npm.exe doit être configurés pour déployer une application node.js certaines variables d’environnement.

- Paramètre de contrôle d’accès par l’installation de certificats de sécurité.

### <a name="build-a-package-by-using-visual-studio"></a>Création d’un package à l’aide de Visual Studio

Si vous utilisez Visual Studio 2015 pour créer votre application, vous pouvez utiliser la commande Package pour créer automatiquement un package qui correspond à la disposition ci-dessus.

Pour créer un package, cliquez droit sur le projet d’application dans l’Explorateur de solutions et choisissez la commande Package, comme illustré ci-dessous :

![Empaquetage d’une application avec Visual Studio][vs-package-command]

Lorsque l’emballage est terminée, vous trouverez l’emplacement du package dans la fenêtre **sortie** . Notez que l’étape d’emballage produit automatiquement lorsque vous déployez ou que vous déboguez votre application dans Visual Studio.

### <a name="test-the-package"></a>Tester le package

Vous pouvez vérifier la structure du package localement par le biais de PowerShell à l’aide de la commande **Test-ServiceFabricApplicationPackage** . Cette commande va vérifier les problèmes d’analyse de manifeste et vérifier toutes les références. Notez que cette commande vérifie uniquement l’exactitude structurelle des répertoires et des fichiers dans le package. Elle ne vérifiera pas tout le code ou les données du contenu du package au-delà de vérification que tous les fichiers nécessaires sont présents.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Cette erreur indique que le fichier *MySetup.bat* référencé dans le manifeste de service **SetupEntryPoint** est manquant dans le package de code. Après avoir ajouté le fichier manquant, la vérification de l’application passe :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Une fois que l’application est correctement empaquetée et passe la vérification, il est prêt pour le déploiement.

## <a name="next-steps"></a>Étapes suivantes

[Déployer et supprimer des applications][10]

[Gestion des paramètres de l’application pour plusieurs environnements][11]

[RunAs : Exécution d’une application de Service Fabric avec les autorisations de sécurité][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
