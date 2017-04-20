<properties
    pageTitle="Présentation approfondie des plans de Service d’application Azure | Microsoft Azure"
    description="Découvrez comment les programmes de Service d’application pour le travail de Service d’application Azure, et comment ils tirer profit de votre expérience de gestion."
    keywords="application service, azure application service, plan de service application évolutive, échelle, coût de service d’application"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Présentation approfondie des plans de Service d’application Azure#

Un plan de Service de l’application représente un ensemble de fonctionnalités et de capacité que vous pouvez partager entre plusieurs applications. Applications Web, applications Mobile, fonction les applications ou applications API, dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714) tous les exécutent dans un plan de Service de l’application. Ces plans prennent en charge les cinq niveaux de tarification : *libre*, *Shared*, *base*, *Standard*et *Premium*. Chaque couche possède ses propres capacités et la capacité. Dans le même abonnement et l’emplacement géographique, les applications peuvent partager un plan. Toutes les applications qui partagent un plan peuvent utiliser toutes les fonctionnalités qui sont définies par les niveaux du plan. Toutes les applications qui sont associées à un plan de s’exécuter sur le plan définit les ressources.

Par exemple, si votre projet est configuré pour utiliser les deux instances de « petits » dans le niveau de service standard, toutes les applications qui sont associées à ce plan s’exécutent sur les deux instances et ont accès à la fonctionnalité de niveau de service standard. Instances du plan sur lequel les applications sont en cours d’exécution sont entièrement gérés et hautement disponible.

Cet article explore les caractéristiques clés, comme la couche et de l’échelle d’un plan de Service de l’application et comment ils entrent en jeu lors de la gestion de vos applications.

## <a name="apps-and-app-service-plans"></a>Les applications et les plans de Service de l’application

Une application de Service d’application peut être associée qu’un plan de Service de l’application à un moment donné.

Les applications et les plans sont contenus dans un groupe de ressources. Un groupe de ressources sert à la limite du cycle de vie pour chaque ressource qui est qu’il contient. Vous pouvez utiliser des groupes de ressources pour gérer ensemble de tous les éléments d’une application.

Dans la mesure où un groupe de ressources unique peut avoir plusieurs plans de Service de l’application, vous pouvez affecter différentes applications à différentes ressources physiques. Par exemple, vous pouvez séparer les ressources entre les environnements de développement, de test et de production. Ayant des environnements distincts pour la production et de développement/test vous permet d’isoler les ressources. De cette façon, le test de charge sur une nouvelle version de vos applications ne concurrence pas pour les mêmes ressources que vos applications de production, qui servent de clients réels.

Lorsque vous avez plusieurs plans dans un groupe de ressources unique, vous pouvez également définir une application qui s’étend sur les régions géographiques. Par exemple, une application hautement disponible en cours d’exécution dans deux régions comprend au moins deux plans, une pour chaque région et une application associée à chaque plan. Dans ce cas, toutes les copies de l’application puis figurent dans un groupe de ressources unique. Ayant un groupe de ressources avec plusieurs plans et plusieurs applications rend facile à gérer, de contrôler et d’afficher l’état de santé de l’application.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Créer un plan de Service d’application ou utiliser l’existant

Lorsque vous créez une application, vous devez envisager la création d’un groupe de ressources. D’autre part, si l’application que vous allez créer est un composant d’une application plus importante, cette application doit être créée dans le groupe de ressources qui est alloué pour la plus grande application.

Si la nouvelle application est une application entièrement nouveau ou une partie d’un plus grand, vous pouvez choisir d’utiliser un plan de Service d’application existant pour héberger ou créez-en un nouveau. La présente décision est plus une question de la capacité et la charge attendue.

Si cette nouvelle application va utiliser beaucoup de ressources et ont différents facteurs à partir d’autres applications de mise à l’échelle hébergé dans un plan existant, il est recommandé d’isoler dans son propre plan.

Lorsque vous créez un plan, vous pouvez affecter un nouvel ensemble de ressources pour votre application et renforce le contrôle sur la répartition des ressources, car chaque plan obtient son propre jeu d’instances.

Étant donné que vous pouvez déplacer des applications sur plusieurs plans, vous pouvez modifier la façon dont les ressources sont allouées dans le plus grand de l’application.

Enfin, si vous souhaitez créer une application dans une autre région, et cette zone n’a pas un plan existant, vous pouvez créer un plan dans cette zone pour être en mesure d’héberger votre application.

## <a name="create-an-app-service-plan"></a>Créer un plan de Service d’application

