<properties
    pageTitle="Création et utilisation d’un équilibreur de charge interne avec un environnement de Service d’application | Microsoft Azure"
    description="Création et utilisation d’un équipement de direction auxiliaire avec une ILB"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>

# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>À l’aide d’un équilibreur de charge interne avec un environnement de Service d’application #

La fonctionnalité Environments(ASE) de Service d’application est une option de service Premium du Service application Azure qui fournit une fonctionnalité améliorée de configuration qui n’est pas disponible dans les tampons mutualisées.  Essentiellement, la fonctionnalité ASE déploie le Service d’application Azure dans votre Network(VNet) virtuel Azure.  Pour obtenir une compréhension des fonctionnalités proposées par les environnements de Service application lire [ce qu’est un environnement de Service d’application] [ WhatisASE] documentation.  Si vous ne connaissez pas les avantages de fonctionnement dans un VNet lire le [Forum aux questions sur réseau virtuel Azure][virtualnetwork].  


## <a name="overview"></a>Vue d’ensemble ##


Un équipement de direction auxiliaire peut être déployé avec un point de terminaison accessible internet ou une adresse IP dans votre VNet.  Pour définir l’adresse IP à une adresse de VNet que vous avez besoin pour déployer votre ASE avec un Balancer(ILB) de charge interne.  Lorsque votre équipement de direction auxiliaire est configuré avec un ILB de vous fournissez :

- votre propre domaine ou un sous-domaine.  Pour simplifier, ce document suppose le sous-domaine, mais vous pouvez le configurer dans les deux cas.  
- le certificat utilisé pour le protocole HTTPS
- Gestion du service DNS pour le sous-domaine.  


En retour, vous pouvez faire des choses telles que :

- héberger des applications intranet, comme les applications métier, en toute sécurité dans le nuage à laquelle vous accédez via un Site à Site ou ExpressRoute VPN
- applications d’hôte dans le nuage qui ne figurent pas dans les serveurs DNS publics
- créer des applications de serveur principal internet isolé qui peuvent intégrer en toute sécurité vos applications front-end


#### <a name="disabled-functionality"></a>Fonctionnalités désactivées ####

Il y a des choses que vous ne pouvez pas faire lors de l’utilisation d’un équipement de direction auxiliaire ILB.  Ces éléments sont les suivants :

- à l’aide de IPSSL
- attribution d’adresses IP à des applications spécifiques
- achat et à l’aide d’un certificat avec une application via le portail.  Vous pouvez bien entendu toujours obtenir les certificats directement auprès d’une autorité de certificat et l’utiliser avec vos applications, pas par le biais du portail Azure.


## <a name="creating-an-ilb-ase"></a>Création d’un équipement de direction auxiliaire ILB ##

Création d’un équipement de direction auxiliaire ILB n’est pas très différent de la création d’un équipement de direction auxiliaire normalement.  Pour plus d’informations sur la création d’un équipement de direction auxiliaire, lisez [comment créer un environnement de Service d’application][HowtoCreateASE].  Le processus de création d’ILB ASE est identique entre la création d’un VNet lors de la création de l’équipement de direction auxiliaire ou en sélectionnant un VNet existant.  Pour créer un équipement de direction auxiliaire ILB : 

1.  Dans le portail Azure, sélectionnez **Nouveau -> Web + Mobile -> environnement de Service d’application**
2.  Sélectionnez votre abonnement
3.  Sélectionnez ou créez un groupe de ressources
4.  Sélectionnez ou créez un VNet
5.  Créer un sous-réseau, si vous sélectionnez un VNet
6.  Sélectionnez **virtuel / l’emplacement du réseau-> Configuration de VNet** et de définir le Type de l’adresse IP virtuelle interne
7.  Fournir le nom de sous-domaine (il s’agit du sous-domaine utilisé pour les applications créées dans cette ASE)
8.  Cliquez sur Ok, puis créer


![][1]


Au sein de la lame de réseau virtuel, il existe une option de Configuration de VNet.  Ce vous permet de sélectionner entre un VIP externe ou de des VIP internes.  La valeur par défaut est externe.  Si vous les avez définis externe votre ASE utilisera un VIP accessibles d’internet.  Si vous sélectionnez interne, votre ASE sera configuré avec une ILB sur une adresse IP dans votre VNet.  


