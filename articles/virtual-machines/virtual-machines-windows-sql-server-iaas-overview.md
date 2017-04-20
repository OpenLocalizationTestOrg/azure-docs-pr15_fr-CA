<properties
    pageTitle="Vue d’ensemble de SQL Server sur des Machines virtuelles Azure | Microsoft Azure"
    description="Obtenir des informations sur l’exécution des versions complètes de SQL Server sur des machines virtuelles Azure. Obtenir des liens directs vers toutes les images de machine virtuelle de SQL Server et le contenu associé."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/19/2016"
    ms.author="jroth"/>

# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Vue d’ensemble de SQL Server sur des Machines virtuelles Azure

Cette rubrique décrit les options pour l’exécution de SQL Server sur Azure les machines virtuelles (VM), ainsi que [des liens vers les images de portail](#option-1-create-a-sql-vm-with-per-minute-licensing) et une vue d’ensemble des [tâches courantes](#manage-your-sql-vm).

>[AZURE.NOTE] Si vous êtes déjà familiarisé avec SQL Server et que vous souhaitez simplement voir comment déployer un ordinateur virtuel de SQL Server, consultez [fourniture d’une machine virtuelle de SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Vue d’ensemble
Si vous êtes un administrateur de base de données ou un développeur, Azure VM permettent de déplacer vos charges de travail sur site de SQL Server et les applications dans le nuage. La vidéo suivante fournit une vue d’ensemble technique de SQL Server Azure VM.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

La vidéo couvre les domaines suivants :

|Heure|Zone|
|---|---|
| 00:21 | Quels sont les ordinateurs virtuels d’Azure ? |
| 01:45 | Sécurité |
| 02:50 | Connectivité |
| 03:30 | Performances et la fiabilité du stockage |
| 05:20 | Tailles de machine virtuelle |
| 05:54 | Haute disponibilité et des SLA |
| 07:30 | Prise en charge de la configuration |
| 08:00 | Surveillance |
| 08:32 | Démonstration : Créer un ordinateur virtuel de SQL Server 2016 |

>[AZURE.NOTE] La vidéo est consacrée à SQL Server 2016, mais Azure fournit des images de machine virtuelle pour les versions antérieures de SQL Server, y compris de 2008, 2012, 2014 et 2016. 

## <a name="scenarios"></a>Scénarios
Il existe de nombreuses raisons que vous pouvez choisir d’héberger vos données dans Azure. Si votre application est déplacée dans Azure, il améliore les performances pour déplacer des données. Mais il existe d’autres avantages. Vous avez automatiquement accès à plusieurs centres de données pour une récupération après incident et de présence globale. Les données sont également hautement sécurisé et durable.

SQL Server exécute sur Azure VM est une option pour le stockage des données relationnelles dans Azure. Il est judicieux de plusieurs scénarios. Par exemple, vous pouvez souhaiter configurer la Azure VM de même que possible pour une machine de SQL Server sur site. Ou vous pouvez exécuter des applications et des services sur le même serveur de base de données. Il existe deux ressources principales qui peuvent vous aider à imaginer d’autres scénarios et considérations :

 - [SQL Server sur des machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fournit une vue d’ensemble des scénarios pour l’utilisation de SQL Server sur Azure VM meilleures. 
 - [Choisir un nuage option de SQL Server : base de données d’Azure, SQL (PaaS) ou de SQL Server sur Azure VM (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) fournit une comparaison détaillée entre SQL de base de données et SQL Server en cours d’exécution sur un ordinateur virtuel.

## <a name="create-a-new-sql-vm"></a>Créer une VM SQL nouveau
Les sections suivantes fournissent des liens directs vers le portail Azure pour les images de la galerie de machine virtuelle de SQL Server. En fonction de l’image que vous sélectionnez, vous pouvez soit payer pour SQL Server sur une base par minute, les coûts de gestion de licences, ou vous pouvez amener votre propre licence (BYOL).

Trouver des conseils étape par étape de ce processus dans le didacticiel, la [fourniture d’une machine virtuelle de SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Consultez également les [méthodes conseillées en matière de performances pour les machines virtuelles de SQL Server](virtual-machines-windows-sql-performance.md), qui explique comment sélectionner la taille de l’ordinateur approprié et d’autres fonctionnalités disponibles lors de la mise en service.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Option 1 : Créer une VM SQL avec le Gestionnaire de licences par minute
Le tableau suivant propose une matrice d’images de SQL Server disponibles dans la bibliothèque de la machine virtuelle. Cliquez sur un lien pour commencer à créer une VM SQL nouveau avec votre version spécifiée, une édition et un système d’exploitation.

|Version|Système d'exploitation|Édition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**LE SERVICE PACK 1 DE SQL 2014**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**2014 DE SQL**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**LE SERVICE PACK 3 DE SQL 2012**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**LE SERVICE PACK 2 DE SQL 2012**|Windows Server 2012 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**LE SERVICE PACK 2 DE SQL 2012**|Windows Server 2012|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**LE SERVICE PACK 3 DE SQL 2008 R2**|Windows Server 2008 R2|[Entreprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**LE SERVICE PACK 3 DE SQL 2008 R2**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Option 2 : Créer une VM SQL avec une licence existante
Vous pouvez également placer votre propre licence (BYOL). Dans ce scénario, vous ne payez que pour la machine virtuelle sans aucun frais supplémentaires pour les licences de SQL Server. Pour utiliser votre propre licence, utilisez la matrice de versions de SQL Server, éditions et des systèmes d’exploitation ci-dessous. Dans le portail, ces noms d’image sont précédés de **{BYOL}**.

|Version|Système d'exploitation|Édition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**Le Service Pack 1 de SQL Server 2014**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**Le Service Pack 2 de SQL Server 2012**|Windows Server 2012 R2|[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Pour utiliser des images BYOL VM, vous devez avoir et Enterprise Agreement à [Mobilité licence par le biais de Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Vous devez également une licence valide pour la version / l’édition de SQL Server à utiliser. Vous devez [fournir les informations nécessaires de la BYOL à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dans les **10** jours suivant la mise en service de votre machine virtuelle.

## <a name="manage-your-sql-vm"></a>Gérer votre machine virtuelle de SQL
Après la mise en service de votre machine virtuelle de SQL Server, il existe plusieurs tâches de gestion facultatifs. Dans de nombreux aspects, vous configurez et gérer SQL Server exactement comme vous devez être gérées par une instance de SQL Server sur site. Toutefois, certaines tâches sont spécifiques à Azure. Les sections suivantes illustrent certaines de ces zones avec des liens vers plus d’informations.

### <a name="connect-to-the-vm"></a>Se connecter à la machine virtuelle
L’une des étapes de gestion la plus basiques est pour vous connecter à votre ordinateur virtuel de SQL Server grâce à des outils, tels que SQL Server Management Studio (SSMS). Pour obtenir des instructions sur la façon de se connecter à votre ordinateur virtuel de nouveau SQL Server, voir [se connecter à un ordinateur de virtuel de SQL Server sur Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migration de vos données
Si vous avez une base de données existante, vous souhaiterez déplacer jusqu'à la VM SQL qui vient d’être mis en service. Pour une liste d’options de migration et de conseils, reportez-vous à la section [migration d’une base de données SQL Server sur un ordinateur virtuel d’Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurer la haute disponibilité
Si vous avez besoin d’une disponibilité élevée, envisagez de configurer des groupes de disponibilité de SQL Server. Cela implique plusieurs Azure ordinateurs virtuels dans un réseau virtuel. Le portail Azure a un modèle qui définit cette configuration pour vous. Pour plus d’informations, consultez [configurer un groupe de disponibilité AlwaysOn dans Azure le Gestionnaire de ressources des machines virtuelles](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si vous souhaitez configurer manuellement votre groupe de disponibilité et d’un écouteur associés, reportez-vous à la section [Configurer des groupes de disponibilité AlwaysOn dans Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Pour d’autres considérations de haute disponibilité, consultez [haute disponibilité et reprise après sinistre pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Sauvegarder vos données
Ordinateurs virtuels Azure peuvent bénéficier de [Automatique de sauvegarde](virtual-machines-windows-sql-automated-backup.md), ce qui crée régulièrement des sauvegardes de votre base de données pour le stockage des objets blob. Vous pouvez également utiliser cette technique. Pour plus d’informations, voir [Utiliser le stockage Azure pour SQL Server sauvegarde et restauration](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Pour une vue d’ensemble de toutes les options de sauvegarde et de restauration, voir [sauvegarde et restauration pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatiser les mises à jour
Azure VM permet [Application automatique des correctifs](virtual-machines-windows-sql-automated-patching.md) pour planifier une fenêtre de maintenance de l’installation de windows importants et SQL Server met automatiquement à jour.

### <a name="customer-experience-improvement-program-ceip"></a>Programme d’amélioration de l’expérience utilisateur (CEIP)
Le programme (amélioration) est activée par défaut. Cela envoie régulièrement des rapports à Microsoft afin d’améliorer SQL Server. Il n’y a aucune tâche de gestion requis avec ce programme, sauf si vous souhaitez désactiver après le déploiement. Vous pouvez personnaliser ou désactiver le CEIP en vous connectant à la machine virtuelle avec Bureau à distance. Puis, exécutez l’utilitaire **d’erreur de SQL Server et les rapports d’utilisation** . Suivez les instructions pour désactiver le rapport. 

Pour plus d’informations, reportez-vous à la section Amélioration de la rubrique [d’Accepter les termes du contrat de licence](https://msdn.microsoft.com/library/ms143343.aspx) . 

## <a name="next-steps"></a>Étapes suivantes
[Explorer le chemin d’accès de la formation](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur des machines virtuelles Azure.

Question plus ? Tout d’abord, consultez [SQL Server sur le Forum aux questions sur Azure Machines virtuelles](virtual-machines-windows-sql-server-iaas-faq.md). Mais également ajouter vos questions ou commentaires au bas de toute rubrique SQL VM pour interagir avec Microsoft et la Communauté.
