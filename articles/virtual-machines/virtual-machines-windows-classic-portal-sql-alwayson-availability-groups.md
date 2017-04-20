<properties
    pageTitle="Configurez toujours sur le groupe de disponibilité dans Azure VM - classique"
    description="Créer un groupe de disponibilité permanente avec les Machines virtuelles Azure. Ce didacticiel utilise principalement de l’interface utilisateur et les outils au lieu de l’écriture de scripts."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="mikeray" />

# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurez toujours sur le groupe de disponibilité dans Azure VM - classique

> [AZURE.SELECTOR]
- [Le Gestionnaire de ressources : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Le Gestionnaire de ressources : manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classique : l’interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classique : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Ce didacticiel de bout en bout pour vous montre comment implémenter des groupes de disponibilité à l’aide de SQL Server Always On s’exécutant sur des machines virtuelles Azure.

À la fin du didacticiel, votre solution SQL Server Always On dans Azure se compose des éléments suivants :

- Un réseau virtuel contenant plusieurs sous-réseaux, y compris un front-end et un back-end sous-réseau

- Un contrôleur de domaine à un domaine Active Directory (AD)

- Ordinateurs virtuels déployés sur le sous-réseau du back-end et le joint au domaine AD de deux SQL Server

- Un cluster WSFC 3-nœud avec le modèle de quorum nœud majoritaire

- Un groupe de disponibilité avec deux réplicas validation synchrone d’une base de données de disponibilité

L’illustration suivante est une représentation graphique de la solution.

![Architecture de laboratoire de test pour AG dans Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Notez qu’il s’agit d’une des configurations possibles. Par exemple, vous pouvez réduire le nombre de machines virtuelles pour un groupe de disponibilité de deux répliques pour pouvoir enregistrer sur le calcul des heures dans Azure à l’aide du contrôleur de domaine en tant que témoin de partage de fichier de quorum dans un cluster à 2 nœuds WSFC. Cette méthode réduit le nombre de machines virtuelles par un à partir de la configuration ci-dessus.

Ce didacticiel suppose que les éléments suivants :

- Vous avez déjà un compte Azure.

- Vous savez déjà comment configurer une VM de SQL Server classique à partir de la galerie de la machine virtuelle à l’aide de l’interface utilisateur.

- Vous avez déjà une solide compréhension des toujours sur les groupes de disponibilité. Pour plus d’informations, consultez [Toujours sur les groupes de disponibilité (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si vous vous intéressez à l’aide de toujours sur les groupes de disponibilité avec SharePoint, consultez également [Configurer SQL Server 2012 toujours sur disponibilité des groupes de SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx).

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Créez le réseau virtuel et un serveur de contrôleur de domaine

Commencer avec un nouveau compte d’essai Azure. Lorsque vous avez terminé la configuration de votre compte, vous devez être dans l’écran d’accueil du portail Azure classique.

1. Cliquez sur le bouton **Nouveau** dans le coin inférieur gauche de la page, comme indiqué ci-dessous.

    ![Cliquez sur Nouveau dans le portail](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)

1. Cliquez sur **Services de réseau**, **Un réseau virtuel** et puis cliquez sur **Créer des personnalisées**, comme indiqué ci-dessous.

    ![Créer des réseaux virtuels](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)

1. Dans la boîte de dialogue **Créer un réseau virtuel** , créez un nouveau réseau virtuel en parcourant les pages avec les paramètres ci-dessous. 

  	|Page|Paramètres|
|---|---|
|Détails du réseau virtuel|**NOM = ContosoNET**<br/>**RÉGION = ouest des États-Unis**|
|Les serveurs DNS et la connectivité VPN|Aucun|
|Espaces d’adressage de réseau virtuel|Paramètres figurent dans la capture d’écran ci-dessous : ![Créer des réseaux virtuels](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png)|

1. Ensuite, vous créez la machine virtuelle que vous utiliserez en tant que contrôleur de domaine (DC). Cliquez sur **Nouveau** , puis à **Calculer**, puis **Machine virtuelle**et **De la galerie**, comme illustré ci-dessous.

    ![Créer un ordinateur virtuel](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)

1. Dans la boîte de dialogue **Création d’un ordinateur virtuel** , configurer une nouvelle machine virtuelle en parcourant les pages avec les paramètres ci-dessous. 

  	|Page|Paramètres|
|---|---|
|Sélectionnez le système d’exploitation de machine virtuelle|Windows Server 2012 R2 Datacenter|
|Configuration de machine virtuelle|**DATE de publication de la VERSION** = (dernier)<br/>**Nom de l’ordinateur virtuel** = ContosoDC<br/>**Niveau** = STANDARD<br/>**Taille** = A2 (2 noyaux)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|
|Configuration de machine virtuelle|**SERVICE CLOUD** = créer un nouveau service de cloud<br/>**Nom DNS du SERVICE CLOUD** = nom de service de cloud unique<br/>**Nom DNS** = un nom unique (ex : ContosoDC123)<br/>**Région/groupe d’affinité/virtuel réseau** = ContosoNET<br/>**Sous-réseaux du réseau virtuel** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage généré automatiquement<br/>**Jeu de disponibilité** = (aucun)|
|Options de la machine virtuelle|Utiliser les paramètres par défaut|

Une fois que vous avez terminé la configuration de la nouvelle machine virtuelle, attendez que la machine virtuelle pour être provsioned. Ce processus prend du temps, et si vous cliquez sur l’onglet de la **Machine virtuelle** dans Azure portal classique, vous pouvez voir ContosoDC cyclique États de **départ (Provisioning)** **arrêté**, **démarrage**, **en cours d’exécution (Provisioning)**et enfin **en cours d’exécution**.

Le serveur de contrôleur de domaine est désormais correctement configuré. Ensuite, vous allez configurer le domaine Active Directory sur ce serveur de contrôleur de domaine.

## <a name="configure-the-domain-controller"></a>Configurer le contrôleur de domaine

Dans les étapes suivantes, vous configurez l’ordinateur ContosoDC en tant que contrôleur de domaine pour corp.contoso.com.

1. Dans le portail, sélectionnez la machine **ContosoDC** . Sous l’onglet **tableau de bord** , cliquez sur **se connecter** pour ouvrir un fichier RDP pour accès Bureau à distance.

    ![Se connecter à l’ordinateur de Vritual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)

1. Connectez-vous avec votre configuré le compte administrateur (**\AzureAdmin**) et le mot de passe (**Contoso ! 000**).

1. Par défaut, le tableau de bord du **Gestionnaire de serveur** doit être affiché.

1. Cliquez sur le lien **Ajouter des rôles et fonctionnalités** sur le tableau de bord.

    ![Ajouter des rôles serveur Explorateur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)

1. Cliquez sur **suivant** jusqu'à ce que vous obtenez à la section des **Rôles de serveur** .

1. Sélectionnez les rôles des **Services de domaine Active Directory** et le **Serveur DNS** . Lorsque vous y êtes invité, ajouter des fonctionnalités supplémentaires requises par ces rôles.

    >[AZURE.NOTE] Vous obtiendrez une avertissement qu’il n’existe aucune adresse IP statique de validation. Si vous testez la configuration, cliquez sur Continuer. Pour la production de scénarios [utiliser PowerShell pour définir l’adresse IP de l’ordinateur du contrôleur de domaine](./virtual-network/virtual-networks-reserved-private-ip.md).

    ![Rôles de boîte de dialogue Ajouter](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)

1. Cliquez sur **suivant** jusqu'à ce que vous atteigniez la section **Confirmation** . Activez la case à cocher **redémarrer le serveur de destination automatiquement si nécessaire** .

1. Cliquez sur **installer**.

1. Une fois les fonctionnalités de l’installation, revenir au tableau de bord du **Gestionnaire de serveur** .

1. Sélectionnez la nouvelle option de **services AD DS** sur le volet de gauche.

1. Cliquez sur le lien **plus** dans la barre d’avertissement jaune.

    ![Boîte de dialogue AD DS sur la machine virtuelle du serveur DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)

1. Dans la colonne **Action** de la boîte de dialogue **Tous les détails des tâches serveur** , cliquez sur **promouvoir ce serveur en contrôleur de domaine**.

1. Dans l' **Assistant Configuration de Services de domaine Active Directory**, utilisez les valeurs suivantes :

  	|Page|Paramètre|
|---|---|
|Configuration de déploiement|**Ajouter une nouvelle forêt** = sélectionné<br/>**Nom du domaine racine** = corp.contoso.com|
|Options de contrôleur de domaine|**Mot de passe** = Contoso ! 000<br/>**Confirmer le mot de passe** = Contoso ! 000|

1. Cliquez sur **suivant** pour passer à d’autres pages de l’Assistant. Sur la page **Prerequisites Check** , vérifiez que vous voyez le message suivant : **toutes les vérifications des éléments prérequis passé avec succès**. Notez que vous devez examiner les messages d’avertissement applicables, mais il est possible de poursuivre l’installation.

1. Cliquez sur **installer**. L’ordinateur virtuel **ContosoDC** redémarre automatiquement.

## <a name="configure-domain-accounts"></a>Configurer les comptes de domaine

Les étapes suivantes configurer les comptes Active Directory (AD) pour une utilisation ultérieure.

1. Reconnectez-vous à l’ordinateur **ContosoDC** .

1. Dans le **Gestionnaire de serveur** sélectionnez **Outils** , puis cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)

1. Dans le **Centre d’administration Active Directory** select **corp (local)** dans le volet de gauche.

1. Dans le volet de **tâches de** droite, sélectionnez **Nouveau** , puis cliquez sur **utilisateur**. Utilisez les paramètres suivants :

  	|Paramètre|Valeur|
|---|---|
|**Prénom**|Installer|
|**SamAccountName de l’utilisateur**|Installer|
|**Mot de passe**|Contoso ! 000|
|**Confirmer le mot de passe**|Contoso ! 000|
|**Autres options de mot de passe**|Sélectionné|
|**Mot de passe n’expire jamais**|Archivés|

1. Cliquez sur **OK** pour créer l’utilisateur à **installer** . Ce compte sera utilisé pour configurer le cluster de basculement et le groupe de disponibilité.

1. Créer deux utilisateurs supplémentaires avec les mêmes étapes : **CORP\SQLSvc1** et **CORP\SQLSvc2**. Ces comptes seront utilisés pour les instances de SQL Server. Ensuite, vous devez donner des autorisations nécessaires pour la configuration des clusters de basculement Service Windows (WSFC) **CORP\Install** .

1. Dans le **Centre d’administration Active Directory**, sélectionnez **corp (local)** , dans le volet gauche. Puis, dans le volet de **tâches de** droite, cliquez sur **Propriétés**.

    ![Propriétés de l’utilisateur de l’entreprise](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)

1. Sélectionnez les **Extensions**, puis cliquez sur le bouton **Avancé** sous l’onglet **sécurité** .

1. Dans la boîte de dialogue **Paramètres de sécurité avancés pour corp** . Cliquez sur **Ajouter**.

1. Cliquez sur **Sélectionner une entité de sécurité**. Recherchez **CORP\Install**. Cliquez sur **OK**.

1. Sélectionnez les autorisations **lire toutes les propriétés** et les **objets de créer un ordinateur** .

    ![Autorisations de l’utilisateur de l’entreprise](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)

1. Cliquez sur **OK**, puis cliquez sur **OK** à nouveau. Fermez la fenêtre de propriétés de corp.

Maintenant que vous avez terminé de configurer les objets Active Directory et l’utilisateur, vous créez trois machines virtuelles de SQL Server et les ajouter dans ce domaine.

## <a name="create-the-sql-server-vms"></a>Créer des ordinateurs virtuels SQL Server

Ensuite, créez les trois ordinateurs virtuels, y compris un nœud de cluster WSFC et deux machines virtuelles de SQL Server. Pour créer des ordinateurs virtuels, revenez au portail Azure classique, cliquez sur **Nouveau**, **Compute**, **Machine virtuelle**, puis **De la galerie**. Puis, utilisez les modèles dans le tableau suivant pour vous aider à créer des ordinateurs virtuels.

|Page|VM1|ORDINATEUR VIRTUEL 2|VM3|
|---|---|---|---|
|Sélectionnez le système d’exploitation de machine virtuelle|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 RTM Enterprise**|**SQL Server 2014 RTM Enterprise**|
|Configuration de machine virtuelle|**DATE de publication de la VERSION** = (dernier)<br/>**Nom de l’ordinateur virtuel** = ContosoWSFCNode<br/>**Niveau** = STANDARD<br/>**Taille** = A2 (2 noyaux)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|**DATE de publication de la VERSION** = (dernier)<br/>**Nom de l’ordinateur virtuel** = ContosoSQL1<br/>**Niveau** = STANDARD<br/>**Taille** = A3 (4 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|**DATE de publication de la VERSION** = (dernier)<br/>**Nom de l’ordinateur virtuel** = ContosoSQL2<br/>**Niveau** = STANDARD<br/>**Taille** = A3 (4 cœurs)<br/>**Nouveau nom d’utilisateur** = AzureAdmin<br/>**Nouveau mot de passe** = Contoso ! 000<br/>**Confirmer** = Contoso ! 000|
|Configuration de machine virtuelle|**SERVICE CLOUD** = le nom DNS du Service nuage de Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe d’affinité/virtuel réseau** = ContosoNET<br/>**Sous-réseaux du réseau virtuel** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage généré automatiquement<br/>**Disponibilité définir** = créer une disponibilité définie<br/>**Nom du jeu de disponibilité** = SQLHADR|**SERVICE CLOUD** = le nom DNS du Service nuage de Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe d’affinité/virtuel réseau** = ContosoNET<br/>**Sous-réseaux du réseau virtuel** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage généré automatiquement<br/>**Disponibilité définie** = SQLHADR (vous pouvez également configurer la disponibilité définie après que l’ordinateur a été créé. Les trois ordinateurs doivent être affectés à l’ensemble de la disponibilité SQLHADR.)|**SERVICE CLOUD** = le nom DNS du Service nuage de Unique créé précédemment (ex : ContosoDC123)<br/>**Région/groupe d’affinité/virtuel réseau** = ContosoNET<br/>**Sous-réseaux du réseau virtuel** = Back(10.10.2.0/24)<br/>**Compte de stockage** = utiliser un compte de stockage généré automatiquement<br/>**Disponibilité définie** = SQLHADR (vous pouvez également configurer la disponibilité définie après que l’ordinateur a été créé. Les trois ordinateurs doivent être affectés à l’ensemble de la disponibilité SQLHADR.)|
|Options de la machine virtuelle|Utiliser les paramètres par défaut|Utiliser les paramètres par défaut|Utiliser les paramètres par défaut|

<br/>

>[AZURE.NOTE] La configuration précédente suggère des machines virtuelles de couche STANDARD, dans la mesure où les ordinateurs de la couche de base ne prennent pas en charge les points de terminaison avec équilibrage de charge requis pour créer ultérieurement des écouteurs d’un groupe de disponibilité. En outre, les tailles de machine suggérés ici sont conçus pour tester des groupes de disponibilité dans Azure VM. Pour obtenir des performances optimales sur les charges de travail, consultez les recommandations pour la configuration dans les [méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md)et de tailles de machine SQL Server.

Une fois que les trois ordinateurs virtuels sont entièrement mis en service, vous devez les joindre au domaine **corp.contoso.com** et accorder des droits administratifs de CORP\Install pour les ordinateurs. Pour ce faire, procédez comme suit pour chacun des trois ordinateurs virtuels.

1. Tout d’abord, modifiez l’adresse de serveur DNS préféré. Commencez par télécharger le fichier (RDP) à distance Bureau de chaque ordinateur virtuel dans votre répertoire local en sélectionnant la machine virtuelle dans la liste, puis en cliquant sur le bouton **se connecter** . Pour sélectionner un ordinateur virtuel, cliquez n’importe où mais la première cellule de la ligne, comme illustré ci-dessous.

    ![Téléchargez le fichier RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)

1. Lancez le fichier RDP que vous avez téléchargé et vous connecter à la machine virtuelle à l’aide de votre configuré le compte administrateur (**BUILTIN\AzureAdmin**) et le mot de passe (**Contoso ! 000**).

1. Une fois que vous êtes connecté, vous devez voir le tableau de bord du **Gestionnaire de serveur** . Dans le volet gauche, cliquez sur **Serveur Local** .

1. Sélectionnez le lien de **l’adresse IPv4 attribuée par DHCP, IPv6 est activé** .

1. Dans la fenêtre **Connexions réseau** , cliquez sur l’icône de réseau.

    ![Modification du serveur DNS préféré de machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)

1. Dans la barre de commande, cliquez sur **Modifier les paramètres de cette connexion** (selon la taille de votre fenêtre, vous devrez peut-être cliquez sur la double flèche droite pour afficher cette commande).

1. Sélectionnez **Internet Protocol Version 4 (TCP/IPv4)** , puis cliquez sur Propriétés.

1. Sélectionnez utiliser le serveur DNS suivant résout et spécifiez **10.10.2.4** dans **serveur DNS préféré**.

1. L' adresse **10.10.2.4** est l’adresse attribuée à un ordinateur virtuel dans le sous-réseau de 10.10.2.0/24 dans un réseau virtuel Azure, et cet ordinateur virtuel est **ContosoDC**. Pour vérifier l’adresse IP de **ContosoDC**de, utilisez la **contosodc de nslookup** à l’invite de commandes, comme indiqué ci-dessous.

    ![Utiliser NSLOOKUP pour trouver l’adresse IP de contrôleur de domaine](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)

1. Cliquez sur O,**K** , puis sur **Fermer** pour valider les modifications. Vous êtes maintenant en mesure de rejoindre la machine virtuelle pour **corp.contoso.com**.

1. Dans la fenêtre du **Serveur Local** , cliquez sur le lien de **groupe de travail** .

1. Dans la section **Nom de l’ordinateur** , cliquez sur **Modifier**.

1. Activez la case à cocher **domaine** et tapez **corp.contoso.com** dans la zone de texte. Cliquez sur **OK**.

1. Dans la boîte de dialogue contextuelle de **Sécurité de Windows** , spécifiez les informations d’identification pour le compte administrateur du domaine par défaut (**CORP\AzureAdmin**) et le mot de passe (**Contoso ! 000**).

1. Lorsque vous voyez le message « Bienvenue dans le domaine corp.contoso.com », cliquez sur **OK**.

1. Cliquez sur **Fermer**, puis cliquez sur **Redémarrer maintenant** dans la boîte de dialogue contextuelle.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Ajoutez l’utilisateur Corp\Install en tant qu’administrateur sur chaque ordinateur virtuel :

1. Attendez que la machine virtuelle est redémarrée, puis lancer le fichier RDP à nouveau pour vous connecter à la machine virtuelle en utilisant le compte **BUILTIN\AzureAdmin** .

1. Dans le **Gestionnaire de serveur** sélectionnez **Outils**, puis cliquez sur **Gestion de l’ordinateur**.

    ![Gestion de l’ordinateur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)

1. Dans la fenêtre **Gestion de l’ordinateur** , développez **utilisateurs et groupes locaux**et puis sélectionnez **groupes**.

1. Double-cliquez sur le groupe **administrateurs** .

1. Dans la boîte de dialogue **Propriétés d’administrateurs** , cliquez sur le bouton **Ajouter** .

1. Entrez l’utilisateur **CORP\Install**, puis cliquez sur **OK**. À l’invite d’informations d’identification, utiliser le compte de **AzureAdmin** avec la **Contoso ! 000** mot de passe.

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés de l’administrateur** .

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Ajouter la fonctionnalité **Clustering avec basculement** à chaque machine virtuelle.

1. Dans le tableau de bord du **Gestionnaire de serveur** , cliquez sur **Ajouter des rôles et fonctionnalités**.

1. **Ajout de rôles et de fonctionnalités Assistant**, cliquez sur **suivant** jusqu'à ce que vous arrivez à la page de **fonctionnalités** .

1. Sélectionnez le **Clustering avec basculement**. Lorsque vous y êtes invité, ajoutez toutes les autres fonctions dépendantes.

    ![Ajouter la fonctionnalité de clustering de machine virtuelle](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)

1. Cliquez sur **suivant**, puis cliquez sur **installer** sur la page de **Confirmation** .

1. Lors de l’installation de la fonctionnalité **Clustering avec basculement** est terminée, cliquez sur **Fermer**.

1. Se déconnecter de l’ordinateur virtuel.

1. Répétez les étapes décrites dans cette section pour les trois serveurs-- **ContosoWSFCNode**, **ContosoSQL1**et **ContosoSQL2**.

Les ordinateurs virtuels de SQL Server sont maintenant configurés et en cours d’exécution, mais ils sont installés avec SQL Server avec les options par défaut.

## <a name="create-the-wsfc-cluster"></a>Créer le Cluster WSFC

Dans cette section, vous créez le cluster WSFC qui héberge le groupe de disponibilité que vous créerez ultérieurement. Maintenant, tu aurais dû faire suivantes pour chacun des trois ordinateurs virtuels que vous allez utiliser dans le cluster WSFC :

- Entièrement mis en service dans Azure

- VM joint au domaine

- Ajout de **CORP\Install** au groupe Administrateurs local

- Ajout de la fonctionnalité Clustering avec basculement

Tous ces sont les composants requis sur chaque ordinateur virtuel avant de le joindre au cluster WSFC.

Notez également que le réseau virtuel Azure ne se comporte pas de la même manière qu’un réseau local. Vous devez créer le cluster dans l’ordre suivant :

1. Créer un cluster à nœud unique sur l’un des nœuds (**ContosoSQL1**).

1. Modifier l’adresse IP du cluster en une adresse IP inutilisée (**10.10.2.101**).

1. Mettez le nom de cluster est en ligne.

1. Ajoutez les autres nœuds (**ContosoSQL2** et **ContosoWSFCNode**).

Suivez les étapes ci-dessous pour accomplir ces tâches entièrement configure le cluster.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en utilisant le compte de domaine **CORP\Install**.

1. Dans le tableau de bord du **Gestionnaire de serveur** , sélectionnez **Outils**, puis cliquez sur **Gestionnaire du Cluster de basculement**.

1. Dans le volet gauche, cliquez sur **Gestionnaire du Cluster de basculement**et puis cliquez sur **créer un Cluster**, comme indiqué ci-dessous.

    ![Créer le Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)

1. Dans l’Assistant Création d’un Cluster, créez un cluster d’un nœud en parcourant les pages avec les paramètres ci-dessous :

  	|Page|Paramètres|
|---|---|
|Avant de commencer|Utiliser les paramètres par défaut|
|Sélectionnez les serveurs|Tapez **ContosoSQL1** dans **Entrez le nom de serveur** et cliquez sur **Ajouter**|
|Avertissement de validation|Sélectionnez **n° je ne nécessitent pas de prise en charge de Microsoft pour ce cluster et que vous ne souhaitez donc pas à exécuter les tests de validation. Lorsque je clique sur Suivant, passez à Création du cluster**.|
|Point d’accès pour administrer le Cluster.|Type **Cluster1** dans **nom du Cluster**|
|Confirmation|Par défaut, sauf si vous utilisez des espaces de stockage. Consultez la Remarque qui suit ce tableau.|

    >[AZURE.WARNING] Si vous utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739), ce qui regroupe plusieurs disques dans des pools de stockage, vous devez désactiver la case à cocher **Ajouter tout le stockage éligible au cluster** sur la page de **Confirmation** . Si vous ne décochez pas cette option, les disques virtuels seront détachés pendant le processus de clustering. Par conséquent, elles aussi n’apparaîtra pas dans le Gestionnaire de disques ou de l’Explorateur tant que les espaces de stockage sont supprimés du cluster et reconnectée à l’aide de PowerShell.

1. Dans le volet gauche, développez le **Gestionnaire du Cluster de basculement**, puis cliquez sur **Cluster1.corp.contoso.com**.

1. Dans le volet central, faites défiler jusqu'à la section de **Ressources de Cluster principal** et développez le **nom : Clutser1** plus d’informations. Vous devez voir le **nom** et les ressources **d’Adresse IP** dans l’état **d’Échec** . La ressource d’adresse IP ne peut pas être mise en ligne car le cluster est affecté à la même adresse IP que celle de l’ordinateur lui-même, qui est une adresse en double.

1. Avec le bouton droit de la ressource **Adresse IP** , puis cliquez sur **Propriétés**.

    ![Propriétés du cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)

1. Sélectionnez **l’Adresse IP statique** et spécifiez **10.10.2.101** dans la zone de texte adresse. Puis, cliquez sur **OK**.

1. Dans la section **Ressources principales du Cluster** , avec le bouton droit **nom : Cluster1** et cliquez sur **Mettre en ligne**. Puis, attendez que les deux ressources sont en ligne. Lorsque la ressource de nom de cluster est en ligne, il met à jour le serveur de contrôleur de domaine avec un compte d’ordinateur d’Active Directory. Ce compte AD sera utilisé pour exécuter le service de cluster de groupe disponibilité ultérieurement.

1. Enfin, vous ajoutez les nœuds restants dans le cluster. Dans l’arborescence du navigateur, cliquez sur **Cluster1.corp.contoso.com** et cliquez sur **Ajouter un nœud**, comme illustré ci-dessous.

    ![Ajouter le nœud au Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)

1. Dans l' **Assistant Ajout de nœud**, cliquez sur **suivant**. Dans la page **Sélectionner des serveurs** , ajouter **ContosoSQL2** et **ContosoWSFCNode** à la liste en tapant le nom du serveur dans **Entrez le nom de serveur** , puis sur **Ajouter**. Lorsque vous avez terminé, cliquez sur **suivant**.

1. Dans la page **Avertissement de Validation** , cliquez sur **non** (dans un scénario de production vous devez effectuer les tests de validation). Ensuite, cliquez sur **suivant**.

1. Dans la page de **Confirmation** , cliquez sur **suivant** pour ajouter les nœuds.

    >[AZURE.WARNING] Si vous utilisez des [Espaces de stockage](https://technet.microsoft.com/library/hh831739), ce qui regroupe plusieurs disques dans des pools de stockage, vous devez désactiver la case à cocher **Ajouter tout le stockage éligible au cluster** . Si vous ne décochez pas cette option, les disques virtuels seront détachés pendant le processus de clustering. Par conséquent, elles aussi n’apparaîtra pas dans le Gestionnaire de disques ou de l’Explorateur tant que les espaces de stockage sont supprimés du cluster et reconnectée à l’aide de PowerShell.

1. Une fois les nœuds ajoutés au cluster, cliquez sur **Terminer**. Gestionnaire du Cluster de basculement doit désormais afficher que votre cluster a trois nœuds et de les répertorier dans le conteneur des **nœuds** .

1. Se déconnecter de la session Bureau à distance.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Préparer les Instances de SQL Server pour le groupe de disponibilité

Dans cette section, vous allez effectuer les opérations suivantes sur les **ContosoSQL1** et **contosoSQL2**:

- Ajouter une connexion d’accès pour **NT AUTHORITY\System** avec un autorisations nécessaires pour l’instance de SQL Server par défaut

- Ajouter **CORP\Install** comme un rôle sysadmin pour l’instance de SQL Server par défaut

- Ouvrir le pare-feu pour l’accès distant de SQL Server

- Activer la fonctionnalité toujours sur les groupes de disponibilité

- Convertir le compte de service SQL Server **CORP\SQLSvc1** et **CORP\SQLSvc2**, respectivement

Ces actions peuvent être effectuées dans un ordre quelconque. Néanmoins, les étapes ci-dessous guidera les dans l’ordre. Suivez les étapes de **ContosoSQL1** et de **ContosoSQL2**:

1. Si vous n’êtes pas connecté en dehors de la session Bureau à distance pour la machine virtuelle, faites-le maintenant.

1. Lancer les fichiers RDP pour **ContosoSQL1** et **ContosoSQL2** et connectez-vous en tant que **BUILTIN\AzureAdmin**.

1. Tout d’abord, ajoutez **NT AUTHORITY\System** aux ouvertures de session SQL Server et avec les autorisations nécessaires. Lancement **de SQL Server Management Studio**.

1. Cliquez sur **connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, développez **sécurité**, puis puis **connexions**.

1. Cliquez sur le nom d’accès **NT AUTHORITY\System** , puis cliquez sur **Propriétés**.

1. Dans la page **éléments sécurisables** pour le serveur local, sélectionnez **accorder** les autorisations suivantes, puis cliquez sur **OK**.

    - Modifier un groupe de disponibilité

    - Connexion SQL

    - Affichage État du serveur

1. Ensuite, ajoutez **CORP\Install** comme un rôle **sysadmin** pour l’instance de SQL Server par défaut. Dans l' **Explorateur d’objets**, cliquez sur **connexions** , puis cliquez sur **Nouvelle connexion**.

1. Tapez **CORP\Install** dans la zone **nom d’ouverture de session**.

1. Dans la page **Rôles de serveur** , sélectionnez **sysadmin**. Puis, cliquez sur **OK**. Une fois la connexion créée, vous pouvez le voir en développant des **connexions** dans **l’Explorateur d’objets**.

1. Ensuite, vous créez une règle de pare-feu pour SQL Server. À partir de l’écran de **démarrage** , lancez **Le pare-feu Windows avec sécurité avancée**.

1. Dans le volet gauche, sélectionnez **Règles de trafic entrant**. Dans le volet droit, cliquez sur **Nouvelle règle**.

1. Dans la page **Type de règle** , sélectionnez le **programme**, puis cliquez sur **suivant**.

1. Dans la page **programme** , sélectionnez le **chemin d’accès de ce programme** et tapez **%ProgramFiles%\Microsoft SQL Server\MSSQL12. MSSQLSERVER\MSSQL\Binn\sqlservr.exe** dans la zone de texte (si vous suivez ces instructions, mais à l’aide de SQL Server 2012, le répertoire de SQL Server est **MSSQL11. MSSQLSERVER**). Puis cliquez sur **suivant**.

1. Dans la page **Action** , conserver d' **Autoriser la connexion** sélectionnée et cliquez sur **suivant**.

1. Dans la page de **profil** , acceptez les paramètres par défaut et cliquez sur **suivant**.

1. Dans la page **nom** , spécifiez un nom de règle, tel que **SQL Server (règle de programme)** dans la zone de texte **nom** , puis cliquez sur **Terminer**.

1. Ensuite, vous activez la fonctionnalité de **Toujours sur les groupes de disponibilité** . À partir de l’écran de **démarrage** , lancez **Le Gestionnaire de Configuration SQL Server**.

1. Dans l’arborescence du navigateur, cliquez sur **Services de SQL Server**, puis cliquez sur le service **SQL Server (MSSQLSERVER)** et cliquez sur **Propriétés**.

1. Cliquez sur l’onglet **Toujours de haute disponibilité** , puis sélectionnez **Activer toujours sur les groupes de disponibilité**, comme indiqué ci-dessous, puis cliquez sur **Appliquer**. Cliquez sur **OK** dans la boîte de dialogue contextuelle et ne fermez pas encore de la fenêtre Propriétés. Vous allez redémarrer le service SQL Server une fois que vous modifiez le compte de service.

    ![Activez toujours sur les groupes de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)

1. Ensuite, vous modifiez le compte de service SQL Server. Cliquez sur l’onglet **Connexion** , puis tapez **CORP\SQLSvc1** (pour **ContosoSQL1**) ou **CORP\SQLSvc2** (pour **ContosoSQL2**) dans la zone **Nom du compte**, puis renseignez et confirmer le mot de passe et puis cliquez sur **OK**.

1. Dans la fenêtre contextuelle, cliquez sur **Oui** pour redémarrer le service SQL Server. Une fois que le service SQL Server est redémarré, les modifications apportées dans la fenêtre Propriétés sont efficaces.

1. Ouvrez une session sur les ordinateurs virtuels.

## <a name="create-the-availability-group"></a>Créer le groupe de disponibilité

Vous êtes maintenant prêt à configurer un groupe de disponibilité. Voici une description de ce que vous ferez :

- Créer une nouvelle base de données (**MyDB1**) sur **ContosoSQL1**

- Prendre à la fois une sauvegarde complète et une sauvegarde du journal des transactions de la base de données

- Restaurer la version complète et de sauvegardes de journal à **ContosoSQL2** avec l’option **NORECOVERY**

- Créer le groupe de disponibilité (**AG1**) avec validation synchrone et basculement automatique des réplicas secondaires lisibles

### <a name="create-the-mydb1-database-on-contososql1"></a>Créer la base de données MyDB1 sur ContosoSQL1 :

1. Si vous n’êtes pas déjà de connecté sur les sessions Bureau à distance pour **ContosoSQL1** et **ContosoSQL2**, faites-le maintenant.

1. Lancez le fichier RDP pour **ContosoSQL1** et connectez-vous en tant que **CORP\Install**.

1. Dans l' **Explorateur de fichiers**, sous * *C:\**, créez un répertoire appelé * *sauvegarde**. Vous utiliserez ce répertoire permet de sauvegarder et de restaurer votre base de données.

1. Le nouveau répertoire d’avec le bouton droit, pointez sur **partager avec**, puis cliquez sur **des personnes spécifiques**, comme indiqué ci-dessous.

    ![Créer un dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)

1. Ajouter **CORP\SQLSvc1** et lui donner l’autorisation de **Lecture/écriture** , puis ajouter des **CORP\SQLSvc2** et lui donner l’autorisation de **lecture** , comme illustré ci-dessous, puis cliquez sur **partage**. Une fois le processus de partage des fichiers est terminée, cliquez sur **terminé**.

    ![Accorder des autorisations pour le dossier de sauvegarde](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)

1. Ensuite, vous créez la base de données. Dans le menu **Démarrer** , lancez **SQL Server Management Studio**, puis cliquez sur **connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, cliquez sur **bases de données** , puis cliquez sur **Nouvelle base de données**.

1. Dans la zone **nom de la base de données**, tapez **MyDB1**, puis cliquez sur **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Effectuez une sauvegarde de MyDB1 complète et la restaurer sur ContosoSQL2 :

1. Ensuite, vous effectuez une sauvegarde complète de la base de données. Dans l' **Explorateur d’objets**, développez **bases de données**, puis cliquez sur **MyDB1**, pointez sur **tâches**puis puis cliquez sur **Sauvegarder**.

1. Dans la section **Source** , conserver le **type de sauvegarde** est défini sur **complète**. Dans la section **Destination** , cliquez sur **Supprimer** pour supprimer le chemin d’accès de fichier par défaut pour le fichier de sauvegarde.

1. Dans la section **Destination** , cliquez sur **Ajouter**.

1. Dans la zone de texte **nom de fichier** , tapez ** \\ContosoSQL1\backup\MyDB1.bak**. Ensuite, cliquez sur **OK**, puis cliquez sur **OK** pour sauvegarder la base de données. Une fois l’opération de sauvegarde terminée, cliquez sur **OK** pour fermer la boîte de dialogue.

1. Ensuite, vous prenez un journal des transactions copie de sauvegarde de la base de données. Dans l' **Explorateur d’objets**, développez **bases de données**, puis cliquez sur **MyDB1**, pointez sur **tâches**puis puis cliquez sur **Sauvegarder**.

1. Dans type de **sauvegarde** , sélectionnez **Journal des transactions**. Conserver le chemin d’accès du fichier de **Destination** défini sur celui que vous avez spécifié précédemment et que vous cliquez sur **OK**. Une fois l’opération de sauvegarde terminée, cliquez de nouveau sur **OK** .

1. Ensuite, vous restaurez les sauvegardes de journal entiers et de transaction sur **ContosoSQL2**. Lancez le fichier RDP pour **ContosoSQL2** et connectez-vous en tant que **CORP\Install**. Laissez ouverte la session Bureau à distance pour **ContosoSQL1** .

1. Dans le menu **Démarrer** , lancez **SQL Server Management Studio**, puis cliquez sur **connexion** pour vous connecter à l’instance de SQL Server par défaut.

1. Dans l' **Explorateur d’objets**, cliquez sur **bases de données** , puis cliquez sur **Restaurer la base de données**.

1. Dans la section **Source** , sélectionnez le **périphérique**, puis cliquez sur le **...** bouton.

1. Dans la zone **Sélectionnez les périphériques de sauvegarde**, cliquez sur **Ajouter**.

1. Dans l’emplacement du fichier de sauvegarde, tapez \\ContosoSQL1\backup, puis cliquez sur Actualiser, sélectionnez MyDB1.bak, puis cliquez sur OK puis cliquez à nouveau sur OK. Vous devez maintenant voir la sauvegarde complète et la sauvegarde du journal de la sauvegarde à restaurer le volet.

1. Aller à la page Options, puis sélectionnez RESTORE WITH NORECOVERY dans l’état de la récupération, puis cliquez sur OK pour restaurer la base de données. Une fois l’opération de restauration terminée, cliquez sur OK.

### <a name="create-the-availability-group"></a>Créez le groupe de disponibilité :

1. Revenir à la session Bureau à distance pour **ContosoSQL1**. Dans l' **Explorateur d’objets** dans SSMS, cliquez **Toujours sur haute disponibilité** , puis cliquez sur **Assistant Nouveau groupe de disponibilité**, comme illustré ci-dessous.

    ![Lancer l’Assistant Nouveau groupe de disponibilité](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)

1. Dans la page **d’Introduction** , cliquez sur **suivant**. Dans la page **Spécifier un nom de groupe de disponibilité** , tapez **AG1** dans **nom du groupe de disponibilité**, puis cliquez à nouveau sur **suivant** .

    ![L’Assistant Nouvelle AG, spécifiez nom de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)

1. Dans la page **Sélectionner les bases de données** , sélectionnez **MyDB1** et cliquez sur **suivant**. La base de données remplit les conditions requises pour un groupe de disponibilité parce que vous avez effectuées au moins une sauvegarde complète sur le réplica principal prévu.

    ![L’Assistant Nouvelle AG, sélectionnez les bases de données](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)

1. Dans la page **Spécifier des réplicas** , cliquez sur **Ajouter un réplica**.

    ![L’Assistant Nouvelle AG, spécifier des réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)

1. La boîte de dialogue **se connecter au serveur** s’affiche. Tapez **ContosoSQL2** dans la zone **nom du serveur**, puis cliquez sur **se connecter**.

    ![L’Assistant Nouvelle AG, se connecter au serveur](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)

1. Dans la page **Spécifier les réplicas** , vous devez maintenant voir **ContosoSQL2** répertoriées dans **Les réplicas disponibles**. Configurez les réplicas comme indiqué ci-dessous. Lorsque vous avez terminé, cliquez sur **suivant**.

    ![L’Assistant Nouvelle AG, spécifier des réplicas (complète)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)

1. Dans la page **Sélectionner la synchronisation initiale des données** , cliquez sur **joindre uniquement** et cliquez sur **suivant**. Vous avez déjà effectué la synchronisation des données manuellement lorsque vous avez effectué les sauvegardes complète et de transaction sur **ContosoSQL1** et que vous les restaurer sur **ContosoSQL2**. Au lieu de cela, vous pouvez choisir pas à effectuer la sauvegarde et restauration sur la base de données et sélectionnez **complet** pour permettre à l’Assistant Nouveau groupe de disponibilité d’effectuer la synchronisation des données pour vous. Toutefois, cela n’est pas recommandée pour les très grandes bases de données qui sont trouvent dans certaines entreprises.

    ![L’Assistant Nouvelle AG, activez la synchronisation des données initiale](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)

1. Dans la page de **Validation** , cliquez sur **suivant**. Cette page doit ressembler à ci-dessous. Un avertissement est pour la configuration de port d’écoute, car vous n’avez pas configuré un écouteur de groupe de disponibilité. Vous pouvez ignorer cet avertissement, dans la mesure où ce didacticiel ne configure pas un port d’écoute. Pour configurer le port d’écoute après la fin de ce didacticiel, consultez [configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

    ![Nouvel Assistant de Validation, AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)

1. Dans la page **Résumé** , cliquez sur **Terminer**, puis attendez pendant que l’Assistant configure le nouveau groupe de disponibilité. Dans la page de **progression** , vous pouvez cliquer sur **plus de détails** pour afficher la progression détaillée. Une fois que l’Assistant est terminé, vérifiez que la page de **résultats** , vérifiez que le groupe de disponibilité est créé, comme indiqué ci-dessous, puis cliquez sur **Fermer** pour quitter l’Assistant.

    ![Résultats de l’Assistant Nouvelle AG,](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)

1. Dans l' **Explorateur d’objets**, développez **Toujours sur une disponibilité élevée**, puis développez **Groupes de disponibilité**. Vous devez maintenant voir le nouveau groupe de disponibilité de ce conteneur. Droit **AG1 (principal)** , puis cliquez sur **Afficher le tableau de bord**.

    ![Afficher le tableau de bord AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)

1. Votre **Toujours sur tableau de bord** doit être similaire à celui illustré ci-dessous. Vous pouvez voir les réplicas, le mode de basculement de chaque réplica et l’état de synchronisation.

    ![Tableau de bord AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)

1. Revenir au **Gestionnaire de serveur**, sélectionnez **Outils**et lancez ensuite le **Gestionnaire du Cluster de basculement**.

1. Développez **Cluster1.corp.contoso.com**, puis développez **Services et applications**. Sélectionnez les **rôles** et notez que le groupe de rôles de la disponibilité **d’AG1** a été créé. Notez que AG1 n’a pas de n’importe quelle adresse IP par la base de données des clients peuvent se connecter au groupe de disponibilité, car vous n’avez pas configuré un port d’écoute. Vous pouvez vous connecter directement au nœud principal pour des opérations de lecture / écriture et le nœud secondaire pour les requêtes en lecture seule.

    ![AG dans Gestionnaire du Cluster de basculement](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

>[AZURE.WARNING] Ne tentez pas de basculer le groupe de disponibilité à partir du Gestionnaire du Cluster de basculement. Toutes les opérations de basculement sur incident doivent être effectuées à partir de **Toujours sur tableau de bord** dans SSMS. Pour plus d’informations, consultez [Restrictions sur à l’aide de la WSFC Cluster Gestionnaire de basculement avec des groupes de disponibilité](https://msdn.microsoft.com/library/ff929171.aspx).

## <a name="next-steps"></a>Étapes suivantes
Vous avez maintenant correctement implémenté SQL Server Always On en créant un groupe de disponibilité dans Azure. Pour configurer un port d’écoute pour ce groupe de disponibilité, consultez [configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

Pour plus d’informations sur l’utilisation de SQL Server dans Azure, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