Après avoir sélectionné interne, la possibilité d’ajouter des adresses IP à votre équipement de direction auxiliaire est supprimée, et au lieu de cela, vous avez besoin fournir le sous-domaine de l’équipement de direction auxiliaire.  Dans un équipement de direction auxiliaire avec une adresse IP virtuelle externe, le nom de l’équipement de direction auxiliaire est utilisé dans le sous-domaine pour les applications créées dans ce ASE.  
Si votre équipement de direction auxiliaire a été appelée ***contosotest*** et votre application dans la mesure où ASE a été appelée ***mytest*** puis le sous-domaine seraient le format ***contosotest.p.azurewebsites.net*** , et l’URL de cette application serait ***mytest.contosotest.p.azurewebsites.net***.  
Si vous définissez le Type de l’adresse IP virtuelle interne, le nom de votre équipement de direction auxiliaire n’est pas utilisé dans le sous-domaine pour l’équipement de direction auxiliaire.  Vous spécifiez le sous-domaine explicitement.  Si votre sous-domaine a été ***contoso.corp.net*** et si vous faites une application dans la mesure où ASE nommé ***timereporting*** l’URL pour cette application serait ***timereporting.contoso.corp.net***.


## <a name="apps-in-an-ilb-ase"></a>Applications dans un équipement de direction auxiliaire ILB ##

Création d’une application dans un équipement de direction auxiliaire ILB est identique à la création d’une application dans un équipement de direction auxiliaire normalement.  

1. Dans le portail Azure, sélectionnez **Nouveau -> Web + Mobile -> Web** ou **Mobile** ou **API App**
2. Entrez le nom de l’application
2. Sélectionnez l’abonnement
3. Permet de sélectionner ou de créer le groupe de ressources
4. Sélectionnez ou créez des Plan(ASP) de Service d’application.  Si la création d’un nouveau ASP Sélectionnez votre ASE comme emplacement, puis sélectionnez le pool de travail que vous souhaitez que vos pages ASP doit être créé dans.  Lorsque vous créez la page ASP, vous sélectionnez votre ASE comme l’emplacement et le pool de travail.  Lorsque vous spécifiez le nom de l’application, vous verrez que le sous-domaine sous le nom de votre application est remplacé par le sous-domaine pour votre ASE.   
5. Sélectionnez Créer.  Vous devez sélectionner la case à cocher **Ajouter au tableau de bord** si vous souhaitez que l’application s’affiche sur votre tableau de bord.  

![][2]


Sous le nom de l’application, le nom de sous-domaine est mis à jour pour refléter le sous-domaine de votre équipement de direction auxiliaire.  


## <a name="post-ilb-ase-creation-validation"></a>Validation de création de post ILB ASE ##

ILB ASE est légèrement différente de l’ASE ILB.  Comme déjà indiqué vous avez besoin gérer votre propre DNS et vous devez également fournir votre propre certificat pour les connexions HTTPS.  


Après avoir créé votre ASE, vous remarquerez que le sous-domaine indique le sous-domaine spécifié et il est un nouvel élément dans le menu **paramètre** appelé **Certificat de ILB**.  L’équipement de direction auxiliaire est créé avec un certificat auto-signé qui le rend plus facile de tester HTTPS.  Le portail vous indique que vous devez fournir votre propre certificat pour HTTPS, mais c’est à vous d’avoir un certificat qui accompagne votre sous-domaine.  

![][3]


Si vous essayez simplement de choses et que vous ne savez pas comment créer un certificat, vous pouvez utiliser l’application de la console MMC IIS pour créer un certificat signé automatiquement.  Une fois qu’il est créé, vous pouvez l’exporter dans un fichier .pfx et puis chargez-le dans l’interface utilisateur de certificat ILB. Lorsque vous accédez à un site sécurisé avec un certificat auto-signé, votre navigateur vous donne un avertissement que le site que vous accédez n’est pas sécurisé en raison de l’impossibilité de valider le certificat.  Si vous souhaitez éviter cet avertissement, que vous avez besoin d’un certificat signé qui correspond à votre sous-domaine et a une chaîne de confiance qui est reconnue par votre navigateur.

![][6]

Si vous souhaitez essayer le flux avec vos propres certificats et tester l’accès HTTP et HTTPS à votre équipement de direction auxiliaire :

1.  Accédez à l’interface utilisateur de l’équipement de direction auxiliaire après création ASE **ASE -> Paramètres -> certificats de ILB**
2.  Définir le certificat ILB en sélectionnant le fichier pfx du certificat et de mot de passe.  Cette étape prend un certain temps pour traiter et affiche le message en cours d’une opération de mise à l’échelle.
3.  Obtenir l’adresse ILB pour votre ASE (**ASE -> Propriétés -> adresse IP virtuelle**)
4.  Créer une application web dans l’équipement de direction auxiliaire après la création 
5.  Créer un ordinateur virtuel si vous n’en avez pas dans ce VNET (pas dans le même sous-réseau que le saut ASE ou éléments)
6.  Configurer DNS pour le sous-domaine.  Vous pouvez utiliser un caractère générique avec votre sous-domaine dans votre DNS ou si vous souhaitez effectuer des tests simples, modifier le fichier hosts sur votre machine virtuelle pour définir le nom de l’application web à l’adresse IP du protocole VIP.  Si votre équipement de direction auxiliaire a le nom de sous-domaine. ilbase.com et que vous faites le mytestapp application web afin qu’il serait être adressé au mytestapp.ilbase.com puis définissez cette valeur dans votre fichier hosts.  (Sur Windows le fichier hosts est à C:\Windows\System32\drivers\etc\)
7.  Utilisez un navigateur sur cet ordinateur virtuel et accédez à http://mytestapp.ilbase.com (ou quel que soit le nom de votre application web est à votre sous-domaine)
8.  Utilisez un navigateur sur cet ordinateur virtuel et cliquez sur https://mytestapp.ilbase.com que vous ne devrez pas accepter l’absence de sécurité si vous utilisez un certificat auto-signé.  


