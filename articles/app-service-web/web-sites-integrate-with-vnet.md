<properties 
    pageTitle="Intégrer une application avec un réseau virtuel Azure" 
    description="Montre comment une application dans le Service d’application Azure de se connecter à un réseau virtuel Azure nouvel ou existant" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer votre application avec un réseau virtuel Azure #

Ce document décrit la fonctionnalité d’intégration de Service d’application Azure réseau virtuel et montre comment les configurer avec des applications de [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Si vous ne connaissez pas Azure de réseaux virtuels (VNETs), il s’agit d’une fonctionnalité qui vous permet de placer la plupart de vos ressources d’Azure dans un réseau internet non routables vous contrôlez l’accès à.  Ces réseaux peut être liés à vos sur réseaux de site à l’aide d’une variété de technologies VPN.  Pour en savoir plus sur les réseaux virtuels Azure démarrer avec les informations ici : [Vue d’ensemble du réseau virtuel Azure][VNETOverview].  

Le Service d’application Azure a deux formes.  

1. Les systèmes de plusieurs clients prenant en charge la gamme complète des plans de tarification
1. La fonctionnalité de prime d’environnement de Service d’application (ASE) qui déploie dans votre VNET.  

Ce document passe par l’intégration de VNET et d’environnement de Service d’application pas.  Si vous souhaitez en savoir plus sur la fonctionnalité de l’équipement de direction auxiliaire puis démarrer avec les informations ici : [Présentation de l’environnement de Service d’application][ASEintro].

Intégration de VNET donne l’accès de votre application web pour les ressources de votre réseau virtuel mais n’accorde pas d’accès privé pour votre application web à partir du réseau virtuel.  Accès aux sites privés est disponible uniquement avec un équipement de direction auxiliaire configurée avec un équilibreur de charge interne (ILB).  Pour plus d’informations sur l’utilisation d’un équipement de direction auxiliaire ILB, commencez par l’article ici : [Création et utilisation d’un équipement de direction auxiliaire ILB][ILBASE]. 

Un scénario courant où vous utiliseriez VNET intégration permet l’accès à une base de données ou de services web s’exécutant sur une machine virtuelle dans votre réseau virtuel Azure à partir de votre application web.  Grâce à l’intégration de VNET vous n’avez pas besoin d’exposer un point de terminaison publique pour les applications sur votre ordinateur virtuel mais peut utilisent à la place des adresses internet non routables privées.  

La fonctionnalité d’intégration du VNET :

- nécessite un Standard ou un plan de tarification de prime 
- fonctionne avec Classic(V1) ou VNET de Manager(V2) de ressources 
- prend en charge les protocoles TCP et UDP
- fonctionne avec les applications Web, Mobile et API
- permet à une application pour se connecter à VNET (1) seul à la fois
- permet jusqu'à 5 VNETs s’intègre dans un Plan de Service d’application 
- permet à la même VNET être utilisée par plusieurs applications dans un Plan de Service d’application
- prend en charge d’un contrat SLA de 99,9 % en raison d’une dépendance sur la passerelle VNET

Il y a des choses que l’intégration de VNET n’est pas compatible, notamment :

- montage d’un lecteur
- Intégration avec Active Directory 
- NetBios
- accès à un site privé

### <a name="getting-started"></a>Mise en route ###
Voici quelques éléments à garder à l’esprit avant la connexion de votre application web à un réseau virtuel :

- Intégration de VNET fonctionne uniquement avec des applications dans un **Standard** ou **Premium** , plan de tarification.  Si vous activez la fonctionnalité, puis adapter votre Plan de Service d’application d’un plan de tarification non pris en charge vos applications seront perdues leurs connexions à le VNETs qu’ils utilisent.  
- Si votre réseau virtuel cible existe déjà, il doit avoir les VPN site-à-point activé avec une passerelle de routage dynamique avant qu’il peut être connecté à une application.  Vous ne pouvez pas activer le réseau privé virtuel (VPN) point-à-site si la passerelle est configurée avec un routage statique.
- Le VNET doit être dans le même abonnement comme votre Plan(ASP) de Service d’application.  
- Les applications qui s’intègrent à un VNET utilise le serveur DNS qui est spécifié pour ce VNET.
- Par défaut vos applications intégrées achemine le trafic dans votre VNET selon les itinéraires définis dans votre VNET.  


## <a name="enabling-vnet-integration"></a>Activation de l’intégration de VNET ##

Ce document se concentre essentiellement de l’utilisation du portail Azure pour l’intégration de VNET.  Pour activer l’intégration de la VNET avec votre application à l’aide de PowerShell, suivez les instructions fournies ici : [connecter votre application à votre réseau virtuel à l’aide de PowerShell][IntPowershell].

Vous avez la possibilité de connecter votre application à un réseau virtuel nouveau ou existant.  Si vous créez un nouveau réseau dans le cadre de votre intégration puis en plus de la simple création du VNET, une passerelle de routage dynamique sera préconfigurée pour vous, et pointez sur Site VPN sera activé.  

>[AZURE.NOTE] Configuration d’une nouvelle intégration de réseau virtuel peut prendre plusieurs minutes.  

Pour activer VNET intégration ouvert votre application Paramètres puis sélectionnez réseau.  L’interface utilisateur qui s’ouvre propose trois options de mise en réseau.  Ce guide seulement va dans l’intégration de VNET bien hybride connexions et environnements de Service d’application sont décrits plus loin dans ce document.  

Si votre application n'est pas dans le plan de tarification l’interface utilisateur lorsque vous permettra à l’échelle de votre plan à plan de tarification supérieur de votre choix.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Activation de l’intégration de VNET avec un VNET existant###
L’intégration de VNET interface utilisateur vous permet de sélectionner dans une liste de vos VNETs.  Le VNETs classique indique qu’ils le sont par exemple par le mot « Classique » entre parenthèses en regard du nom VNET.  La liste est triée tel que le Gestionnaire de ressources VNETs sont répertoriés en premier.  Dans l’image ci-dessous, vous pouvez voir que seul VNET peut être sélectionné.  Il existe plusieurs raisons qu’un VNET est grisé, y compris :

- le VNET est dans un autre abonnement que votre compte a accès à
- le VNET n’a pas de Point pour le Site activé
- le VNET ne dispose pas d’une passerelle de routage dynamique


![][2]

Pour activer l’intégration suffit de cliquer sur le VNET que vous souhaitez intégrer à.  Après avoir sélectionné le VNET, votre application sera automatiquement redémarrée pour que les modifications soient prises en compte.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Activer le Point de Site dans un VNET classique #####
Si votre VNET n’a pas de passerelle ni a Point au Site vous devez d’abord définir qui.  Pour ce faire, pour un VNET classique, atteindre l' [Azure Portal] [ AzurePortal] et afficher la liste de Networks(classic) virtuel.  À partir d’ici, cliquez sur le réseau que vous souhaitez intégrer et cliquez sur la zone Taille sous Essentials appelé connexions VPN.  À partir de là, vous pouvez créer votre point pour site VPN et même créer une passerelle.  Après avoir atteint par le point de site avec une expérience de création de passerelle, il sera environ 30 minutes avant d’être prêt.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>Activation de Point de Site dans un gestionnaire de ressources de VNET #####

Pour configurer un gestionnaire de ressources de VNET avec une passerelle et pointez sur Site, vous devez utiliser PowerShell comme indiqué ici, [configurer une connexion Point-à-Site, à un réseau virtuel à l’aide de PowerShell][V2VNETP2S].  L’interface utilisateur pour effectuer cette fonction n’est pas encore disponible. 

### <a name="creating-a-pre-configured-vnet"></a>Création d’un VNET préconfigurée ###
Si vous souhaitez créer un nouveau VNET qui est configuré avec une passerelle et Point-à-Site, le Service d’application de mise en réseau de l’interface utilisateur a la possibilité de faire cela, mais uniquement pour un gestionnaire de ressources VNET.  Si vous souhaitez créer un VNET classique avec une passerelle et un Point-à-Site vous devez le faire manuellement via l’interface utilisateur de gestion de réseau. 

Pour créer un VNET le Gestionnaire de ressources par le biais de l’interface d’intégration VNET, sélectionnez **Créer nouveau réseau virtuel** et fournir le :

- Nom du réseau virtuel
- Bloc d’adresse de réseau virtuel
- Nom du sous-réseau
- Bloc d’adresse de sous-réseau
- Bloc d’adresse de passerelle
- Bloc d’adresse de point-à-Site

Si vous souhaitez que ce VNET pour vous connecter à un des autre votre réseau vous devez éviter le prélèvement d’espace d’adressage IP qui chevauche les réseaux.  

>[AZURE.NOTE] Création du Gestionnaire de ressources VNET avec une passerelle prend environ 30 minutes et actuellement ne s’intégrera pas le VNET avec votre application.  Après avoir créé votre VNET avec la passerelle, que vous devez revenir à votre application, l’interface utilisateur de VNET intégration et sélectionnez votre VNET de nouveau.

![][3]

VNETs Azure sont généralement créés dans les adresses de réseau privé.  Par défaut, l’intégration de VNET fonctionnalité achemine tout le trafic destiné à ces plages d’adresses IP dans votre VNET.  Les plages d’adresses IP privées sont les suivantes :

- 10.0.0.0/8 - Ceci est le même que 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - Ceci est le même que 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - Ceci est le même que 192.168.0.0 - 192.168.255.255
 
L’espace d’adressage VNET doit être spécifié en notation CIDR.  Si vous ne connaissez pas la notation CIDR, il s’agit d’une méthode permettant de spécifier des blocs d’adresses à l’aide d’une adresse IP et un entier qui représente le masque de réseau. Comme une référence rapide, pensez que 10.1.0.0/24 serait 256 adresses et 10.1.0.0/25 serait 128 adresses.  Une adresse IPv4 avec un /32 serait le 1 adresse.  

Si vous définissez les informations de serveur DNS ici puis qui sont définies pour votre VNET.  Après la création de VNET, vous pouvez modifier ces informations à partir de l’expérience utilisateur VNET.

Lorsque vous créez un VNET classique à l’aide de l’interface d’intégration VNET, il créera un VNET dans le même groupe de ressources que votre application. 

## <a name="how-the-system-works"></a>Comment fonctionne le système ##
Dans les coulisses cette fonctionnalité s’appuie sur la technologie Point-à-Site VPN pour connecter votre application à vos VNET.   Applications de Service d’application Azure ont une architecture mutualisée système, ce qui empêche la mise en service d’une application directement dans une VNET est possible avec les machines virtuelles.  En s’appuyant sur la technologie point-à-site nous limiter l’accès réseau à seulement la machine virtuelle qui héberge l’application.  Accès au réseau est plus limité sur ces hôtes d’application afin que les réseaux que vous configurez leur accès peuvent uniquement accéder à vos applications.  

![][4]
 
Si vous n’avez pas configuré un serveur DNS à votre réseau virtuel, que vous devez utiliser des adresses IP.  Lorsque vous utilisez des adresses IP, n’oubliez pas que le principal avantage de cette fonctionnalité est qu’elle vous permet d’utiliser les adresses privées au sein de votre réseau privé.  Si vous définissez votre application jusqu'à l’utilisation des adresses IP publiques pour l’un de vos ordinateurs virtuels, puis vous n’utilisez pas la fonctionnalité d’intégration de VNET et communiquez sur internet.


##<a name="managing-the-vnet-integrations"></a>Gérer les intégrations de VNET##

La possibilité de se connecter et se déconnecter d’un VNET est au niveau de l’application.  Les opérations susceptibles d’affecter l’intégration VNET sur plusieurs applications sont au niveau de l’ASP.  À partir de l’interface utilisateur qui apparaît au niveau de l’application, vous pouvez obtenir des détails sur votre VNET.  La plupart des informations est également indiqué au niveau de l’ASP.  

![][5]

À partir de la page d’état de la fonctionnalité réseau, vous pouvez voir si votre application est connectée à votre VNET.  Si votre passerelle VNET est hors service pour raison quelconque puis cela indiquerait comme non connecté.  

Les informations que vous avez désormais disponibles dans l’application de que l’interface utilisateur de niveau VNET intégration est le même que les informations détaillées que vous obtenez à partir de la page ASP.  Voici les éléments :

- Nom VNET - ce lien ouvre l’interface utilisateur réseau
- Emplacement - Ceci correspond à l’emplacement de votre VNET.  Il est possible d’intégrer un VNET dans un autre emplacement.
- État du certificat - il existe des certificats permet de sécuriser la connexion VPN entre l’application et le VNET.  Cela reflète un test pour vous assurer qu’ils sont synchronisés.
- État de la passerelle - vos passerelles convient vers le bas pour une raison quelconque votre application ne peut pas accéder aux ressources dans le VNET.  
- Espace d’adressage VNET - il s’agit de l’espace d’adressage IP pour votre VNET.  
- Pointez sur l’espace d’adressage de Site - c’est le point de l’espace d’adressage de site IP pour votre VNET.  Votre application va afficher la communication comme provenant d’une des adresses IP dans cet espace d’adressage.  
- Site de l’espace d’adressage de site - vous pouvez utiliser des connexions VPN de Site à Site à votre VNET de se connecter aux ressources sur site ou à d’autres VNETs.  Si vous avez configuré les plages IP défini avec que connexion VPN s’affiche ici.
- Serveurs DNS - si vous avez des serveurs DNS configurés avec votre VNET, puis elles sont répertoriées ici.
- Adresses IP routée vers le VNET - là sont une liste d’adresses IP que votre VNET est définie pour le routage.  Ces adresses s’affichera ici.  

La seule opération que vous pouvez prendre dans la vue de l’application de l’intégration de votre VNET est pour vous déconnecter de votre application à partir de la VNET il est actuellement connecté à.  Pour faire cela cliquez simplement sur Déconnexion en haut.  Cette action ne modifie pas votre VNET.  Le VNET et sa configuration, notamment les passerelles reste inchangée.  Si vous souhaitez supprimer de votre VNET vous devez d’abord supprimer les ressources qu’il contient, notamment les passerelles.  

Le mode Plan de Service d’application a un certain nombre d’autres opérations.  Il est également accessible différemment qu’à partir de l’application.  Pour accéder à l’interface utilisateur de gestion de réseau ASP suffit d’ouvrir votre interface utilisateur de ASP et de la faire défiler vers le bas.  Il existe un élément d’interface utilisateur appelé l’état de la fonctionnalité réseau.  Il donnera des détails secondaires autour de votre intégration VNET.  En cliquant sur cette interface utilisateur, l’interface utilisateur état de fonctionnalité réseau s’ouvre.  Si vous cliquez sur « Cliquez ici pour gérer » l’interface utilisateur qui répertorie les intégrations de VNET dans ce ASP vous s’ouvre.

![][6]

L’emplacement de la page ASP est bon de garder à l’esprit lorsque vous examinez les emplacements des VNETs vous intégrez avec.  Lorsque le VNET est dans un autre emplacement vous aurez plus de chances de voir les problèmes de latence.  

Les VNETs intégrées avec est un rappel sur le nombre de VNETs vos applications sont intégrées avec cet ASP et de combien vous pouvez disposer.  

Pour afficher des détails supplémentaires sur chaque VNET, il suffit de cliquer sur le VNET qui vous intéressez.  Outre les détails qui ont été indiqué plus haut que vous verrez également une liste des applications dans cette ASP utilisant ce VNET.  

En ce qui concerne les actions, il existe deux actions principales.  La première est la possibilité d’ajouter des itinéraires qui Attirez le laisser votre application dans votre VNET.  La seconde action est la possibilité de synchroniser les informations de réseau et les certificats.

![][7]

**Routage** Comme déjà mentionné les itinéraires définis dans vos VNET sont ce qui est utilisé pour l’acheminement du trafic dans votre VNET à partir de votre application.  Il existe quelques utilisations où souhaitent envoyer le trafic supplémentaire à partir d’une application dans la VNET et leur cette fonctionnalité est fournie.  Que se passe-t-il pour le trafic une fois que c’est comment le client configure leur VNET.  

**Certificats** L’état du certificat reflète une vérification effectuée par le Service d’application pour valider que les certificats que nous utilisons pour la connexion VPN sont toujours correctes.  Lors de l’intégration de VNET est activée, alors si c’est la première intégration qui VNET à partir de toutes les applications dans cette ASP, il est un échange obligatoire de certificats afin de garantir la sécurité de la connexion.  Avec les certificats, nous obtenons la configuration DNS, itinéraires et autres choses similaires qui décrivent le réseau.
Si les certificats ou les informations de réseau sont modifié, vous devrez cliquez « Synchronisation réseau ».  **Remarque**: lorsque vous cliquez sur « Synchronisation réseau » puis vous entraîne une brève interruption de la connectivité entre votre application et votre VNET.  Pendant que votre application ne sera pas redémarrée la perte de connectivité peut provoquer votre site de fonctionner correctement.  

##<a name="accessing-on-premise-resources"></a>L’accès aux ressources du site##

Un des avantages de la fonctionnalité d’intégration de VNET est si votre VNET est connecté au réseau sur site avec un VPN de Site à Site puis vos applications peuvent avoir accès aux ressources sur site à partir de votre application.  Pour que cela fonctionne même si vous devez mettre à jour votre passerelle VPN sur site avec les itinéraires pour votre Point de Site IP range.  Lors de la connexion VPN de Site à Site est tout d’abord configurer les scripts utilisés pour configurer doivent définir des gammes, y compris votre Point de VPN de Site.  Si vous ajoutez le Point à VPN Site après votre créer votre VPN de Site à Site, vous devez mettre à jour manuellement les itinéraires.  Pour plus d’informations sur la procédure à suivre varient par passerelle et ne sont pas décrits ici.  

>[AZURE.NOTE] Alors que la fonctionnalité d’intégration de VNET fonctionnera avec un VPN de Site à Site pour accéder aux ressources du site il ne fonctionne actuellement pas avec un VPN ExpressRoute à faire de même.  Cela est vrai lors de l’intégration avec un classique ou un gestionnaire de ressources VNET.  Si vous avez besoin d’accéder aux ressources via un VPN ExpressRoute vous pouvez utiliser un équipement de direction auxiliaire pouvant s’exécuter dans votre VNET. 

##<a name="pricing-details"></a>Détails de la tarification##
Il existe quelques nuances que vous devez être conscient lors de l’utilisation de la fonctionnalité d’intégration de VNET de tarification.  Il existe 3 frais liés à l’utilisation de cette fonctionnalité :

- ASP, les exigences de niveau de prix
- Les coûts de transfert de données
- Coûts de la passerelle VPN.

Pour vos applications pouvoir utiliser cette fonctionnalité, ils doivent être dans un Standard ou un Plan de Service Premium App.  Vous pouvez voir plus de détails sur ces coûts ici : [Application de tarification Service][ASPricing]. 

En raison du mode Point pour les connexions VPN de Site sont traitées, vous avez toujours des frais pour les données sortantes via une connexion VNET intégration même si le VNET se trouve dans le même centre de données.  Pour voir ce que ces frais sont un coup de œil ici : [Détails de la tarification de transfert de données][DataPricing].  

Le dernier élément est le coût des passerelles VNET.  Si vous n’avez pas besoin les passerelles pour autre chose tel que VPN Site à Site puis vous payez pour les passerelles prendre en charge de la fonctionnalité d’intégration de VNET.  Il y a plus d’informations sur ces coûts ici : [Prix de passerelle VPN][VNETPricing].  

##<a name="troubleshooting"></a>Résolution des problèmes##

Cela ne signifie pas que votre expérience sera libre de problème lorsque la fonction est facile à configurer.  Si vous rencontrez les problèmes d’accès à votre point de terminaison souhaité il sont certains utilitaires que vous pouvez utiliser pour tester la connectivité à partir de la console de l’application.  Il existe deux expériences de console que vous pouvez utiliser.  L’une est à partir de la console Kudu et l’autre est la console que vous pouvez atteindre dans le portail Azure.  Pour accéder à la console de Kudu à partir de votre application, accédez à outils -> Kudu.  C’est le même que si vous passez à [nom de site]. scm.azurewebsites.net.  Une fois qu’ouvre simplement aller à l’onglet console de débogage.  Pour obtenir la console hébergée portail Azure, puis à partir de votre application, accédez à outils -> Console.  


####<a name="tools"></a>Outils####

Les outils ping, nslookup et tracert ne fonctionneront via la console en raison de contraintes de sécurité.  Pour remplir le là void ont été ajoutées deux outils.  Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe.  La syntaxe est la suivante :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser nameresolver pour vérifier les noms d’hôtes qui dépend de votre application.  De cette façon, vous pouvez tester si vous avez de quoi que ce soit mal configuré avec votre serveur DNS ou peut-être n’avez pas accès à votre serveur DNS.

L’outil suivant vous permet de tester la connectivité TCP à une combinaison d’hôte et le port.  Cet outil est appelé tcpping.exe et que la syntaxe est :

    tcpping.exe hostname [optional: port]

Cet outil indique si peut atteindre un hôte spécifique et un port, mais n’effectuera pas la même tâche que vous obtenez avec ICMP en fonction utilitaire ping.  L’utilitaire ping ICMP vous indique si votre hôte est actif.  Avec tcpping vous découvrez si vous pouvez accéder à un port spécifique sur un hôte.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Accès aux VNET le débogage hébergé des ressources####

Il existe un certain nombre de choses qui peuvent empêcher de votre application d’atteindre un hôte spécifique et un port.  La plupart du temps il est l’une des trois choses :

- **Il existe un pare-feu de la façon**  Si vous disposez d’un pare-feu dans la manière dont vous sera atteint le délai d’attente TCP.  C’est dans ce cas des 21 secondes.  Utilisez l’outil tcpping pour tester la connectivité.  Des délais d’attente TCP peut être fait beaucoup de choses derrière un pare-feu, mais commencer.  
- **DNS n’est pas accessible**  Le délai d’attente DNS est de 3 secondes par le serveur DNS.  Si vous avez des serveurs DNS 2 qui est de 6 secondes.  Utilisez nameresolver pour voir si le serveur DNS fonctionne.  N’oubliez pas que vous ne pouvez pas utiliser nslookup comme qui n’utilise pas le serveur DNS configuré avec votre VNET.
- **Plage d’adresse IP non valide de P2S** Le point de la plage d’adresses IP de site doit se trouver dans les plages d’IP privées RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) si la plage utilise des IPs en dehors de qui il choses ne fonctionnent pas.  

