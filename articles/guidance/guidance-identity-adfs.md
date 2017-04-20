<properties
   pageTitle="Mise en œuvre d’ADFS dans Azure | Microsoft Azure"
   description="Comment implémenter une architecture de réseau sécurisée hybride avec l’autorisation du Service de fédération Active Directory dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network,active-directory"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/13/2016"
   ms.author="telmos"/>

# <a name="implementing-active-directory-federation-services-adfs-in-azure"></a>Mise en œuvre d’Active Directory Federation Services (ADFS) dans Azure

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les méthodes conseillées pour l’implémentation d’un réseau hybride sécurisé qui permet d’étendre votre réseau local vers Azure, et qui utilise [Active Directory Federation Services (ADFS)] [ active-directory-federation-services] pour effectuer des fédérés d’authentification et d’autorisation pour les composants s’exécutant dans le nuage. Cette architecture s’étend de la structure décrite par [Extension de Active Directory vers Azure][implementing-active-directory].

> [AZURE.NOTE] Azure dispose de deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise un gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

ADFS peut s’exécuter sur place, mais dans un scénario hybride où les applications se trouvent dans Azure, il peut être plus efficace d’implémenter cette fonctionnalité dans le nuage. Scénarios d’utilisation classiques de cette architecture sont les suivantes :

- Les applications hybride dans lequel exécuter des charges de travail partiellement sur site et partiellement dans Azure.

- Solutions utilisant l’autorisation fédérée à exposer les applications web aux organisations partenaires.

- Systèmes qui prennent en charge les accès à partir de navigateurs web s’exécutant en dehors du pare-feu de votre entreprise.

- Systèmes qui permettent aux utilisateurs d’accéder aux applications web en vous connectant à partir de périphériques externes autorisés, tels que des ordinateurs distants, ordinateurs portables et autres périphériques mobiles. 

Pour plus d’informations sur la façon dont ADFS fonctionne, consultez [Vue d’ensemble de Active Directory Federation Services][active-directory-federation-services-overview]. En outre, l’article [Déploiement ADFS dans Azure] [ adfs-intro] contient une introduction détaillée étape par étape pour la mise en œuvre d’ADFS dans Azure.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le schéma suivant met en évidence les éléments importants de cette architecture (*Cliquez pour agrandir*). Pour plus d’informations sur tout élément non liée à ADFS, lire la [mise en œuvre d’une architecture de réseau sécurisée hybride dans Azure][implementing-a-secure-hybrid-network-architecture], [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]et [l’implémentation d’une architecture de réseau sécurisée hybride avec des identités Active Directory dans Azure][implementing-active-directory]:

[! [0]][0]

>[AZURE.NOTE] Ce diagramme décrit les cas d’utilisation suivants :
>
>- Code de l’application en cours d’exécution à l’intérieur d’une organisation partenaire accède à une application web hébergée à l’intérieur de votre VNet d’Azure.
>
>- Un utilisateur externe, enregistré (avec les informations d’identification stockées à l’intérieur d’ajoute) accédant à une application web hébergée à l’intérieur de votre VNet d’Azure.
>
>- Un utilisateur se connecte à votre VNet en utilisant un dispositif agréé en exécutant une application web hébergée à l’intérieur de votre VNet d’Azure.
>
>En outre, cette architecture se concentre sur la fédération passive, où les serveurs de fédération décident quand et comment pour authentifier un utilisateur. L’utilisateur doit fournir des informations de connexion lorsqu’une application démarre en cours d’exécution. C’est le mécanisme généralement utilisé par les navigateurs web et implique un protocole qui redirige le navigateur vers un site où les utilisateurs peuvent fournir leurs informations d’identification. ADFS prend également en charge la fédération active dans laquelle une application prend la responsabilité de fournir les informations d’identification sans autre intervention de l’utilisateur, mais que ce cas est en dehors de la portée de cette architecture.

- **Sous-réseau de l’ajoute.** Les serveurs ajoute sont contenus dans leur propre sous-réseau. Règles NSG pour protéger les serveurs ajoute et peuvent fournir un pare-feu contre le trafic de sources inconnues.

- **Ajoute des serveurs.** Il s’agit de contrôleurs de domaine en cours d’exécution en tant qu’ordinateurs virtuels dans le nuage. Ces serveurs peuvent fournir l’authentification des identités locales au sein du domaine.

- **Sous-réseau d’ADFS.** Les serveurs ADFS peuvent se trouver au sein de leur propre sous-réseau, avec les règles NSG agissant en tant que pare-feu.

- **Serveurs ADFS.** Les serveurs ADFS fournissent l’authentification et l’autorisation fédérée. Dans cette architecture, ils effectuent les tâches suivantes :

    - Ils peuvent recevoir des jetons de sécurité contenant les déclarations effectuées par un serveur de fédération du partenaire pour un utilisateur partenaire. ADFS peut vérifier que ces jetons sont valides avant de transmettre les demandes à l’application web s’exécutant dans Azure. L’application web d’entreprise (dans Azure) peut utiliser ces déclarations pour autoriser les demandes. Dans ce scénario, l’application web d’entreprise est la *partie utilisatrice*, et il est de la responsabilité du serveur de fédération partenaire à émettre des revendications qui sont comprises par l’application web d’entreprise. Les serveurs de fédération du partenaire sont désignées en tant que *partenaires de compte* car ils soumettent des demandes d’accès pour le compte de comptes authentifiés de l’organisation partenaire. Les serveurs ADFS sont appelés *partenaires de ressources* , car ils fournissent un accès aux ressources (applications web, dans le cas présent).

    - Ils peuvent s’authentifier (via ADDS et le [Service d’enregistrement de périphérique Active Directory][ADDRS]) et autoriser les demandes entrantes émanant des utilisateurs externes en cours d’exécution un navigateur web ou un périphérique qui a besoin d’accéder à vos applications web d’entreprise. 

    Les serveurs ADFS sont configurés en tant que batterie, accédée via un équilibreur de charge Azure. Cette structure permet d’améliorer la disponibilité et l’évolutivité. En outre, notez que les serveurs ADFS ne sont pas exposés directement sur Internet, plutôt tout le trafic Internet est filtré à travers les serveurs proxy ADFS web application et un réseau de périmètre.

