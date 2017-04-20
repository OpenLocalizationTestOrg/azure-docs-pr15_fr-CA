<properties
    pageTitle="Migrer une base de données SQL Server à SQL Server sur une machine virtuelle | Microsoft Azure"
    description="Découvrez comment migrer une base de données des utilisateurs locaux à SQL Server sur une machine virtuelle Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrer une base de données SQL Server à SQL Server dans un ordinateur virtuel d’Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Modèle de gestionnaire de ressources.


Il existe un certain nombre de méthodes pour la migration d’une base de données des utilisateurs de SQL Server sur site à SQL Server dans un ordinateur virtuel d’Azure. Cet article sera Décrivez brièvement plusieurs méthodes, recommander la meilleure méthode pour divers scénarios et inclure un [didacticiel](#azure-vm-deployment-wizard-tutorial) qui vous guide à l’aide de l’Assistant de **déploiement d’une base de données de SQL Server sur une machine virtuelle de Microsoft Azure** . 

La méthode à l’aide de l’Assistant de **déploiement d’une base de données de SQL Server sur une machine virtuelle de Microsoft Azure** décrit dans le [didacticiel](#azure-vm-deployment-wizard-tutorial) concerne uniquement le modèle de déploiement classique. 

## <a name="what-are-the-primary-migration-methods"></a>Quelles sont les méthodes de migration principal ?

Les méthodes de migration principal sont :

- Utilisez la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM
- Effectuer la sauvegarde sur site à l’aide de la compression et le copier manuellement le fichier de sauvegarde sur l’ordinateur virtuel Azure
- Effectuer une sauvegarde à l’URL et de restauration dans l’ordinateur virtuel Azure à partir de l’URL
- Et puis copiez les fichiers journaux et de données pour le stockage des objets blob Azure attachement et de détachement puis à SQL Server dans Azure VM à partir d’URL
- Convertir la machine physique des locaux pour le disque dur virtuel d’Hyper-V, télécharger vers le stockage des objets Blob Azure et déployer comme nouvelle machine virtuelle à l’aide de télécharger le disque dur virtuel
- Expédition de disque dur à l’aide du Service d’importation/exportation de Windows
- Si vous disposez d’un déploiement AlwaysOn sur site, de l' [Assistant Ajout d’un réplica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) permet de créer un réplica dans Azure et de basculement sur incident, orienter les utilisateurs vers l’instance de base de données Azure
- SQL Server, [la réplication transactionnelle](https://msdn.microsoft.com/library/ms151176.aspx) permet de configurer l’instance de SQL Server Azure en tant qu’abonné et désactivez la réplication, orienter les utilisateurs vers l’instance de base de données Azure



## <a name="choosing-your-migration-method"></a>Choix de votre méthode de migration

Pour des performances de transfert de données optimale, migration des fichiers de base de données de la machine virtuelle de Azure à l’aide d’un fichier de sauvegarde compressé est généralement la meilleure méthode. Il s’agit de la méthode utilisée par le [déploiement d’une base de données de SQL Server à un Assistant Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) . Cet Assistant est la méthode recommandée pour la migration d’un local utilisateur de base de données en cours d’exécution sur SQL Server 2005 ou version ultérieure vers SQL Server 2014 ou supérieur lorsque le fichier de sauvegarde de base de données compactée est inférieure à 1 To.

Pour minimiser les interruptions de service pendant le processus de migration de base de données, utilisez l’option AlwaysOn, ou l’option de réplication transactionnelle.

S’il n’est pas possible d’utiliser les méthodes ci-dessus, migrer manuellement votre base de données. À l’aide de cette méthode, vous allez généralement commencer avec une sauvegarde de base de données suivie d’une copie de la sauvegarde dans Azure et puis effectuer une restauration de base de données de la base de données. Vous pouvez également copier les fichiers de base de données eux-mêmes dans Azure et ensuite de les joindre. Il plusieurs méthodes qui vous pouvez d’effectuer ce processus manuel de la migration d’une base de données dans une machine virtuelle d’Azure.

> [AZURE.NOTE] Lors de la mise à niveau vers SQL Server 2014 ou 2016 de SQL Server à partir de versions antérieures de SQL Server, vous devez envisager si des modifications sont nécessaires. Il est recommandé que vous adressez toutes les dépendances sur les fonctionnalités non prises en charge par la nouvelle version de SQL Server dans le cadre de votre projet de migration. Pour plus d’informations sur les éditions prises en charge et les scénarios, consultez [mise à niveau vers SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

Le tableau suivant répertorie des méthodes de migration principal et explique quand utiliser chaque méthode est plus appropriée.

| Méthode  | Version de base de données source  |  Version de base de données de destination | Contrainte de taille de la sauvegarde de base de données source  | Notes  |
|---|---|---|---|---|
| [Utilisez la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 ou version ultérieure | SQL Server 2014 ou supérieur | < 1 to  | Méthode plus rapide et la plus simple, utilisez autant que possible de migrer vers une instance de SQL Server nouvelle ou existante dans une machine virtuelle Azure | 
| [Utilisez l’ajouter Assistant réplica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 ou supérieur | SQL Server 2012 ou supérieur | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Réduit les interruptions de service, lorsque vous avez un déploiement sur site de AlwaysOn |
| [Utilisez la réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 ou version ultérieure | SQL Server 2005 ou version ultérieure | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utiliser lorsque vous devez réduire le temps mort et n’ont pas un déploiement sur site de AlwaysOn |
| [Effectuer la sauvegarde sur site à l’aide de la compression et le copier manuellement le fichier de sauvegarde sur l’ordinateur virtuel Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 ou version ultérieure | SQL Server 2005 ou version ultérieure | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilisez uniquement lorsque vous ne pouvez pas utiliser l’Assistant, par exemple lorsque la version de base de données de destination est inférieur à SQL Server 2012 SP1 CU2 ou la taille de la sauvegarde de base de données est supérieure à 1 To (12,8 To avec 2016 de SQL Server) |
| [Effectuer une sauvegarde à l’URL et de restauration dans l’ordinateur virtuel Azure à partir de l’URL](#backup-to-url-and-restore) | SQL Server 2012 SP1 CU2 ou supérieur | SQL Server 2012 SP1 CU2 ou supérieur | < 12,8 To pour SQL Server 2016, sinon < 1 to | Généralement à l’aide de [sauvegarde à l’URL](https://msdn.microsoft.com/library/dn435916.aspx) est équivalent à l’aide de l’Assistant, les performances et pas tout à fait aussi facile |
| [Et puis copiez les fichiers journaux et de données pour le stockage des objets blob Azure attachement et de détachement puis à SQL Server dans des machines virtuelles Azure à partir d’URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 ou version ultérieure | SQL Server 2014 ou supérieur | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilisez cette méthode lorsque vous envisagez de [stocker ces fichiers à l’aide du service de stockage Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) et les joignez à SQL Server en cours d’exécution dans un ordinateur virtuel Azure, en particulier avec les bases de données très volumineuses |
| [Convertir les machine locale pour les disques durs virtuels Hyper-V pour le stockage des objets Blob Azure et de déployer un ordinateur virtuel à l’aide du disque dur virtuel chargé](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 ou version ultérieure | SQL Server 2005 ou version ultérieure | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | À utiliser lorsque vous [amener votre propre licence de SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md), lors de la migration d’une base de données que vous exécuterez sur une version antérieure de SQL Server, ou lors de la migration d’utilisateur et du système de bases de données entre eux dans le cadre de la migration de base de données dépend des autres bases de données utilisateur et/ou des bases de données système. |
| [Expédition de disque dur à l’aide du Service d’importation/exportation de Windows](#ship-hard-drive) | SQL Server 2005 ou version ultérieure | SQL Server 2005 ou version ultérieure | [Limite de stockage Azure VM](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utiliser le [Service d’importation/exportation de Windows](../storage/storage-import-export-service.md) lorsque la méthode de copie manuelle est trop lent, telles que des bases de données très volumineux |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Didacticiel Assistant de déploiement Azure VM

Utilisez l’Assistant de **déploiement d’une base de données de SQL Server sur une machine virtuelle de Microsoft Azure** dans Microsoft SQL Server Management Studio pour faire migrer un serveur SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, 2014 de SQL Server ou SQL Server 2016 utilisateur de locaux des bases de données (jusqu'à 1 To) à 2014 de SQL Server ou SQL Server 2016 sur une machine virtuelle Azure. Utilisez cet Assistant pour migrer une base de données de l’utilisateur à un ordinateur virtuel de Azure existant ou à un VM Azure avec SQL Server créé par l’Assistant au cours du processus de migration. Lorsque vous migrez une base de données vers une version plus récente de SQL Server, la base de données est automatiquement mis à niveau au cours du processus.

La méthode est pour seulement le modèle de déploiement classique. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Obtenir la dernière Version de la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM

Utiliser la dernière version de Microsoft SQL Server Management Studio pour SQL Server pour vous assurer que vous disposez de la dernière version de l’Assistant de **déploiement d’une base de données de SQL Server sur une machine virtuelle de Microsoft Azure** . La dernière version de cet Assistant intègre les dernières mises à jour sur le portail classique Azure et prend en charge les images Azure VM plus récents dans la galerie (les versions antérieures de l’Assistant peut ne pas fonctionneront). Pour obtenir la dernière version de Microsoft SQL Server Management Studio pour SQL Server, [le télécharger](http://go.microsoft.com/fwlink/?LinkId=616025) et l’installer sur un ordinateur client avec une connexion à la base de données que vous prévoyez pour la migration et à internet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Configurer l’ordinateur virtuel Azure et l’instance de SQL Server (si applicable)

Si vous migrez vers un existant Azure VM, les étapes de configuration suivantes sont requises :

- Configurer la machine virtuelle d’Azure et l’instance de SQL Server pour activer la connectivité à partir d’un autre ordinateur en suivant les étapes pour [vous connecter à l’instance SQL Server virtuelle de SSMS sur un autre ordinateur](virtual-machines-windows-sql-connect.md). Uniquement les images 2014 de SQL Server et SQL Server 2016 dans la galerie sont pris en charge si vous effectuez une migration à l’aide de l’Assistant.
- Configurer un point de terminaison ouvert pour le service SQL Server Cloud carte sur la passerelle de Microsoft Azure avec port privé de 11435. Ce port est créé dans le cadre de la mise en service de SQL Server 2014 ou 2016 de SQL Server sur une machine virtuelle de Microsoft Azure. La carte du nuage crée également une règle de pare-feu Windows pour autoriser les connexions TCP entrantes sur le port par défaut 11435. Ce point de terminaison permet à l’Assistant à utiliser le service de l’adaptateur de nuage pour copier les fichiers de sauvegarde à partir de l’instance locale de la machine virtuelle d’Azure. Pour plus d’informations, reportez-vous à la section [Carte de nuage pour SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Créer le point de terminaison de carte nuage](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Exécuter l’utilisation à la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM

1. Ouvrez Microsoft SQL Server Management Studio pour 2016 de Microsoft SQL Server et connectez-vous à l’instance de SQL Server contenant la base de données utilisateur vous migrerez vers une machine virtuelle d’Azure.
2. Avec le bouton droit de la base de données que vous migrez, pointez sur tâches et puis cliquez sur déployer pour une machine virtuelle de Microsoft Azure.

    ![Démarrer l’Assistant](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Dans la page d’Introduction, cliquez sur Suivant.
4. Dans la page Paramètres de la Source, se connecter à l’instance de SQL Server contenant la base de données que vous allez faire migrer vers une machine virtuelle d’Azure.
5. Spécifiez un emplacement temporaire pour les fichiers de sauvegarde. Si vous vous connectez à un serveur distant, vous devez spécifier un lecteur réseau.

    ![Paramètres de la source](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Cliquez sur Suivant.
7. Dans la page connexion à Microsoft Azure, cliquez sur connexion et ouvrez une session dans votre compte Azure.
8. Sélectionnez l’abonnement que vous souhaitez utiliser, puis cliquez sur Suivant.

    ![Signe dans Azure](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. Dans la page Paramètres de déploiement, vous pouvez spécifier un nouveau ou un Service Cloud existant nom et le nom de l’ordinateur virtuel :

 - Spécifiez un nouveau nom de nom de Service de Cloud et de la Machine virtuelle pour créer un nouveau Service en nuage avec un nouvel ordinateur virtuel Azure à l’aide d’une image de la galerie de 2016 de SQL Server ou de SQL Server 2014.

     - Si vous spécifiez un nouveau nom de Service en nuage, spécifiez le compte de stockage que vous souhaitez utiliser.

     - Si vous spécifiez un nom de Service Cloud existant, le compte de stockage est récupéré et saisi pour vous.

 - Spécifiez un nom de Service Cloud existant et le nouveau nom d’ordinateur virtuel pour créer une nouvelle machine virtuelle Azure dans un Service Cloud existant. Ne spécifier qu’une photo de la galerie 2014 de SQL Server ou SQL Server 2016.
 - Spécifiez un nom de Service Cloud existant et le nom de Machine virtuelle pour utiliser un ordinateur virtuel Azure. Ce doit être une image générée à l’aide d’une image de la galerie 2014 de SQL Server ou SQL Server 2016.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Cliquez sur paramètres
  - Si vous avez spécifié un nom de Service Cloud existant et le nom de la Machine virtuelle, le système vous demandera de fournir le nom d’utilisateur et le mot de passe.

        ![Paramètres machine Azure](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Si vous avez spécifié un nouveau nom de l’ordinateur virtuel, vous êtes invité à sélectionner une image à partir de la liste d’images de la galerie et fournir les informations suivantes :
      - Image – sélectionnez uniquement 2014 de SQL Server ou SQL Server 2016
        - Nom d’utilisateur
        - Nouveau mot de passe
        - Confirmer le mot de passe
        - Emplacement
        - Taille.
    - En outre, cliquez sur pour accepter le certificat généré automatiquement pour cette nouvelle Azure Machine virtuelle Microsoft, puis sur OK.

    ![Nouveaux paramètres de machine Azure](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Spécifiez le nom de la base de données cible s’il est différent du nom de la base de données source. Si la base de données cible existe déjà, le système sera automatiquement incrémenter le nom de la base de données plutôt que de remplacer la base de données existante.
12. Cliquez sur Suivant, puis sur Terminer.

    ![Résultats](./media/virtual-machines-windows-migrate-sql/results.png)

13. Lorsque l’Assistant a terminé, vous connecter à votre ordinateur virtuel et vérifiez que votre base de données a été migrée.
14. Si vous avez créé un nouvel ordinateur virtuel, vous devez configurer l’ordinateur virtuel Azure et l’instance de SQL Server en suivant les étapes pour [vous connecter à l’instance SQL Server virtuelle de SSMS sur un autre ordinateur](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Sauvegarde de fichier et le copier sur la machine virtuelle et de la restauration

Utilisez cette méthode lorsque vous ne pouvez pas utiliser la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM soit parce que vous effectuez une migration vers une version de SQL Server avant SQL Server 2014 ou votre fichier de sauvegarde est supérieur à 1 To. Si votre fichier de sauvegarde est supérieur à 1 To, vous devez le pister car la taille maximale d’un disque VM est de 1 To. Pour migrer une base de données de l’utilisateur à l’aide de cette méthode manuelle, utilisez les étapes générales suivantes :

1.  Effectuez une sauvegarde complète de la base de données à un emplacement local.
2.  Créer ou télécharger une machine virtuelle avec la version de SQL Server souhaité.
3.  L’installation en fonction de vos besoins en matière de connectivité. Voir [se connecter à un ordinateur de virtuel de SQL Server sur Azure (responsable de ressources)](virtual-machines-windows-sql-connect.md).
4.  Copiez vos fichiers de sauvegarde de votre machine virtuelle à l’aide du Bureau à distance, l’Explorateur Windows ou la commande de copie à partir d’une invite de commande.

## <a name="backup-to-url-and-restore"></a>Sauvegarde d’URL et de restauration

Utilisez la méthode de [sauvegarde pour une URL](https://msdn.microsoft.com/library/dn435916.aspx) lorsque vous ne pouvez pas utiliser la déployer une base de données de SQL Server à un Assistant Microsoft Azure VM parce que votre fichier de sauvegarde est supérieur à 1 To et que vous effectuez une migration à partir d’et vers SQL Server 2016. Pour les bases de données plus petits que 1 To ou exécutant une version de SQL Server avant de 2016 de SQL Server, l’utilisation de l’Assistant est recommandée. Avec 2016 de SQL Server, les jeux de sauvegarde par répartition sont pris en charge, sont recommandés pour des performances et requis pour dépasser les limites de taille par blob. Pour les très grandes bases de données, l’utilisation du [Service d’importation/exportation de Windows](../storage/storage-import-export-service.md) est recommandée.

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Et les copier vers les URL attachement et de détachement à partir d’URL

Utilisez cette méthode lorsque vous envisagez de [stocker ces fichiers à l’aide du service de stockage Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) et les joignez à SQL Server en cours d’exécution dans un ordinateur virtuel Azure, en particulier avec les bases de données très volumineuses. Pour migrer une base de données de l’utilisateur à l’aide de cette méthode manuelle, utilisez les étapes générales suivantes :

1.  Détacher les fichiers de base de données de l’instance de base de données sur site.
2.  Copiez les fichiers de base de données détachée dans le stockage blob Azure à l’aide de l' [utilitaire de ligne de commande de AZCopy](../storage/storage-use-azcopy.md).
3.  Joindre les fichiers de base de données à partir de l’URL d’Azure à l’instance de SQL Server dans la machine virtuelle d’Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Convertir à la machine virtuelle et de télécharger vers l’URL et de déployer en tant que nouvelle machine virtuelle

Utilisez cette méthode pour migrer toutes les bases de données système et utilisateur dans une instance de SQL Server sur site pour les machines virtuelles Azure. Utilisez les étapes générales suivantes pour migrer d’une instance de SQL Server entière à l’aide de cette méthode manuelle :

1.  Convertir des ordinateurs physiques ou virtuels pour les disques durs virtuels Hyper-V à l’aide du [Convertisseur de Machine virtuelle Microsoft](http://technet.microsoft.com/library/dn873998.aspx).
2.  Télécharger les fichiers VHD sur le stockage Azure à l’aide de l' [applet de commande Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.  Déployer une nouvelle machine virtuelle en utilisant le disque dur virtuel chargé.

> [AZURE.NOTE] Pour migrer une application entière, pensez à l’aide de la [Récupération de Site Azure](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Disque dur de livraison

La [méthode de Service d’importation/exportation de Windows](../storage/storage-import-export-service.md) permet de transférer de grandes quantités de données de fichiers vers le stockage Azure Blob dans les situations où le téléchargement via le réseau est prohibitif ou pas réalisable. Avec ce service, vous envoyez un ou plusieurs disques durs contenant ces données à un centre de données Azure, où vos données sont téléchargées vers votre compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’exécution de SQL Server Azure machines virtuelles en fonctionnement, voir [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Pour obtenir des instructions sur la création d’une Machine virtuelle Azure à SQL Server à partir d’une image capturée, reportez-vous à la section [Conseils & astuces de « clonage » des machines virtuelles de Azure SQL à partir d’images capturées](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) sur le blog les ingénieurs SQL Server CSS.