Si ces éléments ne répondent pas votre problème, recherchez les éléments simples tels que :  

- La passerelle s’affiche-t-il comme étant dans le portail ?
- Certificats apparaissent comme étant synchronisées ?
- Quelqu'un modifier la configuration réseau sans effectuer un « réseau de synchronisation » dans les ASP affectés ? 

Si la passerelle est arrêté puis mettre sauvegarder.  Si vos certificats ne sont pas synchronisées basculer vers l’affichage ASP de votre intégration VNET, puis appuyez sur « Synchronisation réseau ».  Si vous suspectez qu’une modification apportée à la configuration de votre VNET et il n’a pas été synchronisation serait avec vos pages ASP, puis passer en mode d’intégration de votre VNET ASP et atteint juste « Réseau de synchronisation » comme un rappel, cela entraînera une brève interruption avec votre connexion VNET et vos applications.  

Si tout cela est bien vous devez approfondir un peu plus loin :

- Y a-t-il des autres applications à l’aide de VNET intégration pour atteindre des ressources dans la même VNET ? 
- Vous accédez à la console de l’application et tcpping permet d’accéder à d’autres ressources dans votre VNET ?  

Si une ou l’autre des éléments ci-dessus sont remplies, votre intégration VNET est fine et le problème est ailleurs.  Il l’est être plus complexe, car il n’existe aucun moyen simple pour savoir pourquoi vous ne peut pas atteindre un hôte : port.  Voici quelques-unes des causes :