- **Sous-réseau de proxy ADFS.** Les serveurs proxy d’ADFS peuvent être contenues au sein de leur propre sous-réseau, avec les règles NSG assurant la protection. Les serveurs de ce sous-réseau sont exposés à Internet via un ensemble de matériel de réseau virtuel qui fournit un pare-feu entre votre réseau virtuel Azure et Internet.

- **Serveurs proxy (WAP), d’applications web ADFS.** Ces ordinateurs jouent le rôle en tant que serveurs ADFS pour les demandes entrantes à partir de périphériques externes et les organisations partenaires. Les serveurs WAP agissent en tant que filtre, protection contre les serveurs ADFS d’un accès direct à partir de l’Internet public. Comme avec les serveurs ADFS, déployer le WAP serveurs dans une batterie de serveurs avec équilibrage de charge vous donne plus de disponibilité et d’évolutivité que le déploiement d’un ensemble de serveurs autonomes.

    >[AZURE.NOTE] Pour plus d’informations sur l’installation de serveurs WAP, voir [installer et configurer le serveur de Proxy d’Application Web][install_and_configure_the_web_application_proxy_server]

- **Organisation de partenaire.** Il s’agit d’une exemple partenaire d’entreprise, qui s’exécute une application web qui demande l’accès à l’application web en cours d’exécution dans Azure. Le serveur de fédération de l’organisation partenaire authentifie les requêtes localement et soumet les jetons de sécurité contenant des revendications dans ADFS exécuté dans Azure. ADFS dans Azure valide les jetons de sécurité, et si elles sont valides, il peut transmettre les demandes à l’application web en cours d’exécution dans Azure pour autoriser les.

    >[AZURE.NOTE] Vous pouvez également configurer un tunnel VPN à l’aide de la passerelle d’Azure pour fournir un accès direct à ADFS pour les partenaires approuvés. Demandes reçues à partir de ces partenaires ne passent pas par les serveurs WAP.

## <a name="recommendations"></a>Recommandations

Cette section résume les recommandations pour la mise en œuvre d’ADFS dans Azure, couvrant :

- Recommandations de la machine virtuelle.

- Recommandations de mise en réseau.

- Recommandations de disponibilité.

- Recommandations de sécurité.

- Recommandations d’installation ADFS.

- Recommandations d’approbation ADFS.

### <a name="vm-recommendations"></a>Recommandations de machine virtuelle

Créer des ordinateurs virtuels avec des ressources suffisantes pour gérer le volume prévu du trafic. Utiliser la taille de l’hébergement ADFS sur le site comme point de départ des ordinateurs existants. Surveiller l’utilisation des ressources. Vous pouvez redimensionner les ordinateurs virtuels et les mettre à l’échelle vers le bas si elles sont trop grandes.

Suivez les recommandations figurant dans [l’exécution d’une machine virtuelle Windows sur Azure][vm-recommendations].

### <a name="networking-recommendations"></a>Recommandations de mise en réseau

Configurer l’interface réseau pour chacun des ordinateurs virtuels hébergeant des serveurs ADFS et WAP avec des adresses IP statiques privées.

Ne donnez pas les adresses IP publiques de machines virtuelles d’ADFS. Pour plus d’informations, consultez [Considérations relatives à la sécurité][security-considerations].

Définir l’adresse IP des serveurs DNS préférés et secondaires pour les interfaces réseau pour chaque ADFS et le WAP VM référencer les ordinateurs virtuels ajoute (qui doit être en cours d’exécution DNS). Cette étape est nécessaire pour activer chaque ordinateur virtuel de joindre le domaine.

### <a name="availability-recommendations"></a>Recommandations de disponibilité

Créer une batterie de serveurs ADFS avec au moins deux serveurs pour accroître la disponibilité du service ADFS.

Utilisez des comptes de stockage différentes pour chaque VM ADFS dans la batterie de serveurs. Cette approche permet de s’assurer qu’une panne dans un compte de stockage unique ne rend pas la batterie entière inaccessible.

Créer des ensembles de disponibilité d’Azure distinct pour l’ADFS et les ordinateurs virtuels de WAP. Vérifiez qu’il existe au moins deux VM dans chaque jeu. Chaque jeu de disponibilité doit avoir au moins deux domaines de la mise à jour et les deux domaines d’erreur.

Configurez les équilibreurs de charge pour les ordinateurs virtuels d’ADFS et des ordinateurs virtuels de WAP comme suit :

- Utilisez un équilibreur de charge Azure pour fournir l’accès externe aux ordinateurs virtuels WAP et un équilibreur de charge interne pour répartir la charge entre les serveurs ADFS dans la batterie de serveurs ADFS.

- Ne transmettre que le trafic figurant sur le port 443 (HTTPS) vers les serveurs ADFS/WAP.

- Donner une adresse IP statique à l’équilibreur de charge.

- Créer une sonde de santé utilisant le protocole TCP plutôt que de HTTPS. Vous pouvez tester le port 443 pour vérifier qu’un serveur ADFS fonctionne.

    >[AZURE.NOTE] Serveurs ADFS utilisent le protocole d’Indication de nom de serveur (SNI), que de tenter de sonde à l’aide d’un point de terminaison HTTPS à partir de l’échec d’équilibrage de la charge.

- Ajouter un enregistrement DNS *A* pour le domaine de l’équilibreur de charge ADFS. 

    Spécifiez l’adresse IP de l’équilibreur de charge et donnez-lui un nom dans le domaine (par exemple, adfs.contoso.com). C’est le nom par lequel les clients et les serveurs WAP accéder à la batterie de serveurs ADFS.

### <a name="security-recommendations"></a>Recommandations de sécurité

Éviter l’exposition directe des serveurs ADFS à Internet. Serveurs ADFS sont les ordinateurs joints au domaine disposant d’une autorisation complète d’accorder des jetons de sécurité. Si un serveur ADFS est compromis, un utilisateur malveillant peut émettre des jetons d’accès complet à toutes les applications web et aux serveurs de fédération qui sont protégés par ADFS. Si votre système doit gérer les requêtes des utilisateurs externes n’est pas nécessairement reliant les sites partenaires de confiance, utilisez les serveurs WAP pour traiter ces demandes. Pour plus d’informations, consultez la rubrique [placer un serveur Proxy de fédération][where-to-place-an-fs-proxy].

