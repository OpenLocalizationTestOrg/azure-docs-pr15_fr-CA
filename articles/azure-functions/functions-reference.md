<properties
    pageTitle="Référence du développeur de fonctions Azure | Microsoft Azure"
    description="Comprendre les concepts de fonctions d’Azure et les composants qui sont communes à toutes les langues et les liaisons."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure fonctions, fonctions, traitement de l’événement, webhooks, calcul dynamique, architecture sans serveur"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Référence du développeur de fonctions Azure

Fonctions Azure partagent quelques concepts techniques de base et des composants, quelle que soit la langue ou la liaison que vous utilisez. Avant de passer à l’apprentissage des détails spécifiques à une langue donnée ou d’une liaison, veillez à lire par le biais de cette vue d’ensemble qui s’applique à tous les éléments.

Cet article suppose que vous avez déjà lu de la [vue d’ensemble des fonctions d’Azure](functions-overview.md) et que vous êtes familiarisé avec [les concepts de WebJobs SDK, tels que les déclencheurs, liaisons et l’exécution de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Fonctions Azure est basé sur le SDK WebJobs. 


## <a name="function-code"></a>Code de la fonction

Une *fonction* est le concept principal dans les fonctions d’Azure. Vous écrivez du code pour une fonction dans une langue de votre choix et enregistrez les fichiers de code et un fichier de configuration dans le même dossier. La configuration est au format JSON et le fichier est nommé `function.json`. Plusieurs langues sont prises en charge, et chacun d’eux possède une expérience légèrement différente, optimisée pour la mieux adaptée à cette langue. 

Le `function.json` fichier contient la configuration spécifique à une fonction, y compris de ses liaisons. Le runtime lit ce fichier pour déterminer les événements à déclencher de, les données à inclure lors de l’appel de la fonction et où envoyer les données transmises à partir de la fonction elle-même. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Vous pouvez empêcher que le runtime exécute la fonction en définissant le `disabled` propriété `true`.

Le `bindings` propriété est qui vous permet de configurer à la fois des déclencheurs et des liaisons. Chaque liaison de partage quelques paramètres communs et des paramètres qui sont spécifiques à un type de liaison. Chaque liaison requiert les paramètres suivants :

|Propriété|Valeurs/Types|Commentaires|
|---|-----|------|
|`type`|chaîne|Type de liaison. Par exemple, `queueTrigger`.
|`direction`|'in', 'out'| Indique si la liaison est de recevoir des données dans la fonction ou de l’envoi de données à partir de la fonction.
| `name` | chaîne | Le nom qui sera utilisé pour les données liées dans la fonction. Pour C#, il s’agit un nom d’argument ; pour JavaScript, il sera la clé dans une liste de clé/valeur.

## <a name="function-app"></a>Application de la fonction

Une application de fonction se compose d’un ou plusieurs des fonctions individuelles qui sont gérées par le Service d’application Azure. Toutes les fonctions dans une application de la fonction partagent le même plan de tarification, le déploiement continue et la version du runtime. Les fonctions écrites dans plusieurs langages peuvent partager la même application de la fonction. Considérez une application de fonction comme un moyen d’organiser et de gérer les fonctions de votre collectivement. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (scripts et hôte web)

Le runtime, ou l’hôte de script, est l’hôte WebJobs SDK sous-jacente, qui écoute les événements, collecte et envoie les données et exécute votre code. 

Pour faciliter les déclencheurs HTTP, il existe également un hôte web qui permet de vous asseoir face à l’hôte de script dans les scénarios de production. Cela permet d’isoler l’hôte de script du trafic de front-end gérés par l’hôte web.

## <a name="folder-structure"></a>Structure de dossier

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Lorsque vous définissez un projet pour le déploiement des fonctions à une application de la fonction dans le Service d’application Azure, vous pouvez traiter cette structure de dossiers comme code de votre site. Vous pouvez utiliser les outils existants tels que l’intégration continue et de déploiement, ou des scripts de déploiement personnalisés pour ce faire déployer l’installation du package de temps ou de code transpilation.

>[AZURE.NOTE] Le `wwwroot` dossier ici est où vos fichiers seront obtenir déployés sur. Toutefois, vous ne devez pas inclure ce dossier dans les fichiers que vous déployez, qui risque d’entraîner avec `wwwroot\wwwroot`. Au lieu de cela, votre `host.json` fichier et la fonction des dossiers doivent être directement à la racine de ce que vous déployez.

## <a id="fileupdate"></a>Comment mettre à jour les fichiers d’application de fonction

L’éditeur de fonctions intégrée dans le portail Azure vous permet de mettre à jour le fichier *function.json* et le fichier de code pour une fonction. Pour télécharger ou mettre à jour d’autres fichiers tels que *package.json* ou *project.json* ou dépendances, vous devez utiliser d’autres méthodes de déploiement.

Fonction applications reposent sur le Service de l’application, afin que toutes les [options de déploiement disponibles pour les applications web standard](../app-service-web/web-sites-deploy.md) sont disponibles pour les applications de fonction ainsi. Voici certaines méthodes que vous pouvez utiliser pour télécharger ou mettre à jour les fichiers d’application de fonction. 

#### <a name="to-use-app-service-editor"></a>Pour utiliser l’éditeur de Service d’application

1. Dans le portail de fonctions d’Azure, cliquez sur **paramètres d’application de fonction**.

2. Dans la section **Paramètres avancés** , cliquez sur **accéder aux paramètres de Service d’application**.

3. Cliquez sur **Éditeur de Service d’application** dans le Menu d’application Nav sous **Outils de développement**.

4.  Cliquez sur **OK**.

    Une fois chargé de l’éditeur de Service d’application, vous verrez les dossiers de fichiers et de la fonction *host.json* sous *wwwroot*. 

5. Ouvrir des fichiers pour les modifier, ou glisser- déposer à partir de votre ordinateur de développement pour télécharger des fichiers.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Pour utiliser le point de terminaison de l’application de la fonction SCM (Kudu)

1. Naviguez jusqu'à : `https://<function_app_name>.scm.azurewebsites.net`.

2. Cliquez sur **Console de débogage > CMD**.

3. Accédez à `D:\home\site\wwwroot\` mise à jour *host.json* ou `D:\home\site\wwwroot\<function_name>` pour mettre à jour les fichiers d’une fonction.

4. Glisser-déplacer un fichier à télécharger dans le dossier approprié dans la grille de fichier. Il existe deux zones dans la grille de fichier dans laquelle vous pouvez déposer un fichier. Pour les fichiers *.zip* , une zone apparaît avec l’étiquette « Faites glisser ici pour télécharger et décompresser. » Pour les autres types de fichiers, déplacer dans la grille de fichier, mais en dehors de la zone « unzip ».

#### <a name="to-use-ftp"></a>Utilisation de FTP

1. Suivez les instructions fournies [ici](../app-service-web/web-sites-deploy.md#ftp) afin d’obtenir le FTP configuré.

2. Lorsque vous êtes connecté au site app fonction, copier un fichier de mise à jour de *host.json* à `/site/wwwroot` ou copier les fichiers de fonction à `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Utiliser le déploiement en continu

Suivez les instructions de la rubrique [déploiement continue pour les fonctions d’Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Exécution en parallèle

Lorsque plusieurs événements de déclenchement se produisent plus rapidement qu’un runtime mono-thread de fonction pour les traiter, le runtime peut invoquer la fonction plusieurs fois en parallèle.  Si une application de la fonction utilise le [Plan de Service dynamique](functions-scale.md#dynamic-service-plan), l’application de la fonction peut évoluer automatiquement.  Chaque instance de l’application de la fonction, si l’application s’exécute sur le Plan de Service dynamique ou un régulière [Plan de Service d’application](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), peut-être traiter des appels de fonction simultanés en parallèle à l’aide de plusieurs threads.  Le nombre maximal d’appels de fonction simultanés dans chaque instance d’application de fonction varie en fonction de la taille de la mémoire de l’application de la fonction. 

## <a name="azure-functions-pulse"></a>Fonctions Azure Pulse  

Impulsion est un flux d’événement en direct qui affiche le nombre d’exécutions de la fonction, ainsi que les réussites et les échecs. Vous pouvez également analyser votre durée d’exécution moyenne. Mais nous ajouterons plus de fonctionnalités et de personnalisation lui au fil du temps. Vous pouvez accéder à la page **d’impulsions** à partir de l’onglet **analyse** .

## <a name="repositories"></a>Référentiels

Le code pour les fonctions d’Azure est open source et stockés dans des référentiels de GitHub :

* [Exécution de fonctions Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure portal de fonctions](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modèles de fonctions Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensions d’Azure SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Liaisons

Voici un tableau de toutes les liaisons pris en charge.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Problèmes liés aux rapports

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Référence du développeur Azure fonctions C#](functions-reference-csharp.md)
* [Référence du développeur Azure fonctions F#](functions-reference-fsharp.md)
* [Référence du développeur de fonctions NodeJS Azure](functions-reference-node.md)
* [Les liaisons et les déclencheurs de fonctions azure](functions-triggers-bindings.md)
* [Fonctions d’azure : le voyage](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) sur le blog de l’équipe de Service d’application Azure. Un historique de la façon dont les fonctions Azure a été développée.





