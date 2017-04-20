<properties
    pageTitle="Considérations de sécurité pour SQL Server dans Azure | Microsoft Azure"
    description="Cette rubrique fait référence aux ressources créés avec le modèle de déploiement classique et fournit des recommandations pour la sécurisation de SQL Server en cours d’exécution dans une Machine virtuelle Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
   editor=""    
   tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considérations de sécurité pour SQL Server sur des Machines virtuelles Azure
 
Cette rubrique inclut les consignes de sécurité générale qui aident à établir un accès sécurisé aux instances de SQL Server dans un ordinateur virtuel d’Azure. Toutefois, afin d’assurer une meilleure protection pour vos instances de base de données SQL Server dans Azure, nous vous recommandons de mettre en œuvre les pratiques de sécurité classiques sur site en plus les méthodes conseillées de sécurité pour Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Pour plus d’informations sur les pratiques de sécurité SQL Server, consultez [SQL Server 2008 R2 meilleures pratiques de sécurité - opérationnels et les tâches d’administration](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure est conforme à plusieurs réglementations du secteur et les normes qui vous permet de créer une solution compatible avec SQL Server en cours d’exécution sur une Machine virtuelle. Pour plus d’informations sur la conformité aux réglementation avec Azure, consultez le [Centre de confiance Azure](https://azure.microsoft.com/support/trust-center/).

Voici une liste de recommandations de sécurité à prendre en compte lors de la configuration et la connexion à l’instance de SQL Server dans un ordinateur virtuel d’Azure.

## <a name="considerations-for-managing-accounts"></a>Considérations pour la gestion des comptes :

- Créer un compte administrateur local unique qui n’est pas nommé **administrateur**.

- Utilisez des mots de passe forts complexes de tous vos comptes. Pour plus d’informations sur la création d’un mot de passe, reportez-vous à l’article [conseils pour la création d’un mot de passe fort](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) .

- Par défaut, Azure sélectionne l’authentification Windows lors de l’installation de la Machine virtuelle de SQL Server. Par conséquent, la connexion **SA** est désactivée et un mot de passe est attribué par le programme d’installation. Nous recommander la connexion **SA** à ne pas utilisé ou activé. Voici les stratégies de remplacement si vous souhaitez une connexion d’accès SQL :
    - Créer un compte SQL disposant d’un membre sysadmin.
    - Si vous devez utiliser une connexion d’accès **SA** , activer la connexion et renommez-le et affecter un nouveau mot de passe.
    - Les deux options qui ont été citées précédemment nécessitent une modification du mode d’authentification **SQL Server et le Mode d’authentification Windows**. Pour plus d’informations, consultez [Changement de Mode d’authentification](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considérations pour la sécurisation des connexions à des machines virtuelles Azure :

- Envisager d’utiliser un [Réseau virtuel d’Azure](../virtual-network/virtual-networks-overview.md) pour administrer les ordinateurs virtuels à des ports publics RDP.

- Utilisez un [Groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) (NSG) pour autoriser ou refuser le trafic réseau de votre ordinateur virtuel. Si vous souhaitez utiliser un NSG et un point de terminaison ACL déjà en place, d’abord supprimer le point de terminaison ACL. Pour plus d’informations sur la procédure à suivre, consultez [Gestion des Access Control Lists (ACL) pour les points de terminaison à l’aide de PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Si vous utilisez des points de terminaison, supprimer des points de terminaison sur l’ordinateur virtuel si vous ne les utilisez pas. Pour obtenir des instructions sur l’utilisation des ACL avec les points de terminaison, voir [gérer le ACL sur un point de terminaison](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Activez une option de connexion chiffrée pour une instance du moteur de base de données SQL Server dans Azure Virtual Machines. Configurez l’instance SQL server avec un certificat signé. Pour plus d’informations, voir [Activer des connexions chiffrées dans le moteur de base de données](https://msdn.microsoft.com/library/ms191192.aspx) et la [Syntaxe de chaîne de connexion](https://msdn.microsoft.com/library/ms254500.aspx).

- Si vos ordinateurs virtuels doivent être accessibles uniquement à partir d’un réseau spécifique, utilisez le pare-feu Windows pour restreindre l’accès à certaines adresses IP ou les sous-réseaux du réseau.

## <a name="next-steps"></a>Étapes suivantes

Si vous êtes également intéressé par les meilleures pratiques concernant les performances, consultez les [Méthodes conseillées en matière de performances pour SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

D’autres rubriques liées à l’exécution de SQL Server dans Azure VM, consultez [SQL Server sur la vue d’ensemble d’ordinateurs virtuels Azure](virtual-machines-windows-sql-server-iaas-overview.md).