Les serveurs ADFS de place et WAP dans des sous-réseaux distincts avec leurs propres pare-feu. Vous pouvez utiliser des règles NSG pour définir des règles de pare-feu. Si vous avez besoin d’une protection plus complète vous pouvez implémenter un périmètre de sécurité autour des serveurs à l’aide d’une paire de sous-réseaux et NVAs, comme décrit dans le document de [mise en œuvre d’une architecture de réseau sécurisée hybride avec accès à Internet dans Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access]. Notez que tous les pare-feu doivent autoriser le trafic sur le port 443 (HTTPS).

Restreindre l’accès de la connexion directe aux serveurs ADFS et WAP. Seul le personnel DevOps doit pouvoir se connecter.

Ne pas joindre les serveurs WAP au domaine.

### <a name="adfs-installation-recommendations"></a>Recommandations d’installation ADFS

L’article de [déploiement d’une batterie de serveurs de fédération] [ Deploying_a_federation_server_farm] fournit des instructions détaillées pour l’installation et la configuration d’ADFS. Avant de configurer le premier serveur ADFS dans la batterie de serveurs, effectuez les tâches suivantes :

1. Obtenir un certificat public approuvé pour effectuer l’authentification du serveur. Le *nom du sujet* doit contenir le nom par lequel les clients accèdent au service de fédération. Cela peut être le nom DNS inscrit pour l’équilibrage de charge, par exemple, *adfs.contoso.com* (évitez d’utiliser les noms génériques tels que **. contoso.com*, pour des raisons de sécurité). Utiliser le même certificat sur tous les ordinateurs virtuels de serveur ADFS. Vous pouvez acheter un certificat auprès d’une autorité de certification de confiance, mais si votre organisation utilise les Services de certificat Active Directory vous pouvez créer votre propre. 

    L' *autre nom du sujet* est utilisée par le service DRS pour permettre l’accès à partir de périphériques externes. Il s’agit de forme *enterpriseregistration.contoso.com*.

    Pour plus d’informations, consultez [obtenir et configurer un certificat SSL pour ADFS][adfs_certificates].

2. Sur le contrôleur de domaine, générer une nouvelle clé racine pour le Service de Distribution de clés. Définir la durée effective à l’heure actuelle, moins de 10 heures (cette configuration réduit le délai qui peut se produire dans la distribution et la synchronisation des clés dans le domaine). Cette étape est nécessaire pour prendre en charge la création du compte de service de groupe qui sont utilisées pour exécuter le service ADFS. La commande Powershell suivante montre un exemple de cette procédure :

    ```powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ```

3. Ajouter chaque serveur ADFS VM au domaine.

>[AZURE.NOTE] Pour installer ADFS, le contrôleur de domaine exécutant l’émulateur de contrôleur principal de domaine de rôle FSMO pour le domaine doit être en cours d’exécution et accessible à partir d’ordinateurs virtuels ADFS.

### <a name="adfs-trust-recommendations"></a>ADFS d’approbation des recommandations

Établir une approbation de fédération entre votre installation ADFS et les serveurs de fédération des organisations partenaires. Configurez tout filtrage de revendications et le mappage requis. Ce processus nécessite :

- DevOps personnel **chaque organisation partenaire** pour ajouter une approbation de tiers de confiance pour les applications web accessibles par le biais de vos serveurs ADFS.

- DevOps personnel **dans votre organisation** pour configurer l’approbation des demandes fournisseur pour activer vos serveurs ADFS d’approuver les déclarations qui les organisations partenaires fournissent.

- DevOps personnel **dans votre organisation** pour configurer ADFS pour transmettre les demandes à des applications web de votre organisation.

    Pour plus d’informations, consultez [Établissement d’une approbation de fédération][establishing-federation-trust].

Publier des applications web de votre société et les rendre disponibles pour les partenaires externes à l’aide de la pré-authentification via les serveurs WAP. Pour plus d’informations, consultez [publier des Applications à l’aide de la pré-authentification d’ADFS][publish_applications_using_AD_FS_preauthentication]

Notez que ADFS prend en charge une augmentation et transformation de jeton. Azure Active Directory ne fournit pas cette fonctionnalité. Avec ADFS, lorsque vous configurez les relations d’approbation, vous pouvez :

- Configurez des transformations de sollicitations pour les règles d’autorisation. Par exemple, vous pouvez mapper la sécurité de groupe à partir d’une représentation utilisée par une organisation partenaire de non Microsoft pour quelque chose qui ajoute peut autoriser dans votre organisation.

- Transformation des revendications d’un format à un autre. Par exemple, vous pouvez mapper de SAML 2.0 SAML 1.1 si votre application prend uniquement en charge les revendications SAML 1.1. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Vous pouvez utiliser SQL Server ou la base de données interne Windows (WID) pour conserver les informations de configuration d’ADFS. WID fournit la redondance de base. Les modifications sont écrites directement à une seule des bases de données ADFS dans le cluster ADFS, tandis que les autres serveurs utilisent la réplication par réception à jour leurs bases de données. À l’aide de SQL Server peut fournir la redondance de la base de données complète et une haute disponibilité à l’aide de la mise en miroir ou de clustering de basculement.

## <a name="security-considerations"></a>Considérations sur la sécurité

ADFS utilise le protocole HTTPS, assurez-vous que les règles NSG pour le sous-réseau contenant le site web de niveau demandes HTTPS de permis de machines virtuelles. Ces demandes peuvent provenir du réseau local, les sous-réseaux qui contient la couche web, couche d’entreprise, couche données, DMZ privée, DMZ publique et le sous-réseau contenant les serveurs ADFS.

Envisagez d’utiliser un ensemble de matériel de réseau virtuel qui enregistre des informations détaillées sur le trafic transitant par la périphérie de votre réseau virtuel à des fins d’audit.

## <a name="scalability-considerations"></a>Évolutivité

Les considérations suivantes, résumées à partir du document de [planification de votre déploiement d’ADFS][plan-your-adfs-deployment], donner un point de départ pour les batteries de serveurs ADFS de redimensionnement :

- Si vous avez moins de 1 000 utilisateurs, ne créez pas de serveurs dédiés de ADFS, mais plutôt installer ADFS sur chacun des serveurs dans le nuage ajoute (Assurez-vous que vous disposez d’au moins deux serveurs ajoute, pour maintenir la disponibilité). Créer un seul serveur WAP.

