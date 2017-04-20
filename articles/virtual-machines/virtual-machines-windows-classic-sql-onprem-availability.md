<properties
    pageTitle="Étendre sur site toujours sur les groupes de disponibilité vers Azure | Microsoft Azure"
    description="Ce didacticiel utilise les ressources créées avec le modèle classique de déploiement et décrit l’utilisation de l’Assistant Ajouter un réplica dans SQL Server Management Studio (SSMS) pour ajouter un réplica toujours sur le groupe de disponibilité dans Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>Étendre sur site toujours sur les groupes de disponibilité vers Azure

Toujours sur les groupes de disponibilité une haute disponibilité des groupes de la base de données en ajoutant des réplicas secondaires. Ces duplications permettent basculement des bases de données en cas de défaillance. Ils permettent en outre à décharger la lecture des charges de travail ou les tâches de sauvegarde.

Vous pouvez étendre des groupes de disponibilité sur site pour Microsoft Azure par la mise en service d’une ou plusieurs VM Azure avec SQL Server et les ajouter en tant que réplicas à vos groupes de disponibilité local.

Ce didacticiel suppose que vous disposez des éléments suivants :

- Un abonnement Azure. Vous pouvez [vous inscrire à une évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- Un toujours sur Disponibilité groupe existant sur site. Pour plus d’informations sur les groupes de disponibilité, consultez [Toujours sur les groupes de disponibilité](https://msdn.microsoft.com/library/hh510230.aspx).

- Connectivité entre le réseau local et votre réseau virtuel Azure. Pour plus d’informations sur la création de ce réseau virtuel, consultez [configurer une connexion VPN de Site à Site dans Azure portal classique](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="add-azure-replica-wizard"></a>Ajouter Assistant réplica Azure

Cette section vous montre comment utiliser l' **Assistant Ajout d’un réplica Azure** pour étendre votre solution toujours sur le groupe de disponibilité de réplicas Azure.

1. À partir de dans SQL Server Management Studio, développez **Toujours sur haute disponibilité** > **Groupes de disponibilité** > **[nom de votre groupe de disponibilité]**.

1. Droit des **Réplicas de disponibilité**, puis cliquez sur **Ajouter un réplica**.

1. Par défaut, **Ajoutez un réplica à l’Assistant groupe de disponibilité** s’affiche. Cliquez sur **suivant**.  Si vous avez sélectionné l’option **ne plus afficher cette page à nouveau** en bas de la page au cours d’une exécution précédente de l’Assistant, cet écran ne s’affichera pas.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)

1. Vous devrez vous connecter à tous les réplicas secondaires existantes. Vous pouvez cliquer sur **Connect...** en regard de chaque réplica, ou vous pouvez cliquer sur **Se connecter tout...** au bas de l’écran. Après l’authentification, cliquez sur **suivant** pour passer à l’écran suivant.

1. Dans la page **Spécifier les réplicas** , plusieurs onglets sont répertoriés en haut : **réplicas**, les **points de terminaison**, **Les préférences de sauvegarde**et **écouteur**. À partir de l’onglet **réplicas** , cliquez sur **Ajouter un réplica Azure...** Pour lancer l’Assistant Ajout d’un réplica Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)

1. Sélectionnez un certificat de gestion Azure existant dans le magasin de certificats local Windows si vous avez installé un autre avant. Sélectionnez ou entrez l’id d’un abonnement Azure si vous avez utilisé un autre avant. Vous pouvez cliquez sur téléchargement pour télécharger et installer un certificat de gestion Azure et télécharger la liste des abonnements à l’aide d’un compte Azure.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)

1. Vous allez renseigner chaque champ de la page avec les valeurs qui serviront à créer la Azure Machine virtuelle (VM) qui héberge le réplica.

  	|Paramètre|Description|
