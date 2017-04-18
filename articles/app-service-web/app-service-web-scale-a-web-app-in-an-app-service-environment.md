<properties 
    pageTitle="Comment faire pour mettre à l’échelle une application dans un environnement de Service d’application" 
    description="Mise à l’échelle d’une application dans un environnement de Service d’application" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="stefsch" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="ccompy"/>

# <a name="scaling-apps-in-an-app-service-environment"></a>Mise à l’échelle des applications dans un environnement de Service d’application #

Dans le Service d’application Azure, il existe généralement trois choses que vous pouvez mettre à l’échelle :

- plan de tarification
- taille du travailleur 
- nombre d’instances.

Dans un équipement de direction auxiliaire il n’est pas nécessaire de sélectionner ou de modifier le plan de tarification.  En termes de fonctionnalités, il est déjà à une prime de prix au niveau de fonctionnalité.  

En ce qui concerne les tailles du travailleur, l’administrateur ASE peut affecter la taille de la ressource de calcul à utiliser pour chaque pool de travail.  Cela signifie que vous pouvez avoir 1 du Pool de travail avec les ressources de calcul P4 et de travailleur Pool 2 avec P1 calculer les ressources, si vous le souhaitez.  Ils n’ont pas ordre de taille.  Pour les détails autour de la taille et de leur prix voir le document ici [Une tarification Service Azure App][AppServicePricing].  Cela laisse les options de mise à l’échelle pour les applications web et des programmes de Service d’application dans un environnement de Service d’application d’être :

- sélection de pool de travail
- nombre d’instances

La modification de des éléments s’effectue par le biais de l’interface utilisateur appropriée pour votre ASE hébergé de programmes de Service d’application.  

![][1]

Vous ne pouvez pas évoluer vos pages ASP dépasse le nombre de ressources de calcul disponible dans le pool de travail figurant dans vos pages ASP.  Si vous devez calculer les ressources dans ce pool de travail, vous devez obtenir de votre administrateur ASE pour les ajouter.  Pour des informations relatives à la reconfiguration de votre équipement de direction auxiliaire, lisez les informations ici : [Comment faire pour configurer un environnement de Service d’application][HowtoConfigureASE].  Vous pouvez également tirer parti des fonctionnalités autoscale ASE pour ajouter la capacité en fonction de la planification ou les mesures.  Pour obtenir plus de détails sur la configuration d’échelle pour l’équipement de direction auxiliaire environnement lui-même voir [Comment faire pour configurer l’échelle dans un environnement de Service d’application][ASEAutoscale].

Vous pouvez créer une application plusieurs plans de service à l’aide de ressources de calcul à partir des registres de travail différents, ou vous pouvez utiliser le même pool de travail.  Par exemple si vous disposez de ressources de calcul disponible (10) 1 du Pool de travail, vous pouvez choisir de créer un plan de service d’application à l’aide de ressources de calcul (6) et envisagez d’un deuxième service d’application qui utilise (4) des ressources de calcul.

### <a name="scaling-the-number-of-instances"></a>Le nombre d’instances de mise à l’échelle ###

Lorsque vous créez votre application web dans un environnement de Service d’application qu’il démarre avec le 1 instance.  Vous pouvez ensuite évoluer à des instances supplémentaires pour fournir des ressources de calcul supplémentaires pour votre application.   

Si votre équipement de direction auxiliaire a une capacité suffisante c’est assez simple.  Vous accédez à votre Plan de Service de App qui contient les sites que vous souhaitez évoluer et sélectionnez échelle.  L’interface utilisateur dans laquelle vous pouvez définir l’échelle de vos pages ASP manuellement ou configurer les règles de l’échelle de vos pages ASP s’ouvre.  À échelle manuellement votre application définie simplement ***l’échelle par*** à ***un nombre d’instances de saisies manuellement***.  À partir d’ici faites glisser le curseur vers la quantité souhaitée ou saisissez-le dans la zone à côté du curseur.  

![][2] 

Les règles de l’échelle pour une page ASP dans un équipement de direction auxiliaire fonctionnent de la même manière comme ils le font normalement.  Vous pouvez sélectionner le ***Pourcentage de l’UC*** sous ***échelle par*** et créer des règles de l’échelle de vos pages ASP basée sur un pourcentage de l’UC, ou vous pouvez créer des règles plus complexes à l’aide des ***règles de planification et de performances***.  Pour voir plus de détails sur la configuration d’échelle utiliser le guide ici [mettre à l’échelle une application dans Azure Application Service][AppScale]. 


### <a name="worker-pool-selection"></a>Sélection de Pool de travail ###

Comme indiqué précédemment, la sélection de pool du travailleur est accessible à partir de l’interface utilisateur de ASP.  Ouvrez la lame du script ASP que vous souhaitez mettre à l’échelle, puis sélectionnez le pool de travail.  Vous verrez tous les pools de travail que vous avez configurée dans votre environnement de Service d’application.  Si vous avez un seul travailleur pool puis vous verra uniquement l’un pool répertorié.  Pour modifier le pool de travail est de vos pages ASP dans, sélectionnez simplement le pool de travail que vous souhaitez que votre Plan de Service App pour atteindre.  

![][3]

Avant de déplacer vos pages ASP à partir du pool d’un travailleur à un autre, il est important de vous assurer de qu'avoir une capacité suffisante pour vos pages ASP.  Dans la liste des pools de travailleur, non seulement le nom de pool de travail n’est répertorié mais vous pouvez également consulter le nombre de travailleurs sont disponibles dans ce pool de travail.  Assurez-vous qu’il existe suffisamment d’instances disponibles pour contenir votre Plan de Service d’application.  Si vous devez calculer plus de ressources dans le pool de travail que vous souhaitez déplacer vers, puis obtenir votre administrateur ASE pour les ajouter.  

> [AZURE.NOTE] Déplacement de qu'une page ASP à partir du pool d’un travail entraînera à froid démarre des applications que ASP.  Cela peut entraîner des requêtes à exécution lente que votre application est à froid démarré sur les nouvelles ressources de calcul.  Le démarrage à froid peut être évité en utilisant l' [application préchauffage capacité] [ AppWarmup] dans le Service d’application Azure.  Le module de l’initialisation de l’Application décrit dans l’article fonctionne également pour un démarrage à froid, car le processus d’initialisation est également appelé lorsque les applications sont à froid démarré sur nouvelles ressources de calcul. 

## <a name="getting-started"></a>Mise en route

Pour vous familiariser avec les environnements de Service d’application, reportez-vous à la section [Comment pour créer une application Service environnement][HowtoCreateASE]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[AppWarmup]: http://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
