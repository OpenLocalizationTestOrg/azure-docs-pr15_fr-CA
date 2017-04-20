<properties
    pageTitle="Quel est un modèle de Service Cloud et un package | Microsoft Azure"
    description="Décrit le modèle de service cloud (.csdef, .cscfg) et le package (.cspkg) dans Azure"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Quel est le modèle de Service Cloud et comment il package ?
Un service en nuage est créé à partir de trois composants, la définition de service _(.csdef)_, la configuration de service _(.cscfg)_et un package de service _(.cspkg)_. Le **ServiceDefinition.csdef** et le **ServiceConfig.cscfg** de fichiers basé sur XML et décrivent la structure du service cloud et comment elle est configurée ; collectivement appelés le modèle. Le **ServicePackage.cspkg** est un fichier zip qui est généré à partir de la **ServiceDefinition.csdef** et, entre autres, contient toutes les dépendances basées sur un fichier binaire requis. Azure crée un service en nuage à partir de la **ServicePackage.cspkg** et l' **ServiceConfig.cscfg**.

Une fois que le service en nuage s’exécute dans Azure, vous pouvez le reconfigurer le fichier **ServiceConfig.cscfg** , mais vous ne pouvez pas modifier la définition.

## <a name="what-would-you-like-to-know-more-about"></a>Que voulez-vous en savoir plus ?

