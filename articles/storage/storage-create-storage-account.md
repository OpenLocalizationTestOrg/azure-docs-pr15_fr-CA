<properties
    pageTitle="Comment faire pour créer, gérer ou supprimer un compte de stockage dans le portail Azure | Microsoft Azure"
    description="Créer un nouveau compte de stockage, gérer vos clés d’accès de compte ou supprimer un compte de stockage dans le portail Azure. Obtenir des informations sur les comptes de stockage standard et premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>À propos des comptes de stockage Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d’ensemble

Un compte de stockage Azure fournit un espace de noms unique pour stocker et accéder à vos objets de données de stockage Azure. Tous les objets dans un compte de stockage sont facturés ensemble en tant que groupe. Par défaut, les données de votre compte sont disponibles uniquement pour vous, le propriétaire du compte.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Facturation de compte de stockage

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé pour vous automatiquement dans l’emplacement de déploiement si vous ne disposez pas déjà d’un compte de stockage à cet emplacement. Il n’est pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques de l’ordinateur virtuel. Le nom de compte de stockage se fondera sur le nom de l’ordinateur virtuel. Consultez la [documentation d’ordinateurs virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus de détails.

## <a name="storage-account-endpoints"></a>Points de terminaison compte de stockage

Chaque objet que vous stockez dans le stockage Azure possède une adresse URL unique. Le nom de compte de stockage constitue le sous-domaine de cette adresse. La combinaison de sous-domaine et nom de domaine, qui est spécifique à chaque service, constitue un *point de terminaison* de votre compte de stockage.

Par exemple, si votre compte de stockage est nommé *mystorageaccount*, les points de terminaison par défaut de votre compte de stockage sont :

- Service de tache : http://*mystorageaccount*. blob.core.windows.net

- Service de table : http://*mystorageaccount*. table.core.windows.net

- File d’attente de service : http://*mystorageaccount*. queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Un compte de stockage Blob expose uniquement le point de terminaison de service d’objet Blob.

L’URL permettant d’accéder à un objet dans un compte de stockage est construit en ajoutant l’emplacement de l’objet dans le compte de stockage pour le point de terminaison. Par exemple, une adresse de blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez également configurer un nom de domaine personnalisé pour votre compte de stockage. Pour les comptes de stockage classique, consultez [configurer un domaine personnalisé, le nom de votre point de terminaison du stockage Blob](storage-custom-domain-name.md) pour plus de détails. Pour les comptes de stockage du Gestionnaire de ressources, cette fonctionnalité n’a pas été ajoutée au [portail Azure](https://portal.azure.com) encore, mais vous pouvez le configurer avec PowerShell. Pour plus d’informations, reportez-vous à l’applet de commande [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de concentrateur, sélectionnez **Nouveau** -> **données + stockage** -> **compte de stockage**.

3. Entrez un nom pour votre compte de stockage. Pour plus d’informations sur l’utilisation du nom de compte de stockage pour traiter vos objets dans le stockage Azure, reportez-vous à la section [points de terminaison compte de stockage](#storage-account-endpoints) .

    > [AZURE.NOTE] Les noms de compte de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des chiffres et des lettres minuscules uniquement.
    >  
    > Le nom de votre compte de stockage doit être unique dans Azure. Le portail Azure indique si le nom de compte de stockage sélectionné est déjà en cours d’utilisation.

4. Spécifier le modèle de déploiement à utiliser : **Le Gestionnaire de ressources** ou **classique**. **Le Gestionnaire de ressources** est le modèle de déploiement recommandé. Pour plus d’informations, consultez [déploiement de présentation du Gestionnaire de ressources et de déploiement classique](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Les comptes de stockage BLOB ne peuvent être créés en utilisant le modèle de déploiement du Gestionnaire de ressources.

5. Sélectionnez le type de compte de stockage : **stockage des objets Blob**à **usage général** ou. **Objectif général** est la valeur par défaut.

    Si **l’objectif général** a été sélectionné, puis spécifiez le niveau de performances : **Standard** ou **Premium**. La valeur par défaut est **Standard**. Pour plus d’informations sur les comptes de stockage standard et haut de gamme, consultez [Introduction au stockage Azure de Microsoft](storage-introduction.md) et [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md).

    Si le **Stockage Blob** a été sélectionné, puis spécifiez le niveau d’accès : **chaude** ou **refroidir**. La valeur par défaut est **chaude**. Reportez-vous à la section [le stockage Blob Azure : refroidir et à chaud des niveaux](storage-blob-storage-tiers.md) pour plus de détails.

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS**, **RA-GRS**ou **ZRS**. La valeur par défaut est **RA-GRS**. Pour plus d’informations sur les options de réplication de stockage Azure, consultez [réplication du stockage Azure](storage-redundancy.md).

7. Sélectionnez l’abonnement dans lequel vous souhaitez créer le nouveau compte de stockage.

8. Spécifiez un groupe de ressources, ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

9. Sélectionnez l’emplacement géographique de votre compte de stockage. Pour plus d’informations sur les services sont disponibles dans la zone, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/#services) .

10. Cliquez sur **créer** pour créer le compte de stockage.

## <a name="manage-your-storage-account"></a>Gérer votre compte de stockage

### <a name="change-your-account-configuration"></a>Modifier la configuration de votre compte

Après avoir créé votre compte de stockage, vous pouvez modifier sa configuration, comme la modification de l’option de réplication utilisée pour le compte ou modifier le niveau d’accès d’un compte de stockage Blob. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **tous les paramètres** et puis cliquez sur **Configuration** pour afficher ou modifier la configuration du compte.

> [AZURE.NOTE] Selon le niveau de performances que vous avez choisi lors de la création du compte de stockage, certaines options de réplication n’est peut-être pas disponibles.

L’option de réplication vous modifiez votre prix. Pour plus d’informations, voir la page de [prix du stockage Azure](https://azure.microsoft.com/pricing/details/storage/) .

Pour les comptes de stockage Blob, modifier le niveau d’accès peut encourir les frais pour la modification, en plus de changer votre prix. Consultez les [comptes de stockage Blob - prix et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus de détails.

### <a name="manage-your-storage-access-keys"></a>Gérer vos clés d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d’accès stockage de 512 bits, qui sont utilisés pour l’authentification lors de l’accès du compte de stockage. En fournissant les deux clés d’accès de stockage, Azure permet de régénérer les clés sans interruption de votre service de stockage ou d’accès à ce service.

> [AZURE.NOTE] Nous vous recommandons de ne pas partager vos clés d’accès de stockage avec quelqu'un d’autre. Pour autoriser l’accès aux ressources de stockage sans révéler des touches d’accès rapide, vous pouvez utiliser une *signature d’accès partagé*. Une signature de l’accès partagé permet d’accéder à une ressource dans votre compte pour un intervalle que vous définissez et avec les autorisations que vous spécifiez. Pour plus d’informations, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Afficher et copier des clés d’accès de stockage

Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **tous les paramètres** et puis cliquez sur les **touches d’accès rapide** pour les afficher, de copier et de régénérer les clés d’accès de votre compte. La lame de **Touches d’accès rapide** inclut également des chaînes de connexion préconfigurés à l’aide de vos clés primaires et secondaires que vous pouvez copier pour les utiliser dans vos applications.

#### <a name="regenerate-storage-access-keys"></a>Régénérer les clés d’accès de stockage

Nous vous conseillons de modifier les touches d’accès à votre compte de stockage périodiquement de manière à préserver la sécurité de vos connexions de stockage. Deux touches d’accès rapide sont affectés afin que vous pouvez gérer les connexions pour le compte de stockage à l’aide d’une touche d’accès rapide pendant que vous régénérez l’autre touche d’accès rapide.

> [AZURE.WARNING] Régénération des touches d’accès rapide peut affecter les services Azure ainsi que vos propres applications dépendant du compte de stockage. Tous les clients qui utilisent la touche d’accès rapide pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Media services** - si vous avez des services de support qui dépendent de votre compte de stockage, vous devez resynchroniser les touches d’accès avec votre service de support après la régénération des clés.

**Applications** - si vous disposez d’applications web ou services en nuage qui utilisent le compte de stockage, vous perdrez les connexions si vous régénérez clés, sauf si vous avez restauré vos clés.

**Explorateurs de stockage** - si vous utilisez des [applications Explorateur de stockage](storage-explorers.md), vous devrez probablement mettre à jour la clé de stockage utilisée par ces applications.

Voici le processus pour les faire pivoter vos clés d’accès de stockage :

1. Mettre à jour les chaînes de connexion dans votre code d’application pour faire référence à la clé d’accès secondaire du compte de stockage.

2. Régénérer la clé d’accès principal pour votre compte de stockage. Sur la lame de **Touches d’accès rapide** , cliquez sur **Régénérer la Key1**et puis cliquez sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettre à jour les chaînes de connexion dans votre code pour faire référence à la nouvelle clé d’accès principal.

4. Régénérer la clé d’accès secondaire de la même manière.

## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

Pour supprimer un compte de stockage que vous n’utilisez plus, naviguez vers le compte de stockage [Azure portal](https://portal.azure.com), puis cliquez sur **Supprimer**. Suppression d’un compte de stockage supprime l’ensemble du compte, y compris toutes les données du compte.

> [AZURE.WARNING] Il n’est pas possible de restaurer un compte de stockage supprimé ou de récupérer le contenu qu’elle contenait avant la suppression. Veillez à sauvegarder tout ce que vous voulez enregistrer avant de supprimer le compte. Cela est également vrai pour toutes les ressources dans le compte, lorsque vous supprimez un blob, table, file d’attente ou fichier, il est définitivement supprimé.

Pour supprimer un compte de stockage qui est associé à une machine virtuelle Azure, vous devez d’abord vous assurer que les disques de l’ordinateur virtuel ont été supprimés. Si vous ne supprimez pas tout d’abord les disques de l’ordinateur virtuel, puis lorsque vous essayez de supprimer votre compte de stockage, vous verrez un message d’erreur semblable à :

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si le compte de stockage utilise le modèle de déploiement standard, vous pouvez supprimer le disque de l’ordinateur virtuel en suivant les étapes dans le [portail Azure](https://manage.windowsazure.com):

1. Accédez au [portail d’Azure classique](https://manage.windowsazure.com).
2. Accédez à l’onglet ordinateurs virtuels.
3. Cliquez sur l’onglet disques.
4. Sélectionnez le disque de données, puis cliquez sur Supprimer le disque.
5. Pour supprimer des images de disque, accédez à l’onglet Images et supprimer les images qui sont stockées dans le compte.

Pour plus d’informations, consultez la [documentation de l’ordinateur virtuel de Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Étapes suivantes

- [Le stockage Blob Azure : Niveaux de froid et de chaud](storage-blob-storage-tiers.md)
- [Réplication du stockage Azure](storage-redundancy.md)
- [Configurez les chaînes de connexion de stockage Azure](storage-configure-connection-string.md)
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- Visitez le [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/).