- Si vous avez entre 1000 et 15000 utilisateurs, créez deux serveurs ADFS dédiées et deux serveurs dédiés de WAP.

- Si vous avez entre 15000 et 60000 utilisateurs, créer entre trois et cinq serveurs ADFS dédiés et au moins deux serveurs dédiés de WAP.

Ces chiffres supposent que vous utilisez deux ordinateurs virtuels à quatre cœurs (D4_v2 Standard, ou mieux) pour héberger les serveurs dans Azure.

Notez que si vous utilisez la base de données interne Windows pour stocker les données de configuration d’ADFS, vous êtes limité à huit serveurs ADFS dans la batterie de serveurs. Si vous pensez avoir besoin plus, puis utiliser SQL Server. Pour plus d’informations, voir [Le rôle de la base de données de Configuration ADFS][adfs-configuration-database].

## <a name="management-considerations"></a>Considérations relatives à la gestion

DevOps personnel doit être préparé à effectuer les tâches suivantes :

- Gérer les serveurs de fédération (gestion de la batterie de serveurs ADFS, gestion de la stratégie d’approbation sur les serveurs de fédération et gérer les certificats utilisés par les services de fédération).

- Gestion des serveurs WAP (gestion de la batterie de serveurs WAP, gérer les certificats WAP).

- Gestion des applications web (configuration des mappages de revendications, les parties et les méthodes d’authentification).

- Sauvegarde des composants ADFS.

## <a name="monitoring-considerations"></a>Considérations relatives au contrôle

Le [Pack de gestion Microsoft System Center pour Active Directory Federation Services 2012 R2] [ oms-adfs-pack] fournit une surveillance proactive et réactive de votre déploiement d’ADFS pour le serveur de fédération. Ce pack d’administration surveille :

- Événements que l’ADFS service d’enregistrements dans les journaux des événements ADFS.

- Les données de performances recueillies par les compteurs de performance d’ADFS. 

- La santé générale de l’applications système et web ADFS (parties de confiance) et fournit des alertes pour les problèmes critiques et les avertissements.

## <a name="solution-components"></a>Composants de la solution

Un exemple de script de solution, [Déployer-ReferenceArchitecture.ps1][solution-script], n’est disponible que vous pouvez utiliser pour mettre en œuvre de l’architecture qui suit les recommandations décrites dans cet article. Ce script utilise le Gestionnaire de ressources Azure modèles. Les modèles sont disponibles sous la forme d’un ensemble de blocs de construction fondamentaux, dont chacun exécute une action spécifique comme la création d’un VNet ou configurer un NSG. L’objectif du script est d’orchestrer le déploiement du modèle.

Les modèles sont paramétrées, avec les paramètres conservés dans des fichiers distincts de JSON. Vous pouvez modifier les paramètres dans ces fichiers pour configurer le déploiement pour répondre à vos besoins. Vous n’avez pas besoin de modifier les modèles eux-mêmes. Notez que vous ne devez pas modifier les schémas des objets dans les fichiers de paramètres.

Lorsque vous modifiez les modèles, créer des objets qui suivent les conventions d’attribution de noms décrites dans [Recommandé des Conventions d’affectation de noms pour les ressources d’Azure][naming-conventions].

L’exemple de solution crée et configure l’environnement dans le nuage comprenant la DMZ de sous-réseau et les serveurs, le sous-réseau de l’ADFS, sous-réseau de proxy ADFS et les serveurs, ajoute, couche web, couche d’entreprise et composants de couche d’accès aux données, passerelle VPN et couche de gestion. L’exemple de solution inclut également une configuration facultative pour la création d’un environnement simulé sur site.

Les sections suivantes décrivent les éléments de la locale et configurations en nuage.

### <a name="on-premises-components"></a>Composants locaux

>[AZURE.NOTE] Ces composants ne sont pas le principal objectif de l’architecture décrite dans ce document et sont fournis pour vous permettre de tester en toute sécurité, de l’environnement en nuage au lieu d’utiliser un environnement de production réel. Pour cette raison, cette section résume uniquement les fichiers de paramètres de clé. Vous pouvez modifier des paramètres tels que les adresses IP ou les tailles des ordinateurs virtuels, mais il est recommandé de laisser la plupart des autres paramètres inchangés.

Cet environnement compose d’une forêt Active Directory pour un domaine nommé contoso.com. Le domaine contient deux serveurs ajoute les adresses IP 192.168.0.4 et 192.168.0.5. Ces deux serveurs exécutent également le service DNS. Le compte d’administrateur local sur les deux ordinateurs virtuels est appelé `testuser` avec le mot de passe `AweS0me@PW`. En outre, la configuration définit une passerelle VPN pour se connecter à la VNet dans le nuage. Vous pouvez modifier la configuration en modifiant les fichiers JSON suivants qui se trouve dans les [**paramètres/locales**] [ on-premises-folder] dossier :

- ** [virtualNetwork.parameters.json][on-premises-vnet-parameters]**. Ce fichier définit l’espace d’adressage de réseau pour l’environnement local.

- ** [desmachines virtuelles-adds.parameters.json][on-premises-virtualmachines-adds-parameters]**. Ce fichier contient la configuration des VM sur site ajoute les services d’hébergement. Par défaut, deux VM *Standard-DS3-v2* est créés.

- ** [virtualNetworkGateway.parameters.json] [ on-premises-virtualnetworkgateway-parameters] ** et ** [connection.parameters.json][on-premises-connection-parameters]**. Ces fichiers contiennent les paramètres pour la connexion VPN à la passerelle VPN d’Azure dans le nuage, y compris la clé partagée à utiliser pour protéger le trafic de traverser la passerelle.

Les fichiers restants dans le dossier contiennent les informations de configuration utilisées pour créer le domaine local à l’aide de cette infrastructure. Vous les utilisez pour installer ajoute, le programme d’installation de DNS, créez une forêt et configurer les sites de réplication pour la forêt.

### <a name="cloud-components"></a>Composants du cloud

Ces composants constituent le cœur de cette architecture. Les [**paramètres/azure**] [ azure-folder] dossier contient les fichiers de paramètres suivants pour la configuration de ces composants :

