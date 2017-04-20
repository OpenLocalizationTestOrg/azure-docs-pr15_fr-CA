<properties
   pageTitle="À l’aide de Elasticsearch en tant que Service Fabric application trace magasin | Microsoft Azure"
   description="Décrit comment les applications Fabric de Service peuvent utiliser Elasticsearch et Kibana pour stocker, index et d’effectuer des recherches dans les traces de l’application (journaux)"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/09/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="use-elasticsearch-as-a-service-fabric-application-trace-store"></a>Stocker des Elasticsearch d’utilisation comme une trace de l’application Fabric du Service
## <a name="introduction"></a>Introduction
Cet article décrit comment les applications [Azure Fabric de Service](https://azure.microsoft.com/documentation/services/service-fabric/) permet de **Elasticsearch** et **Kibana** pour le stockage d’application trace, l’indexation et la recherche. [Elasticsearch](https://www.elastic.co/guide/index.html) est un open source, distribué et évolutif en temps réel recherche analytique moteur et qui est bien adapté à cette tâche. Il peut être installé sur Windows et Linux machines virtuelles Microsoft Azure. Elasticsearch peut traiter efficacement des traces *structuré* produites à l’aide de technologies telles que **Event Tracing for Windows (ETW)**.

ETW est utilisé par le runtime de Fabric du Service d’informations de diagnostic source (traces). C’est la méthode recommandée pour les applications de Service Fabric à leurs informations de diagnostic, de source trop. En utilisant le même mécanisme permet de corrélation entre les traces fourni au moment de l’exécution et fourni par l’application et facilite la résolution des problèmes. Modèles de projet de service Fabric dans Visual Studio incluent une API de journalisation (basée sur la classe .NET **EventSource** ) qui émet des suivis ETW par défaut. Pour une vue d’ensemble du traçage d’application Fabric du Service à l’aide d’ETW, consultez [surveillance et diagnostic des services dans une configuration de développement local machine](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Pour les suivis s’afficheront dans Elasticsearch, ils doivent être capturées dans les nœuds de cluster Service Fabric en temps réel (pendant l’exécution de l’application) et envoyé à un point de terminaison Elasticsearch. Il existe deux options principales pour la capture de trace :

+ **Trace capture en cours**  
L’application, ou plus précisément, les processus de service, sont chargé d’envoyer les données de diagnostic pour la banque de trace (Elasticsearch).

+ **Out-of-process trace capture**  
Un agent séparé est capture des traces à partir du processus de service ou des processus et les envoyer à la banque de la trace.

Ci-dessous, nous décrivons comment configurer Elasticsearch sur Azure, discutez des avantages et inconvénients pour les deux options de saisie et expliquent comment configurer un service de Fabric de Service pour envoyer des données à Elasticsearch.


## <a name="set-up-elasticsearch-on-azure"></a>Configurer Elasticsearch sur Azure
La façon la plus simple pour configurer le service Elasticsearch sur Azure est par le biais de [**modèles d’Azure le Gestionnaire de ressources**](../azure-resource-manager/resource-group-overview.md). Un complète [modèle de gestionnaire de ressources du démarrage rapide Azure pour Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) est disponible à partir du référentiel de modèles Azure Quickstart. Ce modèle utilise des comptes de stockage séparé pour les unités de l’échelle (groupes de nœuds). Il peut également en service client distinct et des nœuds de serveur avec différentes configurations et différents numéros de disques de données associés.

Ici, nous utilisons un autre modèle, appelé **ES-multiposte** à partir du [référentiel d’outils de diagnostic Azure](https://github.com/Azure/azure-diagnostics-tools). Ce modèle est plus facile à utiliser, et crée un cluster Elasticsearch protégé par l’authentification de base HTTP. Avant de poursuivre, téléchargez le référentiel à partir de GitHub sur votre ordinateur (par clonage du référentiel ou téléchargement d’un fichier zip). Le modèle ES-multiposte se trouve dans le dossier portant le même nom.

### <a name="prepare-a-machine-to-run-elasticsearch-installation-scripts"></a>Préparer un ordinateur pour exécuter les scripts d’installation Elasticsearch
La façon la plus simple d’utiliser le modèle ES-multiposte est un script PowerShell d’Azure fourni appelé `CreateElasticSearchCluster`. Pour utiliser ce script, vous devez installer les modules PowerShell et un outil appelé **openssl**. Ce dernier est nécessaire pour la création d’un code SSH qui peut être utilisé pour administrer à distance de votre cluster de Elasticsearch.

`CreateElasticSearchCluster`script est conçu pour faciliter l’utilisation avec le modèle ES-multiposte à partir d’un ordinateur Windows. Il est possible d’utiliser le modèle sur un ordinateur autre que Windows, mais ce scénario est au-delà de la portée de cet article.

1. Si vous n’avez pas leur est déjà installé, installez [**les modules de PowerShell d’Azure**](http://aka.ms/webpi-azps). Lorsque vous y êtes invité, cliquez sur **exécuter**, puis **l’installer**. PowerShell Azure 1.3 ou une version ultérieure est requis.

2. L’outil **openssl** est inclus dans la distribution de [**Git pour Windows**](http://www.git-scm.com/downloads). Si vous ne le n'avez pas déjà fait, installez maintenant le [Git pour Windows](http://www.git-scm.com/downloads) . (Les options d’installation par défaut sont OK.)

3. En supposant que Git a été installé mais non inclus dans le chemin d’accès système, ouvrez une fenêtre de Microsoft Azure PowerShell et exécutez les commandes suivantes :

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Remplacer le `<Git installation folder>` avec l’emplacement Git sur votre ordinateur ; la valeur par défaut est **« C:\Program Files\Git »**. Remarquez le point-virgule au début de la première trajectoire.

4. Assurez-vous que vous êtes connecté sur Azure (via [`Add-AzureRmAccount`](https://msdn.microsoft.com/library/mt619267.aspx) applet de commande) et que vous avez sélectionné l’abonnement qui doit être utilisé pour créer votre cluster recherche élastique. Vous pouvez vérifier que l’abonnement correcte est sélectionnée à l’aide de `Get-AzureRmContext` et `Get-AzureRmSubscription` applets de commande.

5. Si vous ne l’avez pas déjà fait, modifiez le répertoire en cours dans le dossier ES-multiposte.

### <a name="run-the-createelasticsearchcluster-script"></a>Exécutez le script CreateElasticSearchCluster
Avant d’exécuter le script, ouvrez le `azuredeploy-parameters.json` de fichiers et de vérifier ou de fournir des valeurs pour les paramètres de script. Les paramètres suivants sont fournis :

|Nom du paramètre           |Description|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Le nom qui est utilisé pour créer le nom DNS visible publiquement pour le cluster de recherche élastique (en ajoutant le domaine de la région Azure le nom fourni). Par exemple, si la valeur de ce paramètre est « myBigCluster » et la région Azure choisie est-ouest des États-Unis, le nom DNS qui en résulte pour le cluster est myBigCluster.westus.cloudapp.azure.com. <br /><br />Ce nom sert également de nombreux artefacts associé au cluster recherche élastique, tels que les noms de nœud de données un nom de racine.|
|adminUsername           |Le nom du compte administrateur de gestion de cluster recherche élastique (clés SSH correspondantes sont générées automatiquement).|
|dataNodeCount           |Le nombre de nœuds dans le cluster de recherche élastique. La version actuelle du script ne distingue pas les nœuds de données et la requête ; tous les nœuds de jouent ces deux rôles. La valeur par défaut à 3 nœuds.|
|dataDiskSize            |La taille des disques de données (en Go) qui est allouée pour chaque nœud. Chaque nœud reçoit 4 disques de données, exclusivement dédiés au service de recherche élastique.|
|région                  |Le nom de région Azure où le cluster recherche élastique doit se trouver.|
|esUserName              |Le nom d’utilisateur de l’utilisateur qui est configuré pour avoir accès au cluster ES (soumise à l’authentification de base HTTP). Le mot de passe ne fait pas partie du fichier de paramètres et doit être fourni lorsque `CreateElasticSearchCluster` script est appelé.|
|vmSizeDataNodes         |La taille d’Azure VM pour les nœuds de cluster recherche élastique. La valeur par défaut est Standard_D2.|

Vous êtes maintenant prêt à exécuter le script. Exécutez la commande suivante :

```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name> -Region <azure-region> -EsPassword <es-password>
```

où 

|Nom de paramètre de script    |Description|
|-----------------------  |--------------------------|
|`<es-group-name>`        |Le nom du groupe de ressources Azure qui contiendra toutes les ressources de cluster recherche élastique.|
|`<azure-region>`         |Le nom de la région Azure où le cluster recherche élastique doit être créé.|         
|`<es-password>`          |Le mot de passe pour l’utilisateur recherche élastique.|

>[AZURE.NOTE] Si vous obtenez une exception NullReferenceException à partir de l’applet de commande Test-AzureResourceGroup, vous avez oublié d’ouvrir une session sur Azure (`Add-AzureRmAccount`).

Si vous obtenez une erreur d’exécution du script et que vous constatez que l’erreur a été provoquée par une valeur de paramètre de modèle incorrect, corriger le fichier de paramètres et de réexécuter le script avec un nom de groupe de ressources différent. Vous pouvez également réutiliser le même nom de groupe de ressources et le script de nettoyage de l’en ajoutant le `-RemoveExistingResourceGroup` paramètre à l’appel de script.

### <a name="result-of-running-the-createelasticsearchcluster-script"></a>Résultat de l’exécution du script CreateElasticSearchCluster
Après avoir exécuté le `CreateElasticSearchCluster` script, principaux artefacts suivants seront créés. Pour cet exemple, nous supposons que vous avez utilisé « myBigCluster » comme valeur de la `dnsNameForLoadBalancerIP` paramètre et que la région où vous avez créé le cluster est-ouest des États-Unis.

|Artefact|Nom, emplacement et remarques|
|----------------------------------|----------------------------------|
|Code SSH pour l’administration à distance |fichier myBigCluster.key (dans le répertoire à partir duquel la CreateElasticSearchCluster a été exécuté). <br /><br />Ce fichier de clé permet de se connecter (via le nœud Administration) et le nœud administration aux nœuds de données dans le cluster.|
|Nœud administrateur                        |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Un ordinateur dédié virtuel pour l’administration de cluster distant Elasticsearch--la seule qui permet d’identifier les connexions SSH externes. Il s’exécute sur le même réseau virtuel que tous les nœuds de cluster de Elasticsearch, mais il ne s’exécute pas tous les services Elasticsearch.|
|Nœuds de données                        |myBigCluster1... myBigCluster*N* <br /><br />Nœuds de données qui exécutent les services Elasticsearch et Kibana. Vous pouvez vous connecter via le protocole SSH à chaque nœud, mais uniquement via le nœud Administration.|
|Cluster de Elasticsearch             |http://myBigCluster.westus.cloudapp.Azure.com/es/ <br /><br />Le principal point de terminaison pour le cluster Elasticsearch (Notez le suffixe /es). Il est protégé par l’authentification HTTP de base (les informations d’identification ont été les paramètres spécifiés de l’esUserName/esPassword du modèle ES-multiposte). Le cluster possède également la tête du plug-in installée (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) pour l’administration du cluster de base.|
|Service de Kibana                    |http://myBigCluster.westus.cloudapp.Azure.com <br /><br />Le service Kibana est configuré pour afficher les données de la grappe de Elasticsearch. Il est protégé par les mêmes informations d’authentification que le cluster lui-même.|

## <a name="in-process-versus-out-of-process-trace-capturing"></a>-Process et out-of-process trace capture
Dans l’introduction, nous l’avons mentionné deux méthodes fondamentales de la collecte de données de diagnostic : dans-process et out-of-process. Chacun possède des forces et des faiblesses.

La **trace capture en cours** présentent les avantages suivants :

1. *Déploiement et configuration aisées*

    * La configuration de la collecte de données de diagnostic est juste une partie de la configuration de l’application. Il est facile de toujours conserver « synchronisé » avec le reste de l’application.

    * Configuration par application ou par service est facilement réalisable.

    * Out-of-process trace capture nécessite généralement un déploiement séparé et la configuration de l’agent de diagnostic, qui est une tâche administrative supplémentaire et une source potentielle d’erreurs. La technologie d’agent particulier permet souvent qu’une seule instance de l’agent par l’ordinateur virtuel (nœud). Cela signifie que la configuration de la collection de la configuration du diagnostic est partagée entre toutes les applications et les services qui s’exécutent sur ce nœud.

2. *Flexibilité*

    * L’application peut envoyer les données qu’elle puisse aller, tant qu’il existe une bibliothèque cliente qui prend en charge le système de stockage ciblé. Récepteurs de nouveaux peuvent être ajoutés selon les besoins.

    * Règles de capture, de filtrage et d’agrégation des données complexes peuvent être implémentées.

    * Capture d’une trace d’out-of-process est souvent limitée par les récepteurs de données qui prend en charge de l’agent. Certains agents sont extensibles.

3. *Accès aux données de l’application interne et de contexte*

    * Le sous-système de diagnostic en cours d’exécution dans le processus de service d’application peut accroître facilement les traces avec des informations contextuelles.

    * Dans l’approche out-of-process, les données doivent être envoyées à un agent via un mécanisme de communication interprocessus, par exemple Event Tracing for Windows. Ce mécanisme peut imposer des limitations supplémentaires.

La **trace de la sortie du processus de capture** présente les avantages suivants :

1. *La possibilité de contrôler l’application et de collecter de crash dumps*

    * Dans le processus trace capture peut ne pas aboutir si l’application ne démarre pas ou se bloque. Un agent indépendant a une meilleure chance de capturer des informations de dépannage cruciales.<br /><br />

2. *Maturité, la robustesse et performances éprouvées*

    * Un agent de développé par un fournisseur de plate-forme (par exemple, un agent Microsoft Azure Diagnostics) a été soumis à des tests rigoureux et la bataille-renforcement de la sécurité.

    * Avec la trace dans le processus de capture, les soins doivent être prises pour s’assurer que l’activité d’envoi de données de diagnostic à partir d’un processus d’application ne pas interférer avec les tâches principales de l’application ou provoquer des problèmes de synchronisation ou de performances. Un agent de façon indépendante en cours d’exécution est moins sujet à ces problèmes et est spécialement conçu pour limiter son impact sur le système.

Il est possible de combiner et de bénéficier de ces deux approches. En effet, il peut être la meilleure solution pour de nombreuses applications.

Ici, nous utilisons la **bibliothèque de Microsoft.Diagnostic.Listeners** et la trace dans le processus de capture pour envoyer des données à partir d’une application de Service Fabric à un cluster d’Elasticsearch.

## <a name="use-the-listeners-library-to-send-diagnostic-data-to-elasticsearch"></a>La bibliothèque d’écouteurs permet d’envoyer des données de diagnostic pour Elasticsearch
La bibliothèque Microsoft.Diagnostic.Listeners fait partie de l’exemple d’application de Service Fabric PartyCluster. Pour l’utiliser :

1. Téléchargez [l’exemple PartyCluster](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) à partir de GitHub.

2. Copier les projets Microsoft.Diagnostics.Listeners et Microsoft.Diagnostics.Listeners.Fabric (dossiers entiers) dans le répertoire de l’exemple PartyCluster dans le dossier de la solution de l’application qui doit pour envoyer les données à Elasticsearch.

3. Ouvrez la solution cible, cliquez sur le nœud solution dans l’Explorateur de solutions et choisissez **Ajouter un projet existant**. Ajouter le projet de Microsoft.Diagnostics.Listeners à la solution. Répétez cette opération pour le projet Microsoft.Diagnostics.Listeners.Fabric.

4. Ajoutez une référence de projet à partir de vos projets de service pour les deux projets ajoutés. (Chaque service qui est supposé pour envoyer des données à Elasticsearch doit faire référence à Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric).

    ![Références de projet aux bibliothèques Microsoft.Diagnostics.EventListeners et Microsoft.Diagnostics.EventListeners.Fabric][1]

### <a name="service-fabric-general-availability-release-and-microsoftdiagnosticstracing-nuget-package"></a>Version de la disponibilité générale de tissu de service et de package Nuget de Microsoft.Diagnostics.Tracing
Les applications générées avec la version de disponibilité de Service Fabric général (2.0.135, publié le 31 mars 2016) ciblent **.NET Framework 4.5.2**. Cette version est la version la plus récente de la.NET Framework pris en charge par Azure au moment de la publication de GA. Malheureusement, cette version du framework ne dispose pas de certaines EventListener APIs qui a besoin de la bibliothèque Microsoft.Diagnostics.Listeners. Chaque projet qui utilise la bibliothèque de Microsoft.Diagnostics.Listeners devez utiliser une autre implémentation de source d’événement EventSource (le composant qui forme la base des API d’enregistrement dans les applications de Fabric) et EventListener sont étroitement couplées. Cette implémentation est fournie par le **package Nuget de Microsoft.Diagnostics.Tracing**, créés par Microsoft. Le package est entièrement compatible avec EventSource inclus dans le framework, aucune modification de code ne devrait être nécessaire que les modifications de l’espace de noms référencé.

Pour utiliser la mise en oeuvre du Microsoft.Diagnostics.Tracing de la classe de source d’événement, procédez comme suit pour chaque projet de service qui doit envoyer des données à Elasticsearch :

1. Avec le bouton droit sur le projet de service et sélectionnez **Manage Nuget Packages**.

2. Basculez vers la source du package nuget.org (si elle n’est pas déjà sélectionnée) et recherchez les «**Microsoft.Diagnostics.Tracing**».

3. Installer le `Microsoft.Diagnostics.Tracing.EventSource` package (et ses dépendances).

4. Ouvrez le fichier **ServiceEventSource.cs** ou **ActorEventSource.cs** dans votre projet de service et remplacez le `using System.Diagnostics.Tracing` la directive sur le fichier avec le `using Microsoft.Diagnostics.Tracing` la directive.

Ces étapes ne sera pas nécessaires une fois que le **point 4.6 du.NET Framework** est pris en charge par Microsoft Azure.

### <a name="elasticsearch-listener-instantiation-and-configuration"></a>Configuration et instanciation du récepteur Elasticsearch
La dernière étape pour l’envoi de données de diagnostic à Elasticsearch consiste à créer une instance de `ElasticSearchListener` et le configurer avec les données de connexion Elasticsearch. L’écouteur capture automatiquement tous les événements déclenchés par les classes de source d’événement définis dans le projet de service. Il doit être actif pendant la durée de vie du service, est le meilleur endroit pour créer le code d’initialisation du service. Voici comment se présenterait le code d’initialisation d’un service sans état après les modifications nécessaires (ajouts fait remarquer dans les commentaires commençant par `****`) :

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Runtime;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>        
        private static void Main()
        {
            try
            {
                // **** Instantiate ElasticSearchListener
                var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                ElasticSearchListener esListener = null;
                if (configProvider.HasConfiguration)
                {
                    esListener = new ElasticSearchListener(configProvider);
                }

                // The ServiceManifest.XML file defines one or more service type names.
                // Registering a service maps a service type name to a .NET type.
                // When Service Fabric creates an instance of this service type,
                // an instance of the class is created in this host process.

                ServiceRuntime.RegisterServiceAsync("Stateless1Type", 
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                // Prevents this host process from terminating so services keep running.
                Thread.Sleep(Timeout.Infinite);

                // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                GC.KeepAlive(esListener);
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch les données de connexion doivent être placées dans une section distincte dans le fichier de configuration de service (**PackageRoot\Config\Settings.xml**). Le nom de la section doit correspondre à la valeur passée à la `FabricConfigurationProvider` constructeur, par exemple :

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Les valeurs de `serviceUri`, `userName` et `password` paramètres correspondent respectivement à l’adresse de point de terminaison de cluster Elasticsearch, Elasticsearch nom d’utilisateur et mot de passe. `indexNamePrefix`est le préfixe pour les index de Elasticsearch ; la bibliothèque Microsoft.Diagnostics.Listeners crée un nouvel index pour ses données tous les jours.

### <a name="verification"></a>Vérification
Voilà ! Maintenant, chaque fois que le service est exécuté, il commence à envoyer des traces pour le service Elasticsearch spécifié dans la configuration. Vous pouvez vérifier cela en ouvrant l’UI Kibana associé à l’instance cible de Elasticsearch. Dans notre exemple, l’adresse est http://myBigCluster.westus.cloudapp.azure.com/. Vérifiez que les index avec le préfixe du nom choisi pour le `ElasticSearchListener` instance ont en effet été créés et remplis avec des données.

![Événements d’application PartyCluster Kibana affichage][2]

## <a name="next-steps"></a>Étapes suivantes
- [Pour en savoir plus sur le diagnostic et la surveillance d’un service de Fabric du Service](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png
