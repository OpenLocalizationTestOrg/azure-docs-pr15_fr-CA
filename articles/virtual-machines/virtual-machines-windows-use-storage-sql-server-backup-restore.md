<properties
    pageTitle="Comment faire pour utiliser le stockage Azure pour la sauvegarde de SQL Server et restaurer | Microsoft Azure"
    description="Découvrez comment sauvegarder SQL Server pour le stockage Azure. Explique les avantages de la sauvegarde des bases de données SQL sur le stockage Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilisez le stockage Azure pour SQL Server sauvegarde et restauration

## <a name="overview"></a>Vue d’ensemble

À partir de SQL Server 2012 SP1 CU2, vous pouvez maintenant écrire des sauvegardes SQL Server directement au service de stockage Azure Blob. Cette fonctionnalité vous permet de sauvegarder et de restaurer à partir du service d’Azure Blob avec une base de données de SQL Server locale ou une base de données SQL Server sur une machine virtuelle Azure. Sauvegarde vers le cloud offre des avantages de la disponibilité, illimitées du stockage hors site répliqué geo et la facilité de migration des données vers et à partir du cloud. Vous pouvez émettre des instructions de sauvegarde ou de restauration à l’aide de Transact-SQL ou SMO.

SQL Server 2016 introduit de nouvelles fonctionnalités ; Vous pouvez utiliser la [sauvegarde de l’instantané de fichiers](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et le restaurations extrêmement rapides.

Cette rubrique explique pourquoi vous pouvez choisir d’utiliser le stockage Azure pour les sauvegardes SQL et puis décrit les composants impliqués. Vous pouvez utiliser les ressources fournies à la fin de cet article pour accéder aux procédures pas à pas et des informations supplémentaires à utiliser ce service avec vos sauvegardes de SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Avantages de l’utilisation du Service d’objet Blob Azure pour les sauvegardes SQL Server

Il existe plusieurs défis auxquels vous êtes confronté lors de la sauvegarde de SQL Server. Ces défis incluent la gestion du stockage, risque de défaillance de stockage, accès au stockage hors site et la configuration matérielle. La plupart de ces défis sont traitées à l’aide du service de banque d’Azure Blob pour les sauvegardes SQL Server. Considérez les avantages suivants :

- **La facilité d’utilisation**: stockage de vos sauvegardes dans le BLOB Azure peut être une pratique, flexible et facile pour accéder à une option hors site. Création d’un stockage hors site de vos sauvegardes de SQL Server peut être aussi simple que la modification de vos scripts/tâches existantes à utiliser la syntaxe de **l’URL de sauvegarde** . Stockage hors site doit être généralement assez loin de l’emplacement de base de données de production pour éviter un reprise après sinistre unique peut avoir un impact sur les emplacements de base de données hors site et de la production. En choisissant de [BLOB de géo-répliquer votre Azure](../storage/storage-redundancy.md), vous avez une couche supplémentaire de protection en cas de sinistre susceptible d’affecter la région entière.
- **Sauvegarde d’archivage**: service de stockage des objets Blob Azure l’offre une meilleure alternative à l’option bande souvent utilisées pour archiver des sauvegardes. Stockage sur bande peut nécessiter un transport physique vers une installation hors site et des mesures pour protéger le support. Stockage de vos sauvegardes dans le stockage Blob Azure fournit un instant, hautement disponible, et un durables d’archivage option.
- **Matériel de gestion**: il n’y a pas de surcharge de gestion de matériel avec services Azure. Services Azure gérer le matériel et fournissent géo-réplication de redondance et de protection contre les défaillances matérielles.
- **Stockage illimité**: en activant une sauvegarde directe sur les objets BLOB Azure, vous avez accès au stockage quasiment illimité. Également, la sauvegarde sur un disque VM Azure a le limites en fonction de la taille de l’ordinateur. Il existe une limite au nombre de disques que vous pouvez attacher à une machine virtuelle Azure pour les sauvegardes. Cette limite est de 16 disques d’une instance supplémentaire de grande taille et moins pour les instances de plus petits.
- **Disponibilité de la sauvegarde**: sauvegardes stockées dans des objets BLOB Azure sont disponibles depuis n’importe où et à tout moment et est aisément accessible pour les restaurations à un de SQL Server sur site ou à un autre de SQL Server en cours d’exécution dans une Machine de virtuelle Azure, sans le besoin d’attachement/détachement de base de données ou de télécharger et d’attacher le disque dur virtuel.
- **Coût**: payez uniquement pour le service qui est utilisé. Peut être économique sous la forme d’une option d’archivage hors site et de sauvegarde. Consultez la [Calculatrice de tarification Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculatrice de tarification de") [l’Azure prix de l’article]et(http://go.microsoft.com/fwlink/?LinkId=277059 "prix article") pour plus d’informations.
- **Snapshots de stockage**: lorsque les fichiers de base de données sont stockés dans un blob Azure et vous utilisez 2016 de SQL Server, vous pouvez utiliser la [sauvegarde d’instantané des fichiers](http://msdn.microsoft.com/library/mt169363.aspx) pour effectuer des sauvegardes quasi instantanées et le restaurations extrêmement rapides.

Pour plus d’informations, consultez [SQL Server sauvegarde et restauration avec le Service de stockage de Blob Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

Les deux sections suivantes présentent le service de stockage Azure Blob, y compris les composants SQL Server requis. Il est important de comprendre les composants et leur interaction pour utiliser la sauvegarde et restauration à partir du service de stockage Azure Blob.

## <a name="azure-blob-storage-service-components"></a>Composants de Service de stockage de Blob Azure

Les composants Azure suivants sont utilisés pour les sauvegardes sur le service de stockage Azure Blob.

| Composant               | Description                          |
|---------------------|-------------------------------|
| **Compte de stockage** | Le compte de stockage est le point de départ pour tous les services de stockage. Pour accéder à un service de stockage de Blob Azure, tout d’abord créer un compte de stockage Azure. Pour plus d’informations sur le service de stockage Azure Blob, voir [comment utiliser le Service de stockage de Blob Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Conteneur** | Un conteneur fournit un regroupement d’un ensemble d’objets BLOB et peut stocker un nombre illimité d’objets BLOB. Pour écrire un SQL Server sauvegarde à un service d’objet Blob d’Azure, vous devez disposer au moins du conteneur racine créé. |
| **Objet BLOB** | Un fichier de tout type et la taille. Objets BLOB est adressables en utilisant le format d’URL suivant : **https://[storage account].blob.core.windows.net/[container]/[blob]**. Pour plus d’informations sur les Blobs de page, voir le [bloc de description et de Blobs de Page](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Composants de SQL Server

Les composants de SQL Server suivants sont utilisés pour les sauvegardes sur le service de stockage Azure Blob.

| Composant               | Description                          |
|---------------------|-------------------------------|
| **URL** | Une URL spécifie un identificateur URI (Uniform Resource) pour un fichier de sauvegarde unique. L’URL est utilisée pour indiquer l’emplacement et le nom du fichier de sauvegarde de SQL Server. L’URL doit pointer vers un blob réel, et pas seulement d’un conteneur. Si le blob n’existe pas, il est créé. Si un blob existant n’est spécifiée, Échec de la sauvegarde, à moins que le > option avec FORMAT est spécifiée. Voici un exemple de l’URL que vous spécifiez dans la commande de sauvegarde : **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS est recommandée mais pas obligatoire. |
| **Informations d’identification** | Les informations requises pour se connecter et s’authentifier sur le service de stockage Azure Blob sont stockées sous la forme d’une information d’identification.  Afin de SQL Server écrire des sauvegardes sur une Azure Blob ou une restauration à partir de celui-ci, les informations d’identification SQL Server doivent être créée. Pour plus d’informations, reportez-vous à la section [Informations d’identification de SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Si vous choisissez de copier et de télécharger un fichier de sauvegarde au service de stockage Azure Blob, vous devez utiliser un type de blob de page comme option de stockage si vous envisagez d’utiliser ce fichier pour les opérations de restauration. RESTAURATION à partir d’un type de bloc de blob échoue avec une erreur.

## <a name="next-steps"></a>Étapes suivantes

1. Créer un compte Azure si vous n’en avez pas déjà. Si vous évaluez Azure, envisagez de [version d’évaluation gratuite](https://azure.microsoft.com/free/).

1. Suivez ensuite un des didacticiels suivants qui vous guidera dans la création d’un compte de stockage et de l’exécution d’une restauration.

    - **SQL Server 2014**: [didacticiel : SQL Server 2014 de sauvegarde et restauration pour Microsoft Azure Blob Service de stockage](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [didacticiel : à l’aide du service de stockage de Microsoft Azure Blob avec les bases de données SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Consultez la documentation supplémentaire commençant par [SQL Server sauvegarde et restauration avec le Service de stockage de Microsoft Azure Blob](https://msdn.microsoft.com/library/jj919148.aspx).

Si vous rencontrez des problèmes, consultez la rubrique [Sauvegarde de SQL Server à l’URL de meilleures pratiques et de résolution des problèmes](https://msdn.microsoft.com/library/jj919149.aspx).

Pour les autres SQL Server sauvegarde et options de restauration, voir [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