- vous avez un pare-feu sur votre hôte interdisant l’accès à la voie de l’application à partir de votre point de la plage d’adresses IP de site.  Franchissement des sous-réseaux souvent requiert l’accès Public.
- l’hôte cible est en panne
- votre application est en panne
- vous avez l’adresse IP ou le nom d’hôte incorrect
- votre application est à l’écoute sur un port autre que celui que vous attendiez.  Vous pouvez le vérifier en allant sur cet hôte et en utilisant « netstat - aon » à partir de l’invite de commandes.  Vous verrez le processus ID est à l’écoute sur le port.  
- les groupes de sécurité de votre réseau sont configurés de telle manière que leur empêchent l’accès à votre application hôte et le port à partir de votre point de la plage d’adresses IP de site

N’oubliez pas que vous ne savez pas quel IP dans votre Point à la plage IP du Site que votre application utilisera donc vous devez permettre l’accès de l’intégralité de la plage.  

Étapes de débogage supplémentaires :

- Ouvrez une session sur une autre VM dans votre VNET et tenter d’atteindre vos ressources : port de l’hôte à partir de là.  Il existe certains utilitaires de ping TCP que vous pouvez utiliser à cet effet ou que vous pouvez même utiliser telnet si besoin est.  Le but ici est simplement pour déterminer si votre connectivity est il à partir de cet autre ordinateur virtuel. 
- mettre une application sur une autre machine virtuelle et le test de l’accès à ce port et l’hôte à partir de la console à partir de votre application  
####<a name="on-premise-resources"></a>Ressources de site####
Si votre ne peut pas atteindre les ressources sur site, la première chose à vérifier est si vous pouvez contacter une ressource dans votre VNET.  Si qui fonctionne, les étapes sont assez faciles.  À partir d’un ordinateur virtuel dans votre VNET vous devez tenter d’atteindre l’application sur site.  Vous pouvez utiliser telnet ou un utilitaire ping TCP.  Si votre ordinateur virtuel ne peut pas accéder à votre ressource de site sur puis vérifiez d’abord votre connexion VPN de Site à Site fonctionne.  S’il fonctionne, puis vérifiez les mêmes choses indiqués plus haut, ainsi que sur la configuration de passerelle de site et l’état.  

