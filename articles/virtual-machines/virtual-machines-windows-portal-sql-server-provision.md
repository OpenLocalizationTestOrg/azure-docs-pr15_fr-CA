<properties
    pageTitle="Mettre en service un ordinateur virtuel de SQL Server | Microsoft Azure"
    description="Créer et vous connecter à un ordinateur virtuel de SQL Server dans Azure via le portail. Ce didacticiel utilise le mode de Gestionnaire des ressources."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Mettre en service un ordinateur virtuel de SQL Server dans le portail Azure

> [AZURE.SELECTOR]
- [Portail](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Ce didacticiel de bout en bout pour vous montre comment utiliser le portail Azure pour mettre en service un ordinateur virtuel exécutant SQL Server.

La galerie d’Azure virtual machine (VM) inclut plusieurs images qui contiennent de Microsoft SQL Server. En quelques clics, vous pouvez sélectionner l’une des images SQL VM à partir de la galerie et mettre en service dans votre environnement Azure.

Dans ce didacticiel, vous allez :

- [Sélectionnez une image SQL VM à partir de la galerie](#select-a-sql-vm-image-from-the-gallery)
- [Configurer et créer la machine virtuelle](#configure-the-vm)
- [Ouvrez la machine virtuelle avec Bureau à distance](#open-the-vm-with-remote-desktop)
- [Se connecter à SQL Server à distance](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Sélectionnez une image SQL VM à partir de la galerie

1. Connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre compte.

    >[AZURE.NOTE] Si vous ne disposez pas d’un compte Azure, visitez [Azure version d’essai gratuite](https://azure.microsoft.com/pricing/free-trial/).

1. Dans le portail Azure, cliquez sur **Nouveau**. Le portail ouvre la lame de **Nouveau** . Les ressources de la machine virtuelle de SQL Server sont dans le groupe de **Machines virtuelles** du marché.

1. De la lame de **Nouveau** , cliquez sur **ordinateurs virtuels**.

1. Pour afficher toutes les images disponibles, cliquez sur **Afficher tous** sur la lame de **Machines virtuelles** .

    ![Les Machines virtuelles Azure lame](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Sous **serveurs de base de données**, cliquez sur **SQL Server**. Vous devrez peut-être faire défiler vers le bas pour localiser les **serveurs de base de données**. Examinez les modèles disponibles de SQL Server.

    ![Virtual Machine SQL photos](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Chaque modèle identifie une version de SQL Server et un système d’exploitation. Sélectionnez une de ces images à partir de la liste. Examinez ensuite la lame de détails qui fournit une description de l’image de machine virtuelle.

    >[AZURE.NOTE] Images VM de SQL incluent les coûts de licence pour SQL Server dans la tarification par minute de la machine virtuelle que vous créez. Il existe une autre option pour mettre votre-propriétaire-licence (BYOL) et de payer uniquement pour la machine virtuelle. Les noms d’image sont préfixés par {BYOL}. Pour plus d’informations sur cette option, consultez [mise en route de SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).

1. Sous **Sélectionnez un modèle de déploiement**, vérifiez que **Le Gestionnaire de ressources** est sélectionné. Le Gestionnaire de ressources est le modèle de déploiement recommandé pour les nouvelles machines virtuelles. Cliquez sur **créer**.

    ![Créer des machines virtuelles de SQL avec le Gestionnaire de ressources](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configuration de la machine virtuelle
Il existe cinq lames permettant de configurer un ordinateur virtuel de SQL Server.

| Étape               | Description                          |
|---------------------|-------------------------------|
| **Notions de base**              | [Configurer les paramètres de base](#1-configure-basic-settings)      |
| **Taille**                | [Choisissez la taille de la machine virtuelle](#2-choose-virtual-machine-size)   |
| **Paramètres**            | [Configurer les fonctionnalités facultatives](#3-configure-optional-features)   |
| **Paramètres de SQL Server** | [Configurer les paramètres du serveur SQL](#4-configure-sql-server-settings) |
| **Résumé**             | [Passez en revue le résumé](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. configurer les paramètres de base
Sur la lame de **Notions de base** , fournissez les informations suivantes :

* Entrez une **nom**de machine virtuelle unique.
* Spécifiez un **nom d’utilisateur** pour le compte d’administrateur local sur l’ordinateur virtuel. Ce compte est également ajouté au rôle de serveur fixe **sysadmin** de SQL Server.
* Fournir un mot de **passe**.
* Si vous disposez de plusieurs abonnements, vérifiez que l’abonnement est correct pour la nouvelle machine virtuelle.
* Dans la zone de **groupe de ressources** , tapez un nom pour un nouveau groupe de ressources. Sinon, pour utiliser une ressource groupe cliquez sur **Sélectionnez existante**. Un groupe de ressources est une collection de ressources liées dans Azure (machines virtuelles, comptes de stockage, les réseaux virtuels, etc.).

    >[AZURE.NOTE] À l’aide d’un nouveau groupe de ressources est utile si vous venez de test ou en savoir plus sur les déploiements SQL Server dans Azure. Lorsque vous avez terminé votre test, supprimez le groupe de ressources pour supprimer automatiquement toutes les ressources associées à ce groupe de ressources et la machine virtuelle. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

* Sélectionnez un **emplacement** pour ce déploiement.
* Cliquez sur **OK** pour enregistrer les paramètres.

    ![Lame de bases SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Choisissez la taille de la machine virtuelle
À l’étape de **taille** , choisissez une taille de machine virtuelle dans la lame de **Choisir une taille** . La lame affiche initialement les tailles recommandées ordinateur basés sur le modèle que vous avez sélectionné. Il estime également que le coût mensuel pour exécuter la machine virtuelle.

![Options de taille de mémoire virtuelle de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Pour les charges de travail de production, nous vous recommandons de sélectionner une taille de machine virtuelle qui prend en charge le [Stockage de la prime](../storage/storage-premium-storage.md). Si vous n’exigez pas ce niveau de performances, utilisez le bouton **Afficher tout** , qui affiche toutes les options de taille de machine. Par exemple, vous pouvez utiliser une taille plus petite de la machine pour un environnement de test ou de développement.

>[AZURE.NOTE] Pour plus d’informations sur la machine virtuelle tailles voir [tailles pour les machines virtuelles](virtual-machines-windows-sizes.md). Pour les considérations sur la taille de la mémoire virtuelle de SQL Server, consultez [les procédures recommandées pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Choisissez la taille de votre ordinateur, puis cliquez sur **Sélectionner**.

## <a name="3-configure-optional-features"></a>3. configurer les fonctionnalités facultatives
Sur la lame de **paramètres** , configurez le stockage Azure, mise en réseau et la surveillance de la machine virtuelle.

- Sous **stockage**, spécifiez un **type de disque** de Standard ou Premium (SSD). Stockage de prime est recommandé pour les charges de travail.

>[AZURE.NOTE] Si vous sélectionnez une taille de l’ordinateur qui ne prend pas en charge Premium stockage Premium (SSD), la taille de votre ordinateur change automatiquement.  

- Sous **compte de stockage**, vous pouvez accepter le nom de compte de stockage automatiquement mis en service. Vous pouvez également cliquer sur **compte de stockage** pour choisir un compte existant et de configurer le type de compte de stockage. Par défaut, Azure crée un nouveau compte de stockage avec un stockage redondant localement. Pour plus d’informations sur les options de stockage, voir [réplication de stockage Azure](../storage/storage-redundancy.md).

- Sous **réseau**, vous pouvez accepter les valeurs automatiquement remplis. Vous pouvez également cliquer sur chaque fonctionnalité pour configurer manuellement le **réseau virtuel**, **sous-réseau**, **adresse IP publique**et **Groupe de sécurité réseau**. Pour les besoins de ce didacticiel, conservez les valeurs par défaut.

- Azure permet **d’analyse** par défaut avec le même compte de stockage désigné pour la machine virtuelle. Vous pouvez modifier ces paramètres.

- Sous **jeu de disponibilité**, spécifiez un jeu de disponibilité. Pour les besoins de ce didacticiel, vous pouvez sélectionner **Aucun**. Si vous envisagez de définir des groupes de disponibilité AlwaysOn de SQL, configurer la disponibilité pour éviter de recréer l’ordinateur virtuel.  Pour plus d’informations, voir [Gérer la disponibilité des ordinateurs virtuels](virtual-machines-windows-manage-availability.md).

Lorsque vous avez terminé la configuration de ces paramètres, cliquez sur **OK**.

## <a name="4-configure-sql-server-settings"></a>4. Configurez les paramètres du serveur SQL
Sur la lame de **paramètres de SQL Server** , configurer des paramètres spécifiques et optimisations pour SQL Server. Les paramètres que vous pouvez configurer pour SQL Server sont les suivantes.

| Paramètre               |
|---------------------|
| [Connectivité](#connectivity)              |
| [Authentification](#authentication)                |
| [Configuration du stockage](#storage-configuration)            |
| [Automatisée des correctifs](#automated-patching) |
| [Sauvegarde automatisée](#automated-backup)             |
| [Intégration de la chambre forte de clé Azure](#azure-key-vault-integration)             |
| [Services de R](#r-services) |

### <a name="connectivity"></a>Connectivité
Sous la **connectivité SQL**, spécifiez le type d’accès que vous souhaitez à l’instance de SQL Server sur cet ordinateur virtuel. Pour les besoins de ce didacticiel, sélectionnez **publiques (internet)** pour autoriser les connexions à SQL Server à partir d’ordinateurs ou des services sur internet. Avec cette option sélectionnée, Azure configure automatiquement le pare-feu et le groupe de sécurité de réseau pour autoriser le trafic sur le port 1433.  

![Options de connectivité SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Pour vous connecter à SQL Server via internet, vous devez également activer l’authentification SQL Server, qui est décrit dans la section suivante.

>[AZURE.NOTE] Il est possible d’ajouter plus de restrictions pour les communications réseau à votre ordinateur virtuel de SQL Server. Pour cela, en modifiant le groupe de sécurité du réseau une fois que la machine virtuelle est créée. Pour plus d’informations, consultez [ce qu’est un groupe de sécurité réseau (NSG) ?](../virtual-network/virtual-networks-nsg.md)

Si vous préférez ne pas activer les connexions au moteur de base de données via internet, choisissez l’une des options suivantes :

- **Local (à l’intérieur de la machine virtuelle uniquement)** pour autoriser les connexions à SQL Server les uniquement à partir de la machine virtuelle.
- **Privé (au sein de réseaux virtuels)** pour autoriser les connexions à SQL Server à partir d’ordinateurs ou des services sur le même réseau virtuel.

>[AZURE.NOTE] L’image de l’ordinateur virtuel pour SQL Server Express n’active pas automatiquement le protocole TCP/IP. Cela est vrai même pour les options de connectivité publics et privés. Pour l’édition Express, vous devez utiliser le Gestionnaire de Configuration SQL Server pour [activer manuellement le protocole TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) après la création de la machine virtuelle.

En général, pour améliorer la sécurité en choisissant la connectivité la plus restrictive qui permet à votre scénario. Mais toutes les options sont sécurisables via les règles du groupe de sécurité de réseau et l’authentification SQL et Windows.

**Port** par défaut 1433. Vous pouvez spécifier un numéro de port différent.
Pour plus d’informations, consultez [se connecter à un ordinateur virtuel SQL Server (Gestionnaire de ressources) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Authentification
Si vous avez besoin de l’authentification SQL Server, cliquez sur **Activer** l’authentification **SQL**.

![Authentification SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Si vous envisagez d’accéder à SQL Server via internet (c'est-à-dire, l’option de connectivité), vous devez activer l’authentification SQL ici. Accès du public à la SQL Server requiert l’utilisation de l’authentification SQL.

Si vous activez l’authentification SQL Server, spécifiez un **nom d’utilisateur** et le **mot de passe**. Ce nom d’utilisateur est configuré en tant que connexion d’authentification de SQL Server et membre du rôle serveur fixé **sysadmin** . Pour plus d’informations sur les Modes d’authentification, reportez-vous à la section [Choisir un Mode d’authentification](http://msdn.microsoft.com/library/ms144284.aspx) .

Si vous n’activez pas l’authentification SQL Server, vous pouvez utiliser le compte administrateur local sur l’ordinateur virtuel pour vous connecter à l’instance de SQL Server.

### <a name="storage-configuration"></a>Configuration du stockage
Cliquez sur **configuration du stockage** pour spécifier les conditions de stockage.

![Configuration du stockage SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Si vous sélectionnez stockage Standard, cette option n’est pas disponible. Optimisation du stockage automatique est disponible uniquement pour le stockage de la prime.

Vous pouvez spécifier des exigences comme entrées/sorties par seconde (IOPs), le débit en Mo/s et la taille de l’espace de stockage total. Configurez ces valeurs à l’aide de l’échelle coulissante. Le portail calcule automatiquement le nombre de disques en fonction de ces besoins.

Par défaut, Azure optimise le stockage pour 5000 Ops ES/s, 200 Mo et 1 To d’espace de stockage. Vous pouvez modifier ces paramètres de stockage en fonction de la charge de travail. Sous **stockage optimisé pour**, sélectionnez une des options suivantes :

- **Général** , est le paramètre par défaut et prend en charge de la plupart des charges de travail.
- Traitement de **transactionnelle** optimise le stockage pour les charges de travail OLTP traditionnels de la base de données.
- **Entreposage de données** optimise le stockage pour les charges de travail d’analyse et de création de rapports.

>[AZURE.NOTE] Les limites supérieures sur les curseurs varient en fonction de la taille de votre ordinateur virtuel sélectionné.

### <a name="automated-patching"></a>Automatisée des correctifs
**Automatisée des correctifs** est activée par défaut. La correction automatique permet d’Azure pour automatiquement des correctifs de SQL Server et le système d’exploitation. Spécifiez un jour de la semaine, heure et durée pour une fenêtre de maintenance. Azure exécute l’application des correctifs dans cette fenêtre de maintenance. La planification de la fenêtre maintenance utilise les paramètres régionaux de machine virtuelle pour le moment. Si vous ne souhaitez pas Azure pour automatiquement des correctifs de SQL Server et le système d’exploitation, cliquez sur **désactiver**.  

![SQL automatisée de correctifs](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Pour plus d’informations, reportez-vous à la section [Automatisée de correctifs pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Sauvegarde automatisée
Activer les sauvegardes automatiques de la base de données pour toutes les bases de données sous **sauvegarde automatisée**. Sauvegarde automatique est désactivée par défaut.

Lorsque vous activez la sauvegarde automatisée de SQL, vous pouvez configurer les éléments suivants :

- Période de rétention (jours) pour les sauvegardes
- Compte à utiliser pour les sauvegardes de stockage
- Option de cryptage et de mot de passe pour les sauvegardes

Pour crypter la sauvegarde, cliquez sur **Activer**. Spécifiez le **mot de passe**. Azure crée un certificat pour crypter les sauvegardes et utilise le mot de passe pour protéger le certificat.

![Sauvegarde automatisée de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Pour plus d’informations, voir [Sauvegarde automatisée de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Intégration de clé coffre-fort Azure
Pour stocker des secrets de la sécurité pour le chiffrement dans Azure, cliquez sur **intégration Azure vault clé** et cliquez sur **Activer**.

![Intégration de coffre-fort clé de SQL Azure](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

Le tableau suivant répertorie les paramètres requis pour configurer l’intégration de stockage en chambre forte de clé Azure.

|PARAMÈTRE|DESCRIPTION|EXEMPLE|
|----------|----------|-------|
|**Chambre forte de clé URL** |L’emplacement de la chambre forte de clé.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nom principal** |Azure Active Directory nom principal de service. Ce nom est également appelé ID de Client.  |fde2b411 - 33d 5-4e11-af04eb07b669ccf2|
| **Secret principal**|Azure Active Directory service principal secret. Ce mot de passe est également appelé le Secret du Client. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nom d’identification**|**Nom d’identification**: AKV intégration crée des informations d’identification au sein de SQL Server, ce qui permet de la machine virtuelle pour avoir accès à la chambre forte de clé. Choisissez un nom pour cette information d’identification.| mycred1|

Pour plus d’informations, voir [Configurer l’intégration Azure clé de coffre-fort pour SQL Server sur Azure VM](virtual-machines-windows-ps-sql-keyvault.md).

Lorsque vous avez terminé de configurer les paramètres de SQL Server, cliquez sur **OK**.

### <a name="r-services"></a>Services de R
Pour SQL Server 2016 Enterprise edition, vous avez la possibilité d’activer les [Services de R de SQL Server](https://msdn.microsoft.com/library/mt604845.aspx). Cela vous permet d’utiliser analytique avancée avec 2016 de SQL Server. Sur la lame de **Paramètres de SQL Server** , cliquez sur **Activer** .

![Activer les Services de R SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Pour les images de SQL Server qui ne sont pas 2016 Enterprise edition, l’option pour activer les Services de R est désactivée.

## <a name="5-review-the-summary"></a>5. Vérifiez le résumé
Sur la lame de **Résumé** , passez en revue le résumé et cliquez sur **OK** pour créer les ressources spécifiées pour cette machine virtuelle, groupe de ressources et de SQL Server.

Vous pouvez surveiller le déploiement à partir du portail azure. Le bouton **Notifications** en haut de l’écran indique l’état de base du déploiement.

>[AZURE.NOTE] Pour vous permettre une idée sur le temps de déploiement, j’ai déployé un VM SQL à la région des États-Unis avec les paramètres par défaut. Ce déploiement de test a un total de 26 minutes pour terminer. Mais vous pouvez rencontrer plus rapide ou plus lent temps de déploiement en fonction de votre région et les paramètres sélectionnés.

## <a name="open-the-vm-with-remote-desktop"></a>Ouvrez la machine virtuelle avec Bureau à distance

Pour vous connecter à l’ordinateur virtuel avec le Bureau à distance, utilisez les étapes suivantes :

1. Après la création de la machine virtuelle d’Azure, l’icône de la machine virtuelle apparaît sur votre tableau de bord Azure. Vous pouvez également le trouver en parcourant vos ordinateurs virtuels existants. Cliquez sur votre nouvelle machine virtuelle SQL. Une lame de **machine virtuelle** affiche les détails de votre machine virtuelle.
1. En haut de la lame **d’ordinateur virtuel** , cliquez sur **se connecter**.
1. Le navigateur télécharge un fichier RDP de la machine virtuelle. Ouvrez le fichier RDP.
    ![Bureau à distance à la machine virtuelle de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. La connexion Bureau à distance vous avertit que l’éditeur de cette connexion à distance ne peut pas être identifié. Cliquez sur **se connecter** pour continuer.
1. Dans la boîte de dialogue **Sécurité de Windows** , cliquez sur **utiliser un autre compte**.
1. Pour le type de **nom d’utilisateur** ** \<nom d’utilisateur >**, où <user name> est le nom d’utilisateur que vous avez spécifié lorsque vous avez configuré l’ordinateur virtuel. Vous devez ajouter une barre oblique inverse initiale avant le nom.
1. Entrez le **mot de passe** que vous avez précédemment configurée pour cet ordinateur virtuel, puis cliquez sur **OK** pour vous connecter.
1. Si une autre boîte de dialogue **Connexion Bureau à distance** vous demande si vous souhaitez vous connecter, cliquez sur **Oui**.

Après que vous être connecté à la machine virtuelle de SQL Server, vous pouvez lancer SQL Server Management Studio et se connecter avec l’authentification Windows à l’aide de vos informations d’identification d’administrateur local. Si vous avez activé l’authentification SQL Server, vous pouvez également vous connecter avec l’authentification SQL à l’aide de la connexion d’accès SQL et d’un mot de passe que vous avez configuré au cours de la mise en service.

Accès à l’ordinateur vous permet de vous permet de modifier directement la machine et les paramètres de SQL Server en fonction de vos besoins. Par exemple, Impossible de configurer les paramètres de pare-feu ou de modifier les paramètres de configuration de SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Se connecter à SQL Server à distance

Dans ce didacticiel, nous avons sélectionné les accès **publics** pour la machine virtuelle et **L’authentification SQL Server**. Ces paramètres configurés automatiquement l’ordinateur virtuel pour permettre les connexions SQL Server à partir de n’importe quel client sur internet (en supposant qu’ils ont le nom de connexion SQL correcte).

>[AZURE.NOTE] Si vous n’avez pas sélectionné le Public au cours de la mise en service, des étapes supplémentaires sont requises pour accéder à votre instance de SQL Server via internet. Pour plus d’informations, voir [se connecter à un ordinateur virtuel de SQL Server](virtual-machines-windows-sql-connect.md).

Les sections suivantes montrent comment se connecter à votre instance de SQL Server sur votre ordinateur virtuel à partir d’un autre ordinateur sur internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de SQL Server dans Azure, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md) et les [Questions fréquemment posées](virtual-machines-windows-sql-server-iaas-faq.md).

Pour une vidéo de présentation de SQL Server Azure machines virtuelles en fonctionnement, regardez [Azure VM est la meilleure plate-forme pour 2016 de SQL Server](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorer le chemin d’accès de la formation](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.