L’adresse IP de votre ILB figure dans les propriétés de l’adresse IP virtuelle

![][4]


## <a name="using-an-ilb-ase"></a>À l’aide d’un équipement de direction auxiliaire ILB ##

#### <a name="network-security-groups"></a>Groupes de sécurité de réseau ####

ASE ILB permet l’isolement réseau pour vos applications comme les applications ne sont pas accessibles ou même connus par internet.  Ceci est excellent pour l’hébergement de sites intranet, tels que les applications d’entreprise.  Lorsque vous avez besoin de restreindre l’accès même davantage encore utilisables Groups(NSGs) de sécurité réseau pour contrôler l’accès au niveau du réseau. 


Si vous souhaitez utiliser NSGs pour restreindre davantage l’accès, vous devez vous assurer que vous n’interrompez pas la communication dont l’équipement de direction auxiliaire a besoin pour fonctionner.  Même si l’accès HTTP/HTTPS est uniquement par l’intermédiaire de la ILB utilisée par l’équipement de direction auxiliaire l’ASE dépend toujours de la ressource à l’extérieur de la VNet.  Pour voir l’accès réseau est toujours requise rechercher les informations dans le document de [Contrôle de trafic entrant pour un environnement de Service d’application] [ ControlInbound] et le document sur les [Détails de Configuration de réseau pour les environnements de Service d’application avec ExpressRoute][ExpressRoute].  


Pour configurer votre NSGs que vous devez connaître l’adresse IP utilisée par Azure pour gérer votre équipement de direction auxiliaire.  Cette adresse IP est également l’adresse IP sortante à partir de votre équipement de direction auxiliaire s’il effectue des requêtes internet.  Pour trouver cette adresse IP adresse accédez à **Paramètres -> Propriétés** et trouver l' **Adresse IP sortante**.  

![][5]


#### <a name="general-ilb-ase-management"></a>Gestion de l’équipement auxiliaire de direction générale ILB ####

Gestion des ILB ASE est en grande partie identique à la gestion d’un équipement de direction auxiliaire normalement.  Vous devez mettre à l’échelle des regroupements de votre travail pour héberger plusieurs instances d’ASP et à faire évoluer vos serveurs frontaux pour gérer le volume croissant du trafic HTTP/HTTPS.  Pour plus d’informations générales sur la gestion de la configuration d’un équipement de direction auxiliaire, consultez le document sur la [configuration d’un environnement de Service d’application][ASEConfig].  


Les éléments supplémentaires de gestion sont la gestion des certificats et la gestion du service DNS.  Vous devez obtenir et télécharger le certificat utilisé pour le protocole HTTPS, après la création de la ILB ASE et remplacez-le avant son expiration.  Car Azure possède le domaine de base nous pouvons fournir des certificats pour ASEs avec une adresse IP virtuelle externe.  Étant donné que le sous-domaine utilisé par ASE ILB peut être n’importe quoi, vous devez fournir votre propre certificat pour HTTPS. 


#### <a name="dns-configuration"></a>Configuration de DNS ####

Lorsque vous utilisez une adresse IP virtuelle externe le DNS est géré par Azure.  N’importe quelle application créée dans votre équipement de direction auxiliaire est automatiquement ajoutée à DNS Azure qui est un serveur DNS public.  Dans la ILB ASE, vous devrez gérer votre propre serveur DNS.  Pour un sous-domaine donné, par exemple contoso.corp.net, vous avez besoin créer des enregistrements qui pointent vers votre adresse ILB pour DNS A :

    * 
    publication *.SCM ftp 


## <a name="getting-started"></a>Mise en route
Tous les articles et comment-de pour les environnements de Service d’application sont disponibles dans le [fichier Lisezmoi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour vous familiariser avec les environnements de Service d’application, consultez [Introduction aux environnements de Service d’application][WhatisASE]

Pour plus d’informations sur la plate-forme de services d’application Azure, consultez le [Service d’application Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
