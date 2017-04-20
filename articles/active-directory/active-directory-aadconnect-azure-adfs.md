<properties
    pageTitle="Services de fédération Active Directory dans Azure | Microsoft Azure"
    description="Dans ce document, vous apprendrez comment déployer ADFS dans Azure pour haute disponibilité."
    keywords="déployer AD FS dans azure, déployer adfs azure, azure adfs, Federation Services azure, déployer adfs, déployer ADFS, adfs dans azure, déployer adfs dans azure, déployer ADFS dans azure, adfs azure, introduction à ADFS, Azure, AD FS dans Azure, iaas, ADFS, déplacer adfs vers azure"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="anandy;billmath"/>

# <a name="ad-fs-deployment-in-azure"></a>Déploiement AD FS Azure 

AD FS permet la fédération d’identité simple et sécurisée et fonctionnalités de session unique (SSO) Web. Fédération avec Azure AD ou O365 permet aux utilisateurs de s’authentifier à l’aide d’informations d’identification de locaux et d’accéder à toutes les ressources dans le nuage. Par conséquent, il est important de disposer d’une infrastructure AD FS hautement disponible pour garantir l’accès aux ressources à la fois sur site et dans le cloud. Déploiement d’ADFS dans Azure peut aider à disponibilité élevée avec des efforts minimum.
Il existe plusieurs avantages du déploiement d’ADFS dans Azure, certains d'entre eux sont répertoriés ci-dessous :

* **Haute disponibilité** - avec la puissance des jeux de disponibilité d’Azure, vous garantissez une infrastructure hautement disponible.
* **Facile à échelle** – ont besoin de plus de performances ? Migrer facilement les machines plus puissantes en quelques clics dans Azure
* **Redondance de Cross-Geo** – avec redondance géographique Azure vous pouvez être assuré que votre infrastructure est hautement disponible dans le monde entier
* **Facile à gérer** , avec des options de gestion extrêmement simplifiée dans Azure portal, gestion de votre infrastructure est simple et conviviale 

## <a name="design-principles"></a>Principes de conception

![Conception du déploiement](./media/active-directory-aadconnect-azure-adfs/deployment.png)

Le schéma ci-dessus présente la topologie de base recommandée pour démarrer le déploiement de votre infrastructure ADFS dans Azure. Les principes des différents composants de la topologie sont répertoriés ci-dessous :

* **Contrôleur de domaine / serveurs ADFS**: Si vous avez moins de 1 000 utilisateurs vous pouvez simplement installer de rôle AD FS sur vos contrôleurs de domaine. Si vous ne souhaitez pas que l’impact de performance sur les contrôleurs de domaine ou si vous avez plus de 1 000 utilisateurs, puis déployer ADFS sur des serveurs distincts.
* **Serveur WAP** – il est nécessaire de déployer des serveurs Proxy de l’Application Web afin que les utilisateurs peuvent atteindre l’AD FS lorsqu’ils ne sont pas sur le réseau de la société également.
* **DMZ**: le Proxy de l’Application Web serveurs est placé dans la zone DMZ et TCP/443 uniquement l’accès n’est autorisé entre la zone DMZ et le sous-réseau interne.
* **Les équilibreurs de charge**: pour garantir une haute disponibilité des serveurs ADFS et les Proxy de l’Application Web, nous recommandons d’utiliser un équilibreur de charge interne pour les serveurs AD FS et l’équilibreur de charge Azure pour les serveurs Proxy de l’Application Web.
* **Jeux de disponibilité**: pour assurer la redondance à votre déploiement AD FS, il est recommandé de regrouper deux ou plusieurs machines virtuelles dans un ensemble de disponibilité pour les charges de travail similaires. Cette configuration garantit que pendant un événement d’entretien planifié ou non, au moins un ordinateur virtuel sera disponible
* **Comptes de stockage**: il est recommandé de disposer de deux comptes de stockage. Un compte de stockage unique peut entraîner la création d’un point de défaillance unique et peuvent provoquer le déploiement indisponible dans un scénario peu probable où le compte de stockage tombe en panne. Deux comptes de stockage permettra d’associer un compte de stockage pour chaque ligne de l’erreur.
* **Répartition du réseau**: les serveurs Proxy de l’Application Web doivent être déployés dans un réseau de DMZ distinct. Vous pouvez diviser un réseau virtuel en deux sous-réseaux et déployer ensuite l’ou les serveurs Proxy de l’Application Web dans un sous-réseau isolé. Vous pouvez simplement configurer les paramètres du groupe de sécurité réseau pour chaque sous-réseau et autoriser uniquement les communications nécessaires entre les deux sous-réseaux. Plus de détails sont donnés par le scénario de déploiement ci-dessous

