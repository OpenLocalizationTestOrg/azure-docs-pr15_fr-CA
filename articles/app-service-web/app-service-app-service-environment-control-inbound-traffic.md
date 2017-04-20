<properties 
    pageTitle="Comment faire pour contrôler le trafic entrant vers un environnement de Service d’application" 
    description="Découvrez comment configurer des règles de sécurité de réseau pour contrôler le trafic entrant vers un environnement de Service d’application." 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2016" 
    ms.author="stefsch"/>   

# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Comment faire pour contrôler le trafic entrant vers un environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Un environnement de Service d’application peut être créé dans **un** un réseau virtuel du Gestionnaire de ressources Azure, **ou** un déploiement classique de modèle de [réseau virtuel][virtualnetwork].  Un nouveau réseau virtuel et un nouveau sous-réseau peuvent être définis au moment de la que création d’un environnement de Service d’application.  Vous pouvez également un environnement de Service d’application peuvent être créé dans un réseau virtuel déjà existant et du sous-réseau existant.  Avec une modification récente de juin 2016, ASEs peuvent désormais être déployées dans les réseaux virtuels qui utilisent (c'est-à-dire les plages d’adresses publiques ou des espaces d’adressage RFC1918 adresses privées).  Pour plus d’informations sur la création d’un environnement de Service d’application, consultez [comment créer un environnement de Service d’application][HowToCreateAnAppServiceEnvironment].

Un environnement de Service d’application doit toujours être créé dans un sous-réseau car un sous-réseau fournit une limite de réseau qui peut être utilisée pour verrouiller le trafic entrant derrière les périphériques situés en amont et de services tels que le trafic HTTP et HTTPS est accepté uniquement à des adresses IP en amont.

Le trafic réseau entrant et sortant sur un sous-réseau est contrôlé à l’aide d’un [groupe de sécurité réseau][NetworkSecurityGroups]. Contrôle du trafic entrant requiert la création de règles de sécurité de réseau dans un groupe de sécurité du réseau et en assignant la sécurité du réseau de groupe du sous-réseau contenant de l’environnement de Service d’application.

Une fois un groupe de sécurité du réseau est affecté à un sous-réseau, le trafic entrant vers les applications dans l’environnement de Service d’application est autorisés/bloqués en fonction de l’autoriser et refuser des règles définies dans le groupe de sécurité du réseau.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="network-ports-used-in-an-app-service-environment"></a>Ports réseau utilisés dans un environnement de Service d’application ##
Avant de verrouiller le trafic réseau entrant à un groupe de sécurité du réseau, il est important de connaître l’ensemble des ports réseau requis et facultatifs utilisés par un environnement de Service d’application.  Fermer accidentellement désactivé le trafic sur certains ports peut entraîner une perte de fonctionnalités dans un environnement de Service d’application.

Voici une liste des ports utilisés par un environnement de Service d’application. Tous les ports sont **TCP**, sauf mention contraire clairement :

- 454 : **obligatoire le port** utilisé par l’infrastructure Azure pour la gestion et la maintenance des environnements de Service d’application via SSL.  Ne bloquent pas le trafic sur ce port.  Ce port est toujours lié à l’adresse IP virtuelle publique d’un équipement de direction auxiliaire.
- 455 : **obligatoire le port** utilisé par l’infrastructure Azure pour la gestion et la maintenance des environnements de Service d’application via SSL.  Ne bloquent pas le trafic sur ce port.  Ce port est toujours lié à l’adresse IP virtuelle publique d’un équipement de direction auxiliaire.
- 80 : port pour le trafic HTTP entrant aux applications en cours d’exécution dans l’application des programmes de Service dans un environnement de Service d’application par défaut.  Sur ASE compatible ILB, le port est lié à l’adresse ILB de l’équipement de direction auxiliaire.
- 443 : port pour le trafic SSL entrant aux applications en cours d’exécution dans l’application des programmes de Service dans un environnement de Service d’application par défaut.  Sur ASE compatible ILB, le port est lié à l’adresse ILB de l’équipement de direction auxiliaire.
- 21 : canal de contrôle FTP.  Ce port peut être bloqué en toute sécurité si FTP n’est pas utilisé.  Sur ASE compatible ILB, ce port peut être lié à l’adresse ILB pour ASE.
- 990 : canal de contrôle pour FTPS.  Ce port peut être bloqué en toute sécurité si FTPS n’est pas utilisé.  Sur ASE compatible ILB, ce port peut être lié à l’adresse ILB pour ASE.
- 10001-10020 : des canaux de données FTP.  Comme avec le canal de contrôle, ces ports peuvent être bloquées en toute sécurité si FTP n’est pas utilisé.  Sur ASE compatible ILB, ce port peut être lié à une adresse ILB de l’équipement de direction auxiliaire.
- 4016 : utilisé pour le débogage distant avec Visual Studio 2012.  Ce port peut être bloqué en toute sécurité si la fonctionnalité n’est pas utilisée.  Sur ASE compatible ILB, le port est lié à l’adresse ILB de l’équipement de direction auxiliaire.
- 4018 : utilisé pour le débogage distant avec Visual Studio 2013.  Ce port peut être bloqué en toute sécurité si la fonctionnalité n’est pas utilisée.  Sur ASE compatible ILB, le port est lié à l’adresse ILB de l’équipement de direction auxiliaire.
- 4020 : utilisé pour le débogage distant avec Visual Studio 2015.  Ce port peut être bloqué en toute sécurité si la fonctionnalité n’est pas utilisée.  Sur ASE compatible ILB, le port est lié à l’adresse ILB de l’équipement de direction auxiliaire.