>[AZURE.TIP] Si vous disposez d’un environnement de Service d’application, vous pouvez consulter la documentation spécifique aux environnements de Service d’application ici : [créer un Plan de Service application dans un environnement de Service d’application](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Vous pouvez créer un plan de Service de l’application vide à partir de l’expérience de parcourir de plan de Service de l’application ou dans le cadre de la création de l’application.

Dans le [portail Azure](https://portal.azure.com), cliquez sur **Nouveau** > **Web + mobile**et puis sélectionnez **Web App** ou autre type d’application de Service de l’application.
![Créer une application dans Azure portal.][createWebApp]

Vous pouvez sélectionner ou créer le plan de Service d’application de la nouvelle application.

 ![Créer un plan de Service de l’application.][createASP]

Pour créer un nouveau plan de Service de l’application, cliquez sur **[+] créer un**, tapez le nom du **plan de Service de l’application** et puis sélectionnez un **emplacement**approprié. Cliquez sur **couche de tarification**et sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher les options de tarification plus, tels que **Free** et **Shared**. Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Déplacer une application vers un autre plan de Service d’application

Vous pouvez déplacer une application à un plan de service d’application différente dans [Azure portal](https://portal.azure.com). Vous pouvez déplacer des applications entre les plans tant que les plans sont dans le même groupe de ressources et de la région géographique.

Pour déplacer une application vers un autre plan, accédez à l’application que vous souhaitez déplacer. Dans le menu **paramètres** , recherchez **Application Service planning**.

**Application Service planning** ouvre le sélecteur de **plan de Service de l’application** . À ce stade, vous pouvez choisir un plan existant ou créez-en un nouveau. Seuls les plans valides (dans le même groupe de ressources et la situation géographique) sont affichés.

![Sélecteur de plan de Service de l’application.][change]

Chaque plan a son propre niveau de prix. Par exemple, lorsque vous déplacez un site d’une couche libre à un niveau Standard, votre application maintenant peut utiliser toutes les fonctionnalités et les ressources de la couche Standard.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Cloner une application vers un autre plan de Service d’application
Si vous souhaitez déplacer l’application vers une autre région, une autre solution consiste à application de clonage. Clonage de fait une copie de votre application dans un environnement de Service d’application dans une région ou un plan de Service d’application nouveau ou existant.

 ![Cloner une application.][appclone]

Vous pouvez trouver **l’Application de Clone** dans le menu **Outils** .

Le clonage a quelques limitations que vous pouvez en savoir plus sur à [l’application de Service d’application Azure clonage à l’aide d’Azure portal](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Mettre à l’échelle un plan de Service d’application

Il existe trois méthodes pour mettre à l’échelle un plan :

- **Modifier du plan tarification de niveau**. Par exemple, un plan dans la couche de base peut être converti en un niveau Standard ou Premium, et toutes les applications qui sont associées à ce plan maintenant peuvent utiliser les fonctionnalités offertes par le nouveau niveau de service.
- **Modifier la taille d’instance du plan**. Par exemple, un plan dans la couche de base qui utilise des instances de petite taille peut être modifié pour utiliser des instances de grande taille. Toutes les applications qui sont associées à ce plan maintenant peuvent utiliser le davantage de mémoire et ressources processeur qui propose de la taille d’instance plus importante.
- **Modifier le nombre d’instances du plan**. Par exemple, un plan Standard qui est répartie sur trois instances peut être distribuée à 10 instances. Un plan de prime peut être étendu pour 20 instances (sous réserve de disponibilité). Toutes les applications qui sont associées à ce plan maintenant permet de davantage de mémoire et ressources processeur qui offre par le plus grand nombre d’instances.

Vous pouvez modifier la taille de couche et instance tarification en cliquant sur l’option **Échelle des** paramètres de l’application ou le plan de Service de l’application. Les modifications s’appliquent au plan de Service de l’application et affectent toutes les applications qu’il héberge.

 ![Définir les valeurs à mettre à l’échelle d’une application.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Nettoyage de Plan de Service d’application
Les **plans de Service d’application** qui n’ont aucune application qui leur sont associées entraînent encore frais dans la mesure où ils continuent à réserver de la capacité de calcul configurée dans les propriétés d’échelle de plan Service d’application.
Pour éviter des frais d’inattendu, lors de la dernière application hébergée dans un plan de Service de l’application est supprimée, le plan de Service de l’application vide qui en résulte est également supprimé.


## <a name="summary"></a>Résumé

Plans de Service d’application représentent un ensemble de fonctionnalités et de capacité que vous pouvez partager dans vos applications. Plans de Service d’application vous donnent la possibilité de répartir les applications spécifiques à un ensemble de ressources et de poursuivre l’optimisation de l’utilisation de votre ressource Azure. De cette façon, si vous souhaitez économiser de l’argent sur votre environnement de test, vous pouvez partager un plan entre plusieurs applications. Vous pouvez également maximiser le débit pour votre environnement de production par redimensionnement sur plusieurs régions et plans.

## <a name="whats-changed"></a>Ce qui a changé

* Pour un guide pour la modification de sites Web au Service de l’application, voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png
