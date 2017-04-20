<properties
    pageTitle="Créer un compte Azure lot | Microsoft Azure"
    description="Apprenez à créer un compte Azure lot dans le portail Azure pour exécuter des charges de travail parallèles à grande échelle dans le nuage"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Créer un compte Azure lot via le portail Azure

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Gestion de lot .NET](batch-management-dotnet.md)

Apprenez à créer un compte Azure lot dans [Azure portal][azure_portal]et où trouver en important les propriétés de compte comme touches d’accès et les URL de compte. Nous abordons également le traitement par lots de tarification et la liaison d’un compte de stockage Azure à votre compte de lot afin que vous puissiez utiliser [des packages d’application](batch-application-packages.md) et la [rendre persistante la sortie de projet et la tâche](batch-task-output.md).

## <a name="create-a-batch-account"></a>Créer un compte de traitement par lots

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Nouveau** > **Calculer** > **Service de traitement par lots**.

    ![Traitement par lots sur le marché][marketplace_portal]

3. La lame du **Nouveau compte de traitement par lots** s’affiche. Reportez-vous à la section éléments *un* à *e* ci-dessous pour obtenir une description de chaque élément de la lame.

    ![Créer un compte de traitement par lots][account_portal]

    une barre d’outils. **Nom de compte**: un nom unique pour votre compte de traitement par lots. Ce nom doit être unique au sein de la région Azure, que le compte est créé (reportez-vous à la section *emplacement* ci-dessous). Il peut contenir uniquement les caractères minuscules, chiffres et doit être 3 et 24 caractères.

    b. **Abonnement**: un abonnement dans lequel vous souhaitez créer le compte de traitement par lots. Si vous n'avez qu’un seul abonnement, il est sélectionné par défaut.

    c. **Groupe de ressources**: une ressource existante de groupe pour votre nouveau compte de traitement par lots, ou éventuellement créer un autre.

    d. **Emplacement**: région un Azure dans lequel créer le compte de traitement par lots. Seules les régions pris en charge par votre abonnement et le groupe de ressources sont affichées comme des options.

    e. **Compte de stockage** (facultatif) : un compte de stockage **polyvalente** vous associez (link) vers votre nouveau compte de traitement par lots. Pour plus d’informations, consultez [stockage Azure compte](#linked-azure-storage-account) ci-dessous.

4. Cliquez sur **créer** pour créer le compte.

  Le portail indique que c’est le **déploiement** du compte et à la fin, une notification de **déploiements a réussi** s’affiche dans les *Notifications*.

## <a name="view-batch-account-properties"></a>Afficher les propriétés de compte de traitement par lots

Une fois que le compte a été créé, vous pouvez ouvrir la **lame compte de traitement par lots** pour accéder à ses propriétés et les paramètres. Vous pouvez accéder de toutes les propriétés et les paramètres de compte en utilisant le menu de gauche de la lame de compte de traitement par lots.

![Lame de compte de traitement par lots dans Azure portal][account_blade]

* **Lot compte URL**: Applications que vous créez avec le [développement de lot API](batch-technical-overview.md#batch-development-apis) ont besoin d’une URL de compte pour la gestion des ressources et d’exécuter des tâches dans le compte. Une URL de compte par lots a le format suivant :

    `https://<account_name>.<region>.batch.azure.com`

![URL de compte de traitement par lots dans le portail][account_url]

* **Touches d’accès rapide**: vos applications nécessitent également une touche d’accès lors de l’utilisation des ressources dans votre compte de traitement par lots. Pour afficher ou régénérer des touches d’accès rapide de votre compte de traitement par lots, entrez `keys` dans la zone de **recherche** de menu de gauche sur la lame de compte de traitement par lots, puis sélectionnez **les clés**.

    ![Clés de compte lot dans Azure portal][account_keys]

## <a name="pricing"></a>Tarification

Comptes de traitement par lots sont proposés uniquement dans un « niveau libre » qui signifie que vous ne sont pas facturés pour le compte du lot lui-même. Vous sont facturés pour les ressources de calcul Azure sous-jacent qui consomment de vos solutions de traitement par lots et pour les ressources consommées par d’autres services lors de l’exécutent de vos charges de travail. Par exemple, vous sont facturés pour les nœuds de calcul dans vos pools et pour les données vous stockez dans le stockage Azure sous la forme d’entrée ou de sortie pour vos tâches. De même, si vous utilisez la fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots, vous sont facturés pour les ressources de stockage Azure pour stocker vos packages de l’application. Voir [traitement par lots de tarification] [ batch_pricing] pour plus d’informations.

## <a name="linked-azure-storage-account"></a>Compte de stockage Azure lié

Comme mentionné précédemment, vous pouvez (facultativement) lier un compte de stockage **d’usage général** à votre compte de traitement par lots. La fonctionnalité [packages d’applications](batch-application-packages.md) de traitement par lots utilise le stockage blob dans un but général lié compte de stockage, comme le fait la bibliothèque [.NET de Conventions de fichier de traitement par lots](batch-task-output.md) . Fonctionnalités facultatives vous aideront à déployer les applications à que l’exécution de vos tâches de traitement par lots et conserver les données qu’elles produisent.

Lot actuellement prend en charge *uniquement* le type de compte de stockage **polyvalente** comme décrit à l’étape 5, [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Lorsque vous liez un compte de stockage Azure à votre compte de traitement par lots, être sûr de lien *uniquement* un compte de stockage **polyvalente** .

![Création d’un compte de stockage « Usage général »][storage_account]

Nous vous conseillons de créer un compte de stockage pour une utilisation exclusive par votre compte de traitement par lots.

>[AZURE.WARNING] Prenez soin lors de la régénération des touches d’accès rapide d’un compte de stockage lié. Régénérer une seule clé de compte de stockage et cliquez sur **Clés de synchronisation** sur la lame de compte de stockage liée. Attendez cinq minutes afin de permettre les touches pour se propager aux nœuds de calcul dans vos pools, puis régénérer et synchroniser l’autre clé si nécessaire. Si vous régénérez les deux touches en même temps, les nœuds de calcul ne sera pas en mesure de synchroniser une clé et qu’ils perdront l’accès au compte de stockage.

  ![Régénération des clés de compte de stockage][4]

## <a name="batch-service-quotas-and-limits"></a>Limites et quotas du service par lots

Sachez que comme avec votre abonnement Azure et autres services Azure, certains [quotas et limites](batch-quota-limit.md) s’appliquent aux comptes Batch. Quotas en cours pour un compte du lot s’affichent dans le portail dans le **Propriétés**du compte.

![Compte les quotas de traitement par lots dans Azure portal][quotas]

Gardez ces quotas à l’esprit que vous concevez et mise à l’échelle des vos charges de travail de lot. Par exemple, si votre pool n’est pas atteint le nombre de cibles de nœuds de calcul que vous avez spécifié, vous pourriez avoir atteint la limite de quota de base pour votre compte de traitement par lots.

Notez également que vous n’êtes pas limité à un seul compte de traitement par lots pour votre abonnement Azure. Vous pouvez exécuter plusieurs charges de travail de lot dans un seul compte de traitement par lots ou distribuer vos charges de travail entre des comptes de lot dans l’abonnement même, mais dans les différentes régions Azure.

La plupart de ces contingents peuvent être augmentées simplement avec une demande de support gratuit soumise dans le portail Azure. Pour plus d’informations sur la demande des augmentations de quota, reportez-vous à la section [Quotas et limites pour le service de traitement par lots d’Azure](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Autres options de gestion de compte par lots

En plus d’utiliser le portail Azure, vous pouvez également créer et gérer des comptes de lot avec les éléments suivants :

* [Traitement par lots des applets de commande PowerShell](batch-powershell-cmdlets-get-started.md)
* [CLI Azure](../xplat-cli-install.md)
* [Gestion de lot .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes

* Consultez la [vue d’ensemble de la fonctionnalité Azure lot](batch-api-basics.md) pour en savoir plus sur les fonctionnalités et les concepts de service de traitement par lots. L’article aborde les principales ressources de traitement par lots, tels que les pools, les nœuds de calcul, les tâches et les tâches et fournit une vue d’ensemble des fonctionnalités du service qui permettent l’exécution de la charge de travail informatique à grande échelle.

* Apprenez les bases du développement d’une application prenant en charge le traitement par lots à l’aide de la [bibliothèque cliente .NET de traitement par lots](batch-dotnet-get-started.md). L' [article de présentation](batch-dotnet-get-started.md) vous guide dans une application qui utilise le service de traitement par lots à exécuter une charge de travail sur plusieurs nœuds de calcul et inclut l’utilisation de stockage Azure pour la récupération et la mise en attente du fichier de charge de travail.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénération des clés de compte de stockage"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png