## <a name="outbound-connectivity-and-dns-requirements"></a>Connectivité sortante et la configuration DNS requise ##
Dans un environnement de Service d’application fonctionne correctement, il requiert un accès sortant à différents points de terminaison. Une liste complète des points de terminaison externe utilisée par un équipement de direction auxiliaire est dans la section « Requis de connectivité réseau » de l’article de [Configuration réseau pour ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Les environnements de Service d’application nécessitent une infrastructure DNS valide configurée pour le réseau virtuel.  Si pour une raison quelconque, la configuration DNS est modifiée après la création d’un environnement de Service d’application, les développeurs peuvent forcer un environnement de Service d’application pour prélever la nouvelle configuration de DNS.  Déclencher un redémarrage d’environnement propagée à l’aide de l’icône « Redémarrer » situé en haut de la blade de gestion d’environnement de Service d’application dans [Azure portal] [ NewPortal] entraîne l’environnement prélever la nouvelle configuration de DNS.

Il est également recommandé que tous les serveurs DNS personnalisées sur le vnet le programme d’installation à l’avance avant de créer un environnement de Service d’application.  Si la configuration de DNS d’un réseau virtuel est modifiée pendant la création d’un environnement de Service d’application, qui entraîne l’échec de processus de création environnement de Service d’application.  De la même manière, si un serveur DNS personnalisé existe sur l’autre extrémité d’une passerelle VPN et le serveur DNS est inaccessible ou indisponible, le processus de création d’environnement de Service d’application échouera également.

## <a name="creating-a-network-security-group"></a>Création d’un groupe de sécurité du réseau ##
Pour plus d’informations sur la sécurité du réseau les groupes de travail, consultez les suivantes [des informations][NetworkSecurityGroups].  Met en surbrillance les ci-dessous tactile sur les détails des groupes de sécurité du réseau, en mettant l’accent sur la configuration et l’application d’un groupe de sécurité du réseau pour un sous-réseau contenant un environnement de Service d’application.

**Remarque :** Les groupes de sécurité de réseau peuvent être configurés sous forme graphique l’utilisation du [Portail Azure](https://portal.azure.com) ou par le biais de PowerShell d’Azure.

Les groupes de sécurité de réseau créés tout d’abord comme une entité autonome associée à un abonnement. Étant donné que les groupes de sécurité de réseau sont créés dans une région d’Azure, assurez-vous que le groupe de sécurité de réseau est créé dans la même région que l’environnement de Service d’application.

L’exemple suivant illustre la création d’un groupe de sécurité réseau :

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Une fois la création d’un groupe de sécurité du réseau, une ou plusieurs règles de sécurité de réseau sont ajoutés.  Dans la mesure où l’ensemble de règles peut changer avec le temps, il est recommandé d’espacer les le schéma de numérotation utilisé pour les priorités de la règle, pour faciliter l’insertion des règles supplémentaires au fil du temps.

L’exemple suivant montre une règle qui accorde explicitement l’accès aux ports de gestion nécessaires à l’infrastructure Azure pour gérer et maintenir un environnement de Service d’application.  Notez que tout le trafic de gestion au-dessus du SSL et sécurisé par certificats clients, afin que, même si les ports sont ouverts, ils sont inaccessibles par une entité autre que de l’infrastructure de gestion Azure.


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    

Lors du verrouillage de l’accès aux ports 80 et 443 pour « masquer » d’un environnement de Service d’application derrière les périphériques situés en amont ou des services, vous devez connaître l’adresse IP en amont.  Par exemple, si vous utilisez un pare-feu pour applications web (WAF), le WAF aura sa propre adresse IP (ou adresse) qu’il utilise lorsque le trafic de proxy dans un environnement de Service d’application en aval.  Vous devez utiliser cette adresse IP dans le paramètre *SourceAddressPrefix* d’une règle de sécurité de réseau.

Dans l’exemple ci-dessous, le trafic entrant à partir d’une adresse IP en amont est explicitement autorisé.  L' adresse *1.2.3.4* est utilisé comme espace réservé pour l’adresse IP d’un WAF en amont.  Modifiez la valeur pour le correspond à l’adresse utilisée par votre périphérique en amont ou d’un service.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
Si vous le souhaitez est prise en charge FTP, les règles suivantes peuvent servir en tant que modèle pour accorder l’accès au port de contrôle FTP et aux données des ports de canal.  Dans la mesure où le protocole FTP est un protocole avec état, vous n’est peut-être pas capable de router le trafic FTP via un périphérique de pare-feu ou de proxy HTTP/HTTPS traditionnel.  Dans ce cas, vous devrez la *SourceAddressPrefix* une valeur différente affectée à - par exemple la plage d’adresses IP de développeur ou les machines de déploiement sur FTP clients sont en cours d’exécution. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Remarque :** la plage de ports de canal de données peut-être changer au cours de la période d’aperçu.)

Si le débogage distant avec Visual Studio est utilisé, les règles suivantes montrent comment accorder l’accès.  Il existe une règle distincte pour chaque version prise en charge de Visual Studio dans la mesure où chaque version utilise un port différent pour le débogage distant.  Comme avec l’accès FTP, le trafic de débogage distant ne peut pas traverser correctement un WAF traditionnel ou d’un périphérique proxy.  La *SourceAddressPrefix* à la place peut être définie qu’à la plage d’adresses IP des ordinateurs des développeurs Visual Studio en cours d’exécution.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Affectation d’un groupe de sécurité de réseau d’un sous-réseau ##
Un groupe de sécurité du réseau a une règle de sécurité par défaut qui refuse l’accès à tout le trafic externe.  Combiner les règles de sécurité de réseau décrites ci-dessus et la règle de sécurité par défaut bloque le trafic entrant, le résultat est ce que le trafic à partir de l’adresse source plages associées à une action *d’autorisation* sera en mesure d’envoyer du trafic vers les applications en cours d’exécution dans un environnement de Service d’application.

Une fois un groupe de sécurité réseau est rempli avec des règles de sécurité, il doit être affectée au sous-réseau contenant de l’environnement de Service d’application.  La commande affectation fait référence à la fois le nom du réseau virtuel dans lequel l’environnement de Service d’application réside, ainsi que le nom du sous-réseau dans lequel l’environnement de Service d’application a été créé.  

L’exemple ci-dessous montre un groupe de sécurité réseau assigné à un sous-réseau et un réseau virtuel :


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Une fois que l’affectation de groupe de sécurité réseau réussit (l’assignation est une opération longue et peut prendre de quelques minutes), seul le trafic entrant, *Autoriser* règles de correspondance atteindra avec succès des applications dans l’environnement de Service d’application.

Par souci d’exhaustivité, l’exemple suivant montre comment supprimer et donc associer à annuler le groupe de sécurité réseau à partir du sous-réseau :


    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Considérations spéciales pour IP-SSL explicite ##
Si une application est configurée avec un IP-SSL explicite adresse (applicable aux ASEs qui ont seulement un VIP public), au lieu d’utiliser l’adresse IP par défaut de l’environnement de Service d’application, à la fois HTTP et HTTPS les flux de trafic sur le sous-réseau sur un autre ensemble de ports autres que les ports 80 et 443.

Vous trouverez la paire individuelle de ports utilisés par chaque adresse IP à SSL dans l’interface utilisateur du portail à partir de lames de l’environnement Service application détails UX.  Sélectionnez « tous les paramètres »--> « Adresses IP ».  La lame de « Adresses IP » affiche un tableau de toutes les adresses IP-SSL explicitement configurés pour l’environnement de Service d’application, ainsi que de la paire de port spécial qui est utilisée pour router le trafic HTTP et HTTPS associé à chaque adresse IP à SSL.  C’est cette paire port qui doit être utilisé pour les paramètres de DestinationPortRange lors de la configuration des règles d’un groupe de sécurité du réseau.

Lorsqu’une application sur un équipement de direction auxiliaire est configurée pour utiliser IP-SSL, les clients externes ne seront affiche pas et ne doivent pas vous inquiéter sur le mappage de la paire port spécial.  Le trafic vers les applications iront normalement à l’adresse IP à SSL configuré.  La traduction de la paire de port spécial se produit automatiquement en interne pendant le dernier tronçon de routage du trafic sur le sous-réseau contenant de l’équipement de direction auxiliaire. 

## <a name="getting-started"></a>Mise en route

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application][IntroToAppServiceEnvironment]

Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour plus d’informations sur les applications de se connecter en toute sécurité aux ressources de serveur principal à partir d’un environnement de Service d’application, reportez-vous à la section [connexion en toute sécurité aux ressources back-end à partir d’un environnement de Service d’application][SecurelyConnecttoBackend]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[SecurelyConnecttoBackend]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-securely-connecting-to-backend-resources/
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->
 
