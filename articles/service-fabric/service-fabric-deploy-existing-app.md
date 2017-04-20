<properties
   pageTitle="Déployer un fichier exécutable existant pour Azure Fabric de Service | Microsoft Azure"
   description="Procédure pas à pas sur comment empaqueter une application existante en tant qu’invité exécutable, et il peut être déployé sur un cluster Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>Déployer un invité exécutable à un Service de Fabric

Vous pouvez exécuter n’importe quel type d’application, comme les applications natives, node.js et Java dans Azure Fabric de Service. TISSU de service fait référence à ces types d’applications en tant qu’invité exécutables.
Invité exécutables sont traités par le Service de Fabric comme services sans état. Par conséquent, ils sont placés sur les nœuds d’un cluster, en fonction de la disponibilité et les autres mesures. Cet article décrit comment empaqueter et déployer un invité exécutable à un cluster Service Fabric, à l’aide de Visual Studio ou un utilitaire de ligne de commande.

Dans cet article, nous abordons les étapes pour un invité exécutable du package et le déployer sur le Fabric du Service.  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Avantages de l’exécution d’un invité exécutable dans un Service de Fabric

Il existe plusieurs avantages fournis avec un invité exécutable dans un cluster à structure de Service en cours d’exécution :

- Une haute disponibilité. Les applications qui s’exécutent dans le Service Fabric sont rendues disponibles. TISSU de service garantit que les instances d’une application sont en cours d’exécution.
- Surveillance de l’état. Surveillance de santé service Fabric détecte si une application est en cours d’exécution et fournit des informations de diagnostic en cas de panne.   
- Gestion du cycle de vie des applications. En plus de fournir des mises à niveau sans interruption de service, Service de Fabric fournit une restauration automatique vers la version précédente si un événement de mauvaise santé signalé au cours d’une mise à niveau.    
- Densité. Vous pouvez exécuter plusieurs applications dans un cluster, ce qui élimine le besoin pour chaque application s’exécute sur son propre matériel.


## <a name="overview-of-application-and-service-manifest-files"></a>Vue d’ensemble de l’application et les fichiers manifeste de service

Dans le cadre du déploiement d’un invité exécutable, il est utile de comprendre l’emballage Service Fabric et modèle de déploiement comme décrit le [modèle d’application](service-fabric-application-model.md). Le modèle de packaging Service Fabric repose sur deux fichiers XML : les manifestes d’application et des services. La définition de schéma pour les fichiers ApplicationManifest.xml et ServiceManifest.xml est installée avec le Kit de développement logiciel de Service Fabric dans *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

* **Le manifeste d’application** Le manifeste d’application est utilisé pour décrire l’application. Il répertorie les services qui composent il et autres paramètres qui sont utilisés pour définir comment les services d’un ou plusieurs doivent être déployées, telles que le nombre d’instances.

  Dans le Fabric de Service, une application est une unité de déploiement et de mise à niveau. Une application peut être mis à niveau comme une seule unité où sont gérées les défaillances potentielles et restaurations potentielles. TISSU de service garantit que le processus de mise à niveau est réussie, ou, en cas d’échec de la mise à niveau, ne laisse pas de l’application dans un état instable/inconnu.

* **Manifeste de service** Le manifeste de service décrit les composants d’un service. Il inclut des données, telles que le nom et le type de service et son code, la configuration et les données. Le manifeste de service inclut également des paramètres supplémentaires qui peuvent être utilisés pour configurer le service une fois qu’il est déployé.


## <a name="application-package-file-structure"></a>Structure de fichier de package application
Pour déployer une application de Service de Fabric, l’application doit suivre une structure de répertoire prédéfini. L’exemple suivant de cette structure.

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

La ApplicationPackageRoot contient le fichier ApplicationManifest.xml qui définit l’application. Un sous-répertoire pour chaque service inclus dans l’application est utilisé pour contenir tous les artefacts nécessitant le service--le ServiceManifest.xml et, généralement, trois répertoires suivants :

- *Code*. Ce répertoire contient le code de service.
- *Config*. Ce répertoire contient un fichier Settings.xml (et autres fichiers si nécessaire) que le service peut accéder au moment de l’exécution pour récupérer les paramètres de configuration spécifiques.
- *Données*. Il s’agit d’un répertoire supplémentaire pour stocker des données locales supplémentaires que le service peut avoir besoin. Remarque : Les données doivent être utilisées pour ne stocker que des données éphémères. Service de Fabric ne pas copier/répliquer les modifications vers le répertoire de données si le service doit être déplacé--par exemple, lors du basculement.

Remarque : Vous ne devez créer le `config` et `data` répertoires si vous n’en avez pas besoin.

## <a name="packaging-an-existing-executable"></a>Emballage d’un exécutable existant