Maintenant si votre VNET hébergé machine virtuelle peut accéder à votre système de site sur mais que votre application ne peut pas la raison est probablement de l’une des opérations suivantes :
- vos routes ne sont pas configurés avec votre point de plages IP de site dans votre passerelle de site sur
- les groupes de sécurité de votre réseau sont bloque l’accès de votre Point de la plage IP du Site
- vos pare-feu de site sur bloquent le trafic à partir de votre Point de la plage IP du Site
- vous avez un Route(UDR) de défini par l’utilisateur dans votre VNET qui empêche votre Point en fonction du trafic d’atteindre votre réseau de site à

## <a name="hybrid-connections-and-app-service-environments"></a>Les connexions hybride et les environnements de Service d’application##
Il existe 3 fonctions qui permettent l’accès aux ressources VNET hébergé.  Ils sont les suivantes :

- Intégration de VNET
- Connexions hybride
- Environnements de Service d’application

Les connexions hybride vous demande d’installer un agent de relais appelé la Manager(HCM) de connexion hybride dans votre réseau.  Le HCM doit pouvoir se connecter aux Azure ainsi à votre application.  Cette solution est particulièrement grande à partir d’un réseau distant, par exemple sur le réseau local ou même un autre nuage hébergé réseau car il ne nécessite pas un point de terminaison accessible d’internet.  Le HCM s’exécute uniquement sous Windows, et vous pouvez avoir jusqu'à 5 instances en cours d’exécution pour fournir une haute disponibilité.  Les connexions hybride ne prend en charge TCP bien et chaque point de terminaison HC doit correspondre à une combinaison spécifique d’hôte : port.  

