<properties
   pageTitle="TISSU et déploiement de conteneurs de service | Microsoft Azure"
   description="TISSU de service et l’utilisation des conteneurs pour déployer des applications de microservice. Cet article décrit les fonctionnalités qui fournit de tissu de Service pour les conteneurs et comment déployer une image de conteneur Windows dans un cluster"
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
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Aperçu : Déployer un conteneur Windows Service fabric

> [AZURE.SELECTOR]
- [Déployer Windows conteneur](service-fabric-deploy-container.md)
- [Déployer le conteneur de Docker](service-fabric-deploy-container-linux.md)

Cet article vous guide dans le processus de création des services dans les conteneurs de Windows. 

>[AZURE.NOTE] Cette fonctionnalité est en mode Aperçu pour Linux et pas actuellement disponibles pour une utilisation avec Windows Server 2016. Ce sera disponible dans l’aperçu pour Windows Server 2016 dans la prochaine version de Service Fabric et pris en charge dans la version suivante après cela.

Service de Fabric a plusieurs fonctionnalités de conteneur qui vous aident à créer des applications qui sont composées de microservices qui sont en conteneur. Il s’agit des services. 

Les fonctionnalités incluent ;

- L’activation et le déploiement d’images de conteneur
- Gouvernance de ressources
- Authentification du référentiel
- Port du conteneur sur le port de l’hôte
- Découverte de conteneur à l’autre et de la communication
- Possibilité de configurer et de définir des variables d’environnement

Examinons chacune des fonctions à son tour lors de la compression d’un service en conteneur à inclure dans votre application.

## <a name="packaging-a-windows-container"></a>Emballage d’un conteneur de Windows