##<a name="steps-to-deploy-ad-fs-in-azure"></a>Étapes pour déployer ADFS dans Azure

Les étapes décrites dans cette section décrivent le guide de déploiement de le ci-dessous représenté infrastructure AD FS dans Azure.

### <a name="1-deploying-the-network"></a>1. le déploiement du réseau

Comme indiqué ci-dessus, vous pouvez soit créer deux sous-réseaux dans un seul réseau virtuel ou bien créer deux réseaux virtuels complètement différents (VNet). Cet article se concentrent sur le déploiement d’un réseau virtuel unique et diviser en deux sous-réseaux. Il s’agit actuellement une approche plus facile que de deux VNets distincts nécessiterait un VNet à VNet passerelle pour les communications.

**1.1 créer des réseaux virtuels**

![Créer des réseaux virtuels](./media/active-directory-aadconnect-azure-adfs/deploynetwork1.png)
    
Dans le portail Azure, réseau virtuel et que vous pouvez déployer le réseau virtuel et un sous-réseau immédiatement avec un simple clic. Sous-réseau INT est également définie et est maintenant prêt pour les machines virtuelles à ajouter.
L’étape suivante consiste à ajouter un autre sous-réseau au réseau, c'est-à-dire le sous-réseau de la DMZ. Pour créer le sous-réseau de zone DMZ, tout simplement

* Sélectionnez le réseau nouvellement créé
* Dans les propriétés de, sélectionnez sous-réseau
* Dans le sous-réseau panneau cliquez sur le bouton Ajouter
* Fournir les informations sous-réseau d’espace nom et l’adresse pour créer le sous-réseau

![Sous-réseau](./media/active-directory-aadconnect-azure-adfs/deploynetwork2.png)


![Sous-réseau DMZ](./media/active-directory-aadconnect-azure-adfs/deploynetwork3.png)

**1.2. création du réseau de groupes de sécurité**

Un groupe de sécurité réseau (NSG) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorisent ou refusent le trafic réseau vers les instances de la machine virtuelle dans un réseau virtuel. NSGs peuvent être associés à des sous-réseaux ou des instances VM individuelles au sein de ce sous-réseau. Lorsqu’un NSG est associé à un sous-réseau, les règles de la liste ACL s’appliquent à toutes les instances de la machine virtuelle de ce sous-réseau.
Pour ce guide, nous allons créer deux NSGs : un pour un réseau interne et un réseau de périmètre. Seront intitulées NSG_INT et NSG_DMZ respectivement.

![Créer NSG](./media/active-directory-aadconnect-azure-adfs/creatensg1.png)

Après la création du NSG, il y aura des règles de trafic sortant entrants et 0 0. Une fois que les rôles sur les serveurs respectifs sont installés et opérationnels, les règles d’entrée et de sortie peuvent être effectuées selon le niveau de sécurité souhaité.

![Initialisation du NSG](./media/active-directory-aadconnect-azure-adfs/nsgint1.png)

Après avoir créé les NSGs, associer NSG_INT à sous-réseau INT et NSG_DMZ avec le sous-réseau DMZ. Une capture d’écran de l’exemple est donné ci-dessous :

![Configurer du NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure1.png)

* Cliquez sur sous-réseaux pour ouvrir le panneau des sous-réseaux
* Sélectionnez le sous-réseau à associer à du NSG 

