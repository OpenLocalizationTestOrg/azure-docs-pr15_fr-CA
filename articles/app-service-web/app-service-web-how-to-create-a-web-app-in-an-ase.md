<properties
    pageTitle="Créer une application web dans un environnement de Service d’application"
    description="Apprenez à créer des applications web et application des plans de service, dans un environnement de Service d’application"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="create-a-web-app-in-an-app-service-environment"></a>Créer une application web dans un environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment créer des applications web et des plans de Service de l’application dans un [Environnement de Service d’application](app-service-app-service-environment-intro.md) (ASE). 

> [AZURE.NOTE] Si vous souhaitez apprendre à créer une application web mais que vous n’avez pas besoin de le faire dans un environnement de Service d’application, reportez-vous à la section [créer une application web de .NET](web-sites-dotnet-get-started.md) ou l’un des didacticiels pour d’autres langues et les infrastructures connexes.

## <a name="prerequisites"></a>Conditions préalables

Ce didacticiel suppose que vous avez créé un environnement de Service d’application. Si vous n’avez pas fait, consultez [créer un environnement de Service d’application](app-service-web-how-to-create-an-app-service-environment.md). 

## <a name="create-a-web-app"></a>Créer une application web

1. Dans le [Portail Azure](https://portal.azure.com/), cliquez sur **Nouveau > Web + Mobile > Web App**. 

    ![][1]

2. Sélectionnez votre abonnement.  

    Si vous disposez de plusieurs abonnements n’oubliez pas que pour créer une application dans votre environnement de Service d’application, vous devez utiliser le même abonnement que vous avez utilisé lors de la création de l’environnement. 

3. Sélectionnez ou créez un groupe de ressources.

    *Groupes de ressources* vous permettent de gérer les ressources connexes Azure en tant qu’unité et sont utiles lors de l’établissement des règles de *contrôle d’accès basé sur les rôles* (RBAC) pour vos applications. Pour plus d’informations, consultez [gestion de vos ressources Azure][ResourceGroups]. 

4. Sélectionnez ou créez un plan de Service de l’application.

    *Plans de Service de l’application* sont gérés ensembles d’applications web.  Normalement lorsque vous sélectionnez la tarification, le prix facturé est appliqué sur le plan de Service de l’application plutôt que sur les applications individuelles. Dans un équipement de direction auxiliaire, vous payez pour les instances de calcul affectées à l’équipement de direction auxiliaire au lieu de ce que vous avez répertoriés avec vos pages ASP.  Pour accroître le nombre d’instances d’une application web de vous mettre à l’échelle des instances de votre application de Service plan et elle affecte toutes les applications web de ce plan.  Certaines fonctionnalités telles que les connecteurs de site ou l’intégration de VNET ont également des restrictions de quantité dans le plan.  Pour plus d’informations, consultez [vue d’ensemble des plans de Service d’application Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    Vous pouvez identifier les plans de Service de l’application dans votre ASE en examinant l’emplacement qui est indiqué sous le nom de plan.  

    ![][5]

    Si vous souhaitez utiliser un plan de Service d’application qui existe déjà dans votre environnement de Service d’application, sélectionnez ce plan. Si vous souhaitez créer un nouveau plan de Service d’application, consultez la section suivante de ce didacticiel, [créez un plan de Service d’application dans un environnement de Service d’application](#createplan).

5. Entrez le nom de votre application web, puis cliquez sur **créer**. 

    Si votre équipement de direction auxiliaire utilise une adresse IP virtuelle externe l’URL d’une application dans un équipement de direction auxiliaire est : [*nom de site*]. [*nom de votre environnement de Service d’application*]. p.azurewebsites.net au lieu de [*nom de site*]. azurewebsites.net
    
    Si votre équipement de direction auxiliaire utilise une adresse IP virtuelle interne puis l’URL d’une application dans la mesure où est ASE : [*nom de site*]. [*sous-domaine spécifié lors de la création de l’équipement auxiliaire de direction*]   
    Après avoir sélectionné vos pages ASP lors de la création de l’équipement de direction auxiliaire vous verrez le sous-domaine mise à jour sous le **nom**

## <a name="createplan"></a>Créer un plan de Service d’application

Lorsque vous créez un plan de Service de l’application dans un environnement de Service d’application, votre choix de travailleur est différents, qu’il n’y a aucun travailleur partagé dans un équipement de direction auxiliaire.  Les travailleurs, que vous devez utiliser sont ceux qui ont été alloués pour l’équipement de direction auxiliaire par l’administrateur.  Cela signifie que pour créer un plan, vous devez disposer de plusieurs travailleurs affectés à votre pool de travail ASE que le nombre total d’instances sur l’ensemble de vos plans déjà dans ce pool de travail.  Si vous n’avez pas suffisamment les travailleurs dans votre pool de travail ASE pour créer votre plan, vous devez travailler avec votre administrateur ASE pour les ajouter.

Une autre différence avec les plans de Service de l’application hébergée par un environnement de Service d’application est le manque de sélection tarification.  Lorsque vous disposez d’un environnement de Service d’application, vous payez pour les ressources de calcul utilisés par le système et n’avez pas de frais supplémentaires pour les plans dans cet environnement.  Normalement, lorsque vous créez un plan de Service d’application vous sélectionnez un plan de tarification qui détermine votre facturation.  Un environnement de Service d’application est essentiellement un emplacement privé où vous pouvez créer du contenu.  Vous payez pour l’environnement et ne pas pour héberger votre contenu.

Les instructions suivantes indiquent comment créer un plan de Service de l’application lorsque vous créez une application web, comme expliqué dans la section précédente de ce didacticiel.

1. Cliquez sur **Créer un nouveau** dans l’interface utilisateur de sélection de plan et de fournir un nom pour votre plan comme vous le feriez normalement en dehors d’un équipement de direction auxiliaire.

2. Sélectionnez l’équipement auxiliaire de direction que vous souhaitez utiliser dans le sélecteur de votre emplacement.

    Un environnement de Service d’application étant essentiellement un emplacement de déploiement privé, il s’affiche sous emplacement. 

    ![][2]

    Après la sélection d’un équipement de direction auxiliaire dans le sélecteur de l’emplacement, la création de plan de Service d’application interface utilisateur met à jour.  L’emplacement affiche désormais le nom du système de l’ASE et la zone dans, et le sélecteur de plan de tarification est remplacé par un sélecteur de pool de travail.  

    ![][3]

### <a name="selecting-a-worker-pool"></a>Sélection d’un pool de travail

Normalement dans le Service d’application Azure et en dehors d’un environnement de Service d’application, il y a 3 tailles de calcul qui sont disponibles avec la sélection d’un plan de prix dédié.  De la même manière, pour un équipement de direction auxiliaire vous pouvez définir jusqu'à 3 pools des travailleurs et spécifier la taille de calcul utilisé pour ce pool de travail.  Ce que cela signifie pour les locataires de l’équipement de direction auxiliaire est qu’au lieu de sélectionner un plan de tarification avec taille de calcul pour votre plan de Service de l’application, si vous sélectionnez ce qu’on appelle un *pool de travail*.  

La sélection de pool de travail l’interface utilisateur affiche la taille de calcul utilisée pour ce pool de travail sous le nom.  La quantité disponible fait référence pour calcule combien les instances sont disponibles pour une utilisation dans ce pool.  Le pool total peut-être en fait avoir plusieurs instances à ce nombre, mais cette valeur fait référence à combien n’est tout simplement pas en cours d’utilisation.  Si vous avez besoin ajuster votre environnement de Service d’application pour ajouter des ressources de calcul plus voir la [configuration de votre environnement de Service d’application](app-service-web-configure-an-app-service-environment.md).

![][4]

Dans cet exemple, vous consultez uniquement deux pools de travail disponibles. C’est parce que l’administrateur ASE alloué uniquement des hôtes dans ces pools de deux travailleur.  La troisième s’inscrive lorsqu’il existe des ordinateurs virtuels alloués dans celui-ci.  

## <a name="after-web-app-creation"></a>Après la création d’applications web

Il existe quelques considérations pour exécuter des applications web et gérer des plans de Service de l’application dans un équipement de direction auxiliaire qui doivent être prises en compte.  

Comme indiqué précédemment, le propriétaire de l’équipement auxiliaire de direction est responsable de la taille du système et par conséquent ils sont également chargés de veiller à ce qu’il y a une capacité suffisante pour héberger les plans de Service de l’application souhaitées. S’il n’y a aucune disposition des employés, vous ne serez pas en mesure de créer votre plan de Service de l’application.  C’est également la valeur True pour l’évolution verticale de votre application web.  Si vous avez besoin de davantage d’instances vous devrez obtenir votre administrateur d’environnement de Service d’application pour ajouter d’autres travailleurs.

Après la création de votre application web et Service de l’application, il est conseillé de mettre à l’échelle.  Dans un équipement de direction auxiliaire, vous devez toujours avoir au moins 2 instances de votre plan de Service d’application de la tolérance de panne pour vos applications.  Mise à l’échelle d’un plan de Service de l’application dans un équipement de direction auxiliaire est le même normalement via le plan de Service d’application l’interface utilisateur.  Pour plus d’informations sur la mise à l’échelle, [la mise à l’échelle d’une application web dans un environnement de Service d’application](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/
