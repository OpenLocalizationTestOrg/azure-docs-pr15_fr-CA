<properties 
    pageTitle="Comment faire pour créer un environnement de Service d’application" 
    description="Description de flux de création pour les environnements de service d’application" 
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
    ms.date="09/22/2016" 
    ms.author="ccompy"/>

# <a name="how-to-create-an-app-service-environment"></a>Comment faire pour créer un environnement de Service d’application #

### <a name="overview"></a>Vue d’ensemble ###

Les environnements de Service d’application (ASE) sont une option de service Premium du Service application Azure qui fournit une fonctionnalité améliorée de configuration qui n’est pas disponible dans les tampons mutualisées.  La fonctionnalité ASE déploie essentiellement le Service d’application Azure dans les réseau virtuel du client.  Pour obtenir une compréhension des fonctionnalités proposées par les environnements de Service application lire [ce qu’est un environnement de Service d’application] [ WhatisASE] documentation.

### <a name="before-you-create-your-ase"></a>Avant de créer votre ASE ###

Il est important d’être conscient des choses que vous ne pouvez pas modifier.  Les aspects que vous ne pouvez pas modifier sur votre équipement de direction auxiliaire après sa création sont les suivantes :

- Emplacement
- Abonnement
- Groupe de ressources
- VNet utilisé
- Sous-réseau utilisé 
- Taille de sous-réseau

Lors de l’enlèvement d’une VNet et en spécifiant un sous-réseau, assurez-vous qu’il est assez grand pour s’adapter à une croissance future.  

### <a name="creating-an-app-service-environment"></a>Création d’un environnement de Service d’application ###

Il existe deux façons d’accéder à l’interface utilisateur de création ASE.  Il peut être trouvé en recherchant dans le marché Azure ***Environnement de Service d’application*** ou par l’intermédiaire de nouveau -> Web + Mobile -> environnement de Service d’application.  Pour créer un équipement de direction auxiliaire :

1. Indiquez le nom de votre équipement de direction auxiliaire.  Le nom qui est spécifié pour l’équipement de direction auxiliaire servira pour les applications créées dans l’équipement de direction auxiliaire.  Si le nom de l’équipement de direction auxiliaire est appsvcenvdemo le nom de sous-domaine serait. *appsvcenvdemo.p.azurewebsites.net*.  Si vous avez créé une application nommée *mytestapp* pour la donc il serait adressable à *mytestapp.appsvcenvdemo.p.azurewebsites.net*.  Vous ne pouvez pas utiliser un espace blanc le nom de votre équipement de direction auxiliaire.  Si vous utilisez des caractères en majuscules dans le nom, le nom de domaine sera la totale version en minuscules du même nom.  Si vous utilisez un ILB puis le nom de votre équipement de direction auxiliaire n’est pas utilisé dans votre sous-domaine mais plutôt explicitement lors de la création de l’équipement de direction auxiliaire

    ![][1]

2. Sélectionnez votre abonnement.  L’abonnement utilisé pour votre équipement de direction auxiliaire est également celui qui seront créées avec toutes les applications dans cette ASE.  Vous ne pouvez pas placer votre équipement de direction auxiliaire dans un VNet qui se trouve dans un autre abonnement

3. Sélectionnez ou spécifiez un nouveau groupe de ressources.  Le groupe de ressources utilisé pour votre équipement de direction auxiliaire doit être celle qui est utilisée pour votre VNet.  Si vous sélectionnez un VNet existant la sélection du groupe de ressources pour votre ASE sera mis à jour pour refléter celle de votre VNet.

    ![][2]