La fonctionnalité de l’environnement de Service d’application vous permet d’exécuter une instance du Service application Azure dans votre VNET.  Cela permet à vos applications d’accéder aux ressources dans votre VNET sans étapes supplémentaires.  Parmi les autres avantages d’un environnement de Service d’application est que vous pouvez utiliser 8 travailleurs de noyau dédié avec 14 Go de RAM.  Un autre avantage est que vous pouvez mettre à l’échelle du système pour répondre à vos besoins.  Contrairement aux environnements mutualisées où vos pages ASP est limitée dans la taille, dans un équipement de direction auxiliaire vous contrôlez combien de ressources vous souhaitez donner au système.  En ce qui concerne le focus de réseau de ce document, une des choses que vous obtenez avec ASE pas grâce à l’intégration de VNET est qu’il peut travailler avec un VPN ExpressRoute.  

S’il existe que certains utilisent le chevauchement de cas, aucune de ces fonctionnalités peut remplacer tous les autres.  Permet de savoir quelle fonction utiliser dépend de vos besoins et comment vous souhaitez l’utiliser.  Par exemple :

- Si vous êtes développeur et que vous souhaitez simplement exécuter un site dans Azure et accéder à la base de données sur la station de travail sous votre bureau le plus simple à utiliser est hybride connexions.  
- Si vous avez une grande organisation qui souhaite placer un grand nombre de propriétés web dans le public en nuage et les gérer dans votre propre réseau, puis vous souhaitez accéder à l’environnement de Service d’application.  
- Si vous avez un certain nombre de services d’application hébergée d’applications et que vous souhaitez simplement accéder à des ressources dans votre VNET VNET intégration est la meilleure façon de procéder.  

