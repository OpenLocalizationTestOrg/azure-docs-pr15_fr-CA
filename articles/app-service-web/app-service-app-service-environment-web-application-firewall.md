<properties 
    pageTitle="Configuration d’un pare-feu pour applications Web (WAF) pour un environnement de Service d’application" 
    description="Apprenez à configurer un pare-feu pour applications web devant votre environnement de Service d’application." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configuration d’un pare-feu pour applications Web (WAF) pour un environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Web pare-feu d’application comme les [WAF Barracuda pour Azure](https://www.barracuda.com/programs/azure) qui est disponible dans l’aide [d’Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) sécuriser que vos applications web en inspectant le trafic web afin de bloquer les injections SQL, Cross-Site Scripting, les téléchargements de logiciels malveillants & application DDoS et autres attaques de trafic entrant. Il inspecte également les réponses à partir des serveurs web de back-end de Data Loss Prevention (DLP). Combiné à l’isolement et la mise à l’échelle supplémentaires fournies par les environnements de Service d’application, il fournit un environnement idéal pour les applications web critiques hôte professionnels qui devront supporter les requêtes malveillantes et le volume de trafic élevé.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Programme d’installation ##
Pour ce document, nous allons configurer notre environnement de Service d’application derrière la charge plusieurs équilibrée des instances de Barracuda WAF afin que seul le trafic provenant du WAF peut atteindre l’environnement de Service d’application et il ne sera pas accessible à partir de la zone DMZ. Nous devons également Azure Traffic Manager devant nos instances Barracuda WAF pour équilibrer les charges sur les régions et les centres de données Azure. Un diagramme de haut niveau de la configuration ressemblerait à ce qui est indiqué ci-dessous.

![Architecture][Architecture] 

> Remarque : Avec l’introduction de la [prise en charge de l’ILB d’environnement de Service d’application](app-service-environment-with-internal-load-balancer.md), vous pouvez configurer l’équipement de direction auxiliaire pour être inaccessibles à partir de la zone DMZ et n’est disponible que pour le réseau privé. 

## <a name="configuring-your-app-service-environment"></a>Configuration de votre environnement de Service d’application ##
Pour configurer un environnement de Service d’application voir [notre documentation](app-service-web-how-to-create-an-app-service-environment.md) sur le sujet. Une fois que vous disposez d’un environnement de Service d’application créé, vous pouvez créer des [Applications Web](app-service-web-overview.md), les [Applications d’API](../app-service-api/app-service-api-apps-why-best-platform.md) et les [Applications Mobile](../app-service-mobile/app-service-mobile-value-prop.md) dans cet environnement qui sera être protégé derrière le WAF nous configurer dans la section suivante.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configuration de votre Service de Cloud de WAF Barracuda ##
Barracuda possède un [article détaillé](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sur le déploiement de ses WAF sur une machine virtuelle dans Azure. Mais dans la mesure où la redondance et nous pas introduire un point de défaillance unique, que vous souhaitez déployer au moins 2 WAF instance ordinateurs virtuels dans le même Service de Cloud lorsqu’ils suivent ces instructions.

### <a name="adding-endpoints-to-cloud-service"></a>Ajout de points de terminaison pour le Service en nuage ###
Une fois que vous avez 2 ou plusieurs instances de WAF VM dans votre Service Cloud [Azure portal](https://portal.azure.com/) permet d’ajouter des points de terminaison HTTP et HTTPS sont utilisées par votre application, comme illustré dans l’image ci-dessous.

![Configurer le point de terminaison][ConfigureEndpoint]

Si vos applications utilisent des autres points de terminaison, veillez à ajouter ceux à cette liste. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configuration de Barracuda WAF via son portail de gestion ###
Barracuda WAF utilise TCP Port 8000 pour la configuration par l’intermédiaire de son portail de gestion. Puisque nous avons plusieurs instances des ordinateurs virtuels WAF vous devrez répéter cette procédure pour chaque instance de la machine virtuelle. 


> Remarque : Une fois que vous avez terminé WAF, supprimez le point de terminaison TCP/8000 tous vos ordinateurs virtuels WAF de sécuriser votre WAF.

Ajouter le point de terminaison de gestion, comme illustré dans l’image ci-dessous pour configurer votre WAF Barracuda.

![Ajouter le point de terminaison de gestion][AddManagementEndpoint]
 
Utilisez un navigateur pour accéder à l’extrémité de la gestion de votre Service Cloud. Si votre Service Cloud est appelée test.cloudapp.net, vous accédez à ce point de terminaison en accédant à http://test.cloudapp.net:8000. Vous devriez voir une page de connexion comme ci-dessous que vous pouvez vous connecter à l’aide des informations d’identification que vous avez spécifié dans la phase d’installation WAF VM.

![Page de connexion de gestion][ManagementLoginPage]

Une fois votre connexion doit s’afficher un tableau de bord que celui dans l’image ci-dessous présente des statistiques de base sur la protection WAF.

![Tableau de bord de gestion][ManagementDashboard]

Cliquez sur l’onglet Services vous permet de configurer votre WAF pour les services qu’il protège. Pour plus d’informations sur la configuration de votre WAF Barracuda, vous pouvez consulter [leur documentation](https://techlib.barracuda.com/waf/getstarted1). Dans l’exemple ci-dessous, une application Web de Azure desservant le trafic HTTP et HTTPS a été configurée.

![Ajouter des Services de gestion][ManagementAddServices]

> Remarque : Selon la configuration de vos applications et quelles fonctionnalités sont utilisées dans votre environnement de Service d’application, vous devrez de transmettre le trafic pour le protocole TCP ports autres que 80 et 443, par exemple, si vous avez le programme d’installation IP SSL pour une application Web. Pour une liste des ports réseau utilisés dans les environnements de Service d’application, reportez-vous à la section Ports réseau de [la documentation du trafic entrant de contrôle](app-service-app-service-environment-control-inbound-traffic.md) .

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configuration du Gestionnaire de trafic Microsoft Azure (facultatif) ##
Si votre application est disponible dans plusieurs régions, vous ne souhaitez pas charger les équilibrer derrière [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Pour ce faire, vous pouvez ajouter un point de terminaison dans [Azure portal classique](https://manage.azure.com) à l’aide le nom de Service Cloud pour votre WAF dans le profil de Traffic Manager, comme indiqué dans l’image ci-dessous. 

![Point de terminaison de Traffic Manager][TrafficManagerEndpoint]

Si votre application requiert une authentification, assurez-vous que vous avez une ressource qui ne nécessite pas d’authentification pour le Gestionnaire de trafic avec la commande ping pour connaître la disponibilité de votre application. Vous pouvez configurer l’URL sous la section Configurer dans [Azure portal classique](https://manage.azure.com) comme indiqué ci-dessous.

![Configurer le Gestionnaire de trafic][ConfigureTrafficManager]

Pour transférer les commandes ping Traffic Manager à partir de votre WAF à votre application, vous devez d’installation traductions du site Web sur votre WAF Barracuda pour transférer le trafic vers votre application, comme indiqué dans l’exemple ci-dessous.

![Traduction de site Web][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Sécurisation du trafic vers un environnement de Service d’application à l’aide de groupes de sécurité réseau (NSG)##
Suivez la [documentation du trafic entrant de contrôle](app-service-app-service-environment-control-inbound-traffic.md) pour plus d’informations sur la restriction du trafic de votre environnement de Service d’application de la WAF uniquement à l’aide de l’adresse de votre Service Cloud. Voici un exemple de commande Powershell pour effectuer cette tâche pour le port TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Remplacez le SourceAddressPrefix avec l’adresse IP virtuelle (VIP) du Service de Cloud de votre WAF.

> Remarque : L’adresse IP virtuelle de votre Service Cloud change lorsque vous supprimez et recréez le Service en nuage. Veillez à mettre à jour l’adresse IP du groupe de ressources de réseau une fois que vous le faire. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
