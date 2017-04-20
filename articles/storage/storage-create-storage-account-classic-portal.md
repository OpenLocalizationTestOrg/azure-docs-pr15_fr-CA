<properties
    pageTitle="Comment faire pour créer, gérer ou supprimer un compte de stockage dans le portail classique Azure | Microsoft Azure"
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

Un compte de stockage Azure vous donne accès aux services Azure Blob, la file d’attente, Table et fichier dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos objets de données de stockage Azure. Par défaut, les données de votre compte sont disponibles uniquement pour vous, le propriétaire du compte.

Il existe deux types de comptes de stockage :

- Un compte de stockage standard inclut le stockage Blob, Table, file d’attente et fichier.
- Un compte de stockage prime prend actuellement en charge uniquement les disques des machines virtuelles Azure. Consultez [prime stockage : stockage hautes performances pour les charges de travail de Machine virtuelle Azure](storage-premium-storage.md) pour une vue d’ensemble approfondie du stockage de la prime.

## <a name="storage-account-billing"></a>Facturation de compte de stockage

Vous êtes facturé pour l’utilisation de stockage Azure basées sur votre compte de stockage. Les coûts de stockage sont basés sur les quatre facteurs : capacité de stockage, schéma de réplication, les opérations de stockage et sortie de données.

- Capacité de stockage fait référence à la proportion de votre unité de compte de stockage vous sont l’utilisation pour stocker des données. Le coût de stockage des données est déterminé par la quantité de données que vous stockez et comment elle est répliquée.
- Réplication détermine le nombre de copies de vos données est conservé à la fois et dans quels emplacements.
- Les transactions font référence pour tous les lire et écrire des opérations dans le stockage Azure.
- Sortie de données fait référence aux données transférées à partir d’une région d’Azure. Lorsque les données de votre compte de stockage sont accessible par une application qui n’est pas en cours d’exécution dans la même région, si cette application est un service en nuage ou tout autre type d’application, vous êtes chargé de la sortie de données. (Pour des services Azure, vous pouvez prendre des mesures pour regrouper vos données et les services dans le même centre de données pour réduire ou éliminer les frais de sortie de données.)  

