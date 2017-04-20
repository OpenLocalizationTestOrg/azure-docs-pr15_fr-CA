<properties
   pageTitle="Spécification de points de terminaison de service Service Fabric | Microsoft Azure"
   description="Comment décrire les ressources du point de terminaison dans un manifeste de service, y compris comment configurer des points de terminaison HTTPS"
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

# <a name="specify-resources-in-a-service-manifest"></a>Spécifier des ressources dans un manifeste de service

## <a name="overview"></a>Vue d’ensemble

Le manifeste de service autorise les ressources utilisées par le service d’être déclaré ou modifié sans modifier le code compilé. Azure Fabric de Service prend en charge la configuration des ressources du point de terminaison pour le service. L’accès aux ressources qui sont spécifiés dans le manifeste de service peut être contrôlé par la SecurityGroup dans le manifeste d’application. La déclaration de ressources permet à ces ressources à être modifié au moment du déploiement, c'est-à-dire que le service n’a pas besoin d’introduire un nouveau mécanisme de configuration. La définition de schéma pour le fichier ServiceManifest.xml est installée avec le Kit de développement logiciel de Service de Fabric et des outils à *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Points de terminaison

Lorsqu’une ressource de point de terminaison est définie dans le manifeste de service, Service de Fabric affecte des ports à partir de la plage de ports réservés d’application lorsqu’un port n’est pas spécifié explicitement. Par exemple, observez le point de terminaison *ServiceEndpoint1* spécifié dans l’extrait de manifeste fourni après ce point. En outre, les services peuvent demander également un port spécifique dans une ressource. Les réplicas de service en cours d’exécution sur différents nœuds de cluster peuvent être affectés des numéros de port différents, tandis que les répliques d’un service en cours d’exécution sur le même nœud partagent le port. Les réplicas de service peuvent ensuite utiliser ces ports en fonction des besoins pour la réplication et à l’écoute des demandes du client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Pour plus d’informations sur le référencement des points de terminaison à partir des paramètres de package de configuration (settings.xml) du fichier de lecture, voir la rubrique [configuration dynamique des Services fiables](service-fabric-reliable-services-configuration.md) .

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Exemple : spécification d’un point de terminaison HTTP pour votre service

Le manifeste de service suivante définit une ressource de point de terminaison TCP et deux ressources de point de terminaison HTTP dans le &lt;ressources&gt; élément.

Points de terminaison HTTP sont automatiquement QU'ACL serait par Fabric du Service.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Exemple : spécification d’un point de terminaison HTTPS pour votre service

Le protocole HTTPS assure l’authentification du serveur et est également utilisé pour crypter les communications client-serveur. Pour activer HTTPS sur votre service de Fabric de Service, spécifiez le protocole dans la *ressources -> points de terminaison -> point de terminaison* section du manifeste de service, comme indiqué plus haut pour le point de terminaison *ServiceEndpoint3*.

>[AZURE.NOTE] Impossible de modifier le protocole d’un service au cours de la mise à niveau de l’application sans elle constituant une rupture de modifier.


Voici un exemple ApplicationManifest dont vous avez besoin de définir pour le protocole HTTPS. L’empreinte numérique du certificat doit être fourni. Le EndpointRef est une référence à EndpointResource dans ServiceManifest, pour lequel vous définissez le protocole HTTPS. Vous pouvez ajouter plus d’une EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