- ** [virtualNetwork.parameters.json][vnet-parameters]**. Ce fichier définit la structure de le VNet pour les ordinateurs virtuels et d’autres composants dans le nuage. Il inclut des paramètres, tels que le nom, espace d’adressage, des sous-réseaux et les adresses des serveurs DNS requis. Notez que les adresses DNS indiqués dans cet exemple montre comment référencent les adresses IP des serveurs DNS locaux, ainsi que le serveur DNS d’Azure par défaut. Modifier ces adresses pour faire référence à la configuration de votre propre DNS si vous n’utilisez pas l’exemple d’environnement local :

    ```json
    {
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "ra-adfs-network-rg",
                "addressPrefixes": [
                    "10.0.0.0/16"
                ],
                "subnets": [
                    {
                        "name": "dmz-private-in",
                        "addressPrefix": "10.0.0.0/27"
                    },
                    {
                        "name": "dmz-private-out",
                        "addressPrefix": "10.0.0.32/27"
                    },
                    {
                        "name": "dmz-public-in",
                        "addressPrefix": "10.0.0.64/27"
                    },
                    {
                        "name": "dmz-public-out",
                        "addressPrefix": "10.0.0.96/27"
                    },
                    {
                        "name": "mgmt",
                        "addressPrefix": "10.0.0.128/25"
                    },
                    {
                        "name": "GatewaySubnet",
                        "addressPrefix": "10.0.255.224/27"
                    },
                    {
                        "name": "web",
                        "addressPrefix": "10.0.1.0/24"
                    },
                    {
                        "name": "biz",
                        "addressPrefix": "10.0.2.0/24"
                    },
                    {
                        "name": "data",
                        "addressPrefix": "10.0.3.0/24"
                    },
                    {
                        "name": "adds",
                        "addressPrefix": "10.0.4.0/27"
                    },
                    {
                        "name": "adfs",
                        "addressPrefix": "10.0.5.0/27"
                    },
                    {
                        "name": "proxy",
                        "addressPrefix": "10.0.6.0/27"
                    }
                ],
                "dnsServers": [
                    "192.168.0.4",
                    "192.168.0.5",
                    "168.63.129.16"
                ]
            }
        }
    }
    ```

- ** [desmachines virtuelles-adds.parameters.json ] [ virtualmachines-adds-parameters] **. Ce fichier configure les ordinateurs virtuels en cours d’exécution ajoute dans le nuage. La configuration se compose de deux VM. Vous devez modifier le nom d’utilisateur admin et le mot de passe dans le `virtualMachineSettings` section et vous pouvez éventuellement modifier la taille de la mémoire virtuelle pour répondre aux besoins du domaine :

    Pour plus d’informations, consultez [Extension d’Active Directory vers Azure][extending-ad-to-azure].

    ```json
            "virtualMachinesSettings": {
                "value": {
                    "namePrefix": "ra-adfs-ad",
                    "computerNamePrefix": "aad",
                    "size": "Standard_DS3_v2",
                    "osType": "Windows",
                    "adminUsername": "testuser",
                    "adminPassword": "AweS0me@PW",
                    "osAuthenticationType": "password",
                    "nics": [
                        {
                            "isPublic": "false",
                            "subnetName": "adds",
                            "privateIPAllocationMethod": "Static",
                            "startingIPAddress": "10.0.4.4",
                            "enableIPForwarding": false,
                            "dnsServers": [
                            ],
                            "isPrimary": "true"
                        }
                    ],
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "2012-R2-Datacenter",
                        "version": "latest"
                    },
                    "dataDisks": {
                        "count": 1,
                        "properties": {
                            "diskSizeGB": 127,
                            "caching": "None",
                            "createOption": "Empty"
                        }
                    },
                    "osDisk": {
                        "caching": "ReadWrite"
                    },
                    "extensions": [
                    ],
                    "availabilitySet": {
                        "useExistingAvailabilitySet": "No",
                        "name": "ra-adfs-as"
                    }
                }
            },
            "virtualNetworkSettings": {
                "value": {
                    "name": "ra-adfs-vnet",
                    "resourceGroup": "ra-adfs-network-rg"
                }
            },
            "buildingBlockSettings": {
                "value": {
                    "storageAccountsCount": 2,
                    "vmCount": 2,
                    "vmStartIndex": 1
                }
            }
        }
    ```

- ** [Ajouter-ajoute-domaine-controller.parameters.json][add-adds-domain-controller-parameters]**. Ce fichier contient les paramètres pour la création du domaine CONTOSO couvrant les serveurs ajoute. Il utilise des extensions personnalisées qui établissent le domaine et lui ajouter les serveurs à ajouter. Sauf si vous créez des serveurs supplémentaires ajoute (auquel cas vous devez les ajouter à la `vms` tableau), modifier leur nom par défaut ou pour créer un domaine sous un nom différent, vous n’avez pas besoin de modifier ce fichier.

- ** [loadBalancer-adfs.parameters.json] [loadbalancer-adfs-parameters] ** Le fichier contient deux ensembles de paramètres de configuration. Le `virtualMachineSettings` section définit les ordinateurs virtuels qui hébergent le service ADFS dans le nuage. Par défaut, le script crée deux de ces ordinateurs virtuels dans le même jeu de disponibilité :

    ```json
        "virtualMachinesSettings": {
            "value": {
                "namePrefix": "ra-adfs-adfs",
                "computerNamePrefix": "adfs",
                "size": "Standard_DS1_v2",
                "osType": "windows",
                "adminUsername": "testuser",
                "adminPassword": "AweS0me@PW",
                "osAuthenticationType": "password",
                "nics": [
                    {
                        "isPublic": "false",
                        "subnetName": "adfs",
                        "privateIPAllocationMethod": "Static",
                        "startingIPAddress": "10.0.5.4",
                        "isPrimary": "true",
                        "enableIPForwarding": false,
                        "dnsServers": [ ]
                    }
                ],
                "imageReference": {
                    "publisher": "MicrosoftWindowsServer",
                    "offer": "WindowsServer",
                    "sku": "2012-R2-Datacenter",
                    "version": "latest"
                },
                "dataDisks": {
                    "count": 1,
                    "properties": {
                        "diskSizeGB": 128,
                        "caching": "None",
                        "createOption": "Empty"
                    }
                },
                "osDisk": {
                    "caching": "ReadWrite"
                },
                "extensions": [ ],
                "availabilitySet": {
                    "useExistingAvailabilitySet": "No",
                    "name": "ra-adfs-adfs-vm-as"
                }
            }
        }
        ...
        "buildingBlockSettings": {
            "value": {
                "storageAccountsCount": 2,
                "vmCount": 2,
                "vmStartIndex": 1
            }
        }
    ```

    Le `loadBalancerSettings` section fournit une description de l’équilibreur de charge pour ces ordinateurs virtuels. L’équilibreur de charge transmet le trafic qui s’affiche sur le port 443 (HTTPS) pour un ou l’autre des ordinateurs virtuels :

    ```json
        "loadBalancerSettings": {
            "value": {
                "name": "ra-adfs-adfs-lb",
                "frontendIPConfigurations": [
                    {
                        "name": "ra-adfs-adfs-lb-fe",
                        "loadBalancerType": "internal",
                        "internalLoadBalancerSettings": {
                            "privateIPAddress": "10.0.5.30",
                            "subnetName": "adfs"
                        }
                    }
                ],
                "backendPools": [
                    {
                        "name": "ra-adfs-adfs-lb-bep",
                        "nicIndex": 0
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "https-rule",
                        "frontendPort": 443,
                        "backendPort": 443,
                        "protocol": "Tcp",
                        "backendPoolName": "ra-adfs-adfs-lb-bep",
                        "frontendIPConfigurationName": "ra-adfs-adfs-lb-fe",
                        "probeName": "https-probe",
                        "enableFloatingIP": false
                    }
                ],
                "probes": [
                    {
                        "name": "https-probe",
                        "port": 443,
                        "protocol": "Tcp",
                        "requestPath": null
                    }
                ],
                "inboundNatRules": [ ]
            }
        },
        "virtualNetworkSettings": {
            "value": {
                "name": "ra-adfs-vnet",
                "resourceGroup": "johns-adfs-network-rg"
            }
        }
    ```