Au-delà du cas d’usage sont aspects liés à des raisons de simplicité.  Si votre VNET est déjà connecté au réseau sur site puis l’intégration de la VNET ou un environnement de Service d’application est un moyen facile de consommer les ressources de site.  D’autre part, si votre VNET n’est pas connecté au réseau sur site, il est beaucoup plus de temps système pour configurer un VPN de site à site avec votre VNET par rapport à l’installation de la HCM.  

Outre les différences fonctionnelles il existe également des différences de tarification.  La fonctionnalité de l’environnement de Service d’application est une prime offre de service mais offre réseau le plus les possibilités de configuration en plus des autres fonctionnalités.  Intégration de VNET peut être utilisée avec le Standard ou Premium ASP et est l’outil idéale pour l’utilisation des ressources dans votre VNET dans le Service d’application de plusieurs utilisateurs en toute sécurité.  Hybride connexions dépend actuellement d’un compte qui dispose des niveaux que Démarrer libre, puis obtenir progressivement plus coûteux de prix basée sur le montant que vous avez besoin de BizTalk.  Lorsqu’il s’agit de travailler sur de nombreux réseaux Cependant, il n’existe aucune autre fonction comme connexions hybride qui peut vous permettre d’accéder aux ressources de plus de 100 réseaux distincts.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