4. Effectuez vos sélections de réseau virtuel et l’emplacement.  Vous pouvez choisir de créer un nouveau VNet ou sélectionnez un VNet existant.  Si vous sélectionnez un nouveau VNet vous pouvez spécifier un nom et un emplacement. La nouvelle VNet auront le 192.268.250.0/23 plage adresse et un sous-réseau nommé **par défaut** qui est défini en tant que 192.168.250.0/24.  Vous pouvez aussi simplement sélectionner un classique préexistant ou VNet le Gestionnaire de ressources.  La sélection du Type de l’adresse IP virtuelle détermine si votre équipement de direction auxiliaire est accessible directement à partir d’internet (externe) ou si elle utilise un équilibreur de charge interne (ILB).  Pour en savoir plus sur les lire [à l’aide d’un équilibreur de charge interne avec un environnement de Service d’application][ILBASE].  Si vous sélectionnez un type de protocole VIP d’externe vous pouvez sélectionner le nombre d’adresses IP externe du système est créé avec des fins IPSSL.  Si vous sélectionnez interne puis vous devez spécifier le sous-domaine qui utilise votre équipement de direction auxiliaire.  ASEs peuvent être déployés dans des réseaux virtuels qui utilisent les *deux* plages d’adresses publiques, espaces d’adressage *ou* RFC1918 () adresses privées).  Pour utiliser un réseau virtuel avec une plage d’adresses publiques, vous devez créer le VNet à l’avance.  Lorsque vous sélectionnez un VNet existant, vous devrez créer un nouveau sous-réseau lors de la création de l’équipement de direction auxiliaire.  **Vous ne pouvez pas utiliser un sous-réseau prédéfini dans le portail.  Si vous créez votre équipement de direction auxiliaire à l’aide d’un modèle de gestionnaire de ressources, vous pouvez créer un équipement de direction auxiliaire à un sous-réseau existant.**  Pour créer un équipement de direction auxiliaire à partir d’une utilisation des modèles ici, les informations de [Création d’un environnement de Service d’application à partir du modèle] [ ILBAseTemplate] et là, la [Création d’un environnement de Service d’application ILB de modèle][ASEfromTemplate].

### <a name="details"></a>Détails ###

Un équipement de direction auxiliaire est créé avec frontal 2 et 2 travailleurs.  Le frontal agissent comme les points de terminaison HTTP/HTTPS et acheminer le trafic vers les travailleurs qui sont les rôles qui hébergent vos applications.   Vous pouvez ajuster la quantité après la création de l’ASE et pouvez même définir des règles d’échelle sur les pools de ressources.  Pour plus d’informations sur la mise à l’échelle manuelle, de gestion et de surveillance d’un environnement de Service d’application ici : [Comment faire pour configurer un environnement de Service d’application][ASEConfig] 

Uniquement l’un équipement de direction auxiliaire peut exister dans le sous-réseau utilisé par l’équipement de direction auxiliaire.  Le sous-réseau ne peut pas être utilisé pour autre chose que l’équipement de direction auxiliaire

### <a name="after-app-service-environment-creation"></a>Après la création d’un environnement de Service d’application ###

Après la création de l’équipement de direction auxiliaire, vous pouvez ajuster les paramètres :

- Quantité de frontal (minimum : 2)
- Quantité de travailleurs (minimum : 2)
- Quantité d’adresses IP disponibles pour IP SSL
- Calculer les tailles de ressources utilisées par le frontal ou les travailleurs (la taille minimale du Front-End est P2)


Il n’y a plus de détails dans le manuel Mise à l’échelle, gestion et surveillance des environnements de Service d’application ici : [Comment faire pour configurer un environnement de Service d’application][ASEConfig] 

Pour plus d’informations sur autoscaling, il existe un guide ici : [Comment faire pour configurer l’échelle dans un environnement de Service d’application][ASEAutoscale]

Il existe des dépendances supplémentaires qui ne sont pas disponibles pour la personnalisation de la base de données et le stockage.  Celles-ci sont gérées par Azure et sont fournis avec le système.  Le système de stockage prend en charge jusqu'à 500 Go pour tout l’environnement du Service application et la base de données est ajusté par Azure selon les besoins de l’échelle du système.


## <a name="getting-started"></a>Mise en route
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction aux environnements de Service d’application][WhatisASE]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/ 
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
[ILBAseTemplate]: http://azure.microsoft.com/documentation/templates/201-web-app-ase-create/
[ASEfromTemplate]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-create-ilb-ase-resourcemanager/