- ** [adfs-batterie-domaine-join.parameters.json ] [ adfs-farm-domain-join-parameters] **. Ce fichier contient les paramètres permettant d’ajouter les serveurs ADFS sur le domaine CONTOSO. Vous ne devez modifier ce fichier si vous avez créé des serveurs ADFS supplémentaires (mettre à jour la `vms` de tableau dans ce cas), ou vous avez modifié le nom de domaine.

- ** [gmsa.parameters.json][gmsa-parameters]**, ** [adfs-batterie-first.parameters.json][adfs-farm-first-parameters]**, et ** [adfs-batterie-rest.parameters.json][adfs-farm-rest-parameters]**. Le script utilise les paramètres dans ces fichiers pour créer la batterie de serveurs ADFS. 

    Le fichier *gmsa.parameters.json* contient les paramètres pour le compte de service de gestion de groupe utilisé par le service ADFS. Vous pouvez modifier ce fichier si vous souhaitez modifier le nom du compte ou du domaine.

    Le fichier *adfs-batterie-first.parameters.json* conserve les informations requises pour créer la batterie de serveurs ADFS et ajouter le premier serveur. Si vous avez modifié le domaine ou le nom du compte de service groupe géré, vous devez mettre à jour ce fichier.

    Le fichier *adfs-batterie-rest.parameters.json* est utilisé pour ajouter les autres serveurs ADFS pour la batterie de serveurs. Là encore, si vous avez modifié le domaine ou le nom du compte de service groupe géré, vous devez mettre à jour ce fichier. Mise à jour de la `vms` de tableau si vous avez créé d’autres serveurs ADFS.

- ** [loadBalancer-adfsproxy.parameters.json][loadBalancer-adfsproxy-parameters]**. Ce fichier est similaire dans la structure et le contenu du fichier *loadBalancer-adfs.parameters.json* . Il contient les données pour la création de serveurs de proxy ADFS et équilibrage de la charge.

- ** [adfsproxy-batterie-first.parameters.json][adfsproxy-farm-first-parameters]**, et ** [adfsproxy-batterie-rest.parameters.json][adfsproxy-farm-rest-parameters]**. Le script utilise les paramètres dans ces fichiers pour créer la batterie de serveurs proxy ADFS. 

- ** [virtualNetworkGateway.parameters.json][virtualnetworkgateway-parameters]**. Ce fichier contient les paramètres utilisés pour créer la passerelle VPN d’Azure dans le nuage, utilisé pour la connexion au réseau local. Vous devez modifier le `sharedKey` valeur dans le `connectionsSettings` section correspond à celle du périphérique VPN sur site. Pour plus d’informations, consultez [implémentation d’une Architecture de réseau hybride avec Azure et sur site VPN][hybrid-azure-on-prem-vpn].

- ** [zone dmz-private.parameters.json] [ dmz-private-parameters] ** et ** [dmz-public.parameters.json ] [ dmz-public-parameters] **. Ces fichiers configurent (public) entrant et sortant (privé) des ordinateurs virtuels qui composent la zone DMZ, protégeant les serveurs dans le nuage. Pour plus d’informations sur ces éléments et leur configuration, consultez [implémentation d’un réseau de périmètre entre Internet et les Azure][implementing-a-secure-hybrid-network-architecture-with-internet-access].

- ** [loadBalancer-web.parameters-json][loadBalancer-web-parameters]**, ** [biz.parameters-loadBalancer-json][loadBalancer-biz-parameters]**, et ** [loadBalancer-data.parameters-json][loadBalancer-data-parameters]**. Ces fichiers de paramètres contiennent les spécifications de la machine virtuelle pour les niveaux d’accès web, métier et données et de configurer les équilibreurs de charge pour chaque niveau. Ce sont les ordinateurs virtuels qui hébergent les applications web et les bases de données et exécuter les charges de travail des professionnels de l’organisation. Vous pouvez modifier la taille et le nombre d’ordinateurs virtuels dans chaque niveau en fonction de vos besoins.

- ** [desmachines virtuelles-mgmt.parameters.json][virtualMachines-mgmt-parameters]**. Ce fichier contient la configuration de la gestion/boîte de déviation de machines virtuelles. Il n’est possible d’obtenir d’ouverture et de l’accès administratif aux ordinateurs virtuels dans le web, business et les niveaux de données à partir de la zone passer. Par défaut, le script crée un seul *Standard_DS1_v2* VM, mais vous pouvez modifier ce fichier pour créer les ordinateurs virtuels de plus grandes ou plus si la charge de travail de gestion est susceptible d’être important.

## <a name="solution-deployment"></a>Déploiement de la solution

La solution suppose que les conditions préalables suivantes :