|---|---|
|**Image**|Sélectionnez la combinaison souhaitée de système d’exploitation et de SQL Server|
|**Taille de mémoire virtuelle**|Sélectionnez la taille de la machine virtuelle qui correspond le mieux à vos besoins professionnels|
|**Nom de la machine virtuelle**|Spécifiez un nom unique pour la nouvelle machine virtuelle. Le nom doit contenir entre 3 et 15 caractères, peut contenir uniquement des lettres, des chiffres et des traits d’union et doit commencer par une lettre et se terminer par une lettre ou un numéro.|
|**Nom d’utilisateur de machine virtuelle**|Spécifiez un nom d’utilisateur qui sera le compte administrateur sur l’ordinateur virtuel|
|**Mot de passe administrateur de machine virtuelle**|Spécifier un mot de passe pour le nouveau compte|
|**Confirmer le mot de passe**|Confirmer le mot de passe du nouveau compte|
|**Réseau virtuel**|Spécifiez le réseau virtuel Azure que la nouvelle machine virtuelle doit utiliser. Pour plus d’informations sur les réseaux virtuels, consultez [Vue d’ensemble de réseau virtuel](../virtual-network/virtual-networks-overview.md).|
|**Sous-réseau de réseau virtuel**|Spécifiez le sous-réseau de réseau virtuel que la nouvelle machine virtuelle doit utiliser.|
|**Domaine**|Confirmez que la valeur prédéfinie pour le domaine est correcte|
|**Nom d’utilisateur de domaine**|Spécifiez un compte qui est dans le groupe Administrateurs local sur les nœuds du cluster local|
|**Mot de passe**|Spécifier le mot de passe pour le nom d’utilisateur de domaine|

1. Cliquez sur **OK** pour valider les paramètres de déploiement.

1. Les termes juridiques sont affichent ensuite. Lisez et cliquez sur **OK** si vous acceptez ces termes.

1. La page **Spécifier les réplicas** s’affiche de nouveau. Vérifiez les paramètres du nouveau réplica Azure sur les onglets des **réplicas**, les **points de terminaison**et les **Préférences de sauvegarde** . Modifier les paramètres pour répondre aux besoins de votre entreprise.  Pour plus d’informations sur les paramètres contenus dans ces onglets, reportez-vous à la section [Spécifier la Page de réplicas (disponibilité Assistant Nouveau groupe Assistant/Ajouter réplica)](https://msdn.microsoft.com/library/hh213088.aspx). Notez que les écouteurs ne peut pas être créés à l’aide de l’onglet du port d’écoute pour les groupes de disponibilité qui contiennent des réplicas Azure. En outre, si un port d’écoute a déjà été créé avant le lancement de l’Assistant, vous recevrez un message indiquant qu’il n’est pas géré dans Azure. Nous allons étudier la création d’écouteurs dans la section **créer un écouteur de groupe de disponibilité** .

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)

1. Cliquez sur **suivant**.

1. Sélectionnez la méthode de synchronisation de données que vous souhaitez utiliser dans la page **Sélectionner la synchronisation initiale des données** , puis cliquez sur **suivant**. La plupart des scénarios, activez **La synchronisation complète des données**. Pour plus d’informations sur les méthodes de synchronisation de données, reportez-vous à la section [Sélectionnez données synchronisation Page initiale (toujours sur Disponibilité groupe Assistants)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Passez en revue les résultats de la **Validation** de page. Résoudre les problèmes en suspens et exécutez à nouveau le contrôle si nécessaire. Cliquez sur **suivant**.

    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)

1. Passez en revue les paramètres sur la page **Résumé** , puis cliquez sur **Terminer**.

1. Le processus de déploiement commence. Lorsque l’Assistant se termine avec succès, cliquez sur **Fermer** pour quitter l’Assistant.

>[AZURE.NOTE] L’Assistant Ajout d’un réplica Azure crée un fichier journal dans Users\User Name\AppData\Local\SQL Server\AddReplicaWizard. Ce fichier journal peut être utilisé pour résoudre les problèmes des déploiements de réplica d’Azure a échoué. Si l’Assistant échoue à l’exécution de n’importe quelle action, toutes les opérations précédentes sont annulées, y compris la suppression de la machine virtuelle mise en service.

## <a name="create-an-availability-group-listener"></a>Créer un écouteur de groupe de disponibilité

Après avoir créé le groupe de disponibilité, vous devez créer un port d’écoute pour les clients pour se connecter aux réplicas. Les écouteurs dirigent les connexions entrantes à un réplica secondaire en lecture seule ou principal. Pour plus d’informations sur les écouteurs, consultez [configurer un écouteur ILB pour toujours sur les groupes de disponibilité dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).

## <a name="next-steps"></a>Étapes suivantes

En plus à l’aide de l' **Assistant Ajout d’un réplica Azure** pour étendre votre toujours sur le groupe de disponibilité sur Azure, vous pouvez également déplacer certaines charges de travail de SQL Server complètement vers Azure. Pour commencer, consultez [une machine de virtuelle de SQL Server sur Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pour les autres rubriques relatives à l’exécution de SQL Server dans Azure VM, consultez [SQL Server Azure machines virtuelles en fonctionnement](virtual-machines-windows-sql-server-iaas-overview.md).
