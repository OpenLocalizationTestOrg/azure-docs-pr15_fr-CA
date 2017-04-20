<properties
    pageTitle="Configurez toujours sur le groupe de disponibilité dans Azure VM automatiquement - Gestionnaire de ressources"
    description="Créer un groupe de disponibilité permanente avec Azure machines virtuelles en mode de Gestionnaire des ressources Azure. Ce didacticiel utilise principalement l’interface utilisateur pour créer automatiquement l’ensemble de la solution."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/20/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-always-on-availability-group-in-azure-vm-automatically---resource-manager"></a>Configurez toujours sur le groupe de disponibilité dans Azure VM automatiquement - Gestionnaire de ressources

> [AZURE.SELECTOR]
- [Le Gestionnaire de ressources : modèle](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Le Gestionnaire de ressources : manuel](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [Classique : l’interface utilisateur](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [Classique : PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

Ce didacticiel de bout en bout pour vous montre comment créer un groupe de disponibilité de SQL Server avec les ordinateurs virtuels de gestionnaire de ressources Azure. Le didacticiel utilise des lames Azure pour configurer un modèle. Vous passez en revue les paramètres par défaut, tapez les paramètres requis et mettre à jour les lames dans le portail que vous passez en revue ce didacticiel.

À la fin du didacticiel, votre solution de groupe de disponibilité de SQL Server dans Azure se compose des éléments suivants :

- Un réseau virtuel contenant plusieurs sous-réseaux, y compris un front-end et un back-end sous-réseau

- Deux contrôleurs de domaines avec un domaine Active Directory (AD)

- Ordinateurs virtuels déployés sur le sous-réseau du back-end et le joint au domaine AD de deux SQL Server

- Un cluster WSFC 3-nœud avec le modèle de quorum nœud majoritaire

- Un groupe de disponibilité avec deux réplicas validation synchrone d’une base de données de disponibilité

L’illustration suivante est une représentation graphique de la solution.

![Architecture de laboratoire de test pour AG dans Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Toutes les ressources dans cette solution appartiennent à un groupe de ressources unique.

Ce didacticiel suppose que les éléments suivants :

- Vous avez déjà un compte Azure. Si vous n’en avez pas, [Inscrivez-vous à un compte d’essai](http://azure.microsoft.com/pricing/free-trial/).

- Vous savez déjà comment configurer un ordinateur virtuel de SQL Server à partir de la galerie de la machine virtuelle à l’aide de l’interface utilisateur. Pour plus d’informations, reportez-vous à la section [mise en service d’un ordinateur virtuel de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md)

- Vous avez déjà une solide compréhension des groupes de disponibilité. Pour plus d’informations, consultez [toujours sur les groupes de disponibilité (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

>[AZURE.NOTE] Si vous vous intéressez à l’aide de groupes de disponibilité avec SharePoint, consultez également [configurer SQL Server 2012 toujours sur les groupes de disponibilité pour SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx).

Dans ce didacticiel, vous allez utiliser le portail Azure pour :

- Sélectionnez le le modèle toujours à partir du portail

- Passez en revue les paramètres du modèle et quelques paramètres de configuration de votre environnement de mise à jour

- Azure moniteur qu’elle crée l’ensemble de l’environnement

- Se connecter à l’un des contrôleurs de domaine, puis sur l’un des serveurs SQL

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]


## <a name="provision-the-cluster-from-the-gallery"></a>Mettre en service le cluster à partir de la galerie

Azure fournit une image de la galerie pour l’ensemble de la solution. Pour localiser le modèle :

1.  Connectez-vous au portail Azure à l’aide de votre compte.
1.  Dans la, cliquez sur portail Azure **+ nouveau.** Le portail s’ouvre la nouvelle blade.
1.  Sur la nouvelle blade recherche **AlwaysOn**.
![Rechercher le modèle de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
1.  Résultats de la recherche, recherchez **SQL Server AlwaysOn Cluster**.
![Modèle de AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
1.  Sélectionnez **un modèle de déploiement** , choisissez **Gestionnaire de ressources**.

### <a name="basics"></a>Notions de base

Cliquez sur les **Notions de base** et configurer les éléments suivants :

- **Nom d’utilisateur administrateur** est un compte d’utilisateur avec des autorisations d’administrateur de domaine et un membre du rôle de serveur fixe sysadmin de SQL Server sur les deux instances de SQL Server. Pour ce didacticiel, utilisez **DomainAdmin**.

- **Mot de passe** est le mot de passe pour le compte d’administrateur de domaine. Utilisez un mot de passe complexe. Confirmer le mot de passe.

- **L’abonnement** est l’abonnement Azure facturera pour exécuter toutes les ressources déployées pour le groupe de disponibilité. Vous pouvez spécifier un autre abonnement si votre compte comporte plusieurs abonnements.

- **Groupe de ressources** est le nom pour le groupe que toutes les ressources Azure créé par ce didacticiel appartient. Pour ce didacticiel, utilisez **SQL-HA-RG**. Pour plus d’informations, reportez-vous à la section (vue d’ensemble du Gestionnaire de ressources Azure) [ressource-groupe-overview.md / #-groupes de ressources].

- **Emplacement** est la région Azure où seront créés les ressources pour ce didacticiel. Sélectionnez une région Azure pour héberger l’infrastructure.

Voici l’aspect de la lame de **Notions de base** :

![Notions de base](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

- Cliquez sur **OK**.

### <a name="domain-and-network-settings"></a>Paramètres de réseau et de domaine

Ce modèle de galerie Azure crée un nouveau domaine avec nouveaux contrôleurs de domaine. Il crée également un nouveau réseau et deux sous-réseaux. Le modèle ne permet pas la création des serveurs dans un domaine existant ou d’un réseau virtuel. L’étape suivante consiste à configurer les paramètres réseau et de domaine.

Passez en revue les valeurs prédéfinies pour les paramètres de réseau et de domaine sur la lame de **domaine et les paramètres réseau** :

- **Nom de domaine racine de forêt** est le nom de domaine qui sera utilisé pour le domaine Active Directory qui hébergera le cluster. Utiliser le didacticiel de **contoso.com**.

- **Nom de réseau virtuel** est le nom de réseau pour le réseau virtuel Azure. Pour ce didacticiel, utilisez **autohaVNET**.

- **Nom de sous-réseau de contrôleur de domaine** est le nom d’une partie du réseau virtuel qui héberge le contrôleur de domaine. Pour ce didacticiel, utilisez **- 1 du sous-réseau**. Ce sous-réseau utilisera de préfixe d’adresse **10.0.0.0/24**.

- **Nom du sous-réseau de SQL Server** est le nom d’une partie du réseau virtuel que hôtes les serveurs SQL et le partage de fichiers témoin. Pour ce didacticiel, utilisez **sous-réseau-2**. Ce sous-réseau utilisera de préfixe d’adresse **10.0.1.0/26**.

Pour en savoir plus sur les réseaux virtuels dans [Azure consultez Vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md).  

Les **paramètres de réseau et de domaine** doit ressembler à ceci :

![Paramètres de réseau et de domaine](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, nous utilisons les valeurs prédéfinies.

- Passez en revue les paramètres, puis cliquez sur **OK**.

###<a name="availability-group-settings"></a>paramètres de groupe de disponibilité

Passez en revue les valeurs prédéfinies pour le groupe de disponibilité et de l’écouteur sur les **paramètres de groupe de disponibilité** .

- **Nom du groupe de disponibilité** est le nom de la ressource en cluster pour le groupe de disponibilité. Pour ce didacticiel, utilisez **Contoso-ag**.

- **nom du groupe de disponibilité écouteur** est utilisé par le cluster et l’équilibrage de charge interne. Clients se connectant à SQL Server peuvent utiliser ce nom pour se connecter à un réplica de la base de données approprié. Pour ce didacticiel, utilisez **écouteur de Contoso**.

-  **port d’écoute de disponibilité groupe** Spécifie que le port TCP le port d’écoute de SQL Server utilisera. Pour ce didacticiel, utilisez le port par défaut **1433**.

Si nécessaire, vous pouvez modifier ces valeurs. Pour ce didacticiel, utilisez les valeurs prédéfinies.  

![paramètres de groupe de disponibilité](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

- Cliquez sur **OK**.

###<a name="vm-size-storage-settings"></a>Taille de la mémoire virtuelle, les paramètres de stockage

**Taille de mémoire virtuelle, les paramètres de stockage** choisir une taille de machine virtuelle de SQL Server et vérifiez les autres paramètres.

- **Machine virtuelle de SQL Server** est la taille d’Azure VM pour les deux serveurs SQL. Choisissez une taille de machine virtuelle appropriée pour votre charge de travail. Si vous générez cet environnement pour l’utilisation de didacticiel **DS2**. Pour les charges de travail choisir une taille de machine virtuelle qui prennent en charge la charge de travail. De nombreuses charges de travail nécessitent **DS4** ou supérieure. Le modèle va générer deux machines virtuelles de cette taille et installer SQL Server sur chacun d’eux. Pour plus d’informations, reportez-vous à la section [tailles pour les machines virtuelles](virtual-machines-linux-sizes.md).

>[AZURE.NOTE]Azure va installer Enterprise Edition de SQL Server. Le coût dépend de l’édition et la taille de la machine virtuelle. Pour obtenir des informations détaillées sur les coûts actuels, consultez [tarification des machines virtuelles](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

- **Taille d’ordinateur virtuel de contrôleur de domaine** est la taille de la machine virtuelle pour les contrôleurs de domaine. Pour ce didacticiel, utilisez **D2**.

- **Machine virtuelle témoin de partage de fichiers** est la taille de la machine virtuelle pour le témoin de partage de fichiers. Pour ce didacticiel, utilisez **A1**.

- **Compte de stockage de SQL** est le nom du compte de stockage pour contenir les données de SQL Server et les disques du système d’exploitation. Pour ce didacticiel, utilisez **alwaysonsql01**.

- **Compte de stockage du contrôleur de domaine** est le nom du compte de stockage pour les contrôleurs de domaine. Pour ce didacticiel, utilisez **alwaysondc01**.

- **Taille de disque de données de SQL Server** to est la taille du disque de données SQL Server en to. Spécifiez un nombre entre 1 et 4. Il s’agit de la taille du disque de données qui sera associé à chaque de SQL Server. Pour ce didacticiel, utilisez **1**.

- **Optimisation du stockage** définit les paramètres de configuration spécifiques de stockage pour les machines virtuelles de SQL Server basés sur le type de charge de travail. Tous les serveurs SQL dans ce cas utilisez le stockage de prime avec mémoire cache disque Azure hôte en lecture seule. En outre, vous pouvez optimiser les paramètres de SQL Server pour la charge de travail en choisissant l’une de ces trois paramètres :

    - **La charge de travail générale** ne définit aucun paramètre de configuration spécifique

    - **De traitement transactionnel** définit l’indicateur de trace 1117 et 1118

    - **Entreposage de données** définit l’indicateur de trace 1117 et 610

Pour ce didacticiel, utilisez **la charge de travail générale**.

![Paramètres de stockage de taille de machine virtuelle](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

- Passez en revue les paramètres, puis cliquez sur **OK**.

####<a name="a-note-about-storage"></a>Une note à propos du stockage

D’autres optimisations dépendant de la taille des disques de données de SQL Server. Pour chaque téraoctet de disque de données, Azure ajoute un stockage de prime supplémentaire 1 To (SSD). Lorsqu’un serveur requiert de 2 To ou plus, le modèle crée un pool de stockage sur chaque SQL Server. Un pool de stockage est une forme de virtualisation du stockage où plusieurs disques sont configurés pour fournir une capacité supérieure, la résilience et les performances.  Ensuite, le modèle crée un espace de stockage du pool de stockage et cela présente comme un unique de données au système d’exploitation. Le modèle désigne ce disque comme disque de données pour SQL Server. Le modèle permet d’augmenter le pool de stockage pour SQL Server avec les paramètres suivants :

- Taille de répartition est le paramètre de l’entrelacement pour le disque virtuel. Il a la valeur 64 Ko pour les charges de travail transactionnelles. Pour les charges de travail de l’entrepôt de données, la valeur est 256 Ko.

- La résilience est simple (sans tolérance).

>[AZURE.NOTE] Stockage de prime Azure est un localement redondant et conserve trois copies des données au sein d’une seule région, capacités supplémentaires de résistance au pool de stockage n’est pas nécessaire.

- Nombre de colonnes correspond au nombre de disques dans le pool de stockage.

Pour plus d’informations sur le stockage de pools de stockage et d’espace voir :

- [Vue d’ensemble des espaces de stockage](http://technet.microsoft.com/library/hh831739.aspx).

- [Pools de stockage et de sauvegarde de Windows Server](http://technet.microsoft.com/library/dn390929.aspx)

Pour plus d’informations sur les meilleures pratiques de configuration de SQL Server, consultez les [méthodes conseillées en matière de performances de SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-performance.md)


###<a name="sql-server-settings"></a>Paramètres de SQL Server

Dans **les paramètres de SQL Server** , examiner et modifier le préfixe de nom d’ordinateur virtuel de SQL Server, version de SQL Server, compte de service SQL Server et mot de passe et l’auto SQL correction de planification de la maintenance.

- **Le préfixe du nom SQL Server** est utilisé pour créer un nom pour chaque SQL Server. Pour ce didacticiel, utilisez **Contoso-ag**. Les noms de SQL Server sera *Contoso-ag-0* et *Contoso-ag-1*.

- **Version de SQL Server** est la version de SQL Server. Pour ce didacticiel, utilisez **SQL Server 2014**. Vous pouvez également choisir de **SQL Server 2012** ou **SQL Server 2016**.

- **Nom utilisateur de compte de service SQL Server** est le nom du compte de domaine pour le service SQL Server. Pour ce didacticiel, utilisez **sqlservice**.

- **Mot de passe** est le mot de passe pour le compte de service SQL Server.  Utilisez un mot de passe complexe. Confirmer le mot de passe.

- **Planification de la maintenance automatique de SQL correction** identifie le jour de la semaine que Azure corrige automatiquement les serveurs SQL. Pour ce didacticiel, tapez **dimanche**.

- **SQL automatique application des correctifs heure de début de maintenance** est l’heure de la journée pour la région Azure lors de la correction automatique commence.

>[AZURE.NOTE]La fenêtre de gestion des correctifs pour chaque machine virtuelle est décalée d’une heure. Seulement une machine virtuelle est corrigée à la fois afin d’éviter toute interruption des services.

![Paramètres de SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Passez en revue les paramètres, puis cliquez sur **OK**.

###<a name="summary"></a>Résumé

Sur la page de résumé Azure valide les paramètres. Vous pouvez également télécharger le modèle. Passez en revue le résumé. Cliquez sur **OK**.

###<a name="buy"></a>Acheter

Cette blade finale contient des **conditions d’utilisation**et **de la vie privée**. Passez en revue ces informations. Lorsque vous êtes prêt pour Azure commencer à créer les machines virtuelles et toutes les autres ressources requises pour le groupe de disponibilité, cliquez sur **créer**.

Le portail Azure crée le groupe de ressources et toutes les ressources.

##<a name="monitor-deployment"></a>Déploiement de l’analyseur

Surveiller la progression du déploiement à partir du portail Azure. Une icône représentant le déploiement est automatiquement mis en attente dans le tableau de bord de portail Azure.

![Tableau de bord Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

##<a name="connect-to-sql-server"></a>Se connecter à SQL Server

Les nouvelles instances de SQL Server sont en cours d’exécution sur les ordinateurs virtuels qui n’ont pas de connexions à internet. Toutefois, les contrôleurs de domaine ont une connexion internet. Pour vous connecter aux serveurs SQL avec Bureau à distance, premier RDP à l’un des contrôleurs de domaine. À partir du contrôleur de domaine, ouvrez une deuxième RDP pour le SQL Server.

Le protocole RDP sur le contrôleur principal de domaine, procédez comme suit :

1.  À partir de l’Azure portail du tableau de bord très que le déploiement a réussi.

1.  Cliquez sur **ressources**.

1.  De la lame de **ressources** , cliquez sur **principal-contrôleur de domaine** qui est le nom de l’ordinateur de l’ordinateur virtuel du contrôleur de domaine principal.

1.  Sur la lame pour **ad-principal-contrôleur de domaine** , cliquez sur **se connecter**. Votre navigateur vous demande si vous souhaitez ouvrir ou enregistrer l’objet de connexion à distance. Cliquez sur **Ouvrir**.
![Se connecter au contrôleur de domaine](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/13-ad-primary-dc-connect.png)
1.  **Connexion Bureau à distance** peut vous avertir que l’éditeur de cette connexion à distance ne peut pas être identifié. Cliquez sur **se connecter**.

1.  Sécurité de Windows vous invite à entrer vos informations d’identification pour vous connecter à l’adresse IP du contrôleur principal de domaine. Cliquez sur **utiliser un autre compte**. **Nom** d’utilisateur, tapez **contoso\DomainAdmin**. C’est le compte que vous avez choisi pour le nom d’utilisateur administrateur. Utilisez le mot de passe complexe que vous avez choisi lorsque vous avez configuré le modèle.

1.  **Bureau à distance** peut vous avertir que l’ordinateur distant ne pourrait pas être authentifié en raison de problèmes dans son certificat de sécurité. Il vous affiche le nom de certificat de sécurité. Si vous avez suivi le didacticiel le nom sera **ad-principal-dc.contoso.com**. Cliquez sur **Oui**.

Vous êtes maintenant connecté au contrôleur de domaine principal. Le protocole RDP pour le SQL Server, procédez comme suit :

1.  Sur le contrôleur de domaine, ouvrez **Connexion Bureau à distance**.

1.  Pour l' **ordinateur**, tapez le nom de l’un des serveurs SQL. Pour ce didacticiel, tapez **sqlserver-0**.

1.  Utilisez le même compte d’utilisateur et le mot de passe que vous avez utilisé pour RDP au contrôleur de domaine.

Vous êtes maintenant connecté à RDP sur le SQL Server. Vous pouvez ouvrez SQL Server management studio, connectez-vous à l’instance par défaut de SQL Server et vérifiez que le groupe availabilty est configuré.


