<properties
   pageTitle="Instructions pour le déploiement de Windows Server Active Directory sur les ordinateurs virtuels Azure | Microsoft Azure"
   description="Si vous savez comment déployer les Services de domaine Active Directory et les Services de fédération Active Directory dans les locaux, savoir comment elles fonctionnent sur des machines virtuelles Azure."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Instructions de déploiement d’Active Directory de Windows Server sur des machines virtuelles Azure

Cet article explique les différences importantes entre le déploiement Windows Server Active Directory Domain Services (AD DS) et Active Directory Federation Services (ADFS) sur site ou de les déployer sur les machines virtuelles Microsoft Azure.

## <a name="scope-and-audience"></a>Portée et le public

L’article est destiné à ceux déjà rencontré avec le déploiement de Active Directory sur site. Il traite des différences entre le déploiement d’Active Directory sur les réseaux virtuels de machines virtuelles/Azure Microsoft Azure et les déploiements Active Directory traditionnelles sur site. Les machines virtuelles Azure et Azure réseaux virtuels font partie d’un Infrastructure-comme-a-Service (IaaS offre aux entreprises de mieux exploiter les ressources informatiques dans le nuage).

Pour ceux qui ne sont pas familiarisés avec le déploiement d’Active Directory, consultez le [Guide de déploiement d’AD DS](https://technet.microsoft.com/library/cc753963) ou [planifier votre déploiement AD FS](https://technet.microsoft.com/library/dn151324.aspx) appropriés.

Cet article suppose que le lecteur est familiarisé avec les concepts suivants :

- Gestion et déploiement du service d’annuaire Active Directory de Windows Server
- Déploiement et configuration de DNS pour la prise en charge d’une infrastructure de service d’annuaire Active Directory de Windows Server
- Gestion et déploiement de Windows Server, Active Directory Federation Services
- Le déploiement, la configuration et la gestion des applications de partie utilisatrice (sites Web et les services web) qui peuvent consommer les jetons Windows Server AD FS
- Concepts générales d’une machine virtuelle, notamment la façon de configurer une machine virtuelle, des disques virtuels et des réseaux virtuels

Cet article présente la configuration requise pour un scénario de déploiement hybride dans le service d’annuaire Active Directory de Windows Server ou un AD FS sont partiellement déployé sur site et partiellement déploiement sur les machines virtuelles Azure. Le document traite tout d’abord les différences essentielles entre Windows Server AD DS et AD FS en cours d’exécution sur des ordinateurs virtuels Azure plutôt que sur les sites et les décisions importantes qui affectent la conception et le déploiement. Le reste de ce document explique les lignes directrices pour chacun des points de décision plus en détail et comment appliquer les instructions pour divers scénarios de déploiement.

Cet article ne traite pas de la configuration d' [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), qui est un service qui fournit des fonctionnalités de contrôle d’accès et de gestion des identités des applications en nuage basée sur REST. Azure de Active Directory (AD Azure) et de Windows Server AD DS sont, toutefois, conçus pour fonctionner ensemble pour fournir une solution de gestion des identités et des accès pour hybride d’aujourd'hui informatique des environnements et des applications modernes. Pour vous aider à comprendre les différences et les relations entre Windows Server AD DS et AD Azure, considérez les points suivants :

1. Vous pouvez exécuter Windows Server AD DS dans le nuage sur des machines virtuelles Azure lorsque vous utilisez Azure pour étendre votre centre de données local dans le nuage.
2. Vous pouvez utiliser AD Azure pour donner à vos utilisateurs de l’authentification unique pour applications de logiciel-as-a-Service (SaaS). Microsoft Office 365 utilise cette technologie, par exemple, et les applications qui s’exécutent sur Azure ou d’autres plates-formes de nuage peuvent également l’utiliser.
3. Vous pouvez utiliser Azure AD (son Service de contrôle d’accès) pour permettre aux utilisateurs de se connecter à l’aide des identités à partir de Facebook, Google, Microsoft et autres fournisseurs d’identité pour les applications qui sont hébergées dans le nuage ou sur site.

Pour plus d’informations sur ces différences, consultez [Identité d’Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Ressources connexes

Vous pouvez télécharger et exécuter l' [Évaluation de la préparation Azure Virtual Machine](https://www.microsoft.com/download/details.aspx?id=40898). L’évaluation sera automatiquement inspecter votre environnement local et générer un rapport personnalisé selon les recommandations de cette rubrique pour vous aider à faire migrer l’environnement vers Azure.

Nous vous recommandons également consulter les didacticiels, les repères et les vidéos qui couvrent les sujets suivants :

- [Configurer un réseau virtuel nuage uniquement dans le portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurer un VPN de Site à Site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)
- [Installer un contrôleur de domaine Active Directory répliqué sur Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IaaS de Pro informatiques : principes de base des machines virtuelles (01)](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IaaS Pro d’informatique : (05) création des réseaux virtuels et connectivité de coexistence](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduction

Les exigences fondamentales pour le déploiement Active Directory de Windows Server sur des machines virtuelles Azure diffèrent peu de déploiement dans des machines virtuelles sur site (et, dans une certaine mesure, les machines physiques). Par exemple, dans le cas du service d’annuaire Active Directory de Windows Server, si les contrôleurs de domaine (DC) que vous déployez sur les ordinateurs virtuels Azure sont des réplicas dans un existant sur site domaine/forêt de l’entreprise, puis le déploiement d’Azure en grande partie peut être traité de la même manière que le traitement de tout autre site Windows Server Active Directory supplémentaire. C'est-à-dire les sous-réseaux doivent être définis dans le service d’annuaire Active Directory de Windows Server, un site créé, les sous-réseaux liés à ce site et connectés à d’autres sites à l’aide de liens de sites appropriés. Toutefois, il existe certaines différences qui sont communes à tous les déploiements Azure et certains qui varient selon le scénario de déploiement spécifique. Deux différences fondamentales sont décrites ci-dessous :

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Les machines virtuelles Azure peut-être une connectivité au réseau local d’entreprise.

Connexion des machines virtuelles Azure à un réseau local d’entreprise requiert à un réseau virtuel Azure, qui inclut un site à site ou site-à-point virtuel privé (VPN) composant réseau capable de connecter de façon transparente des ordinateurs virtuels Azure et machines sur site. Ce composant VPN peut également activer des ordinateurs membres du domaine local accéder à un domaine Active Directory Windows Server dont les contrôleurs de domaine hébergés exclusivement sur des machines virtuelles Azure. Il est important de noter, cependant, qu’en cas d’échec de la connexion VPN, l’authentification et autres opérations qui dépendent de Windows Server Active Directory échouera également. Alors que les utilisateurs peuvent être en mesure de connexion existants mis en cache les informations d’identification, tous les peer-to-peer ou les tentatives d’authentification de client-serveur pour lesquels tickets doivent encore être émis ou devenus obsolètes échoueront.

Pour une démonstration vidéo et une liste des didacticiels pas à pas, y compris [configurer un VPN de Site à Site dans Azure portal](../vpn-gateway/vpn-gateway-site-to-site-create.md), reportez-vous à la section [Réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/) .

> [AZURE.NOTE] Vous pouvez également déployer Windows Server Active Directory sur un réseau virtuel Azure qui n’a pas de connectivité avec un réseau local. Les instructions de cette rubrique, cependant, de supposer qu’un réseau virtuel Azure est utilisé car elle offre des fonctionnalités essentielles de Windows Server d’adressage IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Des adresses IP statiques doivent être configurés avec PowerShell d’Azure.

Les adresses dynamiques sont attribuées par défaut, mais utilisent l’applet de commande Set-AzureStaticVNetIP à la place, affecter une adresse IP statique. Qui définit une adresse IP statique qui rend persistantes par le biais de cicatrisation de service et d’arrêt et de redémarrage d’ordinateur virtuel. Pour plus d’informations, consultez [une adresse IP interne statique pour les machines virtuelles](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termes et définitions

Voici une liste non exhaustive des conditions pour différentes technologies Azure qui seront référencées dans cet article.

- **Les machines virtuelles Azure**: offre le IaaS dans Azure qui permet aux clients de déployer des ordinateurs virtuels exécutant pratiquement n’importe quel traditionnellement la charge serveur sur site.

- **Azure réseau virtuel**: le service de mise en réseau dans Azure qui permet aux clients de créer et de gérer les réseaux virtuels dans Azure et de les lier en toute sécurité à leur propre sur site infrastructure réseau à l’aide d’un réseau privé virtuel (VPN).

- **Adresse IP virtuelle**: une adresse IP avec accès internet qui n’est pas liée à une carte d’interface réseau ou ordinateur spécifique. Les services en nuage sont affectés d’une adresse IP virtuelle pour recevoir le trafic réseau qui est redirigé vers une machine virtuelle d’Azure. Une adresse IP virtuelle est une propriété d’un service de cloud qui peut contenir un ou plusieurs ordinateurs virtuels Azure. Notez également qu’un réseau virtuel Azure peut contenir un ou plusieurs services de cloud. Adresses IP virtuelles fournissent des fonctionnalités natives de l’équilibrage de charge.

- **Adresse IP dynamique**: il s’agit de l’adresse IP qui est uniquement interne. Pour les machines virtuelles qui hébergent les rôles de serveur/DNS du contrôleur de domaine, il doit être configuré comme une adresse IP statique (à l’aide de l’applet de commande Set-AzureStaticVNetIP).

- **Rétablissement du service**: le processus dans lequel Azure renvoie automatiquement un service à un état en cours d’exécution à nouveau après avoir détecté que le service a échoué. Rétablissement de service est un des aspects d’Azure qui prend en charge la disponibilité et la résilience. Bien qu’improbable, le résultat suivant d’un service de cicatrisation incident pour un contrôleur de domaine en cours d’exécution sur un ordinateur virtuel est semblable à un redémarrage non planifié, mais il a quelques effets secondaires :

 - Modifie la carte réseau virtuelle de l’ordinateur virtuel
 - Change l’adresse MAC de la carte réseau virtuelle
 - Le code de processeur/UC de la machine virtuelle
 - La configuration IP de la carte réseau virtuelle ne changera pas tant que l’ordinateur virtuel est connecté à un réseau virtuel et l’adresse IP de l’ordinateur est statique.

 Aucun de ces problèmes affectent Active Directory de Windows Server, car il n’a aucune dépendance sur l’adresse MAC ou l’ID de processeur/UC, et tous les déploiements Active Directory de Windows Server sur Azure sont recommandés pour être exécuté sur un réseau virtuel Azure comme indiqué ci-dessus.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Est-il prudent de virtualiser les contrôleurs de domaine Active Directory de Windows Server ?

Le déploiement de Windows Server Active Directory contrôleurs de domaine sur des machines virtuelles Azure est soumis aux mêmes consignes que locaux sur les contrôleurs de domaine en cours d’exécution sur une machine virtuelle. Exécutant des contrôleurs de domaine virtualisés d’est une pratique de sécurité tant que consignes pour la sauvegarde et la restauration des contrôleurs de domaine. Pour plus d’informations sur les contraintes et les instructions pour l’exécution des contrôleurs de domaine virtualisés, voir [Contrôleurs de domaine exécutant Hyper - v](https://technet.microsoft.com/library/dd363553).

Les hyperviseurs fournissent ou créez un jeu de technologies qui peuvent provoquer des problèmes pour de nombreux systèmes distribués, y compris Windows Server Active Directory. Par exemple, un serveur physique, vous pouvez cloner un disque ou utiliser des méthodes non prises en charge pour restaurer l’état d’un serveur, notamment via des réseaux SAN et ainsi de suite, mais sur un serveur physique sont beaucoup plus difficiles que la restauration d’un instantané d’ordinateur virtuel dans un hyperviseur. Azure offre des fonctionnalités qui peuvent entraîner la même condition indésirable. Par exemple, vous ne devez pas copier de contrôleurs de domaine, les fichiers de disque dur virtuel au lieu d’effectuer des sauvegardes régulières, car leur restauration peut entraîner une situation similaire à l’utilisation des fonctionnalités de snapshot de restauration.

Ces restaurations introduisent des bulles d’USN qui peuvent mener à des États définitivement divergents entre contrôleurs de domaine. Qui peut entraîner des problèmes tels que :

- Objets en attente
- Les mots de passe incohérentes
- Valeurs de l’attribut incohérent
- Différences de schéma si le contrôleur de schéma est annulé

Pour plus d’informations sur l’impact des contrôleurs de domaine, consultez [USN et reprise USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

À partir de Windows Server 2012, [des garanties supplémentaires sont intégrés aux services AD DS](https://technet.microsoft.com/library/hh831734.aspx). Ces protections vous aider à protéger les contrôleurs de domaine virtualisés contre mentionnés ci-dessus, tant que la plate-forme hyperviseur sous-jacent prend en charge les GenerationID de la machine virtuelle. Azure prend en charge les GenerationID de la machine virtuelle, ce qui signifie que les contrôleurs de domaine qui exécutent Windows Server 2012 ou des machines virtuelles Azure ultérieurement sur les garanties supplémentaires.

> [AZURE.NOTE] Vous devez arrêter et redémarrer un ordinateur virtuel qui exécute le rôle de contrôleur de domaine dans Azure dans le système d’exploitation invité au lieu d’utiliser l’option **Arrêter** dans le portail classique Azure. Aujourd'hui, à l’aide du portail classique d’arrêter un ordinateur virtuel entraîne la machine virtuelle d’être libérée. Une machine virtuelle désallouée a l’avantage de ne pas encourir de frais, mais elle réinitialise également le VM-GenerationID, qui n’est pas souhaitable pour un contrôleur de domaine. Lorsque la machine virtuelle-GenerationID est réinitialisé, l’ID d’invocation de la base de données AD DS est également réinitialisé, le pool RID est ignoré et SYSVOL est marquée comme ne faisant pas autorité. Pour plus d’informations, consultez [Introduction à la virtualisation des Services de domaine Active Directory (AD DS)](https://technet.microsoft.com/library/hh831734.aspx) et [DFSR à virtualiser en toute sécurité](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Pourquoi déployer le service d’annuaire Active Directory du serveur Windows Azure machines virtuelles en fonctionnement ?

De nombreux scénarios de déploiement de Windows Server AD DS sont bien adaptées au déploiement en tant que machines virtuelles sur Azure. Par exemple, supposons que vous disposez d’une société en Europe, qui a besoin d’authentifier les utilisateurs à distance en Asie. La société n’a pas précédemment déployé Windows Server Active Directory contrôleurs de domaine en Asie en raison du coût de déploiement limitée et leur expertise pour gérer le déploiement de serveurs après. Par conséquent, les demandes d’authentification à partir de l’Asie sont pris en charge par les contrôleurs de domaine en Europe avec des résultats non optimaux. Dans ce cas, vous pouvez déployer un contrôleur de domaine sur un ordinateur virtuel que vous avez spécifié doit être exécuté dans le centre de données Azure en Asie. Attachement de ce contrôleur de domaine à un réseau virtuel Azure qui est connecté directement à l’emplacement distant améliore les performances de l’authentification.

Azure est également parfaitement comme solution de remplacement pour les sites de reprise après sinistre coûteuse dans le cas contraire. Le relativement faible coût de l’hébergement d’un petit nombre de contrôleurs de domaine et un seul réseau virtuel sur Azure représente une alternative intéressante.

Enfin, vous souhaiterez déployer une application réseau sur Azure, telles que SharePoint, ce qui nécessite Windows Server Active Directory mais ne dépend pas sur le réseau local ou d’entreprise Windows Server Active Directory. Dans ce cas, configuration requise du serveur déploiement d’une forêt isolée sur Azure pour répondre à SharePoint est optimal. Encore une fois, le déploiement d’applications de réseau ne nécessitant pas de connexion au réseau local et à Active Directory d’entreprise est également pris en charge.

> [AZURE.NOTE] Dans la mesure où elle fournit une connexion de couche 3, le composant VPN qui assure la connectivité entre un réseau virtuel Azure et un réseau local peut également activer serveurs membres qui exécutent sur site pour tirer parti des contrôleurs de domaine qui s’exécutent comme des machines virtuelles Azure sur Azure réseau virtuel. Mais si la connexion VPN n’est pas disponible, la communication entre ordinateurs sur site et contrôleurs de domaine Azure ne fonctionnera pas, résultant de l’authentification et à diverses autres erreurs.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contraste entre le déploiement de contrôleurs de domaine Windows Server Active Directory Azure machines virtuelles en fonctionnement et en local

- Pour les scénarios de déploiement Active Directory de Windows Server qui comprend plus d’une machine virtuelle unique, il est nécessaire d’utiliser un réseau virtuel Azure pour préserver la cohérence des adresses IP. Notez que ce guide suppose que les contrôleurs de domaine sont exécutent sur un réseau virtuel Azure.

- Comme avec les contrôleurs de domaine locaux, des adresses IP statiques sont recommandées. Une adresse IP statique peut uniquement être configurée à l’aide de PowerShell d’Azure. Pour plus d’informations, reportez-vous à la section [une adresse IP interne statique pour les machines virtuelles](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Si vous avez suivi de systèmes ou autres solutions qui vérifient de configuration d’adresses IP statiques dans le système d’exploitation invité, vous pouvez affecter la même adresse IP statique pour les propriétés de la carte réseau de la machine virtuelle. Mais sachez que la carte réseau est ignorée si la machine virtuelle subit la cicatrisation de service ou d’arrêt dans le portail classique et a libéré de son adresse. Dans ce cas, l’adresse IP statique au sein de l’invité devez être réinitialisé.

- Déploiement de machines virtuelles sur un réseau virtuel ne pas implique (ou nécessitent) connectivité à un réseau local. le réseau virtuel permet simplement de cette possibilité. Vous devez créer un réseau virtuel pour la communication privée entre Azure et votre réseau local. Vous devez déployer un point de terminaison VPN sur le réseau local. La connexion VPN est ouvert à partir de la Azure au réseau local. Pour plus d’informations, consultez [Vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md) et de [configurer un VPN de Site à Site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Une option pour [créer un réseau privé virtuel site-à-point](../vpn-gateway/vpn-gateway-point-to-site-create.md) est disponible sur des ordinateurs fonctionnant sous Windows de se connecter directement à un réseau virtuel Azure.

- Que vous créiez un virtuel réseau ou non, des frais Azure pour le trafic de sortie mais pas de pénétration. Différentes options de conception d’Active Directory de Windows Server peuvent affecter la quantité de trafic sortant généré par un déploiement. Par exemple, (RODC) déploiement d’un contrôleur de domaine en lecture seule limite le trafic de sortie car il ne réplique pas sortant. Mais la décision de déployer un RODC doit être comparée à la nécessité d’effectuer des opérations d’écriture sur le contrôleur de domaine et de la [compatibilité](https://technet.microsoft.com/library/cc755190) ayant des applications et des services dans le site avec RODC. Pour plus d’informations sur les frais de trafic, reportez-vous à la section [Azure tarification d’un coup de œil](http://azure.microsoft.com/pricing/).

- Bien que vous ayez terminé contrôler sur quel serveur les ressources à utiliser pour les ordinateurs virtuels sur site, telles que la RAM, taille du disque, et ainsi de suite, sur Azure, vous devez sélectionner dans la liste des tailles de serveur préconfigurés. Pour un contrôleur de domaine, un disque de données est nécessaire en plus du disque du système d’exploitation pour stocker la base de données Active Directory de Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Vous pouvez déployer FS Windows Server Active Directory sur les ordinateurs virtuels Azure ?

Oui, vous pouvez déployer FS Windows Server Active Directory sur les ordinateurs virtuels Azure et les [meilleures pratiques de déploiement d’ADFS](https://technet.microsoft.com/library/dn151324.aspx) locaux s’appliquent également au déploiement d’ADFS sur Azure. Mais certaines des meilleures pratiques telles que l’équilibrage de charge et haute disponibilité requièrent des technologies au-delà de ce qu’ADFS offre lui-même. Ils doivent être fournis par l’infrastructure sous-jacente. Nous allons passer en revue quelques-unes de ces meilleures pratiques et voir comment ils peuvent être obtenus en utilisant les Azure VM et un réseau virtuel Azure.

1. **Ne jamais exposer les serveurs (STS) service de jeton de sécurité directement à Internet.**

    Ceci est important car le STS émet des jetons de sécurité. Par conséquent, les serveurs SharePoint Team Services tels que AD FS doivent être traitées avec le même niveau de protection qu’un contrôleur de domaine. Si un STS est compromise, les utilisateurs malveillants ont la possibilité d’émettre des jetons d’accès contenant potentiellement des revendications de leur choix de confiance tierces et d’autres serveurs de STS dans faire confiance à des organisations.

2. **Déployer des contrôleurs de domaine Active Directory pour tous les domaines d’utilisateur sur le même réseau que les serveurs ADFS.**

    AD FS utilisent les Services de domaine Active Directory pour authentifier les utilisateurs. Il est recommandé de déployer des contrôleurs de domaine sur le même réseau que les serveurs ADFS. Ainsi, la continuité d’activité dans le cas où le lien entre le réseau Azure et votre réseau local est rompu et permet des temps de latence réduits et des performances accrues pour les connexions.

3. **Déployer plusieurs nœuds AD FS pour la haute disponibilité et l’équilibrage de la charge.**

    Dans la plupart des cas, la défaillance d’une application qui permet d’AD FS est inacceptable car les applications qui nécessitent des jetons de sécurité sont souvent critiques pour l’entreprise. Par conséquent, et étant donné que AD FS se trouve maintenant dans le chemin d’accès critique pour l’accès à des applications critiques, le service AD FS doit être hautement disponible via plusieurs proxys d’ADFS et les serveurs AD FS. Pour obtenir la répartition des demandes, les équilibreurs de charge sont généralement déployés devant les serveurs AD FS et des serveurs proxy AD FS.

4. **Déployer un ou plusieurs nœuds de Proxy de l’Application Web pour accéder à internet.**

    Lorsque les utilisateurs ont besoin d’accéder aux applications protégées par le service ADFS, le service AD FS doit être disponible à partir d’internet. Pour cela, vous devez déployer le service Web Proxy de l’Application. Il est fortement recommandé de déployer plusieurs nœuds pour les besoins de haute disponibilité et l’équilibrage de la charge.

5. **Restreindre l’accès aux ressources du réseau interne à partir des nœuds de Proxy de l’Application Web.**

    Pour permettre aux utilisateurs externes d’accéder à AD FS à partir d’internet, vous devez déployer des nœuds de Proxy de l’Application Web (AD FS Proxy ou dans les versions antérieures de Windows Server). Les nœuds de proxy d’Application Web sont directement exposés à Internet. Ils ne doivent pas être joint au domaine et ils ne doivent accéder aux serveurs ADFS sur les ports TCP 443 et 80. Il est fortement recommandé que la communication à tous les autres ordinateurs (en particulier les contrôleurs de domaine) est bloquée.

    C’est en général atteint local au moyen d’un réseau de périmètre. Les pare-feu utilisent un mode d’autorisation pour limiter le trafic à partir de la zone DMZ au réseau local (c'est-à-dire que seul le trafic sur les ports spécifiés et les adresses IP spécifiées est autorisé, et tout autre trafic est bloqué).

Le diagramme suivant affiche un traditionnel AD FS déploiement local.

![Diagramme d’un déploiement de Active Directory Federation Services traditionnelles sur site](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Toutefois, car Azure ne fournit pas natif, des fonctions complètes de pare-feu, autres options doivent être utilisé pour restreindre le trafic. Le tableau suivant affiche chaque option et de ses avantages et ses inconvénients.

| Option | Avantage | Inconvénient |
| ------ | --------- | ------------ |
| [Réseau Azure ACL](virtual-networks-acl.md) | Configuration initiale moins coûteuse et plus simple | Configuration d’ACL de réseau supplémentaire requise si les nouveaux ordinateurs virtuels sont ajoutés au déploiement |
| [Pare-feu Barracuda NG](https://www.barracuda.com/products/ngfirewall) | Mode d’autorisation de l’opération et il ne requiert aucune configuration d’ACL de réseau | Une augmentation des coûts et la complexité de la configuration initiale |

Dans ce cas, les étapes de haut niveau pour déployer ADFS sont comme suit :

1. Créer un réseau virtuel avec une connectivité entre sites, à l’aide d’un VPN ou un [ExpressRoute](http://azure.microsoft.com/services/expressroute/).

2. Déployer des contrôleurs de domaine sur le réseau virtuel. Cette étape est facultative, mais recommandée.

3. Déployer des serveurs de AD FS à un domaine sur le réseau virtuel.

4. Créer un [jeu de équilibrage de charge interne](http://azure.microsoft.com/blog/internal-load-balancing/) qui inclut les serveurs ADFS et utilise une nouvelle adresse IP privée dans le réseau virtuel (une adresse IP dynamique).

  1. Mise à jour DNS pour créer le nom de domaine complet pour pointer vers l’adresse IP privée (dynamique), de l’ensemble de l’équilibrage de la charge interne.

5. Pour les nœuds de Proxy de l’Application Web, créez un service en nuage (ou à un réseau virtuel distinct).

6. Déployer les nœuds de Proxy de l’Application Web dans le service en nuage ou de réseau virtuel

  1. Créer un jeu d’équilibrage de la charge externes qui inclut les nœuds de Proxy de l’Application Web.

  2. Mettre à jour le nom DNS externe (FQDN) pour pointer vers le cloud service public adresse IP (l’adresse IP virtuelle).

  3. Configurer les serveurs proxy de Federation Services pour utiliser le nom de domaine complet qui correspond à l’ensemble de l’équilibrage de la charge interne pour les serveurs ADFS.

  4. Mise à jour basée sur les revendications de sites web pour utiliser le nom de domaine externe complet de leur fournisseur de réclamations.

7. Limiter l’accès entre le Proxy de l’Application Web à n’importe quel ordinateur dans le réseau virtuel AD FS.

Pour restreindre le trafic, le jeu avec équilibrage de charge de l’équilibrage de charge d’interne Azure doit être configuré pour que le trafic sur les ports TCP 80 et 443, et tout autre trafic vers l’adresse IP dynamique interne de l’ensemble de l’équilibrage de la charge est abandonné.

![Diagramme des ACL de réseau ADFS avec TCP 443 + 80 est autorisé.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Le trafic vers les serveurs ADFS sont autorisé uniquement par les sources suivantes :

- L’équilibrage de la charge d’interne Azure.
- L’adresse IP d’un administrateur sur le réseau local.

> [AZURE.WARNING] La conception doit empêcher les nœuds de Proxy de l’Application Web d’atteindre les autres ordinateurs virtuels dans le réseau virtuel Azure ou tous les emplacements sur le réseau local. Qui peut être effectuée en configurant des règles de pare-feu de la solution matérielle-logicielle sur site pour les connexions de routage d’Express ou le périphérique VPN pour les connexions VPN de site à site.

Un inconvénient de cette option est nécessaire pour configurer le réseau ACL pour plusieurs périphériques, y compris l’équilibreur de charge interne, les serveurs ADFS et tous les autres serveurs qui sont ajoutés au réseau virtuel. Si n’importe quel périphérique est ajouté au déploiement sans configuration d’ACL de réseau pour restreindre le trafic vers elle, la totalité du déploiement peut être à risque. Si les adresses IP des nœuds Proxy de l’Application Web de changent, le réseau ACL (ce qui signifie que les serveurs proxy doivent être configurés pour utiliser [des adresses IP statiques dynamiques](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)) doit réinitialiser.

![ADFS sur Azure avec réseau ACL.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Une autre option consiste à utiliser l’appliance [Barracuda NG pare-feu](https://www.barracuda.com/products/ngfirewall) pour contrôler le trafic entre les serveurs ADFS et les serveurs proxy de Federation Services. Cette option est conforme aux meilleures pratiques pour la sécurité et la disponibilité et il requiert une administration moins après l’installation initiale car l’appliance Barracuda NG pare-feu fournit un mode d’autorisation de l’administration du pare-feu, et il peut être installé directement sur un réseau virtuel Azure. Cela élimine la nécessité de configurer des listes ACL du réseau chaque fois qu’un nouveau serveur est ajouté au déploiement. Mais, cette option ajoute le coût et la complexité du déploiement initial.

Dans ce cas, deux réseaux virtuels sont déployés au lieu d’un. Nous allons appeler les VNet1 et VNet2. VNet1 contient les serveurs proxy et VNet2 les STSs et la connexion réseau sur le réseau d’entreprise. VNet1 est donc physiquement (bien que presque) isolée à partir de VNet2 et, à son tour, à partir du réseau d’entreprise. VNet1 est ensuite connecté à VNet2 à l’aide d’une technologie de tunneling spéciale connue en tant que Transport Architecture réseau indépendant (TINA). Le tunnel de TINA est associé à chacun des réseaux virtuels à l’aide d’un pare-feu Barracuda NG — un Barracuda sur chacun des réseaux virtuels.  Pour une haute disponibilité, il est recommandé de déployer deux Barracudas sur chaque réseau virtuel ; un actif, l’autre passif. Ils offrent des capacités il extrêmement riches qui nous permet de reproduire le fonctionnement d’un réseau de périmètre traditionnel sur site dans Azure.

![ADFS sur Azure avec pare-feu.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Pour plus d’informations, consultez [AD FS : étendre l’application frontale utilisant les revendications sur site Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Une alternative au déploiement d’ADFS si l’objectif est d’Office 365 authentification unique autonome

Il existe une autre solution pour le déploiement d’Active Directory totalement FS si votre objectif est uniquement d’activer la connexion à Office 365. Dans ce cas, vous pouvez simplement déployer la synchronisation d’annuaire avec le mot de passe le synchronisation locaux et obtenir le même résultat final avec la complexité du déploiement minimal car cette approche ne nécessite pas d’AD FS ou Azure.

Le tableau suivant compare comment les processus d’ouverture de session fonctionnent avec et sans déployer ADFS.

| Office 365 unique ouverture de session à l’aide d’ADFS et la synchronisation d’annuaire | Office 365 même ouverture de session à l’aide de la synchronisation d’annuaire + la synchronisation de mot de passe |
| ------------- | ------------- |
| 1. l’utilisateur ouvre une session sur un réseau d’entreprise et il est authentifié à Active Directory de Windows Server. | 1. l’utilisateur ouvre une session sur un réseau d’entreprise et il est authentifié à Active Directory de Windows Server. |
| 2. l’utilisateur tente d’accéder à Office 365 (je @contoso.com). | 2. l’utilisateur tente d’accéder à Office 365 (je @contoso.com). |
| 3. office 365 redirige l’utilisateur vers Azure AD. | 3. office 365 redirige l’utilisateur vers Azure AD. |
| 4. dans la mesure où Azure AD ne peut pas authentifier l’utilisateur et comprend qu'il existe une relation d’approbation avec ADFS local, il redirige l’utilisateur vers AD FS. | 4. azure AD ne peut pas accepter des tickets Kerberos directement et aucune relation d’approbation n’existe afin que l’utilisateur entre des informations d’identification demandées. |
| 5. l’utilisateur envoie un ticket Kerberos au service SJS AD FS. | 5. l’utilisateur entre le même mot de passe local et AD Azure les valide en fonction du nom d’utilisateur et le mot de passe qui a été synchronisé par la synchronisation d’annuaire. |
| 6. AD FS transforme le ticket Kerberos du format de jeton requis/sinistres et redirige l’utilisateur vers Azure AD. | 6. azure AD redirige l’utilisateur vers Office 365. |
| 7. l’utilisateur s’authentifie auprès d’Azure AD (une autre transformation se produit). |  7. l’utilisateur peut vous connecter à Office 365 et OWA à l’aide du jeton d’Azure AD. |
| 8. azure AD redirige l’utilisateur vers Office 365. |  |
| 9. l’utilisateur est en mode silencieux connecté à Office 365. |  |

Dans l’Office 365 avec la synchronisation d’annuaire avec le scénario de synchronisation de mot de passe (aucun Federation Services), l’ouverture de session unique est remplacé par « même session » où « même » signifie tout simplement que les utilisateurs doivent saisir à nouveau leurs informations d’identification sur site même lorsque vous accédez à Office 365. Notez que ces données peuvent être mémorisées par le navigateur de l’utilisateur afin de réduire les invites suivantes.

### <a name="additional-food-for-thought"></a>Réflexions

- Si vous déployez un proxy de Federation Services sur une machine virtuelle Azure, la connectivité aux serveurs AD FS est nécessaire. S’ils sont sur site, il est recommandé que vous exploitez la connectivité VPN site à site fournie par le réseau virtuel pour permettre aux nœuds du Proxy de l’Application Web communiquer avec leurs serveurs AD FS.

- Si vous déployez un serveur ADFS sur une machine virtuelle Azure, connectivité aux contrôleurs de domaine Windows Server Active Directory, les banques d’attribut et bases de données de Configuration n’est nécessaire et peut-être également nécessiter un ExpressRoute ou une connexion VPN de site à site entre le réseau virtuel Azure et le réseau local.

- Les frais sont appliqués à tout le trafic à partir des ordinateurs virtuels Azure (trafic sortant). Si le coût est le facteur déterminant, il est conseillé de déployer les nœuds de Proxy de l’Application Web sur Azure, laissant l’ADFS serveurs locaux. Si les serveurs ADFS sont déployés sur les ordinateurs virtuels Azure ainsi que, plus des frais pour authentifier les utilisateurs locaux. Le trafic de sortie entraîne un coût indépendamment de si ou pas il parcourt le ExpressRoute ou la connexion de site à site VPN.

- Si vous décidez d’utiliser des fonctionnalités de haute disponibilité des serveurs AD FS d’équilibrage de la charge de serveur natif d’Azure, notez que l’équilibrage de charge fournit des sondes qui sont utilisées pour déterminer l’état de santé des ordinateurs virtuels dans le service cloud. Dans le cas de machines virtuelles Azure (et non les rôles web ou travailleur), une sonde personnalisé doit être utilisée dans la mesure où l’agent répond aux sondes par défaut n’est pas présent sur les ordinateurs virtuels Azure. Par souci de simplicité, vous pouvez utiliser une sonde TCP personnalisé : ceci requiert seulement qu’une connexion TCP (un segment TCP SYN envoyé et a répondu à un segment TCP SYN ACK) être établie afin de déterminer la santé de l’ordinateur virtuel. Vous pouvez configurer la sonde personnalisé pour utiliser n’importe quel port TCP sur lequel vos ordinateurs virtuels sont activement à l’écoute.

> [AZURE.NOTE] Les ordinateurs qui doivent exposer le même ensemble de ports directement à Internet (tels que les ports 80 et 443) ne peuvent pas partager le même service de cloud. Il est, par conséquent, recommandé de créer un nuage dédié service pour les serveurs Windows Server AD FS afin d’éviter les chevauchements entre les conditions de port pour une application et Windows serveur AD FS.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

La section suivante décrit les scénarios de déploiement courants pour attirer l’attention sur des considérations importantes doivent être prises en compte. Chaque scénario comporte des liens vers plus d’informations sur les décisions et les facteurs à prendre en compte.

1. [Les services AD DS : Déployer une application AD DS-aware ne nécessitant pas de connectivité de réseau de l’entreprise](#BKMK_CloudOnly)

    Par exemple, un service orienté Internet SharePoint est déployé sur une machine virtuelle Azure. L’application a pas de dépendances sur les ressources du réseau d’entreprise. L’application a besoin de service d’annuaire Active Directory de Windows Server, mais ne nécessite pas le service d’annuaire Active Directory Windows Server d’entreprise.

2. [AD FS : Étendre une application frontale de revendications sur site à Internet](#BKMK_CloudOnlyFed)

    Par exemple, une application prenant en charge les revendications qui a été déployé avec succès sur site et utilisée par les utilisateurs de l’entreprise doit devenir accessible à partir d’Internet. L’application doit être accessible directement via Internet des partenaires commerciaux à l’aide de leurs propres identités d’entreprise et les utilisateurs existants de l’entreprise.

3. [Les services AD DS : Déployer une application Windows Server AD DS-aware nécessite une connectivité réseau de l’entreprise](#BKMK_HybridExt)

    Par exemple, une application prenant en charge LDAP qui prend en charge de l’authentification intégrée Windows et utilise le service d’annuaire Active Directory de Windows Server comme référentiel pour les données de configuration et de profil d’utilisateur est déployée sur une machine virtuelle Azure. Il est souhaitable que l’application d’exploiter le service d’annuaire Active Directory du serveur Windows d’entreprise existants et de fournir une ouverture de session unique. L’application n’est pas prenant en charge les revendications.

### <a name="BKMK_CloudOnly"></a>1. service d’annuaire Active Directory : Déployer une application AD DS prenant en charge à aucune exigence pour la connectivité de réseau de l’entreprise

![Déploiement des services AD DS nuage seule](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figure 1**

#### <a name="description"></a>Description

SharePoint est déployé sur une machine virtuelle Azure et que l’application a pas de dépendances sur les ressources du réseau d’entreprise. L’application nécessite le service d’annuaire Active Directory de Windows Server mais ne *pas* exiger le service d’annuaire Windows Active Directory du serveur d’entreprise. Aucun Kerberos ou les relations d’approbation fédérées ne sont nécessaires, car les utilisateurs sont automatiquement mis en service par l’intermédiaire de l’application dans le domaine de service d’annuaire Windows Server Active Directory également hébergé dans le nuage sur des machines virtuelles Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [Topologie de réseau](#BKMK_NetworkTopology): créer un réseau virtuel Azure sans connectivité de coexistence (également connu sous le nom de site à site connectivity).

- [Configuration de déploiement de contrôleurs de domaine](#BKMK_DeploymentConfig): déployer un contrôleur de domaine dans une forêt Active Directory de Windows Server de nouvelle et un domaine uniques,. Il doit être déployé avec le serveur DNS Windows.

- [Topologie de site Active Directory de Windows Server](#BKMK_ADSiteTopology): utiliser le site Windows Server Active Directory par défaut (tous les ordinateurs sera dans Default-First-Site-Name).

- [L’adressage IP et DNS](#BKMK_IPAddressDNS):

 - Définir une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
 - Installez et configurez Windows Server DNS sur les contrôleurs de domaine sur Azure.
 - Configurer les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur DNS et le contrôleur de domaine.

- [Catalogue global](#BKMK_GC): le premier contrôleur de domaine de la forêt doit être un serveur de catalogue global. Les contrôleurs de domaine supplémentaires doivent également être configurés en tant que catalogues globaux car dans une forêt à domaine unique, le catalogue global n’exige aucun travail supplémentaire à partir du contrôleur de domaine.

- [Emplacement de la base de données du service d’annuaire Windows Server Active Directory et SYSVOL](#BKMK_PlaceDB): ajouter un disque de données en cours d’exécution sous forme de VM d’Azure pour stocker la base de données Active Directory de Windows Server, les journaux et SYSVOL des contrôleurs de domaine.

- [Sauvegarde et restauration](#BKMK_BUR): déterminer où vous voulez stocker les sauvegardes d’état du système. Si nécessaire, ajoutez un autre disque de données de la machine virtuelle du contrôleur de domaine pour stocker des sauvegardes.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS : étendre une application frontale de revendications sur site à Internet

![Fédération avec connectivité de coexistence](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figure 2**

#### <a name="description"></a>Description

Une application prenant en charge les revendications qui a été déployé avec succès sur site et utilisée par les utilisateurs de l’entreprise doit devenir accessible directement à partir d’Internet. L’application sert à un site Web frontal web à une base de données SQL dans laquelle il stocke des données. Les serveurs SQL utilisés par l’application sont également disponibles sur le réseau d’entreprise. Deux STSs de FS Windows Server Active Directory et un équilibreur de charge ont été déployées sur site pour fournir l’accès aux utilisateurs d’entreprise. L’application doit en outre accessibles directement via Internet des partenaires commerciaux à l’aide de leurs propres identités d’entreprise et les utilisateurs existants de l’entreprise.

Dans le but de simplifier et de satisfaire les besoins de configuration et le déploiement de cette nouvelle exigence, il est décidé que deux autres web frontends et deux serveurs de proxy Windows Server AD FS être installé sur les machines virtuelles Azure. Tous les ordinateurs virtuels de quatre seront exposées directement à Internet et la connectivité au réseau local à l’aide de la fonctionnalité VPN de site à site de réseau virtuel Azure seront fournies.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [Topologie de réseau](#BKMK_NetworkTopology): créer un Azure réseau virtuel et [configurer la connectivité de coexistence](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Pour chacun des certificats de Windows Server AD FS, assurez-vous que l’URL définie dans le modèle de certificat et les certificats qui en résulte peut être atteint par les instances de FS de Windows Server, Active Directory exécute sur Azure. Cela peut nécessiter la connectivité de coexistence pour les parties de votre infrastructure à clé publique. Pour l’exemple si le point de terminaison de la CRL est basé sur LDAP et hébergé exclusivement sur site, puis coexistence connectivité sera nécessaire. Si ce n’est pas souhaitable, il peut être nécessaire d’utiliser des certificats émis par une autorité de certification dont CRL est accessible sur Internet.

- [Configuration de services de cloud](#BKMK_CloudSvcConfig): disposer des deux services en nuage dans l’ordre fournit deux adresses IP virtuelles à équilibrage de charge. Adresse IP virtuelle du premier service nuage est dirigé vers les deux Windows Server AD FS proxy VM sur les ports 80 et 443. Le proxy de Federation Services ordinateurs virtuels Windows Server Active Directory sera configuré pour pointer vers l’adresse IP de l’équilibreur de charge locale qui est face la STSs FS Windows Server Active Directory. Adresse IP virtuelle de la deuxième service cloud s’adressera aux deux ordinateurs virtuels relancer le système frontal web sur les ports 80 et 443. Configurer une sonde personnalisé afin de que l’équilibreur de charge dirige uniquement le trafic fonctionne Windows Server AD FS web et proxy système frontal VMs.

- [Configuration du serveur de fédération](#BKMK_FedSrvConfig): FS configurer Windows Active Directory du serveur comme un serveur de fédération (STS) pour générer des jetons de sécurité pour la forêt Active Directory de Windows Server créé dans le nuage. Fédération de demandes fournisseur des relations d’approbation avec les différents partenaires que vous souhaitez accepter des identités provenant et configurer des relations d’approbation partie utilisatrice avec les différentes applications que vous souhaitez générer des jetons à.

    Dans la plupart des scénarios, serveurs de proxy Windows Server AD FS sont déployés dans une capacité faisant face à Internet pour des raisons de sécurité, tandis que leurs homologues de fédération Windows Server AD FS restent isolés de connectivité directe à Internet. Quel que soit votre scénario de déploiement, vous devez configurer votre service cloud avec une adresse IP virtuelle qui fournira une adresse IP et le port qui est en mesure d’équilibrer la charge sur votre deux instances de FS STS Windows Server Active Directory ou des instances de proxy exposée publiquement.

- [Configuration de haute disponibilité Windows Server AD FS](#BKMK_ADFSHighAvail): il est recommandé de déployer une batterie de serveurs Windows Server AD FS avec au moins deux serveurs pour le basculement et l’équilibrage de la charge. Vous pouvez envisager l’utilisation de la base de données interne Windows (WID) pour les données de configuration de Windows Server, Active Directory Federation Services et utiliser la fonctionnalité d’équilibrage de la charge interne de Azure pour distribuer les requêtes entrantes sur les serveurs de la batterie.

Pour plus d’informations, consultez le [Guide de déploiement d’AD DS](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. service d’annuaire Active Directory : Déployer une application Windows Server AD DS-aware nécessite une connectivité réseau de l’entreprise

![Déploiement des services AD DS de coexistence](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figure 3**

#### <a name="description"></a>Description

Une application prenant en charge LDAP est déployée sur une machine virtuelle Azure. Il prend en charge de l’authentification intégrée Windows et utilise le service d’annuaire Active Directory de Windows Server comme référentiel pour les données de profil utilisateur et de configuration. L’objectif est d’application exploiter le service d’annuaire d’entreprise Windows Server Active Directory existant et de fournir une ouverture de session unique. L’application n’est pas prenant en charge les revendications. Les utilisateurs doivent également accéder à l’application directement à partir d’Internet. Pour optimiser les performances et coût, il est décidé que deux contrôleurs de domaine supplémentaires qui font partie du domaine d’entreprise être déployés avec l’application dans Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Considérations sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [Topologie de réseau](#BKMK_NetworkTopology): créer un réseau virtuel Azure avec [connectivité entre sites](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Méthode d’installation](#BKMK_InstallMethod): déployer des contrôleurs de domaine de réplica à partir du domaine Windows Server Active Directory d’entreprise. Pour un contrôleur de domaine de réplica, vous pouvez installer le service d’annuaire Active Directory du serveur Windows sur l’ordinateur virtuel et éventuellement utiliser la fonctionnalité installer à partir du média afin de réduire la quantité de données qui doivent être répliquées sur le nouveau contrôleur de domaine lors de l’installation. Pour voir un didacticiel, [installer un contrôleur de domaine Active Directory réplique sur Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Même si vous recourez à cette fonctionnalité, il peut être plus efficace de construire le virtuel contrôleur de domaine local et de déplacer le disque dur virtuel entier (VHD, Virtual Private Network) vers le nuage au lieu de répliquer le service d’annuaire Windows Server Active Directory lors de l’installation. Pour la sécurité, il est recommandé de supprimer le disque dur virtuel à partir du réseau local lorsqu’il a été copié vers Azure.

- [Topologie de site Active Directory de Windows Server](#BKMK_ADSiteTopology): créer un nouveau site Azure dans les Sites Active Directory et les Services. Créez un objet de sous-réseau Windows Server Active Directory pour représenter le réseau virtuel Azure et ajouter le sous-réseau au site. Créer un nouveau lien de sites qui inclut le nouveau site Azure et le site dans lequel le point de terminaison VPN du réseau virtuel Azure se trouve afin de contrôler et d’optimiser le trafic de Windows Server Active Directory vers et à partir d’Azure.

- [L’adressage IP et DNS](#BKMK_IPAddressDNS):

 - Définir une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
 - Installez et configurez Windows Server DNS sur les contrôleurs de domaine sur Azure.
 - Configurer les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur DNS et le contrôleur de domaine.

- [Geo-distribué les contrôleurs de domaine](#BKMK_DistributedDCs): configuration des réseaux virtuels supplémentaires si nécessaire. Si votre topologie de site Active Directory requiert des contrôleurs de domaine dans les régions qui correspondent aux différentes régions d’Azure, que vous souhaitez créer des sites Active Directory en conséquence.

- [Les contrôleurs de domaine en lecture seule](#BKMK_RODC): vous pouvez déployer un RODC dans le site d’Azure, en fonction de vos besoins en matière d’exécution écrire des opérations sur le contrôleur de domaine et la compatibilité des applications et des services sur le site avec le RODC. Pour plus d’informations sur la compatibilité des applications, consultez le [guide de compatibilité des applications Read-Only domaine contrôleurs](https://technet.microsoft.com/library/cc755190).

- [Catalogue global](#BKMK_GC): catalogues globaux est nécessaires pour traiter les demandes d’ouverture de session dans les forêts comportant plusieurs domaines. Si vous ne déployez pas un catalogue global dans le site d’Azure, vous implique des frais de trafic sortant dans la mesure où les demandes d’authentification des requêtes GC dans d’autres sites. Pour réduire ce trafic, vous pouvez activer la mise en cache de l’appartenance au groupe universel pour le site Azure dans les Sites Active Directory et les Services.

    Si vous déployez un GC, configurer des liens de site et les coûts des liens de site de sorte que le catalogue global dans le site Azure n’est pas par défaut comme une contrôleur de domaine source par d’autres catalogues globaux qui ont besoin de répliquer les partitions de domaine partielle même.

- [Emplacement de la base de données du service d’annuaire Windows Server Active Directory et SYSVOL](#BKMK_PlaceDB): ajouter un disque de données en cours d’exécution sur Azure VM pour stocker la base de données Active Directory de Windows Server, les journaux et SYSVOL des contrôleurs de domaine.

- [Sauvegarde et restauration](#BKMK_BUR): déterminer où vous voulez stocker les sauvegardes d’état du système. Si nécessaire, ajoutez un autre disque de données de la machine virtuelle du contrôleur de domaine pour stocker des sauvegardes.

## <a name="deployment-decisions-and-factors"></a>Facteurs et les décisions de déploiement

Le tableau suivant résume les zones de technologie Windows Server Active Directory qui sont affectés dans les scénarios précédents ainsi que les décisions correspondantes à prendre en compte, avec des liens vers plus en détail ci-dessous. Certaines zones de technologie peut ne pas être applicables à tous les scénarios de déploiement, et de certaines zones de technologie peuvent être plus critiques à un scénario de déploiement que les autres domaines technologiques.

Par exemple, si vous déployez un contrôleur de domaine de réplica sur un réseau virtuel, et votre forêt comporte un seul domaine, puis en choisissant de déployer un serveur de catalogue global dans ce cas seront pas critique pour le scénario de déploiement, car il ne crée pas des exigences de réplication supplémentaire. En revanche, si la forêt a plusieurs domaines, la décision de déployer un catalogue global sur un réseau virtuel peut affecter la bande passante disponible, les performances, l’authentification, les recherches dans l’annuaire, etc..

| Zone de technologie Windows Server Active Directory | Décisions | Facteurs |
| ---- | ---- | ---- |
| [Topologie de réseau](#BKMK_NetworkTopology) | Créer un réseau virtuel ? | <li>Configuration requise pour accéder aux ressources de l’entreprise</li> <li>Authentification</li> <li>Gestion des comptes</li> |
| [Configuration de déploiement de contrôleurs de domaine](#BKMK_DeploymentConfig) | <li>Déployer une forêt distincte sans les approbations ?</li> <li>Déployer une nouvelle forêt avec la fédération ?</li> <li>Déployer une nouvelle forêt avec approbation de forêt Active Directory de Windows Server ou Kerberos ?</li> <li>Étendre la forêt d’entreprise en déployant un contrôleur de domaine de réplica ?</li> <li>Étendre la forêt d’entreprise en déployant un nouveau domaine enfant ou une arborescence de domaine ?</li> | <li>Sécurité</li> <li>Respect de la réglementation</li> <li>Coût</li> <li>La résilience et la tolérance de panne</li> <li>Compatibilité des applications</li> |
| [Topologie de site Active Directory de Windows Server](#BKMK_ADSiteTopology) | Comment configurer des liens de site, les sites et les sous-réseaux avec Azure de réseau virtuel pour optimiser le trafic et de réduire les coûts ? | <li>Définitions de site et de sous-réseau</li> <li>Propriétés du lien de site et de notification de modification</li> <li>Compression de réplication</li> |
| [L’adressage IP et DNS](#BKMK_IPAddressDNS) | Comment faire pour configurer des adresses IP et résolution de noms ? | <li>L’applet de commande Utilisez la AzureStaticVNetIP de jeu permet d’affecter une adresse IP statique</li> <li>Installer le serveur DNS Windows Server et configurer les propriétés du réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur DNS et le contrôleur de domaine</li> |
| [Geo-distribué les contrôleurs de domaine](#BKMK_DistributedDCs) | Comment faire pour répliquer sur les contrôleurs de domaine sur des réseaux virtuels distincts ? | Si votre topologie de site Active Directory requiert des contrôleurs de domaine dans les zones géographiques correspondant à différentes régions Azure, que vous souhaitez créer des sites Active Directory en conséquence. [Configuration de réseau virtuel pour la connectivité de réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) pour la réplication entre les contrôleurs de domaine sur des réseaux virtuels distincts. |
| [Les contrôleurs de domaine en lecture seule](#BKMK_RODC) | Utiliser des contrôleurs de domaine en lecture seule ou en écriture ? | <li>Filtrer les attributs IEA/personnelles</li> <li>Secrets de filtre</li> <li>Trafic sortant de limite</li> |
| [Catalogue global](#BKMK_GC) | Installer le catalogue global ? | <li>Forêt à domaine unique, vérifiez tous les catalogues globaux de contrôleurs de domaine</li> <li>Pour une forêt à plusieurs domaines, les catalogues globaux est requis pour l’authentification</li> |
| [Méthode d’installation](#BKMK_InstallMethod) | Comment faire pour installer un contrôleur de domaine dans Azure ? | Soit : <li>Installer les services AD DS à l’aide de Windows PowerShell ou Dcpromo</li> <li>Déplacer le disque dur virtuel d’un contrôleur de domaine local virtuel</li> |
| [Emplacement de la base de données du service d’annuaire Windows Server Active Directory et SYSVOL](#BKMK_PlaceDB) | Emplacement de stockage de Windows Server AD DS base de données, les journaux et SYSVOL ? | Modifier les valeurs par défaut de Dcpromo.exe. Ces critiques Active Directory fichiers *doivent* être placés sur des disques de données Azure au lieu de disques du système d’exploitation qui implémentent la mise en cache d’écriture. |
| [Sauvegarde et restauration](#BKMK_BUR) | Comment faire pour sauvegarder et restaurer les données ? | Créer des sauvegardes d’état du système |
| [Configuration du serveur de fédération](#BKMK_FedSrvConfig) | <li>Déployer une nouvelle forêt avec la fédération dans le nuage ?</li> <li>Déployer AD FS sur site et exposer un proxy dans le nuage ?</li> | <li>Sécurité</li> <li>Respect de la réglementation</li> <li>Coût</li> <li>Accès aux applications des partenaires commerciaux</li> |
| [Configuration de services de cloud](#BKMK_CloudSvcConfig) | Un service en nuage est déployé implicitement de la première fois que vous créez un ordinateur virtuel. Vous avez besoin déployer les services de cloud supplémentaire ? | <li>Une machine virtuelle ou ordinateurs virtuels nécessite-t-elle l’exposition directe à Internet ?</li> <li> Le service requiert d’équilibrage de charge ?</li> |
| [Configuration requise du serveur de fédération (dynamique IP ou adresse IP virtuelle) d’adressage IP public et privé](#BKMK_FedReqVIPDIP) | <li>L’instance de FS Windows Server Active Directory doit-il être atteint directement à partir d’Internet ?</li> <li>L’application déployée dans le nuage requiert sa propre adresse IP de l’accès via Internet et un port ?</li> | Créer un service cloud pour chaque adresse IP virtuelle qui est requis par votre déploiement |
| [Configuration de haute disponibilité Windows Server AD FS](#BKMK_ADFSHighAvail) | <li>Le nombre de nœuds dans ma batterie de serveurs Windows Server AD FS ?</li> <li>Le nombre de nœuds à déployer dans ma batterie de serveurs proxy Windows Server AD FS ?</li> | Résilience et tolérance de panne |

### <a name="BKMK_NetworkTopology"></a>Topologie de réseau

Afin de respecter la cohérence des adresses IP et les exigences DNS de Windows Server AD DS, il faut tout d’abord créer un [Azure réseau virtuel](../virtual-network/virtual-networks-overview.md) puis de l’attacher vos ordinateurs virtuels. Lors de sa création, vous devez décider s’il faut étendre éventuellement de connectivité de votre réseau d’entreprise sur site qui connecte de façon transparente des ordinateurs virtuels Azure pour les ordinateurs locaux, cela s’effectue à l’aide des technologies VPN traditionnels et nécessite qu’un point de terminaison VPN être exposée sur le bord du réseau d’entreprise. Autrement dit, le VPN émane Azure au réseau d’entreprise, et vice versa pas.

Notez que des frais supplémentaires s’appliquent lors de l’extension d’un réseau virtuel pour votre réseau local au-delà de frais standard qui s’appliquent à chaque machine virtuelle. En particulier, il existe des frais pour le temps processeur de la passerelle réseau virtuel d’Azure et pour le trafic sortant généré par chaque ordinateur virtuel qui communique avec les ordinateurs locaux via le VPN. Pour plus d’informations sur les frais de trafic réseau, reportez-vous à la section [Azure tarification d’un coup de œil](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuration de déploiement de contrôleurs de domaine

La manière de configurer le contrôleur de domaine dépend de la configuration requise pour le service que vous souhaitez exécuter sur Azure. Par exemple, vous pouvez déployer une nouvelle forêt, isolée à partir de votre propre forêt de l’entreprise, pour tester un preuve de concept, d’une nouvelle application ou d’un autre projet à court terme qui nécessite des services d’annuaire, mais pas d’accès aux ressources d’entreprise internes.

Comme un avantage, une forêt isolée avec que ne réplique pas les contrôleurs de domaine local contrôleurs de domaine, moins réseau le trafic généré par le système lui-même, ce qui entraîne directement en réduisant les coûts. Pour plus d’informations sur les frais de trafic réseau, reportez-vous à la section [Azure tarification d’un coup de œil](http://azure.microsoft.com/pricing/).

Un autre exemple, supposons que vous avez des exigences de confidentialité d’un service, mais le service dépend de l’accès à votre Windows Server Active Directory interne. Si vous êtes autorisé à héberger des données pour le service dans le nuage, vous pouvez déployer un nouveau domaine enfant pour votre forêt interne sur Azure. Dans ce cas, vous pouvez déployer un contrôleur de domaine du nouveau domaine enfant (sans le catalogue global afin d’aider à résoudre les problèmes de confidentialité). Ce scénario, avec un déploiement de contrôleurs de domaine, de réplica nécessite un réseau virtuel pour la connectivité avec vos contrôleurs de domaine locaux.

Si vous créez une nouvelle forêt, choisissez si vous souhaitez utiliser [des approbations Active Directory](https://technet.microsoft.com/library/cc771397) ou [approbations de fédération](https://technet.microsoft.com/library/dd807036). Équilibrer les exigences émanant de la compatibilité, la sécurité, la conformité, coût et résilience. Par exemple, pour tirer parti de [l’authentification sélective](https://technet.microsoft.com/library/cc755844) , vous pouvez choisir de déployer une nouvelle forêt sur Azure et créer une approbation de Windows Server Active Directory entre les forêts local et le nuage. Toutefois, si l’application prend en charge les revendications, vous pouvez déployer les approbations de fédération au lieu d’approbations de forêt Active Directory. Un autre facteur sera le coût plus le trafic d’authentification et de la charge de requête de générer ou répliquer davantage de données en étendant votre local Windows Server Active Directory vers le nuage.

Exigences de disponibilité et tolérance aux pannes affectent également votre choix. Par exemple, si la liaison est interrompue, en exploitant une approbation Kerberos, soit une fédération de confiance des applications sont tous probablement entièrement rompus, sauf si vous avez déployé une infrastructure suffisante sur Azure. Les configurations de déploiement de remplacement tels que les contrôleurs de domaine de réplica (inscriptible ou RODC) augmentent la probabilité d’être en mesure de tolérer les pannes de liaison.

### <a name="BKMK_ADSiteTopology"></a>Topologie de site Active Directory de Windows Server

Vous devez définir correctement les sites et les liens de sites pour optimiser le trafic et de réduire les coûts. Les sites, les liens de sites et les sous-réseaux affectent la topologie de réplication entre les contrôleurs de domaine et le flux du trafic d’authentification. Considérez les frais le trafic suivant puis déployer et configurer des contrôleurs de domaine en fonction des exigences de votre scénario de déploiement :

- Il existe une somme insignifiante par heure pour la passerelle elle-même :

 - Il peut être démarré et arrêté selon vos besoins

 - Si arrêté, Azure VM sont isolés du réseau d’entreprise

- Le trafic entrant est gratuit

- Le trafic sortant est chargé, en fonction [d’un coup de œil la tarification Azure](http://azure.microsoft.com/pricing/). Vous pouvez optimiser les propriétés de lien de site entre les sites local et le nuage comme suit :

 - Si vous utilisez plusieurs réseaux virtuels, configurer les liens de sites et de leurs coûts appropriée pour empêcher le service d’annuaire Active Directory de Windows Server à partir de la définition des priorités du site Azure sur un objet qui peut fournir les mêmes niveaux de service sans frais. Vous pouvez également envisager la désactivation de la passerelle de tous les sites option lien (BASL) (qui est activée par défaut). Cela garantit que les sites uniquement directement connecté répliquent entre eux. Contrôleurs de domaine dans les sites connectés de manière transitive ne sont plus en mesure de répliquer directement entre eux, mais elle doivent être répliquée par le biais de sites ou un site commun. Si les sites intermédiaires deviennent indisponibles pour une raison quelconque, la réplication entre les contrôleurs de domaine dans les sites connectés de manière transitive ne produira pas même si la connectivité entre les sites est disponible. Enfin, dans le cas où les sections du comportement de la réplication transitive restent souhaitables, créer site des ponts de liaison qui contiennent les liens de sites appropriés et les sites, tels que les locaux, sites du réseau d’entreprise.

 - [Coûts de lien de site de configurer](https://technet.microsoft.com/library/cc794882) correctement afin d’éviter le trafic non sollicité. Par exemple, si vous **Essayez de Site le plus proche suivant** paramètre est activé, veillez à ce que les sites de réseau virtuel ne sont pas le proche en augmentant le coût associé à l’objet lien de sites qui connecte le site Azure au réseau de l’entreprise.

 - Configurer lien de site [intervalles](https://technet.microsoft.com/library/cc794878) et [planifications](https://technet.microsoft.com/library/cc816906) , en fonction des exigences de cohérence et de taux de modifications de l’objet. Aligner la planification de la réplication avec une tolérance de la latence. Contrôleurs de domaine répliquent uniquement le dernier état d’une valeur, afin de réduire l’intervalle de réplication peut réduire les coûts s’il existe un objet suffisamment modifier le taux.

- Si en minimisant les coûts est une priorité, assurez-vous que la réplication est planifiée et la notification de modification n’est pas activée. Il s’agit de la configuration par défaut lors de la réplication entre les sites. Ceci n’est pas important si vous déployez un RODC sur un réseau virtuel étant donné que le RODC ne va pas répliquer les modifications sortantes. Mais si vous déployez un contrôleur de domaine accessible en écriture, vous devez vous assurer de que la liaison de sites n’est pas configurée pour répliquer les mises à jour avec une fréquence de superflues. Si vous déployez un serveur de catalogue global (GC), assurez-vous que tous les autres sites qui contient un catalogue global réplique les partitions de domaine à partir d’une source de contrôleur de domaine dans un site qui est connecté au moyen d’un lien de sites ou les liens de sites qui ont un coût inférieur à celui de la CG dans le site d’Azure.


- Il est possible de réduire encore davantage de trafic réseau généré par la réplication entre les sites par modification de l’algorithme de compression de réplication. L’algorithme de compression est contrôlé par l’algorithme de compression HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator entrée REG_DWORD du Registre. La valeur par défaut est 3, ce qui correspond à l’algorithme de compression de Xpress. Vous pouvez modifier la valeur 2, ce qui modifie l’algorithme MSZip. Dans la plupart des cas, ceci augmentera la compression, mais cela se fait au détriment de l’utilisation du processeur. Pour plus d’informations, reportez-vous à la section [fonctionne de la façon dont Active Directory la topologie de réplication](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>L’adressage IP et DNS

Les machines virtuelles Azure sont allouées «-bail DHCP adresses » par défaut. Dans la mesure où les adresses dynamiques Azure réseau virtuel persistent avec une machine virtuelle pour la durée de vie de la machine virtuelle, les exigences de service d’annuaire Active Directory du serveur Windows sont remplies.

Par conséquent, lorsque vous utilisez une adresse dynamique sur Azure, vous êtes en effet à l’aide d’une adresse IP statique, car il est routable pour la période du contrat de location, et la durée du bail est égale à la durée de vie du service nuage.

Toutefois, l’adresse dynamique est libérée si la machine virtuelle est arrêtée. Pour éviter que l’adresse IP est libérée, vous pouvez [utiliser Set-AzureStaticVNetIP pour attribuer une adresse IP statique](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Pour la résolution de nom, déployer vos propres (ou optimisez votre) infrastructure de serveur DNS ; DNS Azure fourni ne respecte pas les besoins de résolution de nom avancé de service d’annuaire Active Directory de Windows Server. Par exemple, il ne pas prendre en charge les enregistrements SRV dynamiques et ainsi de suite. Résolution de noms est un élément de configuration critiques pour les contrôleurs de domaine et les clients à un domaine. Contrôleurs de domaine doivent être capables d’inscrire les enregistrements de ressources et de résoudre les enregistrements de ressources d’autres contrôleurs de domaine.
Pour des raisons de performances et de tolérance de pannes, il est souhaitable pour installer le service DNS de Windows Server sur les contrôleurs de domaine en cours d’exécution sur Azure. Puis, configurez les propriétés Azure réseau virtuel avec le nom et l’adresse IP du serveur DNS. Lorsque vous démarrez d’autres ordinateurs virtuels sur le réseau virtuel, leurs paramètres de résolution du client DNS seront configurés avec le serveur DNS en tant que partie de l’allocation d’adresse IP dynamique.

> [AZURE.NOTE] Impossible de connecter les ordinateurs locaux à un domaine de service d’annuaire Active Directory de Windows Server Active Directory qui est hébergé sur Azure directement sur Internet. La configuration de port requise pour Active Directory et l’opération de jonction de domaine rendent difficile à directement expose les ports nécessaires et en fait un contrôleur de domaine entier à Internet.

Ordinateurs virtuels inscrire leur nom DNS automatiquement au démarrage ou lors d’un changement de nom.

Pour plus d’informations sur cet exemple et un autre exemple qui montre comment mettre en service la premier VM et installer les services AD DS, voir [installation d’une nouvelle forêt Active Directory de Microsoft Azure](active-directory-new-forest-virtual-machine.md). Pour plus d’informations sur l’utilisation de Windows PowerShell, voir [Installer le PowerShell Azure](../powershell-install-configure.md) et [Azure les Cmdlets de gestion](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Geo-distribué les contrôleurs de domaine

Azure offre des avantages lors de l’hébergement de plusieurs contrôleurs de domaine sur des réseaux virtuels différents :

- Tolérance de pannes de plusieurs sites

- Proximité physique aux bureaux de succursale (faible latence)

Pour plus d’informations sur la configuration d’une communication directe entre les réseaux virtuels, reportez-vous à la section [configuration de réseau virtuel à la connectivité de réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Les contrôleurs de domaine en lecture seule

Vous devez choisir si vous souhaitez déployer des contrôleurs de domaine en lecture seule ou en écriture. Vous pourriez être incliné à déployer les RODC parce que vous n’aurez pas de contrôle physique, mais les RODC est conçues pour être déployées dans les emplacements où la sécurité physique est à risque, tels que les succursales.

Azure ne présente pas de risque de la sécurité physique d’une succursale, mais RODC peut encore s’avérer plus efficace dans la mesure où les fonctionnalités qu’ils fournissent sont bien adaptées à ces environnements bien que pour des raisons très différentes. Par exemple, les RODC n’ont aucune réplication sortante et est en mesure de remplir de manière sélective les secrets (mots de passe). En revanche, l’absence de ces secrets peut-être nécessiter le trafic sortant de la demande afin de les valider en tant qu’utilisateur ou ordinateur authentifie. Mais les secrets peuvent être sélectivement préremplies et mis en cache.

RODC fournit un avantage supplémentaire dans et autour de préoccupations IEA et les informations d’identification personnelle car vous pouvez ajouter des attributs qui contiennent des données sensibles sur le RODC filtré le jeu d’attributs (FAS). Les FA est un ensemble personnalisable d’attributs qui ne sont pas répliqués dans le RODC. Vous pouvez utiliser les ports FA sous la forme d’un dispositif de protection au cas où vous ne souhaitez pas stocker les informations d’identification personnelle ou IEA sur Azure ou ne sont pas autorisés. Pour plus d’informations, reportez-vous à la section [attribut filtrée de RODC. définir [(https://technet.microsoft.com/library/cc753459)]

Assurez-vous que les applications seront compatibles avec le RODC que vous prévoyez d’utiliser. De nombreuses applications compatibles Windows Server Active Directory fonctionnent correctement avec les RODC, mais certaines applications peuvent effectuer de façon inefficace ou échouer s’ils ne disposent pas de l’accès à un contrôleur de domaine accessible en écriture. Pour plus d’informations, consultez le [guide de compatibilité des applications les contrôleurs de domaine en lecture seule](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catalogue global

Vous devez choisir d’installer un catalogue global (GC). Dans une forêt à domaine unique, vous devez configurer tous les contrôleurs de domaine en tant que serveurs de catalogue global. Il n’augmente pas les coûts, car il n’y aura aucun trafic de réplication supplémentaire.

Dans une forêt à plusieurs domaines, les catalogues globaux est nécessaires pour développer des appartenances de groupe universel durant le processus d’authentification. Si vous ne déployez pas un catalogue global, les charges de travail sur le réseau virtuel qui s’authentifient par rapport à un DC sur Azure génère indirectement le trafic d’authentification sortante pour interroger le catalogue global local lors de chaque tentative d’authentification.

Les coûts associés aux catalogues globaux sont moins prévisible car ils hébergent chaque domaine (en partie). Si la charge de travail héberge un service faisant face à Internet et authentifie les utilisateurs par rapport à Windows Server AD DS, le coût peut être complètement imprévisible. Pour réduire les requêtes de catalogue global en dehors du site de nuage pendant l’authentification, vous pouvez [Activer le cache de l’appartenance au groupe universel](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Méthode d’installation

Vous devez choisir comment installer les contrôleurs de domaine sur le réseau virtuel :

- Promouvoir de nouveaux contrôleurs de domaine. Pour plus d’informations, reportez-vous à la section [installation d’une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md).

- Déplacer le disque dur virtuel d’un contrôleur de domaine local virtuel vers le nuage. Dans ce cas, vous devez vous assurer que le contrôleur de domaine local virtuel est « déplacé, « pas « copier » ou « clonés ».

Utiliser des machines virtuelles Azure uniquement pour les contrôleurs de domaine (et non « web » ou « travailleur » rôle Azure VM). Ils sont durables et durabilité de l’état d’un contrôleur de domaine est requise. Les machines virtuelles Azure sont conçues pour les charges de travail tels que les contrôleurs de domaine.

N’utilisez pas SYSPREP pour déployer ou cloner des contrôleurs de domaine. La possibilité de cloner des contrôleurs de domaine est uniquement disponible depuis Windows Server 2012. La fonction de clonage nécessite la prise en charge pour VMGenerationID l’hyperviseur sous-jacent. Hyper-V dans Windows Server 2012 et Azure des réseaux virtuels les deux prennent en charge VMGenerationID, comme les fournisseurs de logiciels de virtualisation de tiers.

### <a name="BKMK_PlaceDB"></a>Emplacement de la base de données du service d’annuaire Windows Server Active Directory et SYSVOL

Sélectionnez où localiser la base de données du service d’annuaire Active Directory de Windows Server, les journaux et SYSVOL. Ils doivent être déployés sur les disques de données d’Azure.

> [AZURE.NOTE] Les disques de données Azure sont limités à 1 To.

Lecteurs de disques de données ne font pas écrit de cache par défaut. Les lecteurs de disques de données associés à une machine virtuelle utiliser écriture via la mise en cache. Écriture via la mise en cache de fait que l’écriture tient à stockage Azure durable avant la fin du point de vue du système d’exploitation de la machine virtuelle la transaction. Il assure la durabilité et au détriment des écritures légèrement plus lentes.

Ceci est important pour le service d’annuaire Windows Server Active Directory parce que l’écriture différée-mise en cache disque invalide les hypothèses faites par le contrôleur de domaine. Service d’annuaire Active Directory du serveur Windows essaie de désactiver le cache d’écriture, mais il est sur le disque système d’e/s y répondre. Échec de désactiver le cache d’écriture peut, dans certaines circonstances, introduire reprise USN résultant dans l’attente des objets et autres problèmes.

Recommandé pour les contrôleurs de domaine virtuels, effectuez les opérations suivantes :

- Définir le paramètre de préférence de Cache hôte sur le disque de données Azure pour aucun. Cela évite les problèmes liés à la mise en cache d’écriture pour les opérations de service d’annuaire Active Directory.

- Stocker la base de données, des journaux et SYSVOL sur les deux mêmes données ou les disques de données séparés. En général, il s’agit d’un disque distinct du disque utilisé pour le système d’exploitation lui-même. Clé à retenir est que la base de données du service d’annuaire Windows Server Active Directory et SYSVOL ne doivent pas être stockées sur un type de disque du système d’exploitation de Azure. Par défaut, le processus d’installation de AD DS installe ces composants dans le dossier % SystemRoot%, qui n’est pas recommandé pour Azure.

### <a name="BKMK_BUR"></a>Sauvegarde et restauration

N’oubliez pas de quoi est n'est pas pris en charge pour la sauvegarde et la restauration d’un contrôleur de domaine en général et, plus particulièrement celles en cours d’exécution sur un ordinateur virtuel. Reportez-vous à la section [sauvegarde et restauration pour les contrôleurs de domaine virtualisés](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Créer des sauvegardes d’état du système en utilisant uniquement les logiciels de sauvegarde qui sont spécifiquement prenant en charge des besoins en sauvegarde pour le service d’annuaire Active Directory de Windows Server, comme la sauvegarde de Windows Server.

Ne pas copier ou clone les fichiers VHD des contrôleurs de domaine au lieu d’effectuer des sauvegardes régulières. Une restauration jamais est requise, ce faisant, utilisant des disques durs virtuels clonés ou copiés sans Windows Server 2012 et d’un hyperviseur pris en charge présente les bulles USN.

### <a name="BKMK_FedSrvConfig"></a>Configuration du serveur de fédération

La configuration des serveurs de fédération Windows Server Active Directory Federation Services (STSs) dépend en partie de si les applications que vous souhaitez déployer sur Azure ont besoin d’accéder aux ressources de votre réseau local.

Si elles répondent aux critères suivants, vous pourriez déployer les applications de manière isolée à partir de votre réseau local.

- Ils acceptent des jetons de sécurité SAML

- Ils sont exposable à Internet

- Ils n’ont pas accès aux ressources de locaux

Dans ce cas, configurez le Windows Server AD FS STSs comme suit :

1. Configurer une forêt de domaine unique isolée sur Azure.

2. Fournir un accès fédéré à la forêt en configurant une batterie de serveurs de fédération AD FS de Windows Server.

3. Configurer Windows Server AD FS (batterie de serveurs de fédération et batterie de proxy de serveur de fédération) dans la forêt locale.

4. Établir une relation d’approbation de fédération entre les locaux et les instances Azure de Windows Server, Active Directory Federation Services.

En revanche, si les applications requièrent l’accès à des ressources sur site, vous pouvez configurer Windows Server AD FS avec l’application dans Azure comme suit :

1. Configurer la connectivité entre les réseaux locaux et Azure.

2. Configurer une batterie de serveurs Windows Server AD FS fédération dans la forêt locale.

3. Configurer une batterie de proxy de serveur de fédération AD FS de Windows Server sur Azure.

Cette configuration présente l’avantage de réduire l’exposition des ressources locales, similaires à la configuration Windows Server AD FS avec des applications dans un réseau de périmètre.

Notez que, dans les deux cas, vous pouvez établir les relations d’approbation avec plusieurs fournisseurs d’identité, si vous avez besoin de collaboration d’entreprise-entreprise.

### <a name="BKMK_CloudSvcConfig"></a>Configuration de services de cloud

Les services en nuage sont nécessaires si vous voulez exposer un ordinateur virtuel directement à Internet ou pour exposer une application d’équilibrage de charge via Internet. Ceci est possible parce que chaque service en nuage offre une seule adresse IP virtuelle configurable.

### <a name="BKMK_FedReqVIPDIP"></a>Configuration requise du serveur de fédération (dynamique IP ou adresse IP virtuelle) d’adressage IP public et privé

Chaque machine virtuelle Azure reçoit une adresse IP dynamique. Une adresse IP dynamique est une adresse privée accessible uniquement dans Azure. Dans la plupart des cas, cependant, il sera nécessaire de configurer une adresse IP virtuelle pour vos déploiements Windows Server AD FS. L’adresse IP virtuelle est nécessaire afin d’exposer les points de terminaison Windows Server AD FS à Internet et sera utilisé par les clients et les partenaires fédérés pour l’authentification et de gestion continue. Une adresse IP virtuelle est une propriété d’un service de cloud qui contient une ou plusieurs machines virtuelles Azure. Si l’application prenant en charge les revendications déployée sur Azure et Windows Server AD FS est exposés à Internet et partage des ports courants, chacun nécessite une adresse IP virtuelle qui lui sont propres, et il sera donc nécessaire de créer un service cloud pour l’application et une seconde pour Windows Server AD FS.

Pour la définition de l’adresse IP virtuelle de conditions et d’une adresse IP dynamique, consultez les [termes et définitions](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuration de haute disponibilité Windows Server AD FS

Bien qu’il soit possible de déployer des services de fédération AD FS de Windows Server autonome, il est recommandé de déployer une batterie de serveurs au moins deux nœuds pour AD FS STS et proxys pour les environnements de production.

Voir [AD FS 2.0 topologie considérations sur le déploiement](https://technet.microsoft.com/library/gg982489) dans le [AD FS 2.0 Design Guide](https://technet.microsoft.com/library/dd807036) pour déterminer les options de configuration de déploiement les répondre à vos besoins particuliers.

> [AZURE.NOTE] Afin d’obtenir l’équilibrage de charge pour les points de terminaison Windows Server AD FS sur Azure, configurer tous les membres de la batterie de serveurs Windows Server AD FS dans le même service de cloud et utiliser la fonctionnalité d’équilibrage de la charge d’Azure pour HTTP (par défaut 80) et les ports HTTPS (valeur par défaut 443). Pour plus d’informations, voir [équilibrage de la charge Azure sonde](https://msdn.microsoft.com/library/azure/jj151530).
Windows serveur NLB Network Load Balancing () n’est pas pris en charge sur Azure.
