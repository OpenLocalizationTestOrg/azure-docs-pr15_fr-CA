<properties
    pageTitle="Cryptage des Service stockage Azure pour données au repos | Microsoft Azure"
    description="Utilisez la fonctionnalité de cryptage de Service de stockage Azure pour crypter votre stockage de Blob Azure côté service lors du stockage des données et décrypter lors de la récupération des données."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Cryptage des Service stockage Azure pour données au repos

Azure Storage Service de cryptage (SSE) pour données au repos vous permet de protéger et de protéger vos données pour répondre à vos engagements de conformité et sécurité de l’organisation. Grâce à cette fonctionnalité, le stockage Azure automatiquement crypte vos données avant de la conservation, au stockage et déchiffre avant l’extraction. Le cryptage, le décryptage et la gestion des clés sont totalement transparents pour les utilisateurs.

Les sections suivantes fournissent des instructions détaillées sur la façon d’utiliser les fonctionnalités de cryptage des services de stockage ainsi que les scénarios pris en charge et l’utilisateur rencontre.

## <a name="overview"></a>Vue d’ensemble

Le stockage Azure fournit un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Données peuvent être sécurisées en transit entre une application et d’Azure à l’aide de [Cryptage côté Client](storage-client-side-encryption.md), HTTPs ou SMB 3.0. Cryptage des services de stockage fournit le cryptage au repos, gestion de cryptage, décryptage et gestion des clés de manière totalement transparente. Toutes les données sont cryptées à l’aide de 256 bits [cryptage AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), parmi les plus forts les chiffrements par bloc disponibles.

SSE fonctionne en cryptant les données lorsqu’il est écrit dans le stockage Azure et peut être utilisé pour les objets BLOB de bloc, BLOB de la page et ajoute des objets BLOB. Il fonctionne pour les éléments suivants :

-   Comptes de stockage polyvalent et de stockage Blob
-   Stockage standard et Premium 
-   Redondance de tous les niveaux (LRS, ZRS, GRS, GRS-RA)
-   Comptes de stockage Azure de gestionnaire de ressources (mais pas classique) 
-   Toutes les régions