Lors de la compression d’un fichier exécutable d’invité, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou de [créer le package d’application manuellement](#manually). À l’aide de Visual Studio, la structure de package d’application et les fichiers manifeste sont créés par l’Assistant Nouveau projet pour vous.

>[AZURE.NOTE] Pour empaqueter un exécutable dans un service de Windows existant, le plus simple consiste à utiliser Visual Studio.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>À l’aide de Visual Studio pour empaqueter un exécutable existant

Visual Studio fournit un modèle de service de Fabric du Service pour vous aider à déployer un invité exécutable à un cluster de Service Fabric.

Suivez les étapes suivantes pour effectuer la publication :

1. Choisissez la commande Fichier -> Nouveau projet et créez une Application de Service de Fabric.
2. Choisissez invité exécutable comme le modèle de Service.
3. Cliquez sur Parcourir pour sélectionner le dossier avec votre fichier exécutable et remplissez le reste des paramètres pour créer le service.
    - *Comportement de code de Package* peuvent être définies pour copier tout le contenu de votre dossier au projet Visual Studio, qui est utile si l’exécutable ne change pas. Si vous pensez que le fichier exécutable à modifier que vous souhaitez sélectionner dynamiquement des nouvelles versions, vous pouvez choisir de créer un lien vers le dossier à la place. Notez que vous pouvez utiliser des dossiers liés lors de la création du projet d’application dans Visual Studio. Cette liée à l’emplacement source dans le projet, ce qui vous permet de mettre à jour de l’invité exécutable dans la destination source, ayant les mises à jour font partie du package d’application lors de la génération.
    - *Programme* - cliquez sur le fichier exécutable qui doit être exécuté pour démarrer le service.
    - *Arguments* - spécifiez les arguments doivent être passés à l’exécutable. Il peut être une liste de paramètres avec arguments.
    - *WorkingFolder* - Spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier trois valeurs :
        - `CodeBase`Spécifie que le répertoire de travail va être définie dans le répertoire de code dans le package d’application (`Code` répertoire dans la structure de fichier indiquée précédant).
        - `CodePackage`Spécifie que le répertoire de travail va être définie à la racine du package d’application (`GuestService1Pkg` dans la structure de fichier indiquée précédant).
        - `Work`Spécifie que les fichiers sont placés dans un sous-répertoire appelé travail
4. Donnez un nom à votre service, puis cliquez sur OK.
5. Si votre service a besoin d’un point de terminaison pour la communication, vous pouvez maintenant ajouter le protocole, le Port et le Type dans le fichier ServiceManifest.xml. e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Vous pouvez maintenant utiliser le package et publier des action contre votre cluster local à déboguer la solution dans Visual Studio. Lorsque vous êtes prêt vous pouvez publier l’application sur un cluster distant ou archiver la solution au contrôle de code source.
7. Aller à la fin de cet article pour savoir comment afficher vous service d’exécutable d’invité qui s’exécute dans l’Explorateur de tissu de Service.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>Empaquetage manuel et le déploiement d’un exécutable existant
Le processus d’empaquetage manuel d’un exécutable invité est basé sur les opérations suivantes :

1. Créez la structure de répertoire du package.
2. Ajouter des fichiers de code et de la configuration de l’application.
3. Modifiez le fichier de manifeste du service.
4. Modifier le fichier manifeste d’application.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Créer la structure de répertoires du package
Commencez par créer la structure de répertoire, comme décrit précédemment.

### <a name="add-the-applications-code-and-configuration-files"></a>Ajouter des fichiers de code et de la configuration de l’application
Après avoir créé la structure de répertoire, vous pouvez ajouter le code de l’application et les fichiers de configuration dans les répertoires de code et de configuration. Vous pouvez également créer d’autres répertoires ou sous-répertoires dans les répertoires de code ou de configuration.

TISSU de service effectue un xcopy du contenu du répertoire racine, afin qu’il n’y a pas de structure prédéfinie à utiliser d’autres que la création de deux répertoires supérieur, les codes et les paramètres. (Vous pouvez choisir des noms différents si vous le souhaitez. Plus de détails sont dans la section suivante.)

>[AZURE.NOTE] Assurez-vous d’inclure tous les fichiers/dépendances nécessaires à l’application. TISSU de service copie le contenu du package d’application sur tous les nœuds du cluster où les services de l’application vont être déployés. Le package doit contenir tout le code que l’application doit s’exécuter. Nous ne recommandons pas en supposant que les dépendances sont déjà installées.

### <a name="edit-the-service-manifest-file"></a>Modifiez le fichier de manifeste de service
L’étape suivante consiste à modifier le fichier de manifeste de service afin d’inclure les informations suivantes :

- Le nom du type de service. Il s’agit d’un ID de Service Fabric utilise pour identifier un service.
- La commande à utiliser pour lancer l’application (ExeHost).
- À distance n’importe quel script qui doit être exécuté pour définir/configuration de l’application (SetupEntrypoint).

Voici un exemple d’une `ServiceManifest.xml` fichier :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Revenons sur les différentes parties du fichier dont vous avez besoin pour mettre à jour :

#### <a name="updating-the-servicetypes"></a>Mise à jour de la ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Vous pouvez choisir n’importe quel nom que vous souhaitez pour `ServiceTypeName`. La valeur est utilisée dans le `ApplicationManifest.xml` fichier pour identifier le service.
- Vous devez spécifier `UseImplicitHost="true"`. Cet attribut indique le Service Fabric que le service est basé sur une application autonome, afin que tous les besoins de Service Fabric à faire consiste à lancer en tant que processus et surveiller son état de santé.

#### <a name="updating-the-codepackage"></a>Mise à jour de la CodePackage
L’élément CodePackage indique l’emplacement (et version) de code du service.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Le `Name` élément est utilisé pour spécifier le nom du répertoire dans le package d’application qui contient du code. `CodePackage`dispose également de la `version` attribut. Cela peut être utilisé pour spécifier la version du code--et peut également permettre de mise à niveau du code en utilisant l’infrastructure de gestion du cycle de vie du Service Fabric application.
#### <a name="optional-updating-the-setupentrypoint"></a>Facultatif : Mise à jour de la SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L’élément SetupEntrypoint est utilisée pour spécifier tout fichier exécutable ou de traitement par lots doit être exécuté avant le lancée du code. Il est une étape facultative, afin de ne pas devoir être inclus s’il n’y a pas d’initialisation/la configuration requise. Le SetupEntryPoint est exécuté chaque fois que le service est redémarré.

Il est SetupEntrypoint qu’une seule, afin que les scripts d’installation/de configuration doivent être regroupés dans un fichier de lot si le programme d’installation/configuration de l’application nécessite plusieurs scripts. Le SetupEntrypoint peut exécuter n’importe quel type de fichier : fichiers exécutables, les fichiers de commandes et les applets de commande PowerShell. Pour plus d’informations, consultez [Configurer les SetupEntryPoint](service-fabric-application-runas-security.md).

Dans l’exemple précédent, le SetupEntrypoint s’exécute un fichier de commandes appelé `LaunchConfig.cmd` qui se trouve dans le `scripts` sous-répertoire du répertoire de code (en supposant que la valeur de l’élément WorkingFolder à base de code).

#### <a name="updating-the-entrypoint"></a>Mise à jour du point d’entrée

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

La `Entrypoint` dans le fichier de manifeste de service est utilisé pour spécifier comment le service de lancement. Le `ExeHost` élément spécifie le fichier exécutable (et les arguments) qui doit être utilisé pour lancer le service.

- `Program`Spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
- `Arguments`Spécifie les arguments doivent être passés à l’exécutable. Il peut être une liste de paramètres avec arguments.
- `WorkingFolder`Spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier trois valeurs :
    - `CodeBase`Spécifie que le répertoire de travail va être définie dans le répertoire de code dans le package d’application (`Code` répertoire dans la structure de fichier précédent).
    - `CodePackage`Spécifie que le répertoire de travail va être définie à la racine du package d’application (`GuestService1Pkg` dans la structure de fichier précédent).
  - `Work`Spécifie que les fichiers sont placés dans un sous-répertoire appelé travail

Le WorkingFolder est utile pour définir le répertoire de travail correct afin que les chemins d’accès relatifs peuvent être utilisés par les scripts de l’application ou l’initialisation.

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>Les points de terminaison de mise à jour et l’enregistrement avec le Service de dénomination pour la communication

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Dans l’exemple précédent le `Endpoint` élément spécifie que l’application peut écouter sur les extrémités. Dans cet exemple, l’application Node.js écoute http sur le port 3000.

En outre, vous pouvez demander Fabric du Service pour publier ce point de terminaison pour le Service d’attribution de noms afin que les autres services peuvent découvrir l’adresse de point de terminaison pour ce service. Cela vous permet d’être en mesure de communiquer entre les services qui sont des fichiers exécutables d’invité.
L’adresse de point de terminaison publié est de la forme `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme`et `PathSuffix` sont des attributs facultatifs. `IPAddressOrFQDN`est l’adresse IP ou nom de domaine complet du nœud cet exécutable est placé sur et est calculé automatiquement.

Dans l’exemple suivant une fois que le service est déployé, dans l’Explorateur de Fabric Service vous consultez similaire à un point de terminaison `http://10.1.4.92:3000/myapp/` publiée pour l’instance de service ou s’il s’agit d’un ordinateur local, vous consultez `http://localhost:3000/myapp/`. 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Vous pouvez utiliser ces adresses avec le [proxy inverse](service-fabric-reverseproxy.md) pour communiquer entre les services.

### <a name="edit-the-application-manifest-file"></a>Modifier le fichier manifeste d’application

Une fois que vous avez configuré le `Servicemanifest.xml` fichier, vous devez apporter des modifications à la `ApplicationManifest.xml` fichier pour vous assurer que le type de service approprié et le nom sont utilisés.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

Dans le `ServiceManifestImport` élément, vous pouvez spécifier un ou plusieurs des services que vous souhaitez inclure dans l’application. Services sont référencés avec `ServiceManifestName`, qui spécifie le nom du répertoire où le `ServiceManifest.xml` trouve le fichier.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Configurer la journalisation
Pour les fichiers exécutables invité, il est utile être en mesure de voir les journaux de console pour déterminer si les scripts de configuration et d’application affichent toutes les erreurs.
Redirection de la console peut être configurés dans le `ServiceManifest.xml` de fichiers à l’aide de la `ConsoleRedirection` élément.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`peut être utilisé pour rediriger la sortie de la console (stdout et stderr) vers un répertoire de travail afin qu’elles puissent être utilisées pour vérifier qu’il n’y a pas d’erreurs au cours de l’installation ou l’exécution de l’application dans le cluster Service Fabric.

    * `FileRetentionCount`Détermine le nombre de fichiers est enregistré dans le répertoire de travail. Une valeur de 5 signifie, par exemple, que les fichiers journaux pour les exécutions précédentes de cinq sont stockés dans le répertoire de travail.
    * `FileMaxSizeInKb`Spécifie la taille maximale des fichiers journaux.

Les fichiers journaux sont enregistrés dans un des répertoires de travail du service. Pour déterminer où se trouvent les fichiers, vous devez utiliser l’Explorateur de Fabric de Service pour déterminer le nœud que le service est en cours d’exécution et que le répertoire de travail est utilisé. Ce processus est traité plus loin dans cet article.

## <a name="deployment"></a>Déploiement
La dernière étape consiste à déployer votre application. Le script PowerShell suivant montre comment déployer votre application sur le cluster de développement local et de démarrer un nouveau service de Fabric du Service.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un service de Fabric de Service peut être déployé dans différentes « configurations ». Par exemple, il peut être déployé en tant qu’instances uniques ou multiples, ou il peut être déployé de manière à ce qu’il existe une seule instance du service sur chaque nœud du cluster Service Fabric.

Le `InstanceCount` paramètre de la `New-ServiceFabricService` applet de commande est utilisée pour spécifier le nombre d’instances du service doit être lancé dans le cluster Service Fabric. Vous pouvez définir le `InstanceCount` valeur, selon le type d’application que vous déployez. Les deux scénarios les plus courants sont :

* `InstanceCount = "1"`. Dans ce cas, une seule instance du service est déployée dans le cluster. Le planificateur du Fabric service détermine vers quel noeud va être déployé sur le service.

* `InstanceCount ="-1"`. Dans ce cas, une seule instance du service est déployée sur tous les nœuds du cluster Service Fabric. Le résultat a un (et un seul) instance du service pour chaque nœud du cluster.

Il s’agit d’une configuration utiles pour les applications front-end (par exemple, un point de terminaison reste), car les applications clientes doivent « se connecter » à un des nœuds dans le cluster à utiliser le point de terminaison. Cette configuration peut également être utilisée lorsque, par exemple, tous les nœuds du cluster Service Fabric sont connectés à un équilibreur de charge pour le trafic client peut être distribué sur le service qui s’exécute sur tous les nœuds du cluster.

## <a name="check-your-running-application"></a>Vérifiez votre application en cours d’exécution

Dans l’Explorateur de tissu de Service, identifiez le nœud où le service est en cours d’exécution. Dans cet exemple, il s’exécute sur le nœud 1 :

![Nœud où le service est en cours d’exécution](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si vous accédez au nœud et accédez à l’application, vous voyez les informations essentielles de nœud, y compris son emplacement sur le disque.

![Emplacement sur le disque](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si vous accédez au répertoire en utilisant l’Explorateur de serveurs, vous pouvez trouver le répertoire de travail et le dossier du journal du service comme le montre l’image suivante.

![Emplacement du journal](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment compresser un fichier exécutable d’invité et le déployer sur Fabric du Service. Comme une étape suivante, vous pouvez extraire contenu supplémentaire pour cette rubrique.

- [Exemple pour l’empaquetage et le déploiement d’un invité exécutable sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), y compris un lien vers la version préliminaire de l’outil d’empaquetage
- [Déployer plusieurs fichiers exécutables d’invité](service-fabric-deploy-multiple-apps.md)
- [Créer votre première application Fabric du Service à l’aide de Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
