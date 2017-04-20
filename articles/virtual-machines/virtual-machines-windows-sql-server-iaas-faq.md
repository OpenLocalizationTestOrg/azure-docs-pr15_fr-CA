<properties
    pageTitle="SQL Server sur des Machines virtuelles Azure FAQ | Microsoft Azure"
    description="Cet article fournit des réponses aux questions fréquemment posées à propos de SQL Server en cours d’exécution sur Azure VM."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server sur des Machines virtuelles Azure Forum aux questions

Cette rubrique fournit des réponses à certaines des questions plus fréquentes sur l’exécution de [SQL Server Azure machines virtuelles en fonctionnement](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Forum aux Questions

1. **Comment créer un ordinateur virtuel Azure avec SQL Server ?**

    Il existe deux façons de le faire. La solution la plus simple est de créer un ordinateur virtuel qui inclut SQL Server. Pour voir un didacticiel sur l’inscription pour Azure et créer une VM SQL à partir du portail, [fourniture d’une machine virtuelle de SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous avez également la possibilité de l’installation de SQL Server sur un ordinateur virtuel et la réutilisation d’une licence sur site et [Mobilité de licence par le biais de Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/)manuellement.

1. **Quelle est la différence entre les machines virtuelles de SQL et le service de base de données SQL ?**

    Du point de vue conceptuel, en cours d’exécution de SQL Server sur une machine virtuelle Azure n’est pas si différente de SQL Server en cours d’exécution dans un centre de données distant. En revanche, [SQL de base de données](../sql-database/sql-database-technical-overview.md) offre de base de données-as-a-service. La base de données SQL ne pas accès aux ordinateurs qui hébergent les bases de données. Pour une comparaison complète, consultez [Choisir un nuage option de SQL Server : le SQL Azure (PaaS) de base de données ou de SQL Server sur Azure VM (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Comment puis-je migrer les ma base de données de SQL Server en local dans le nuage ?**

    Tout d’abord créer un ordinateur virtuel Azure avec une instance de SQL Server. Migrer vos bases de données locales pour cette instance. Pour les stratégies de migration de données, voir [migrer une base de données SQL Server à SQL Server dans un ordinateur virtuel d’Azure](virtual-machines-windows-migrate-sql.md).

2. **Puis-je modifier les fonctions installées ou installer une seconde instance de SQL Server sur l’ordinateur virtuel même ?**

    Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter de nouvelles instances de SQL Server ou pour modifier d’autres fonctionnalités installées de SQL Server sur l’ordinateur.

3. **Comment passer à une nouvelle version/édition du SQL Server dans un ordinateur virtuel d’Azure ?**

    Il n’existe actuellement aucune mise à niveau de SQL Server en cours d’exécution dans un ordinateur virtuel d’Azure. Créer un ordinateur virtuel Azure avec la version de SQL Server souhaité/Édition, avant de migrer vos bases de données vers le nouveau serveur à l’aide des [techniques de migration des données](virtual-machines-windows-migrate-sql.md)standard.

4. **Comment puis-je installer ma copie sous licence de SQL Server sur un ordinateur virtuel d’Azure ?**

    Copiez le support d’installation de SQL Server à la machine virtuelle de Windows Server et puis installer SQL Server sur l’ordinateur virtuel. Pour des raisons de licences, vous devez disposer de [Mobilité de licence par le biais de Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Vous devez payer les coûts SQL d’un ordinateur virtuel s’il est utilisé uniquement pour la mise en veille et de basculement ?**

    Si vous créez la VM SQL par le biais de la galerie, puis il vous faut une licence distincte pour la VM SQL en attente et la tarification est le même. Si vous installez manuellement des SQL Server sur une machine virtuelle avec la [Mobilité de la licence](https://azure.microsoft.com/pricing/license-mobility/), vous pouvez avoir une seule instance SQL passive libre pour le basculement. Veuillez consulter les conditions et les restrictions.

6. **Comment sont les service packs et les mises à jour appliquées à un ordinateur virtuel de SQL Server ?**

    Machines virtuelles vous donnent tout contrôle sur l’ordinateur hôte, y compris quand et comment vous appliquez les mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement les mises à jour de windows, ou vous pouvez activer un service planification appelé [Automatique de correction](virtual-machines-windows-classic-sql-automated-patching.md). Automatisée des correctifs installe les mises à jour qui sont marqués comme importants, y compris les mises à jour de SQL Server de cette catégorie. Autres mises à jour facultatives à SQL Server doivent être installés manuellement.

7. **Il est possible de définir des configurations ne pas affichées dans la galerie de la machine virtuelle (par exemple Windows 2008 R2 + SQL Server 2012) ?**

    N° Pour les images de la galerie de machine virtuelle incluant SQL Server, vous devez sélectionner l’une des images fournies.

9. **Comment installer les outils de données de SQL sur mon Azure VM ?**

    Téléchargez et installez les outils de données de SQL à partir des [Outils de données Microsoft SQL Server - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Ressources

Pour une vue d’ensemble de SQL Server Azure machines virtuelles en fonctionnement, regardez vidéo [Azure VM est la meilleure plate-forme pour 2016 de SQL Server](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Vous pouvez également obtenir une bonne introduction dans la rubrique [SQL Server sur des ordinateurs virtuels Azure overview](virtual-machines-windows-sql-server-iaas-overview.md).

Autres ressources sont les suivantes :

- [Mettre en service un ordinateur virtuel de SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [La migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md)
- [Haute disponibilité et reprise après sinistre pour SQL Server sur des Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
- [Modèles d’application et les stratégies de développement de SQL Server sur des Machines virtuelles Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