La page [Tarification de stockage Azure](https://azure.microsoft.com/pricing/details/storage) fournit des informations de tarification détaillées pour une capacité de stockage, la réplication et les transactions. La page de [Détails de tarification de transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées pour la sortie de données.

Pour plus d’informations sur les cibles de performances et de capacité de compte de stockage, reportez-vous à la section [des cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md).

> [AZURE.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé pour vous automatiquement dans l’emplacement de déploiement si vous ne disposez pas déjà d’un compte de stockage à cet emplacement. Il n’est pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques de l’ordinateur virtuel. Le nom de compte de stockage se fondera sur le nom de l’ordinateur virtuel. Consultez la [documentation d’ordinateurs virtuels Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus de détails.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous à l' [Azure Portal classique](https://manage.windowsazure.com).

2. Cliquez sur **Nouveau** dans la barre des tâches en bas de la page. Choisissez **Les Services de données** | **stockage**, puis cliquez sur **Création rapide**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Dans **URL**, entrez un nom pour votre compte de stockage.

    > [AZURE.NOTE] Les noms de compte de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des chiffres et des lettres minuscules uniquement.
    >  
    > Le nom de votre compte de stockage doit être unique dans Azure. Le portail classique Azure indique si le nom de compte de stockage sélectionné est déjà utilisé.

    Voir les [points de terminaison de stockage compte](#storage-account-endpoints) ci-dessous pour plus d’informations sur l’utilisation du nom de compte de stockage pour traiter vos objets dans le stockage Azure.

4. Dans le **Groupe d’affinité des emplacements**, sélectionnez un emplacement pour votre compte de stockage qui est proche de vous ou à vos clients. Si les données de votre compte de stockage seront accessibles à partir d’un autre service d’Azure, tel qu’un Azure virtual machine ou d’un service en nuage, vous souhaiterez sélectionner un groupe d’affinité à partir de la liste à votre compte de stockage dans le même centre de données avec d’autres services Azure que vous utilisez pour améliorer les performances et réduire les coûts de groupe.

    Notez que vous devez sélectionner un groupe d’affinité lors de la création de votre compte de stockage. Vous ne pouvez pas déplacer un compte existant à un groupe d’affinité. Pour plus d’informations sur les groupes d’affinité, consultez [colocalisation de Service avec un groupe d’affinité](#service-co-location-with-an-affinity-group) ci-dessous.

    >[AZURE.IMPORTANT] Pour déterminer quels emplacements sont disponibles pour votre abonnement, vous pouvez appeler l’opération de la [liste de tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Pour répertorier les fournisseurs à partir de PowerShell, appeler [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). À partir de .NET, utilisez la méthode de la [liste](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) de la classe ProviderOperationsExtensions.
    >
    >En outre, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/#services) pour plus d’informations sur les services sont disponibles dans la zone.


5. Si vous avez plus d’un abonnement Azure, le champ **d’abonnement** s’affiche. **Abonnement**, entrez l’abonnement Azure que vous souhaitez utiliser le compte de stockage avec.

6. Dans la **réplication**, sélectionnez le niveau souhaité de réplication pour votre compte de stockage. L’option de réplication recommandée est la réplication redondante géo, qui fournit la durabilité maximale pour vos données. Pour plus d’informations sur les options de réplication de stockage Azure, consultez [réplication du stockage Azure](storage-redundancy.md).

6. Cliquez sur **créer un compte de stockage**.

    Il peut prendre quelques minutes pour créer votre compte de stockage. Pour vérifier l’état, vous pouvez surveiller les notifications au bas du portail Azure classique. Après avoir créé le compte de stockage, votre nouveau compte de stockage a l’état **en ligne** et prêt à être utilisé.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Points de terminaison compte de stockage

Chaque objet que vous stockez dans le stockage Azure possède une adresse URL unique. Le nom de compte de stockage constitue le sous-domaine de cette adresse. La combinaison de sous-domaine et nom de domaine, qui est spécifique à chaque service, constitue un *point de terminaison* de votre compte de stockage.

Par exemple, si votre compte de stockage est nommé *mystorageaccount*, les points de terminaison par défaut de votre compte de stockage sont :

- Service de tache : http://*mystorageaccount*. blob.core.windows.net

- Service de table : http://*mystorageaccount*. table.core.windows.net

- File d’attente de service : http://*mystorageaccount*. queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*. file.core.windows.net

Une fois que le compte a été créé, vous pouvez voir les points de terminaison de votre compte de stockage sur le tableau de bord de stockage dans le [Portail classique d’Azure](https://manage.windowsazure.com) .

L’URL permettant d’accéder à un objet dans un compte de stockage est construit en ajoutant l’emplacement de l’objet dans le compte de stockage pour le point de terminaison. Par exemple, une adresse de blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez également configurer un nom de domaine personnalisé pour votre compte de stockage. Pour plus d’informations, voir la rubrique [Configure un nom de domaine personnalisé pour votre point de terminaison du stockage blob](storage-custom-domain-name.md) .

### <a name="service-co-location-with-an-affinity-group"></a>Colocalisation de service avec un groupe d’affinité

Un *groupe d’affinité* est un regroupement géographique des services Azure et des machines virtuelles avec votre compte de stockage Azure. Un groupe d’affinité peut améliorer les performances de service en localisant les charges de travail ordinateur dans le même centre de données ou à proximité du public cible. Également, aucun frais de facturation ne sont encourus pour la sortie lors de l’accès aux données dans un compte de stockage à partir d’un autre service qui fait partie du même groupe d’affinité.

> [AZURE.NOTE]  Pour créer un groupe d’affinité, ouvrir la zone <b>paramètres</b> du [Portail classique d’Azure](https://manage.windowsazure.com), cliquez sur <b>groupes d’affinité</b>et puis cliquez sur <b>Ajouter un groupe d’affinité</b> ou sur le bouton <b>Ajouter</b> . Vous pouvez également créer et gérer des groupes d’affinité à l’aide de l’API de gestion de Service Azure. Pour plus d’informations, voir <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">opérations sur des groupes d’affinité</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Permet d’afficher, de copier et de régénérer les clés d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux clés d’accès stockage de 512 bits, qui sont utilisés pour l’authentification lors de l’accès du compte de stockage. En fournissant les deux clés d’accès de stockage, Azure permet de régénérer les clés sans interruption de votre service de stockage ou d’accès à ce service.

> [AZURE.NOTE] Nous vous recommandons de ne pas partager vos clés d’accès de stockage avec quelqu'un d’autre. Pour autoriser l’accès aux ressources de stockage sans révéler des touches d’accès rapide, vous pouvez utiliser une *signature d’accès partagé*. Une signature de l’accès partagé permet d’accéder à une ressource dans votre compte pour un intervalle que vous définissez et avec les autorisations que vous spécifiez. Pour plus d’informations, consultez [L’aide partagés accès Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) .

Dans le [Portail classique d’Azure](https://manage.windowsazure.com), permet de **Gérer les clés** sur le tableau de bord ou la page de **stockage** permet d’afficher, de copier et de régénérer les touches d’accès de stockage qui sont utilisés pour accéder aux services Blob, Table et file d’attente.

### <a name="copy-a-storage-access-key"></a>Copier une clé d’accès de stockage  

**Gérer les clés** vous permet de copier une clé d’accès de stockage à utiliser dans une chaîne de connexion. La chaîne de connexion nécessite le nom de compte de stockage et d’une clé à utiliser pour l’authentification. Pour plus d’informations sur la configuration des chaînes de connexion pour accéder aux services de stockage Azure, consultez [Configurer les chaînes de connexion stockage Azure](storage-configure-connection-string.md).

1. Dans le [Portail classique d’Azure](https://manage.windowsazure.com), cliquez sur **stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Gestion des clés**.

    **Gérer les touches d’accès rapide** s’ouvre.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Pour copier une clé d’accès de stockage, sélectionnez le texte de touche. Puis avec le bouton droit, puis cliquez sur **Copier**.

### <a name="regenerate-storage-access-keys"></a>Régénérer les clés d’accès de stockage
Nous vous conseillons de modifier les touches d’accès à votre compte de stockage périodiquement de manière à préserver la sécurité de vos connexions de stockage. Deux touches d’accès rapide sont affectés afin que vous pouvez gérer les connexions pour le compte de stockage à l’aide d’une touche d’accès rapide pendant que vous régénérez l’autre touche d’accès rapide.

> [AZURE.WARNING] Régénération des touches d’accès rapide peut affecter les services Azure ainsi que vos propres applications dépendant du compte de stockage. Tous les clients qui utilisent la touche d’accès rapide pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Media services** - si vous avez des services de support qui dépendent de votre compte de stockage, vous devez resynchroniser les touches d’accès avec votre service de support après la régénération des clés.

**Applications** - si vous disposez d’applications web ou services en nuage qui utilisent le compte de stockage, vous perdrez les connexions si vous régénérez clés, sauf si vous avez restauré vos clés. 

**Explorateurs de stockage** - si vous utilisez des [applications Explorateur de stockage](storage-explorers.md), vous devrez probablement mettre à jour la clé de stockage utilisée par ces applications.

Voici le processus pour les faire pivoter vos clés d’accès de stockage :

1. Mettre à jour les chaînes de connexion dans votre code d’application pour faire référence à la clé d’accès secondaire du compte de stockage.

2. Régénérer la clé d’accès principal pour votre compte de stockage. Dans le [Portail classique d’Azure](https://manage.windowsazure.com), à partir du tableau de bord ou la page de **configuration** , cliquez sur **Gestion des clés**. Cliquez sur **Régénérer** sous la clé d’accès principal, puis cliquez sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettre à jour les chaînes de connexion dans votre code pour faire référence à la nouvelle clé d’accès principal.

4. Régénérer la clé d’accès secondaire.

## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

Pour supprimer un compte de stockage que vous n’utilisez plus, cliquer sur **Supprimer** dans le tableau de bord ou de la page de **configuration** . **Supprimer** supprime le compte de stockage dans son intégralité, y compris tous les objets BLOB, les tables et les files d’attente dans le compte.

> [AZURE.WARNING] Il n’est pas possible de restaurer un compte de stockage supprimé ou de récupérer le contenu qu’elle contenait avant la suppression. Veillez à sauvegarder tout ce que vous voulez enregistrer avant de supprimer le compte. Cela est également vrai pour toutes les ressources dans le compte, lorsque vous supprimez un blob, table, file d’attente ou fichier, il est définitivement supprimé.
>
> Si votre compte de stockage contient des fichiers de disque dur virtuel pour un ordinateur virtuel Azure, vous devez supprimer les images et les disques qui utilisent les fichiers de disque dur virtuel avant de pouvoir supprimer le compte de stockage. Tout d’abord, arrêtez l’ordinateur virtuel s’exécute et supprimez-le. Pour supprimer des disques, accédez à l’onglet **disques** et supprimer tous les disques il. Pour supprimer des images, accédez à l’onglet **Images** et supprimer les images qui sont stockées dans le compte.

1. Dans le [Portail classique d’Azure](https://manage.windowsazure.com), cliquez sur **stockage**.

2. Cliquez n’importe où dans l’écriture de compte de stockage, à l’exception du nom, puis cliquez sur **Supprimer**.

     - Ou -

    Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Supprimer**.

3. Cliquez sur **Oui** pour confirmer que vous souhaitez supprimer le compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le stockage Azure, consultez la [documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visitez le [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
