<properties
   pageTitle="Déployer une application Node.js qui utilise MongoDB | Microsoft Azure"
   description="Procédure pas à pas comment plusieurs invité exécutables du package à déployer sur un cluster Service de Azure Fabric"
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
   ms.workload="NA"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>Déployer plusieurs fichiers exécutables d’invité

Cet article explique comment empaqueter et déployer de plusieurs fichiers exécutables d’invité sur Azure Fabric de Service. Pour la création et le déploiement d’un package de Service Fabric unique, lisez Comment [déployer un invité exécutable à un Service de Fabric](service-fabric-deploy-existing-app.md).

Lors de cette procédure pas à pas montre comment déployer une application avec un frontal Node.js qui utilise MongoDB comme magasin de données, vous pouvez appliquer les étapes à n’importe quelle application qui a des dépendances sur une autre application.   

Vous pouvez utiliser Visual Studio pour générer le package d’application qui contient plusieurs fichiers exécutables d’invité. Consultez [L’aide de Visual Studio pour empaqueter une application existante](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable). Après avoir ajouté le premier exécutable invité, cliquez avec le bouton droit sur le projet d’application et sélectionnez l' **Ajouter -> service du nouveau Service de Fabric** pour ajouter le deuxième projet exécutable invité à la solution. Remarque : Si vous choisissez de lier la source dans le projet Visual Studio, création d’une solution Visual Studio, vérifiera que le package d’application est à jour avec les modifications dans la source. 

## <a name="manually-package-the-multiple-guest-executable-application"></a>Créer un package manuellement l’application exécutable invité plusieurs
Vous pouvez également manuellement l’empaqueter l’invité exécutable. Pour l’empaquetage manuel, cet article utilise l’outil de conditionnement de tissu de Service, qui est disponible à l’adresse [http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool).

### <a name="packaging-the-nodejs-application"></a>Empaquetage de l’application Node.js
Cet article suppose que Node.js n’est pas installé sur les nœuds du cluster Service Fabric. Par conséquent, vous devez ajouter Node.exe à la racine de votre application de nœud avant l’empaquetage. La structure de répertoires de l’application Node.js (à l’aide de la structure de web Express et Jade modèle) devrait être similaire à celui ci-dessous :

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

Comme une étape suivante, vous créez un package d’application pour l’application Node.js. Le code ci-dessous crée un package d’application Fabric du Service qui contient l’application Node.js.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Vous trouverez ci-dessous une description des paramètres qui sont utilisés :

- **/source** pointe vers le répertoire de l’application qui doit être empaqueté.
- **/target** définit le répertoire dans lequel le package doit être créé. Ce répertoire doit être différent du répertoire source.
- **AppName** définit le nom de l’application de l’application existante. Il est important de comprendre que ceci se traduit par le nom du service dans le manifeste et pas le nom de l’application de Service Fabric.
- **/exe** définit l’exécutable de lancement, dans ce cas supposée Fabric du Service `node.exe`.
- **/Ma** définit l’argument utilisé pour lancer l’exécutable. Node.js n’est pas installé, Service Fabric doit lancer le serveur web Node.js par l’exécution de `node.exe bin/www`.  `/ma:'bin/www'`Indique à l’outil d’emballage à utiliser `bin/ma` comme argument node.exe.
- **/AppType** définit le nom de type application Fabric du Service.

Si vous accédez au répertoire qui a été spécifié dans le paramètre de l’option /target, vous pouvez voir que l’outil a créé un package de Service Fabric en état de fonctionnement comme illustré ci-dessous :

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Le ServiceManifest.xml généré a désormais une section qui décrit la façon dont le serveur web Node.js doit être lancé, comme illustré dans l’extrait de code ci-dessous :

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
Dans cet exemple, le serveur web Node.js écoute le port 3000, vous devez mettre à jour les informations de point de terminaison dans le fichier ServiceManifest.xml comme indiqué ci-dessous.   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Empaquetage de l’application de MongoDB
Maintenant que l’application Node.js inclus dans le package, vous pouvez continuer et MongoDB du package. Comme mentionné précédemment, les étapes qui vous passez maintenant ne sont pas spécifiques à Node.js et MongoDB. En fait, ils s’appliquent à toutes les applications qui sont destinées à être réunis en une seule application Fabric du Service.  

Pour créer un package MongoDB, vous souhaitez vous assurer que vous créez un package Mongod.exe et Mongo.exe. Les deux fichiers binaires se trouvent dans le `bin` de votre répertoire d’installation de MongoDB. La structure du répertoire est similaire à celui présenté ci-dessous.

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
TISSU de service doit démarrer MongoDB avec une commande semblable à celui ci-dessous, vous devez donc utiliser le `/ma` paramètre lors de la compression de MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] Les données ne sont pas conservées en cas de défaillance d’un nœud si vous placez le répertoire de données MongoDB sur le répertoire local du nœud. Vous devez utiliser le stockage durable ou implémenter un réplica MongoDB afin d’éviter la perte de données.  

Dans PowerShell ou l’invite de commande, nous exécuter l’outil d’empaquetage avec les paramètres suivants :

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Pour ajouter des MongoDB à votre package d’application Fabric du Service, vous devez afin de vous assurer que les points de paramètre /target dans le même répertoire que celui qui contient déjà l’application se manifeste avec l’application Node.js. Vous devez également vous assurer que vous utilisez le même nom de ApplicationType.

Nous allons parcourir le répertoire et d’examiner ce qui a créé l’outil.

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
Comme vous pouvez le voir, l’outil a ajouté un nouveau dossier, MongoDB, au répertoire qui contient les fichiers binaires de MongoDB. Si vous ouvrez le `ApplicationManifest.xml` le fichier, vous pouvez voir que le lot contient à présent l’application de Node.js et MongoDB. Le code ci-dessous présente le contenu du manifeste d’application.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>Publication de l’application
La dernière étape consiste à publier l’application sur le cluster de tissu de Service local en utilisant les scripts PowerShell ci-dessous :

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Une fois que l’application est publiée avec succès vers le cluster local, vous pouvez accéder à l’application de Node.js sur le port que nous avons entré dans le manifeste de service de l’application Node.js – par exemple http://localhost:3000.

Dans ce didacticiel, vous avez vu comment empaqueter facilement des deux applications existantes sous la forme d’une application de Service Fabric. Vous avez également appris à déployer sur le Fabric du Service afin qu’il peut bénéficier de certaines des fonctionnalités de Service Fabric, telles que la haute disponibilité et l’intégration de système de santé.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le déploiement des conteneurs avec [vue d’ensemble du Service de Fabric et des conteneurs](service-fabric-containers-overview.md)