- Vous avez un abonnement Azure existant dans lequel vous pouvez créer des groupes de ressources.

- Avoir téléchargé et installé la version la plus récente de Powershell d’Azure. Consultez [ici] [ azure-powershell-download] pour obtenir des instructions.

Pour exécuter le script qui déploie la solution :

1. Déplacer vers un dossier sur votre ordinateur local et créez les sous-dossiers suivants :

    - Scripts

    - Scripts/paramètres

    - Paramètres/scripts/locales

    - Paramètres/scripts/Azure

2. Télécharger le [Déploiement-ReferenceArchitecture.ps1] [ solution-script] le fichier dans le dossier Scripts

3. Télécharger le contenu de la [paramètres/locales] [ on-premises-folder] dossier dans le dossier Scripts/paramètres/locales :

4. Télécharger le contenu de [paramètres/azure] [ azure-folder] dossier dans le dossier Scripts/paramètres/Azure.

5. Modifiez le fichier de déploiement-ReferenceArchitecture.ps1 dans le dossier Scripts et modifiez les lignes suivantes pour spécifier les groupes de ressources qui doivent être créés ou utilisés pour contenir les ressources créées par le script :

    ```powershell
    # Azure Onpremise Deployments
        $onpremiseNetworkResourceGroupName = "ra-adfs-onpremise-rg"
        
        # Azure ADDS Deployments
        $azureNetworkResourceGroupName = "ra-adfs-network-rg"
        $workloadResourceGroupName = "ra-adfs-workload-rg"
        $securityResourceGroupName = "ra-adfs-security-rg"
        $addsResourceGroupName = "ra-adfs-adds-rg"
        $adfsResourceGroupName = "ra-adfs-adfs-rg"
        $adfsproxyResourceGroupName = "ra-adfs-proxy-rg"
    ```

6. Modifier les fichiers de paramètres dans les Scripts/paramètres/locales et les dossiers de Scripts/les paramètres/Azure. Mettre à jour les références de groupe de ressources dans ces fichiers pour faire correspondre les noms des groupes de ressources affectées aux variables dans le fichier ReferenceArchitecture.ps1 à la déployer. Le tableau suivant répertorie les fichiers de paramètres de référence quel groupe de ressources. Notez que les groupes *ra-adfs-charge de travail-rg*, *ra-adfs-sécurité-rg*, *ra-adfs-ajoute-rg*, *ra-adfs-adfs-rg*et *ra-adfs-proxy-rg* sont uniquement utilisées dans le script PowerShell et ne se produisent pas dans les fichiers de paramètres.

  	|Groupe de ressources|Fichier (s) paramètre|
  	|--------------|--------------|
  	|RA-adfs-locales-rg|parameters\onpremise\connection.Parameters.JSON<br /> parameters\onpremise\virtualMachines-ADDS.Parameters.JSON<br />parameters\onpremise\virtualNetwork-ADDS-DNS.Parameters.JSON<br />parameters\onpremise\virtualNetwork.Parameters.JSON<br />parameters\onpremise\virtualNetworkGateway.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON
  	|RA-adfs-réseau-rg|parameters\onpremise\connection.Parameters.JSON<br />parameters\azure\dmz-Private.Parameters.JSON<br />parameters\azure\dmz-public.Parameters.JSON<br />parameters\azure\loadBalancer-ADFS.Parameters.JSON<br />parameters\azure\loadBalancer-adfsproxy.Parameters.JSON<br />parameters\azure\loadBalancer-biz.Parameters.JSON<br />parameters\azure\loadBalancer-Data.Parameters.JSON<br />parameters\azure\loadBalancer-Web.Parameters.JSON<br />parameters\azure\virtualMachines-ADDS.Parameters.JSON<br />parameters\azure\virtualMachines-Mgmt.Parameters.JSON<br />parameters\azure\virtualNetwork-with-onpremise-and-Azure-DNS.Parameters.JSON<br />parameters\azure\virtualNetwork.Parameters.JSON<br />parameters\azure\virtualNetworkGateway.Parameters.JSON (*deux occurrences*)

    En outre, définir la configuration pour les locaux et cloud composants, comme décrit dans la section [composants de la Solution] [-composants de la solution].

7. Ouvrez une fenêtre PowerShell d’Azure, déplacer vers le dossier de Scripts et exécutez la commande suivante :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> <mode>
    ```

    Remplacer `<subscription id>` avec votre ID d’abonnement Azure.

    Pour `<location>`, spécifier une région Azure, tel que `eastus` ou `westus`.

    Le `<mode>` paramètre peut avoir une des valeurs suivantes :

    - `Onpremise`, pour créer l’environnement simulé sur site.

    - `Infrastructure`, pour créer l’infrastructure de VNet et de sauter la boîte dans le nuage.

    - `CreateVpn`, pour générer la passerelle Azure réseau virtuel et le connecter au réseau local.

    - `AzureADDS`, pour créer les ordinateurs virtuels agissant en tant que serveurs d’ajoute, déployer Active Directory à ces ordinateurs virtuels et créer le domaine dans le nuage.

    - `AdfsVm`Pour créer les ordinateurs virtuels d’ADFS et les joindre au domaine dans le nuage.

    - `ProxyVm`pour générer le proxy ADFS ordinateurs virtuels et les joindre au domaine dans le nuage.

    - `Prepare`, qui effectue toutes les tâches ci-dessus. **C’est l’option recommandée si vous créez un nouveau déploiement et vous n’avez pas une infrastructure sur site.**

    >[AZURE.NOTE] Vous pouvez également exécuter le script avec un `<mode>` paramètre de `Workload` pour créer le web, business et la couche de données VMs et au réseau. Ce programme d’installation n’est pas inclus dans le cadre de la `Prepare` mode.

    Si vous utilisez la `Prepare` , le script prend plusieurs heures, et se termine avec le message *préparation terminée. Installez le certificat à tous les ADFS et proxy VM.*

8.  Pour permettre à ses paramètres DNS prennent effet, redémarrez la zone passer (*ra-adfs-gestion-vm1* dans le groupe *ra-adfs-sécurité-rg* ).

9.  [Obtenir un certificat SSL pour ADFS] [ adfs_certificates] et l’installer sur les ordinateurs virtuels d’ADFS. Notez que vous pouvez vous connecter aux ordinateurs virtuels par le biais de la zone passer ADFS. Les adresses IP sont *10.0.5.4* et *10.0.5.5*. Le nom d’utilisateur par défaut est *contoso\testuser* avec le mot de passe *AweSome@PW*.

    >[AZURE.NOTE] Les commentaires dans le script de déploiement-ReferenceArchitecture.ps1 à ce stade fournissent des instructions détaillées pour créer un certificat auto-signé test et une autorité à l’aide de la `makecert` commande. Toutefois, n’utilisez pas les certificats générés par makecert dans un environnement de production.

10. Exécutez la commande Powershell suivante pour configurer la batterie de serveurs ADFS :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Adfs
    ```