Lors de la compression d’un conteneur, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou de [créer le package d’application manuellement](#manually). À l’aide de Visual Studio, la structure de package d’application et les fichiers manifeste sont créés par l’Assistant Nouveau projet pour vous (disponible dans la prochaine version).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>À l’aide de Visual Studio à une image existante de conteneur du package

>[AZURE.NOTE] Dans une version future des outils du Kit de développement logiciel de Visual Studio, vous ne pourrez pas ajouter un conteneur à une application de la même façon que vous pouvez ajouter un invité exécutable aujourd'hui. Consultez la rubrique de [déployer un invité exécutable à un Service de Fabric](service-fabric-deploy-existing-app.md) . Actuellement, vous devez faire d’Empaquetage manuel comme décrit ci-dessous.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Empaquetage manuel et le déploiement d’un conteneur
Le processus d’empaquetage manuel d’un service en conteneur est basé sur les opérations suivantes :

1. Publié les conteneurs de votre référentiel.
2. Créez la structure de répertoire du package.
3. Modifiez le fichier de manifeste du service.
4. Modifier le fichier manifeste d’application.

## <a name="container-image-deployment-and-activation"></a>Déploiement d’images de conteneur et de l’activation.
Dans le [modèle d’application](service-fabric-application-model.md)du Service Fabric, un conteneur représente un hôte d’application dans lequel plusieurs réplicas de service sont placés. Pour déployer et activer un conteneur, placez le nom de l’image de conteneur dans une `ContainerHost` élément dans le manifeste du service.

Dans le manifeste de service ajouter une `ContainerHost` pour l’entrée de point et de définir la `ImageName` le nom de référentiel de conteneur et de l’image. Le manifeste partiel suivant montre un exemple de déploiement le conteneur appelé *myimage:v1* à partir d’un référentiel appelé *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Vous pouvez fournir des commandes d’entrée à l’image du conteneur en spécifiant l’option `Commands` élément avec une virgule délimités ensemble de commandes à exécuter à l’intérieur du conteneur. 

## <a name="resource-governance"></a>Gouvernance de ressources
La gouvernance de ressources est une fonction du conteneur et restreint les ressources utilisables par le conteneur sur l’hôte. Le `ResourceGovernancePolicy`, spécifié dans le manifeste d’application, offre la possibilité de déclarer des limites de ressources pour un package de code de service. Définir des limites de ressources

- Mémoire
- MemorySwap
- CpuShares (poids relatif du processeur)
- MemoryReservationInMB  
- BlkioWeight (poids relatif de BlockIO). 

>[AZURE.NOTE] Dans une version ultérieure, le support pour la spécification des limites d’e/s de bloc spécifique comme Ops ES/s, en lecture/écriture/s et autres ne seront possible.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Authentification du référentiel
Pour télécharger un conteneur, il se peut que vous deviez fournir des informations d’identification de connexion au référentiel du conteneur. Les informations d’identification de connexion, spécifiées dans le manifeste *d’application* permettent de spécifier les informations de connexion ou d’une clé SSH pour le téléchargement de l’image du conteneur à partir du référentiel de l’image.  L’exemple suivant montre un compte nommé *TestUser* et le mot de passe en texte clair. Ce n’est **pas** recommandée.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Le mot de passe peut et doit être chiffrée à l’aide d’un certificat déployé sur l’ordinateur.

L’exemple suivant montre un compte nommé *TestUser* et le mot de passe crypté à l’aide d’un certificat appelé *MonCert*. Vous pouvez utiliser la `Invoke-ServiceFabricEncryptText` commande Powershell pour créer le texte de chiffrement secrète pour le mot de passe. Consultez cet article [Gestion des secrets dans des applications de Service Fabric](service-fabric-application-secret-management.md) pour plus de détails. La clé privée du certificat pour décrypter le mot de passe doit être déployée sur la machine locale dans une méthode out-of-band (dans Azure c’est via ARM). Puis, lorsque le Service Fabric déploie le package de service sur l’ordinateur, il est en mesure de décrypter le secret et avec le nom de compte, s’authentifier avec le référentiel de conteneur à l’aide de ces informations d’identification.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Port du conteneur sur le port de l’hôte
Vous pouvez configurer un port de l’hôte utilisé pour communiquer avec le conteneur en spécifiant un `PortBinding` dans le manifeste d’application. La liaison de port mappe le port que le service est à l’écoute sur l’intérieur du conteneur à un port sur l’hôte.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Découverte de conteneur à l’autre et de la communication
À l’aide de la `PortBinding` stratégie vous pouvez mapper un port de conteneur à une `Endpoint` dans le manifeste de service, comme illustré dans l’exemple suivant. Le point de terminaison `Endpoint1` pouvez spécifier un port fixe, par exemple le port 80 ou aucun port de spécifier, dans ce cas, un port aléatoire à partir de la plage de ports des clusters application est choisi pour vous.

Pour les récipients d’invité, spécifiant une `Endpoint` comme cela dans le manifeste de service permet de Fabric du Service publier automatiquement ce point de terminaison pour le service d’attribution de noms afin que les autres services s’exécutant dans le cluster peuvent découvrir ce conteneur à l’aide de requêtes repos service résoudre. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

En vous inscrivant avec le service d’attribution de noms, vous pouvez faire aisément communication de conteneur à l’autre dans le code au sein de votre conteneur à l’aide du [proxy inverse](service-fabric-reverseproxy.md). Il vous suffit de faire est de fournir le port d’écoute http proxy inverse et le nom des services que vous souhaitez communiquer avec en définissant les variables d’environnement. Sur la procédure à suivre, consultez la section suivante.  

## <a name="configure-and-set-environment-variables"></a>Configurer et définir des variables d’environnement
Variables d’environnement peuvent être spécifié ennemi chaque module de code dans le service de manifeste pour les deux services déployés dans des conteneurs ou en tant qu’exécutables processus/invité. Ces valeurs de variables d’environnement peuvent être substituées spécifiquement dans le manifeste d’application ou spécifiées au cours du déploiement en tant que paramètres de l’application.

Le manifeste de service extrait de code XML suivant montre comment spécifier des variables d’environnement d’un package de code. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Ces variables d’environnement peuvent être remplacées au niveau de manifeste de l’application :

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

Dans l’exemple ci-dessus, nous avons spécifié une valeur explicite pour le `HttpGateway` variable d’environnement (19000) lors de la valeur de `BackendServiceName` paramètre est défini par le `[BackendSvc]` paramètre d’application. Cela vous permet de spécifier la valeur de `BackendServiceName`la valeur au moment du déploiement de l’application et qui n’a pas de valeur fixe dans le manifeste. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Exemples d’application et le manifeste de service complets
Voici un exemple de manifeste d’application qui illustre les fonctionnalités de conteneur.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


Voici un exemple de manifeste de service (spécifié dans le manifeste d’application précédent) qui affiche les fonctionnalités de conteneur

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