* Je souhaite en savoir plus sur les fichiers [ServiceDefinition.csdef](#csdef) et [ServiceConfig.cscfg](#cscfg) .
* Je sais déjà à ce sujet, me donner [quelques exemples](#next-steps) de ce que je peux configurer.
* Je veux créer le [ServicePackage.cspkg](#cspkg).
* J’utilise Visual Studio et je souhaite...
    * [Créer un nouveau service de cloud][vs_create]
    * [Reconfigurer un service cloud existant][vs_reconfigure]
    * [Déployer un projet de Service Cloud][vs_deploy]
    * [Bureau à distance dans une instance de service cloud][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Le fichier **ServiceDefinition.csdef** spécifie les paramètres qui sont utilisés par Azure pour configurer un service en nuage. Le [Schéma de définition du Service Azure (.csdef fichier)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fournit le format autorisé pour un fichier de définition de service. L’exemple suivant affiche les paramètres qui peuvent être définies pour les rôles Web et Worker :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Vous pouvez utiliser les crochets [] [schéma de définition du Service] pour une meilleure compréhension du schéma XML utilisé ici, toutefois, voici une explication rapide de certains éléments :

**Sites**  
Contient les définitions pour les applications web ou des sites Web qui sont hébergées dans IIS7.

**InputEndpoints**  
Contient les définitions de points de terminaison qui sont utilisées pour contacter le service de cloud.

**InternalEndpoints**  
Contient les définitions de points de terminaison qui sont utilisés par les instances de rôles pour communiquer entre eux.

**ConfigurationSettings**  
Contient les définitions de paramètre pour les fonctions d’un rôle spécifique.

**Certificats**  
Contient les définitions pour les certificats qui sont nécessaires à un rôle. L’exemple de code précédent illustre un certificat qui est utilisé pour la configuration d’Azure Connect.

**LocalResources**  
Contient les définitions des ressources de stockage local. Une ressource de stockage local est un répertoire réservé sur le système de fichiers de l’ordinateur virtuel dans lequel une instance d’un rôle est en cours d’exécution.

**Importations**  
Contient les définitions des modules importés. L’exemple de code précédent illustre les modules pour connexion Bureau à distance et Azure Connect.

**Démarrage**  
Contient les tâches qui sont exécutées lorsque le rôle démarre. Les tâches sont définies dans un fichier exécutable ou le .cmd.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
La configuration des paramètres de votre service cloud est déterminée par les valeurs dans le fichier **ServiceConfiguration.cscfg** . Vous spécifiez le nombre d’instances que vous souhaitez déployer pour chaque rôle dans ce fichier. Les valeurs pour les paramètres de configuration que vous avez définie dans le fichier de définition de service sont ajoutées au fichier de configuration du service. Les empreintes de tous les certificats de gestion qui sont associés avec le service en nuage sont également ajoutées au fichier. Le [Schéma de Configuration du Service Azure (.cscfg fichier)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fournit le format autorisé pour un fichier de configuration de service.

Le fichier de configuration de service n’est pas fourni avec l’application, mais est téléchargé vers Azure sous la forme d’un fichier distinct et est utilisé pour configurer le service en nuage. Vous pouvez télécharger un nouveau fichier de configuration de service sans redéployer votre service cloud. Les valeurs de configuration pour le service en nuage peuvent être modifiés pendant que le service en nuage est en cours d’exécution. L’exemple suivant affiche les paramètres de configuration qui peuvent être définies pour les rôles Web et Worker :

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Vous pouvez faire référence au [Schéma de Configuration de Service](https://msdn.microsoft.com/library/azure/ee758710.aspx) permettant de mieux comprendre le schéma XML utilisé ici, toutefois, voici une explication rapide des éléments :

**Instances**  
Configure le nombre d’instances pour le rôle en cours d’exécution. Pour empêcher votre service cloud potentiellement devenir indisponibles pendant les mises à niveau, il est conseillé de que vous déployez plus d’une instance de vos rôles web orienté. Ce faisant, vous remplissez les instructions de l' [Azure Compute niveau contrat Service (SLA)](http://azure.microsoft.com/support/legal/sla/), qui garantit la connectivité externe de 99,95 % pour des rôles avec accès via Internet lorsque deux ou plusieurs instances de rôle sont déployés pour un service.

**ConfigurationSettings**  
Configure les paramètres pour les instances en cours d’exécution pour un rôle. Le nom de la `<Setting>` éléments doivent correspondre les définitions dans le fichier de définition de service.

**Certificats**  
Configure les certificats qui sont utilisés par le service. L’exemple de code précédent illustre comment définir le certificat pour le module d’accès à distance. La valeur de l’attribut *d’empreinte* doit indiquer l’empreinte numérique du certificat à utiliser.

<p/>

 >[AZURE.NOTE] L’empreinte numérique du certificat peut être ajouté au fichier de configuration à l’aide d’un éditeur de texte, ou la valeur peut être ajoutée dans l’onglet **certificats** de la page de **Propriétés** du rôle dans Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Définition des ports pour les instances de rôle
Azure permet de point d’entrée qu’une seule à un rôle web. Cela signifie que tout le trafic s’effectue via une adresse IP. Vous pouvez configurer vos sites Web pour partager un port en configurant l’en-tête d’hôte pour diriger la demande vers l’emplacement correct. Vous pouvez également configurer vos applications pour écouter des ports connus sur l’adresse IP.

L’exemple suivant illustre la configuration d’un rôle web avec une application web et de site Web. Le site Web est configuré en tant que l’emplacement de saisie par défaut sur le port 80, et les applications web sont configurées pour recevoir des demandes d’un en-tête d’hôte différent qui est appelé « mail.mysite.cloudapp.net ».

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Modification de la configuration d’un rôle
Lorsque celle-ci s’exécute dans Azure, sans déconnecter du service, vous pouvez mettre à jour la configuration de votre service cloud. Pour modifier les informations de configuration, vous pouvez soit télécharger un nouveau fichier de configuration, ou modifier le fichier de configuration en place et l’appliquer à votre service en cours d’exécution. Les modifications suivantes peuvent être apportées à la configuration d’un service :

- **Modification des valeurs des paramètres de configuration**  
Lorsque les modifications de paramètre de configuration, une instance de rôle la possibilité appliquer la modification lorsque l’instance est en ligne ou pour recycler correctement de l’instance et appliquer les modifications, tandis que l’instance est en mode hors connexion.

- **Modification de la topologie du service des instances de rôle**  
Modifications de la topologie n’affectent pas les instances en cours d’exécution, sauf lorsqu’une instance est en cours de suppression. Toutes les autres instances généralement n’avez pas besoin d’être recyclé ; Toutefois, vous pouvez choisir de recycler des instances de rôle en réponse à une modification de la topologie.

- **Modification de l’empreinte numérique du certificat**  
Vous pouvez uniquement mettre à jour un certificat lorsqu’une instance de rôle est en mode hors connexion. Si un certificat est ajouté, supprimé ou modifié lorsqu’une instance de rôle est en ligne, Azure normalement déconnecte l’instance à mettre à jour le certificat et le mettre en ligne une fois la modification terminée.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Gestion des modifications de configuration avec les événements d’exécution de Service
La [Bibliothèque d’exécution Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) inclut l’espace de noms [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , qui fournit des classes pour interagir avec l’environnement Azure à partir du code s’exécutant dans une instance d’un rôle. La classe [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) définit les événements qui sont déclenchés avant et après une modification de configuration suivants :

- **Événements de [modification](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)**  
Cela se produit avant la modification de configuration est appliquée à une instance spécifique d’un rôle de vous donner une chance d’arrêter les instances de rôle si nécessaire.
- **Événement [Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Se produit après que la modification de configuration est appliquée à une instance spécifique d’un rôle.

> [AZURE.NOTE] Dans la mesure où les modifications de certificat toujours les instances d’un rôle en mode hors connexion, ils ne déclenchent pas les événements RoleEnvironment.Changing ou RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Pour déployer une application comme un service en nuage dans Azure, vous devez d’abord l’empaqueter l’application dans le format approprié. Vous pouvez utiliser l’outil de ligne de commande **CSPack** (installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/)) pour créer le fichier de package au lieu de Visual Studio.

**CSPack** utilise le contenu du fichier de définition de service et de fichier de configuration de service pour définir le contenu du package. **CSPack** génère un fichier de package d’application (.cspkg) que vous pouvez télécharger vers Azure via le [portail Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Par défaut, le package se nomme `[ServiceDefinitionFileName].cspkg`, mais vous pouvez spécifier un nom différent à l’aide de la `/out` option de **CSPack**.

**CSPack** se trouve généralement à  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (sous windows) est disponible en exécutant le raccourci **d’invite de commandes Microsoft Azure** qui est installé avec le Kit de développement logiciel.  
>  
>Exécutez le programme CSPack.exe par lui-même pour consulter la documentation sur tous les commutateurs et les commandes.

<p />

>[AZURE.TIP]
Exécuter localement de votre service cloud dans l' **Émulateur de calcul Azure Microsoft**, utilisez l’option **/copyonly** que cette option copie les fichiers binaires de l’application d’une mise en page du répertoire à partir duquel ils peuvent être exécutés dans l’émulateur de calcul.

### <a name="example-command-to-package-a-cloud-service"></a>Exemple de commande pour empaqueter un service cloud
L’exemple suivant crée un package d’application qui contient les informations pour un rôle web. La commande spécifie le fichier de définition de service à utiliser, le répertoire où les fichiers binaires peuvent être trouvés, et le nom du fichier du lot.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Si l’application contienne un rôle web et un rôle worker, la commande suivante est utilisée :

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Où les variables sont définies comme suit :

| Variable                  | Valeur |
| ------------------------- | ----- |
| \[Nom du répertoire\]         | Le sous-répertoire sous le répertoire racine du projet qui contient le fichier .csdef du projet Azure.|
| \[ServiceDefinition\]     | Le nom du fichier de définition du service. Par défaut, ce fichier est nommé ServiceDefinition.csdef.  |
| \[OutputFileName\]        | Le nom du fichier de package généré. En général, ceci est définie sur le nom de l’application. Si aucun nom de fichier n’est spécifié, le package d’application est créé en tant que \[ApplicationName\].cspkg.|
| \[RoleName\]              | Le nom du rôle, tel que défini dans le fichier de définition de service.|
| \[[RoleBinariesDirectory] | L’emplacement des fichiers binaires pour le rôle.|
| \[VirtualPath\]           | Les répertoires physiques pour chaque chemin d’accès virtuel défini dans la section Sites de la définition de service.|
| \[PhysicalPath\]          | Les répertoires physiques du contenu pour chaque chemin d’accès virtuel défini dans le nœud de site de la définition de service.|
| \[RoleAssemblyName\]      | Le nom du fichier binaire pour le rôle.| 


## <a name="next-steps"></a>Étapes suivantes

Je crée un package de service cloud et je souhaite...

* [Configurer le Bureau à distance pour une instance de service cloud][remotedesktop]
* [Déployer un projet de Service Cloud][deploy]

J’utilise Visual Studio et je souhaite...

* [Créer un nouveau service de cloud][vs_create]
* [Reconfigurer un service cloud existant][vs_reconfigure]
* [Déployer un projet de Service Cloud][vs_deploy]
* [Configurer le Bureau à distance pour une instance de service cloud][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
