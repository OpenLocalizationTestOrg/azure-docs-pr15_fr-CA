<properties
    pageTitle="Configurer l’intégration de coffre-fort de clé Azure pour SQL Server sur Azure VM (responsable de ressources)"
    description="Apprenez à automatiser la configuration de chiffrement de SQL Server pour une utilisation avec Azure clé coffre-fort. Cette rubrique explique comment utiliser Azure clé de chambre forte intégration avec les machines virtuelles de SQL Server créées avec le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
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
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurer l’intégration de coffre-fort de clé Azure pour SQL Server sur Azure VM (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
- [Classique](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Vue d’ensemble
Il existe plusieurs fonctionnalités de cryptage de SQL Server, telles que [le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), le [cryptage de niveau colonne (Effacer)](https://msdn.microsoft.com/library/ms173744.aspx)et [cryptage de la sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces formes de chiffrement vous obligent à gérer et stocker les clés de chiffrement utilisé pour le chiffrement. Le service de stockage en chambre forte de clé Azure (AKV) est conçu pour améliorer la sécurité et la gestion de ces clés dans un emplacement sécurisé et hautement disponible. Le [Connecteur de SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server d’utiliser ces clés à partir de la chambre forte de clé Azure.

Si vous exécutez SQL Server sur site avec les ordinateurs, il est la [procédure à suivre pour accéder à Azure chambre forte de clé à partir de votre ordinateur de SQL Server sur site](https://msdn.microsoft.com/library/dn198405.aspx). Mais pour de SQL Server dans Azure VM, vous pouvez gagner du temps à l’aide de la fonctionnalité *d’Intégration de stockage en chambre forte de clé Azure* .

Lorsque cette fonctionnalité est activée, elle automatiquement installe le connecteur de SQL Server configure le fournisseur EKM pour accéder Azure clé Vault et crée les informations d’identification pour pouvoir accéder à Password vault. Si vous avez regardé les étapes décrites dans la documentation sur les sites mentionnés précédemment, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. La seule chose que vous devez toujours faire manuellement est de créer le coffre-fort de clé et les clés. À partir de là, le programme d’installation complet de votre VM SQL est automatisée. Une fois que cette fonctionnalité a terminé cette installation, vous pouvez exécuter les instructions T-SQL pour crypter les sauvegardes des bases de données comme vous le feriez normalement.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>L’activation et la configuration de l’intégration de AKV
Vous pouvez activer l’intégration de AKV au cours de la mise en service ou configurez-le pour les machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Si vous sont la mise en service d’un nouvel ordinateur virtuel de SQL Server avec le Gestionnaire de ressources, le portail Azure fournit une étape pour activer l’intégration de la chambre forte de clé Azure. La fonctionnalité d’Azure clé coffre-fort est disponible uniquement pour l’entreprise, le développeur et l’évaluation éditions de SQL Server.

![Intégration de coffre-fort clé de SQL Azure](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Pour une procédure pas à pas détaillées de mise en service, consultez [fourniture d’une machine virtuelle de SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour les machines virtuelles de SQL Server existantes, sélectionnez votre ordinateur virtuel de SQL Server. Sélectionnez la section de **configuration de SQL Server** de la lame de **paramètres** .

![Intégration de AKV SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

De la lame de la **configuration de SQL Server** , cliquez sur le bouton **Modifier** dans la section Intégration de coffre-fort de clé automatique.

![Configurer l’intégration de AKV SQL pour les machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** au bas de la lame de **configuration de SQL Server** pour enregistrer vos modifications.

>[AZURE.NOTE] Vous pouvez également configurer integration AKV à l’aide d’un modèle. Pour plus d’informations, consultez [modèle quickstart Azure pour l’intégration de la chambre forte de clé Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