Après la configuration, le panneau pour les sous-réseaux doit se présenter comme ci-dessous :

![Sous-réseaux après NSG](./media/active-directory-aadconnect-azure-adfs/nsgconfigure2.png)

**1.3. créer de connexion et sur site**

Nous aurons besoin d’une connexion sur site afin de déployer le contrôleur de domaine (DC) dans azure. Azure offre différentes options de connectivité pour connecter votre infrastructure sur site pour votre infrastructure Azure.

* Point-à-site
* Réseau virtuel de Site à site
* ExpressRoute

Il est recommandé d’utiliser ExpressRoute. ExpressRoute vous permet de créer des connexions entre des centres de données Azure et l’infrastructure dans vos locaux ou dans un environnement de colocalisation. ExpressRoute les connexions ne passent pas sur l’Internet public. Ils offrent plus la fiabilité, vitesses plus rapides, des latences inférieurs et une plus grande sécurité que les connexions classiques sur Internet.
Pendant qu’il est recommandé d’utiliser ExpressRoute, vous pouvez choisir n’importe quelle méthode de connexion plus adapté à votre organisation. Pour en savoir plus sur les ExpressRoute et les différentes options de connectivité à l’aide de ExpressRoute, lisez la [présentation technique de ExpressRoute](https://aka.ms/Azure/ExpressRoute).

### <a name="2-create-storage-accounts"></a>2. Créez des comptes de stockage

Afin de garantir une haute disponibilité et éviter la dépendance sur un compte de stockage unique, vous pouvez créer deux comptes de stockage. Diviser les ordinateurs de chaque ensemble de disponibilité en deux groupes et ensuite affecter chaque groupe à un compte de stockage distinct.

![Créer des comptes de stockage](./media/active-directory-aadconnect-azure-adfs/storageaccount1.png)

### <a name="3-create-availability-sets"></a>3. création d’ensembles de disponibilité

Pour chaque rôle (contrôleur de domaine/AD FS et WAP), créer des jeux de disponibilité qui contient 2 ordinateurs au minimum. Cela permet de garantir une disponibilité supérieure pour chaque rôle. Lors de la création de la disponibilité de jeux, il est essentiel de décider des éléments suivants :
* **Domaines d’erreur**: les ordinateurs virtuels dans le même domaine de pannes partagent la même source d’alimentation et le commutateur de réseau physique. Un minimum de 2 domaines d’erreur sont recommandés. La valeur par défaut est 3, et vous pouvez laisser tel quel pour ce déploiement.
* **Mettre à jour des domaines**: redémarrage ensemble des ordinateurs appartenant au même domaine de mise à jour au cours d’une mise à jour. Vous souhaitez disposer de 2 domaines de mise à jour. La valeur par défaut est 5 et que vous pouvez laisser tel quel pour ce déploiement.

![Jeux de disponibilité](./media/active-directory-aadconnect-azure-adfs/availabilityset1.png)

Créer des jeux de disponibilité suivant

| Ensemble de disponibilité | Rôle | Domaines d’erreur | Mettre à jour des domaines |
|:----------------:|:----:|:-----------:|:-----------|
| contosodcset | CONTRÔLEUR DE DOMAINE/ADFS | 3 | 5 |
| contosowapset | WAP | 3 | 5 |

### <a name="4--deploy-virtual-machines"></a>4. déployer des machines virtuelles
L’étape suivante consiste à déployer des machines virtuelles qui hébergera les différents rôles dans votre infrastructure. Un minimum de deux machines sont recommandés dans chaque ensemble de disponibilité. Créer six ordinateurs virtuels pour le déploiement de base.

| Machine | Rôle | Sous-réseau | Ensemble de disponibilité | Compte de stockage | Adresse IP |
|:-----:|:-----:|:-----:|:-----:|:-----:|:-----:|
|contosodc1|CONTRÔLEUR DE DOMAINE/ADFS|INT|contosodcset|contososac1|Statique|
|contosodc2|CONTRÔLEUR DE DOMAINE/ADFS|INT|contosodcset|contososac2|Statique|
|contosowap1|WAP|ZONE DMZ|contosowapset|contososac1|Statique|
|contosowap2|WAP|ZONE DMZ|contosowapset|contososac2|Statique|

Comme vous l’avez peut-être remarqué, aucun NSG n’a été spécifié. Il s’agit, car azure vous permet d’utiliser NSG au niveau du sous-réseau. Ensuite, vous pouvez contrôler le trafic réseau de machine à l’aide du NSG individuel associé soit le sous-réseau ou bien l’objet de la carte réseau. Lire plus sur [ce qu’est un groupe de sécurité réseau (NSG)](https://aka.ms/Azure/NSG).
Adresse IP statique est recommandé si vous gérez le serveur DNS. Vous pouvez utiliser Azure DNS et à la place, dans les enregistrements DNS pour votre domaine, reportez-vous aux nouveaux ordinateurs par leurs noms de domaine complets Azure.
Une fois le déploiement terminé, votre volet de machine virtuelle doit ressembler à ci-dessous :

![Ordinateurs virtuels déployés](./media/active-directory-aadconnect-azure-adfs/virtualmachinesdeployed_noadfs.png)

### <a name="5-configuring-the-domain-controller--ad-fs-servers"></a>5. Configuration du contrôleur de domaine / serveurs AD FS
 Pour authentifier toutes les demandes entrantes, ADFS devez contacter le contrôleur de domaine. Pour enregistrer le voyage coûteux d’Azure au contrôleur de domaine local pour l’authentification, il est recommandé de déployer un réplica du contrôleur de domaine dans Azure. Pour atteindre une disponibilité élevée, il est recommandé de créer un ensemble de disponibilité au moins 2 contrôleurs de domaine.

|Contrôleur de domaine|Rôle|Compte de stockage|
|:-----:|:-----:|:-----:|
|contosodc1|Réplica|contososac1|
|contosodc2|Réplica|contososac2|

* Promouvoir les deux serveurs en tant que contrôleurs de domaine de réplica avec DNS
* Configurez les serveurs ADFS en installant le rôle AD FS utilisant le Gestionnaire de serveur.

###<a name="6---deploying-internal-load-balancer-ilb"></a>6. déploiement de l’équilibreur de charge interne (ILB)

**6.1. créer le ILB**

Pour déployer un ILB, sélectionnez équilibreurs de charge dans le portail Azure et cliquez sur Ajouter (+).
>[AZURE.NOTE] Si vous ne voyez pas les **Équilibreurs de charge** dans le menu, cliquez sur **Parcourir** dans le coin inférieur gauche du portail et défilement jusqu'à ce que vous voyiez **Les équilibreurs de charge**.  Cliquez sur l’étoile jaune pour l’ajouter à votre menu. Maintenant, sélectionnez la nouvelle icône d’équilibrage de la charge pour ouvrir le panneau pour commencer la configuration de l’équilibreur de charge.

![Parcourir l’équilibreur de charge](./media/active-directory-aadconnect-azure-adfs/browseloadbalancer.png)

* **Nom**: donner le nom approprié à l’équilibreur de charge
* **Schéma**: dans la mesure où ce programme d’équilibrage de charge sera placé devant les serveurs ADFS et est conçu pour les connexions de réseau interne uniquement, sélectionnez « Interne »
* **Réseau virtuel**: choisissez le réseau virtuel dans lequel vous déployez votre AD FS
* **Sous-réseau**: choisissez le sous-réseau interne ici
* **Attribution d’adresses IP**: dynamique

![Équilibreur de charge interne](./media/active-directory-aadconnect-azure-adfs/ilbdeployment1.png)
 
Une fois que vous cliquez sur Créer et l’ILB est déployé, vous devez le voir dans la liste des programmes d’équilibrage de charge :

![Équilibreurs de charge après ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment2.png)
 
Étape suivante consiste à configurer le pool back-end et la back-end sonde.

**6.2. configurer le pool principal ILB**

Dans le panneau des équilibreurs de charge, sélectionnez l’ILB nouvellement créé. Il ouvre le panneau Paramètres. 
1.  Sélectionnez les pools de back-end à partir du Panneau de paramètres
2.  Dans le panneau de pool du back-end ajouter, cliquez sur Ajouter une machine virtuelle
3.  Vous obtenez un panneau où vous pouvez choisir l’ensemble de la disponibilité
4.  Choisissez le jeu de disponibilité AD FS

![Configuration du pool principal ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment3.png)
 
**6.3. Configuration de sonde**

Dans le panneau Paramètres de la ILB, sélectionnez sondes.
1.  Cliquez sur Ajouter
2.  Fournir des détails pour sonde d’un. **Nom**: nom b de sonde. **Protocole**: TCP c. **Port**: 443 (HTTPS) d. **Intervalle**: 5 (valeur par défaut) – il s’agit de l’intervalle auquel ILB chercheront les ordinateurs dans le pool de back-end e. **Limite incorrecte**: 2 (valeur par défaut de val ue) – il s’agit du seuil d’échecs consécutifs de sonde après laquelle ILB déclarer une machine dans le pool principal non réactif et arrêter l’envoi de trafic vers celui-ci.

![Configurez la sonde ILB](./media/active-directory-aadconnect-azure-adfs/ilbdeployment4.png)
 
**6.4. création de règles d’équilibrage de charge**

Pour équilibrer efficacement le trafic, l’ILB doit être configuré avec les règles d’équilibrage de charge. Pour créer une règle, d’équilibrage de la charge 
1.  Sélectionnez la règle à partir du Panneau de paramètres de l’ILB d’équilibrage de charge
2.  Cliquez sur Ajouter dans la panneau de la règle d’équilibrage de la charge
3.  Dans la panneau de la règle d’équilibrage de charge d’ajouter un. **Nom**: indiquez un nom pour la règle b. **Protocole**: sélectionnez TCP c. **Port**: 443 d. **Back-end port**: 443 e. **Pool de serveur principal**: sélectionnez le pool que vous avez créé pour les Federation Services cluster antérieure f. **Sonde**: sélectionnez la sonde créée précédemment pour serveurs AD FS

![Configurer ILB équilibrage de règles](./media/active-directory-aadconnect-azure-adfs/ilbdeployment5.png)

**6.5. mise à jour DNS avec ILB**

Accédez à votre serveur DNS et de créer un enregistrement CNAME pour le ILB. L’enregistrement CNAME doit être le service de fédération avec l’adresse IP pointant vers l’adresse IP de l’ILB. Par exemple, si l’adresse de la DIP de ILB est 10.3.0.8 et installé le service de fédération est fs.contoso.com, puis créez un enregistrement CNAME pour fs.contoso.com pointant vers 10.3.0.8.
Cela permet de garantir que toutes les communications concernant fs.contoso.com fin vers le haut à l’ILB et sont routées de manière appropriée.

###<a name="7---configuring-the-web-application-proxy-server"></a>7. Configuration du serveur Proxy de l’Application Web

**7.1. Configuration des serveurs Proxy de l’Application Web afin d’atteindre les serveurs AD FS**

Afin d’assurer que les serveurs Proxy de l’Application Web sont en mesure d’atteindre les serveurs ADFS derrière le ILB, créez un enregistrement dans la %systemroot%\system32\drivers\etc\hosts pour le ILB. Notez que le nom unique (DN) doit être le nom du service de fédération, par exemple fs.contoso.com. Et l’entrée IP doit être celle de l’adresse IP de l’ILB (10.3.0.8 comme dans l’exemple).

**7.2. installer le rôle de Proxy de l’Application Web**

Après que vous être assuré que les serveurs Proxy de l’Application Web sont en mesure d’atteindre les serveurs ADFS derrière ILB, vous pouvez ensuite installer les serveurs Proxy de l’Application Web. Les serveurs Proxy de l’Application Web n’a pas être joint au domaine. Installer les rôles de Proxy de l’Application Web sur les deux serveurs de Proxy de l’Application Web en sélectionnant le rôle de l’accès distant. Le Gestionnaire de serveur vous guide pour terminer l’installation de WAP.
Pour plus d’informations sur le déploiement de WAP, lire [installer et configurer le serveur de Proxy d’Application Web](https://technet.microsoft.com/library/dn383662.aspx).

###<a name="8---deploying-the-internet-facing-public-load-balancer"></a>8. déploiement Internet face à équilibrage de la charge (Public)

**8.1. création d’Internet vers l’équilibrage de la charge (Public)**
 
Dans le portail Azure, sélectionnez les équilibreurs de charge et puis cliquez sur Ajouter. Dans le panneau d’équilibrage de charge de créer, entrez les informations suivantes
1. **Nom**: nom de l’équilibreur de charge
2. **Schéma**: Public – cette option indique que cet équilibreur de charge doit une adresse publique à Azure.
3. **Adresse IP**: créer une nouvelle adresse IP (dynamique)

![Équilibrage de la charge qui fait face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment1.png)

Après le déploiement, l’équilibreur de charge s’affiche dans la liste de programmes d’équilibrage de charge.

![Liste d’équilibrage de la charge](./media/active-directory-aadconnect-azure-adfs/elbdeployment2.png)
 
**8.2. Affectez un nom DNS pour l’adresse IP publique**

Cliquez sur l’entrée d’équilibrage de la charge qui vient d’être créé dans le panneau d’équilibreurs de charge pour afficher le panneau de configuration. Procédez comme suit pour configurer le nom DNS pour l’adresse IP publique :
1.  Cliquez sur l’adresse IP publique. Le panneau s’ouvre pour l’adresse IP publique et de ses paramètres
2.  Cliquez sur Configuration
3.  Fournissez un nom DNS. Celui-ci deviendra le nom DNS public auquel vous pouvez accéder depuis n’importe où, par exemple contosofs.westus.cloudapp.azure.com. Vous pouvez ajouter une entrée dans le serveur DNS externe pour le service de fédération (comme fs.contoso.com) qui résout le nom DNS de l’équilibreur de charge externe (contosofs.westus.cloudapp.azure.com).

![Configurer internet face à équilibrage de la charge](./media/active-directory-aadconnect-azure-adfs/elbdeployment3.png) 

![Configurer internet face à l’équilibreur de charge (DNS)](./media/active-directory-aadconnect-azure-adfs/elbdeployment4.png)

**8.3. configurer le pool de back-end pour l’équilibreur de charge Internet en vis-à-vis (Public)** 

Suivez les mêmes étapes que dans la création de l’équilibreur de charge interne, pour configurer le pool back-end pour Internet face à un équilibreur de charge (Public) comme la disponibilité pour les serveurs WAP. Par exemple, contosowapset.

![Configurer le pool back-end de l’équilibreur de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment5.png)
 
**8.4. Configuration de sonde**

Suivez les mêmes étapes que dans la configuration de l’équilibreur de charge interne pour configurer la sonde pour le pool de back-end les serveurs WAP.

![Configurez la sonde de l’équilibreur de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment6.png)
 
**8.5. créer des règles d’équilibrage de charge**

Suivez les mêmes étapes que dans ILB pour configurer l’équilibrage de charge règle pour TCP 443.

![Configurer les règles de contrepartie de l’équilibreur de charge face à Internet](./media/active-directory-aadconnect-azure-adfs/elbdeployment7.png)
 
###<a name="9---securing-the-network"></a>9. Sécurisation du réseau

**9.1. Sécurisation du sous-réseau interne**

En général, vous devez les règles suivantes pour sécuriser efficacement de votre sous-réseau interne (dans l’ordre ci-dessous)

|Règle|Description|Flux|
|:----|:----|:------:|
|AllowHTTPSFromDMZ| Autoriser la communication HTTPS à partir de la zone DMZ | Trafic entrant |
|DenyInternetOutbound| Aucun accès à internet | Sortant |

![Les règles d’accès INT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_int.png)

[commentaire] : <> (![les règles d’accès INT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsgintinbound.png)) [commentaire] : <> (![les règles d’accès INT (sortants)](./media/active-directory-aadconnect-azure-adfs/nsgintoutbound.png))
 
**9.2. sécurisation le sous-réseau de zone DMZ**

|Règle|Description|Flux|
|:----|:----|:------:|
|AllowHTTPSFromInternet| Autoriser HTTPS à partir d’internet à la zone DMZ | Trafic entrant|
|DenyInternetOutbound|  N’est pas HTTPS à internet est bloqué | Sortant |

![Les règles d’accès EXT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsg_dmz.png)

[commentaire] : <> (![les règles d’accès EXT (entrant)](./media/active-directory-aadconnect-azure-adfs/nsgdmzinbound.png)) [commentaire] : <> (![les règles d’accès EXT (sortants)](./media/active-directory-aadconnect-azure-adfs/nsgdmzoutbound.png))

>[AZURE.NOTE] Si l’authentification des certificats utilisateur client (authentification de clientTLS à l’aide de X509 les certificats d’utilisateur) est requise, ADFS nécessite TCP port 49443 est activé pour l’accès entrant.

###<a name="10--test-the-ad-fs-sign-in"></a>10. tester l’authentification dans AD FS

Le moyen le plus simple est de tester AD FS à l’aide de la page IdpInitiatedSignon.aspx. Pour être en mesure de faire qu’il est nécessaire pour activer la IdpInitiatedSignOn sur les propriétés de AD FS. Suivez les étapes ci-dessous pour vérifier votre configuration AD FS
1.  Exécuter le sous l’applet de commande sur le serveur AD FS, à l’aide de PowerShell, à défini sur activé.
    Set-AdfsProperties - EnableIdPInitiatedSignonPage $true 
2.  À partir de n’importe quel ordinateur externe accès https://adfs.thecloudadvocate.com/adfs/ls/IdpInitiatedSignon.aspx  
3.  Vous devez voir la page AD FS comme ci-dessous :

![Page de connexion de test](./media/active-directory-aadconnect-azure-adfs/test1.png)

Lors de connexion réussie, il vous fournira un message de réussite comme indiqué ci-dessous :

![Réussite du test](./media/active-directory-aadconnect-azure-adfs/test2.png)

## <a name="template-for-deploying-ad-fs-in-azure"></a>Modèle de déploiement d’AD FS dans Azure

Le modèle déploie une installation 6, 2 contrôleurs de domaine, AD FS et WAP.

[AD FS dans le modèle de déploiement d’Azure](https://github.com/paulomarquesc/adfs-6vms-regular-template-based)

Vous pouvez utiliser un réseau virtuel existant ou créer un nouveau VNET lors du déploiement de ce modèle. Les différents paramètres disponibles pour la personnalisation du déploiement sont énumérées ci-dessous avec la description de l’utilisation du paramètre dans le processus de déploiement. 

| Paramètre | Description |
|:--------|:-----|
|Emplacement| La région pour déployer les ressources en, par exemple, East nous. |
|StorageAccountType| Le type du compte de stockage créé|
|VirtualNetworkUsage| Indique si un nouveau réseau virtuel sera créé ou utiliser un existant|
|VirtualNetworkName| Le nom du réseau virtuel à créer, obligatoire sur l’utilisation du réseau virtuel nouveau ou existant|
|VirtualNetworkResourceGroupName| Spécifie le nom du groupe de ressources où se trouve le réseau virtuel existant. Lorsque vous utilisez un réseau virtuel, ceci devient un paramètre obligatoire pour le déploiement puisse trouver l’ID du réseau virtuel existant|
|VirtualNetworkAddressRange| La plage d’adresses de la nouvelle VNET, obligatoire si vous créez un réseau virtuel|
|InternalSubnetName| Le nom du sous-réseau interne, obligatoire pour les deux options d’utilisation de réseau virtuel (nouveau ou existantes)|
|InternalSubnetAddressRange| La plage d’adresses du sous-réseau interne, qui contient les serveurs ADFS et les contrôleurs de domaine, obligatoires si vous créez un réseau virtuel.|
|DMZSubnetAddressRange| La plage d’adresses du sous-réseau dmz, qui contient les serveurs de proxy application Windows, obligatoires si vous créez un réseau virtuel.|
|DMZSubnetName| Le nom du sous-réseau interne, obligatoire pour les deux options d’utilisation de réseau virtuel (nouveau ou existantes). |
|ADDC01NICIPAddress| L’adresse IP interne du premier contrôleur de domaine, cette adresse IP est attribuée de manière statique pour le contrôleur de domaine et doit être une adresse ip valide au sein du sous-réseau interne|
|ADDC02NICIPAddress| L’adresse IP interne du deuxième contrôleur de domaine, cette adresse IP est attribuée de manière statique pour le contrôleur de domaine et doit être une adresse ip valide au sein du sous-réseau interne|
|ADFS01NICIPAddress| L’adresse IP interne du premier serveur ADFS, cette adresse IP est attribuée de manière statique sur le serveur ADFS et doit être une adresse ip valide au sein du sous-réseau interne|
|ADFS02NICIPAddress| L’adresse IP interne du deuxième serveur ADFS, cette adresse IP est attribuée de manière statique sur le serveur ADFS et doit être une adresse ip valide au sein du sous-réseau interne|
|WAP01NICIPAddress| L’adresse IP interne du premier serveur WAP, cette adresse IP est attribuée de manière statique pour le serveur WAP et doit être une adresse ip valide dans le sous-réseau de zone DMZ|
|WAP02NICIPAddress| L’adresse IP interne du deuxième serveur WAP, cette adresse IP est attribuée de manière statique pour le serveur WAP et doit être une adresse ip valide dans le sous-réseau de zone DMZ|
|ADFSLoadBalancerPrivateIPAddress| L’adresse IP interne de l’équilibreur de charge ADFS, cette adresse IP sera assignée statiquement à l’équilibreur de charge et doit être une adresse ip valide au sein du sous-réseau interne|
|ADDCVMNamePrefix| Préfixe de nom d’ordinateur virtuel pour les contrôleurs de domaine|
|ADFSVMNamePrefix| Préfixe de nom d’ordinateur virtuel pour serveurs ADFS|
|WAPVMNamePrefix| Préfixe du nom de Machine virtuelle pour les serveurs WAP|
|ADDCVMSize| La taille de la mémoire virtuelle des contrôleurs de domaine|
|ADFSVMSize| La taille de la mémoire virtuelle des serveurs ADFS|
|WAPVMSize| La taille de la mémoire virtuelle des serveurs WAP|
|AdminUserName| Le nom de l’administrateur local des ordinateurs virtuels|
|AdminPassword| Le mot de passe pour le compte administrateur local des ordinateurs virtuels|

## <a name="additional-resources"></a>Ressources supplémentaires
* [Jeux de disponibilité](https://aka.ms/Azure/Availability ) 
* [Équilibreur de charge Azure](https://aka.ms/Azure/ILB)
* [Équilibreur de charge interne](https://aka.ms/Azure/ILB/Internal)
* [Équilibrage de la charge avec accès via Internet](https://aka.ms/Azure/ILB/Internet)
* [Comptes de stockage](https://aka.ms/Azure/Storage )
* [Réseaux virtuels Azure](https://aka.ms/Azure/VNet)
* [AD FS et liens de Proxy d’Application Web](http://aka.ms/ADFSLinks) 

## <a name="next-steps"></a>Étapes suivantes

* [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
* [Configuration et gestion de votre AD FS à l’aide d’Azure Connect de publicité](active-directory-aadconnectfed-whatis.md)
* [Déploiement de FS haute disponibilité AD cross-géographique dans Azure avec Azure Traffic Manager](active-directory-adfs-in-azure-with-azure-traffic-manager.md)