11. Dans la zone passer, accédez à *https://adfs.contoso.com/adfs/ls/idpinitiatedsignon.htm* pour tester l’installation d’ADFS (vous receviez un avertissement de certificat, vous pouvez ignorer pour ce test). Vérifiez que la page de connexion Contoso Corporation s’affiche. Connectez-vous en tant que *contoso\testuser* avec le mot de passe *AweS0me@PW*.

12. Installez le certificat SSL sur l’ordinateurs virtuels du proxy ADFS. Les adresses IP sont *10.0.6.4* et *10.0.6.5*.

13. Exécutez la commande Powershell suivante pour configurer le premier serveur de proxy ADFS :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy1
    ```

14. Suivez les instructions affichées par le script pour tester l’installation du premier serveur proxy ADFS.

15. Exécutez la commande Powershell suivante pour configurer le premier serveur de proxy ADFS deuxième :

    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Proxy2
    ```

16. Suivez les instructions affichées par le script pour tester la configuration de proxy ADFS complète.

## <a name="next-steps"></a>Étapes suivantes

- [Azure Active Directory][aad].

- [Azure Active Directory B2C][aadb2c].

<!-- links -->

[vm-recommendations]: ./guidance-compute-single-vm.md#Recommendations
[naming-conventions]: ./guidance-naming-conventions.md
[implementing-active-directory]: ./guidance-identity-adds-extend-domain.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[implementing-a-secure-hybrid-network-architecture-with-internet-access]: ./guidance-iaas-ra-secure-vnet-dmz.md
[DRS]: https://technet.microsoft.com/library/dn280945.aspx
[where-to-place-an-fs-proxy]: https://technet.microsoft.com/library/dd807048.aspx
[ADDRS]: https://technet.microsoft.com/library/dn486831.aspx
[plan-your-adfs-deployment]: https://msdn.microsoft.com/library/azure/dn151324.aspx
[ad_network_recommendations]: #network_configuration_recommendations_for_AD_DS_VMs
[domain_and_forests]: https://technet.microsoft.com/library/cc759073(v=ws.10).aspx
[adfs_certificates]: https://technet.microsoft.com/library/dn781428(v=ws.11).aspx
[create_service_account_for_adfs_farm]: https://technet.microsoft.com/library/dd807078.aspx
[import_server_authentication_certificate]: https://technet.microsoft.com/library/dd807088.aspx
[adfs-configuration-database]: https://technet.microsoft.com/en-us/library/ee913581(v=ws.11).aspx
[active-directory-federation-services]: https://technet.microsoft.com/windowsserver/dd448613.aspx
[security-considerations]: #security-considerations
[recommendations]: #recommendations
[claims-aware applications]: https://msdn.microsoft.com/en-us/library/windows/desktop/bb736227(v=vs.85).aspx
[active-directory-federation-services-overview]: https://technet.microsoft.com/en-us/library/hh831502(v=ws.11).aspx
[establishing-federation-trust]: https://blogs.msdn.microsoft.com/alextch/2011/06/27/establishing-federation-trust/
[Deploying_a_federation_server_farm]: https://technet.microsoft.com/library/dn486775.aspx
[install_and_configure_the_web_application_proxy_server]: https://technet.microsoft.com/library/dn383662.aspx
[publish_applications_using_AD_FS_preauthentication]: https://technet.microsoft.com/library/dn383640.aspx
[managing-adfs-components]: https://technet.microsoft.com/library/cc759026.aspx
[oms-adfs-pack]: https://www.microsoft.com/download/details.aspx?id=41184
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[aad]: https://azure.microsoft.com/documentation/services/active-directory/
[aadb2c]: https://azure.microsoft.com/documentation/services/active-directory-b2c/
[adfs-intro]: ../active-directory/active-directory-aadconnect-azure-adfs.md
[solution-script]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/Deploy-ReferenceArchitecture.ps1
[on-premises-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/onpremise
[on-premises-vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetwork.parameters.json
[on-premises-virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualMachines-adds.parameters.json
[on-premises-virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/virtualNetworkGateway.parameters.json
[on-premises-connection-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/onpremise/connection.parameters.json
[azure-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-identity-adfs/parameters/azure
[vnet-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetwork.parameters.json
[dmz-private-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-private.parameters.json
[dmz-public-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/dmz-public.parameters.json
[virtualnetworkgateway-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualNetworkGateway.parameters.json
[hybrid-azure-on-prem-vpn]: ./guidance-hybrid-network-vpn.md
[virtualmachines-adds-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-adds.parameters.json
[extending-ad-to-azure]: ./guidance-identity-adds-extend-domain.md
[loadbalancer-adfs-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfs.parameters.json
[add-adds-domain-controller-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/add-adds-domain-controller.parameters.json
[gmsa-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/gmsa.parameters.json
[adfs-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-first.parameters.json
[adfs-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-rest.parameters.json
[adfs-farm-domain-join-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfs-farm-domain-join.parameters.json
[loadBalancer-web-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-web.parameters.json
[loadBalancer-biz-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-biz.parameters.json
[loadBalancer-data-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-data.parameters.json
[virtualMachines-mgmt-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/virtualMachines-mgmt.parameters.json
[loadBalancer-adfsproxy-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/loadBalancer-adfsproxy.parameters.json
[adfsproxy-farm-first-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-first.parameters.json
[adfsproxy-farm-rest-parameters]: https://raw.githubusercontent.com/mspnp/reference-architectures/master/guidance-identity-adfs/parameters/azure/adfsproxy-farm-rest.parameters.json
[0]: ./media/guidance-iaas-ra-secure-vnet-adfs/figure1.png "Architecture de réseau hybride avec Active Directory sécurisée"
