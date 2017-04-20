<properties
   pageTitle="Nouveautés Microsoft Power BI incorporé"
   description="Incorporées de BI d’alimentation vous permet d’intégrer des rapports BI de puissance dans vos applications web ou mobiles afin que vous n’avez pas besoin de créer des solutions personnalisées pour visualiser des données pour vos utilisateurs"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="what-is-microsoft-power-bi-embedded"></a>Nouveautés Microsoft Power BI incorporé

Avec **Alimentation BI incorporé**, vous pouvez intégrer des rapports BI d’alimentation directement dans vos applications web ou mobiles.

![](media\powerbi-embedded-whats-is\what-is.png)

Alimentation BI incorporé est un **service Azure** qui permet des éditeurs de logiciels indépendants et les développeurs d’applications à des expériences de données BI de puissance surfaces dans leurs applications. En tant que développeur, vous avez créé des applications, et ces applications disposent de leurs propres utilisateurs et un ensemble de fonctions précis. Ces applications peuvent également se produire pour que certains éléments de données intégrés tels que des graphiques et des rapports qui peuvent maintenant être alimentés par BI de puissance Microsoft Embedded. Les utilisateurs ne doivent d’un compte de puissance BI à utiliser votre application. Ils peuvent continuer à se connecter à votre application, tout comme avant et d’afficher et d’interagir avec la BI de puissance reporting expérience sans nécessiter de licence supplémentaire.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Le Gestionnaire de licences pour l’alimentation de Microsoft BI incorporé

Dans le modèle d’utilisation **Incorporées de BI Microsoft d’alimentation** , l’octroi de licences pour alimentation BI n’est pas la responsabilité de l’utilisateur final.  Au lieu de cela, **restitue** sont achetées par le développeur de l’application qui consomme les effets visuels et sont facturés à l’abonnement qui possède ces ressources.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>La puissance de Microsoft BI intégré de modèle conceptuel

![](media\powerbi-embedded-whats-is\model.png)

Comme tout autre service dans Azure, ressources de puissance BI Embedded sont mis en service par le biais de l' [API de ARM Azure](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource que vous configurez est une **Collection de puissance BI espace de travail**.

## <a name="workspace-collection"></a>Collection de l’espace de travail

Une **Collection de l’espace de travail** est le conteneur de Azure niveau supérieur pour les ressources qui contient 0 ou plusieurs **espaces de travail**.  Un **espace de travail** **Collection** possède toutes les propriétés Azure standard, ainsi que les éléments suivants :

-   **Touches d’accès rapide** – clés utilisées lors de l’appel de manière sécurisée les API de BI d’alimentation (décrit dans une section ultérieure).
-   **Utilisateurs** – les utilisateurs Azure Active Directory (DAS) qui ont des droits d’administrateur pour gérer la Collection d’espace de travail de BI d’alimentation via le portail Azure ou ARM API.
-   **Région** – dans le cadre de la mise en service d’une **Collection de l’espace de travail**, vous pouvez sélectionner une région à être mis en service dans. Pour plus d’informations, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Espace de travail

Un **espace de travail** est un conteneur de contenu BI de puissance, qui peut inclure des groupes de données, les rapports et les tableaux de bord. Un **espace de travail** est vide lors de la création. Lors de l’aperçu, vous allez créer tout le contenu à l’aide de bureau BI de puissance et vous pourrez le télécharger à l’un de vos espaces de travail à l’aide de l' [API de puissance BI reste](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>À l’aide des espaces de travail et les Collections de l’espace de travail
**Collections de l’espace de travail** et les **espaces de travail** sont des conteneurs de contenu qui sont utilisées et organisées de la façon la mieux adaptée à la conception de l’application que vous générez. Il y a différentes manières que vous pouvez organiser de leur contenu. Vous pouvez choisir de placer tout le contenu au sein d’un espace de travail, puis ultérieurement les jetons app pour subdiviser encore le contenu entre vos clients. Vous pouvez également choisir de mettre toutes vos clients dans les espaces de travail distincts afin qu’il existe une séparation entre les deux. Ou bien, vous pouvez choisir d’organiser les utilisateurs par zone et non par le client. Cette conception flexible vous permet de choisir la meilleure façon d’organiser le contenu.

## <a name="cached-datasets"></a>Groupes de données mis en cache

Groupes de données mis en cache peut être utilisé dans l’aperçu.  Toutefois, vous ne peut pas actualiser les données en mémoire cache une fois qu’il a été chargé dans **Microsoft Power BI incorporé**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Authentification et autorisation avec jetons d’application

**Microsoft Power BI Embedded** diffère à votre application d’effectuer toutes les autorisation et authentification de l’utilisateur nécessaires. Il n’y a aucune exigence explicite que vos utilisateurs finaux être clients d’Azure Active Directory (AD Azure).  À la place, votre application exprime **Incorporées de BI Microsoft d’alimentation** d’une autorisation pour le rendu d’un rapport de puissance BI à l’aide des **Jetons d’authentification d’applications (application jetons)**.  Ces **Jetons d’application** sont créés en fonction des besoins lors de votre application veut générer un rapport.  Voir [jetons de l’application](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Les jetons d’authentification application (jetons de l’application)** sont utilisés pour authentifier par rapport à **Microsoft d’alimentation BI incorporé**.  Il existe trois types de **Jetons de l’application**:

1.  Mise en service des jetons - utilisés lors de la mise en service d’un nouvel **espace de travail** dans une **Collection de l’espace de travail**
2.  Jetons de développement - utilisés lorsque vous faites appel directement aux **API de reste de puissance BI**
3.  L’incorporation de jetons - utilisés lors d’appels pour afficher un rapport dans l’iframe incorporé

Ces jetons sont utilisés pour les différentes phases de vos interactions avec **BI d’alimentation Microsoft Embedded**.  Les jetons sont conçus de sorte que vous pouvez déléguer des autorisations à partir de votre application à l’analyse Décisionnelle de puissance. Pour plus d’informations, reportez-vous à la section [Flux de jeton App](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Voir aussi
- [Scénarios courants de Microsoft d’alimentation BI incorporé](power-bi-embedded-scenarios.md)
- [Mise en route de Microsoft d’alimentation BI incorporé](power-bi-embedded-get-started.md)