Pour activer ou désactiver le cryptage de Service de stockage pour un compte de stockage, connectez-vous au [portail Azure](https://azure.portal.com) et sélectionnez un compte de stockage. Sur la lame de paramètres, recherchez la section Service d’objet Blob comme le montre cette capture d’écran, puis cliquez sur cryptage.

![Option de chiffrement de portail affichant de capture d’écran](./media/storage-service-encryption/image1.png)

Une fois que vous cliquez sur le paramètre de cryptage, vous pouvez activer ou désactiver le cryptage de Service de stockage.

![Portail d’écran affichant les propriétés de cryptage](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Scénarios de chiffrement

Cryptage de Service de stockage peut être activé à un niveau de compte de stockage. Il prend en charge les scénarios de client suivants :

-   Cryptage des objets BLOB de bloc, ajouter les BLOB et les objets BLOB de page.

-   Cryptage d’archivés de disques durs virtuels et de modèles portés à Azure en local.

-   Cryptage de système d’exploitation sous-jacent et de disques de données pour les machines virtuelles de IaaS créé à l’aide de vos disques durs virtuels.

SSE a les limitations suivantes :

-   Cryptage des comptes de stockage classique n’est pas pris en charge.

-   Cryptage de stockage classique comptes migrés vers les comptes de stockage de gestionnaire de ressources n’est pas pris en charge.

-   Données existantes - SSE ne crypte les données nouvellement créé après que le cryptage est activé. Si par exemple vous créez un nouveau compte de stockage du Gestionnaire de ressources, mais ne pas activer le chiffrement, puis vous charger les BLOB ou des disques durs virtuels archivés à ce compte de stockage et allumez SSE, ces objets BLOB n’est pas cryptés, sauf si elles sont réécrites ou copiés.

-   Prise en charge de l’outil statistique - activer le chiffrement des ordinateurs virtuels créés depuis le Marketplace approprié à l’aide du [portail Azure](https://portal.azure.com), PowerShell et Azure CLI. L’image de disque dur virtuel de base reste non cryptée ; Toutefois, toutes les écritures effectuées une fois que la machine virtuelle a lancé seront cryptés.

-   Table, files d’attente, et les données de fichiers ne seront pas cryptées.

##<a name="getting-started"></a>Mise en route

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Étape 1 : [créer un nouveau compte de stockage](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Étape 2 : Activer le cryptage.

Vous pouvez activer le cryptage à l’aide du [portail Azure](https://portal.azure.com).

> [AZURE.NOTE] Si vous souhaitez activer ou désactiver le cryptage de Service de stockage sur un compte de stockage par programme, vous pouvez utiliser l' [API de reste du fournisseur de ressources stockage Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx), la [Bibliothèque de Client de fournisseur de ressources de stockage pour .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md)ou la [CLI d’Azure](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Étape 3 : Copier les données de compte de stockage

Si vous activez SSE sur un compte de stockage, puis écrivez les BLOB dans ce compte de stockage, les blobs seront cryptés. Les objets BLOB que déjà se trouvant dans ce compte de stockage ne sera pas crypté jusqu'à ce qu’ils sont réécrits. Vous pouvez copier les données à partir du compte d’une stockage à une avec SSE crypté, ou encore activer SSE et copier les objets BLOB à partir d’un conteneur à un autre pour assurer que les données précédentes sont chiffrées. Vous pouvez utiliser les outils suivants pour effectuer cette opération.

#### <a name="using-azcopy"></a>À l’aide de AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour copier les données vers et depuis le stockage Blob Azure de Microsoft, fichier et Table à l’aide de commandes simples avec des performances optimales. Cela permet de copier vos objets BLOB à partir d’un compte de stockage à un autre qui a SSE activé. 

Pour en savoir plus, visitez le [transfert de données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>En utilisant les bibliothèques de Client de stockage

Vous pouvez copier des données blob vers et depuis le stockage blob ou entre des comptes de stockage à l’aide de notre ensemble de bibliothèques de Client de stockage, notamment .NET, C++, Java, Android, Node.js, PHP, Python et Ruby.

Pour en savoir plus, visitez notre [mise en route de stockage Azure Blob à l’aide de .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>À l’aide d’un Explorateur de stockage

Vous pouvez utiliser un Explorateur de stockage pour créer des comptes de stockage, télécharger et télécharger des données, afficher le contenu des objets BLOB et naviguer dans les répertoires. Vous pouvez utiliser l’une d'entre elles pour charger des objets BLOB à votre compte de stockage avec cryptage activé. Avec certains explorateurs de stockage, vous pouvez également copier des données depuis le stockage de blob existant vers un autre conteneur dans le compte de stockage ou d’un nouveau compte de stockage ayant activé de SSE.

Pour en savoir plus, visitez le site [Des explorateurs de stockage Azure](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Étape 4 : Demander l’état des données cryptées

Une version mise à jour des bibliothèques de stockage Client qui vous permet d’interroger l’état d’un objet afin de déterminer si elle est chiffrée ou non a été déployée. Exemples seront ajoutés à ce document dans un avenir proche.

En attendant, vous pouvez appeler [Obtenir les propriétés de compte](https://msdn.microsoft.com/library/azure/mt163553.aspx) pour vérifier que le compte de stockage a activé le cryptage ou afficher les propriétés de compte de stockage dans le portail Azure.

##<a name="encryption-and-decryption-workflow"></a>Chiffrement et déchiffrement de flux de travail

Voici une brève description du processus de cryptage et de décryptage :

-   Le client Active le cryptage sur le compte de stockage.

-   Lorsque le client écrit les nouvelles données (placer un Blob, placez le bloc, mettre la Page, etc.) pour le stockage Blob. chaque écriture est crypté à l’aide de 256 bits [cryptage AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), parmi les plus forts les chiffrements par bloc disponibles.

-   Lorsque le client a besoin d’accéder aux données (obtenir les Blob, etc.), les données sont automatiquement décryptées avant de retourner à l’utilisateur.

-   Si le chiffrement est désactivé, nouvelles écritures ne sont plus chiffrés et des données cryptées existantes restent cryptées tant que réécrit par l’utilisateur. Tandis que le chiffrement est activé, les écritures pour le stockage des objets Blob seront cryptés. L’état des données ne change pas avec l’utilisateur de basculer entre l’activation/désactivation du chiffrement pour le compte de stockage.

-   Toutes les clés de chiffrement sont stockées cryptées et gérés par Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Forum aux questions sur le cryptage des services de stockage des données au repos

**Q : vous avez un compte de stockage classique. Puis-je activer SSE dessus ?**

R : non, SSE est uniquement pris en charge sur les comptes de stockage de gestionnaire de ressources.

**Q : Comment puis-je crypter les données dans mon compte de stockage classique ?**

R : vous pouvez créer un nouveau compte de stockage du Gestionnaire de ressources et copier vos données à l’aide de [AzCopy](storage-use-azcopy.md) à partir de votre compte de stockage classique existant à votre compte de stockage nouvellement créé le Gestionnaire de ressources. 

Une autre option consiste à migrer votre compte de stockage classique vers un compte de gestion des ressources de stockage. Pour plus d’informations, consultez [Plate-forme prise en charge Migration des ressources IaaS de classique pour le Gestionnaire de ressources](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**Q : vous avez un compte de stockage de gestionnaire de ressources existant. Puis-je activer SSE dessus ?**

R : Oui, mais uniquement récemment écrit BLOB seront cryptés. Il ne pas revenir en arrière et chiffrer les données qui étaient déjà présentes. 

**Q : je souhaite crypter les données en cours dans un compte de stockage de gestionnaire de ressources existant ?**

R : vous pouvez activer SSE à tout moment dans un compte de gestionnaire de ressources de stockage. Toutefois, les objets BLOB qui étaient déjà présentes n’est pas cryptés. Pour crypter les objets BLOB, vous pouvez les copier sur un autre nom ou un autre conteneur et puis supprimez les versions non cryptées.

**Q : j’utilise le stockage de la prime ; puis-je utiliser des SSE ?**

R : Oui, SSE prend en charge les Standard de stockage et le stockage de la prime.

**Q : si je créer un nouveau compte de stockage et activer SSE, puis créer une nouvelle machine virtuelle à l’aide de ce compte de stockage, cela signifie-t-il que mon ordinateur virtuel est crypté ?**

R : Oui. Les disques créés qui utilisent le nouveau compte de stockage seront cryptés, dans la mesure où ils sont créés après l’activation de SSE. Si la machine virtuelle a été créée à l’aide de la Place de marché d’Azure, l’image de disque dur virtuel de base reste non cryptée ; Toutefois, toutes les écritures effectuées une fois que la machine virtuelle a lancé seront cryptés.

**Q : puis-je créer des nouveaux comptes de stockage avec SSE activé à l’aide de PowerShell d’Azure et d’Azure CLI ?**

R : Oui.

**Q : comment beaucoup plus le stockage Azure coût si SSE est activé ?**

R : il n’y a aucun coût supplémentaire.

**Q : qui gère les clés de cryptage ?**

R : les clés sont gérées par Microsoft.

**Q : puis-je utiliser mes propres clés de cryptage ?**

R : nous travaillons sur la fourniture de fonctionnalités pour les clients à mettre leurs propres clés de cryptage.

**Q : est-il possible de révoquer l’accès aux clés de cryptage ?**

A: pas pour l’instant ; les clés sont entièrement gérés par Microsoft.

**Q : est SSE activé par défaut lors de la création d’un nouveau compte de stockage ?**

R : SSE n’est pas activée par défaut ; Vous pouvez utiliser le portail Azure pour l’activer. Vous pouvez aussi activer cette fonctionnalité à l’aide de l’API REST de stockage ressource fournisseur.

**Q : comment est-elle différente de chiffrement de lecteur Azure ?**

R : cette fonctionnalité est utilisée pour crypter les données dans le stockage des objets Blob Azure. Le cryptage de disque Azure est utilisé pour crypter les disques du système d’exploitation et des données dans des machines virtuelles de IaaS. Pour plus d’informations, veuillez consulter notre [Guide de sécurité de stockage](storage-security-guide.md).

**Q : que se passe-t-il si j’activer SSE, puis aller et activer le chiffrement de disque Azure sur les disques ?**

R : il fonctionne de façon transparente. Vos données seront cryptées par les deux méthodes.

**Q : mon compte de stockage est configuré pour être répliquées géo-redondante. Si Activer SSE, est ma copie redondante également crypté ?**

R : Oui, toutes les copies du compte de stockage sont cryptées, et toutes les options de redondance – stockage localement redondants (LRS), redondants de la Zone de stockage (ZRS), géo-redondants stockage (GRS) et stockage de géo-redondants d’un accès en lecture (RA-GRS) – sont pris en charge.

**Q : je ne peux pas activer le cryptage sur mon compte de stockage.**

R : il est un compte de gestionnaire de ressources de stockage ? Comptes de stockage classique ne sont pas pris en charge. 

**Q : SSE n’est autorisé que dans des régions spécifiques ?**

R : le SSE est disponible dans toutes les régions. 

**Q : Comment puis-je contacter quelqu'un si j’ont des problèmes ou que vous souhaitez fournir des commentaires ?**

R : contactez [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) pour tous les problèmes relatifs au cryptage de Service de stockage.

##<a name="next-steps"></a>Étapes suivantes

Le stockage Azure fournit un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez le [Guide de sécurité de stockage](storage-security-guide.md).
