<properties
   pageTitle="Comment marquer une machine virtuelle | Microsoft Azure"
   description="Obtenir des informations sur le balisage d’une machine virtuelle de Windows créée dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>La balise une machine virtuelle Windows Azure


Cet article décrit les différentes manières de balise une machine virtuelle Windows Azure via le modèle de déploiement du Gestionnaire de ressources. Les balises sont des paires clé/valeur définies par l’utilisateur qui peuvent être placés directement sur une ressource ou un groupe de ressources. Azure prend actuellement en charge jusqu'à 15 mots-clés par ressource et groupe de ressources. Les balises peuvent être placés sur une ressource au moment de la création ou ajoutés à une ressource existante. Notez que les balises sont prises en charge pour les ressources créées par le modèle de déploiement du Gestionnaire de ressources uniquement. Si vous souhaitez baliser une machine virtuelle Linux, voir [comment baliser une machine virtuelle de Linux dans Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marquage avec PowerShell

Pour créer, ajouter et supprimer des balises via PowerShell, vous devez tout d’abord configurer votre [environnement de PowerShell avec le Gestionnaire de ressources Azure][]. Une fois que vous avez terminé la configuration, vous pouvez placer des balises sur les ressources informatiques, réseau et stockage, lors de la création ou après avoir créé la ressource via PowerShell. Cet article se concentre sur Afficher/modifier les balises placés sur des Machines virtuelles.

Tout d’abord, accédez à un ordinateur virtuel par le biais de la `Get-AzureRmVM` applet de commande.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Si votre Machine virtuelle contient déjà des balises, vous verrez ensuite toutes les balises sur votre ressource :

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si vous souhaitez ajouter des balises par le biais de PowerShell, vous pouvez utiliser la `Set-AzureRmResource` commande. Remarque lors de la mise à jour des balises via PowerShell, balises sont mis à jour dans son ensemble. Ainsi, si vous ajoutez une balise à une ressource qui a déjà des balises, vous devez inclure tous les marqueurs que vous souhaitez placer sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource par le biais de PowerShell Cmdlets.

Cette applet de commande premier définit toutes les balises placés sur *MyTestVM* à la variable *$tags* , à l’aide de la `Get-AzureRmResource` et `Tags` propriété.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

La deuxième commande affiche les étiquettes pour la variable donnée.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

La troisième commande ajoute une balise supplémentaire à la variable *$tags* . Notez l’utilisation de la **+=** pour ajouter la nouvelle paire clé/valeur à la liste *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

La quatrième commande définit toutes les balises définies dans la variable *$tags* à la ressource donnée. Dans ce cas, il est MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

La cinquième commande affiche toutes les balises de la ressource. Comme vous pouvez le voir, *emplacement* est défini en tant que balise avec *MyLocation* comme valeur.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Pour en savoir plus sur le balisage via PowerShell, extraire les [Applets de commande de ressources Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le balisage de vos ressources d’Azure, consultez [ [Vue d’ensemble du Gestionnaire de ressources Azure][] à l’aide de balises][]et pour organiser vos ressources d’Azure.
* Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources d’Azure, consultez [Présentation de votre facture Azure][] et [intégrer votre consommation de ressources de Microsoft Azure][].

[Environnement PowerShell avec le Gestionnaire de ressources Azure]: ../powershell-azure-resource-manager.md
[Applets de commande de ressources Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Vue d’ensemble de Gestionnaire des ressources Azure]: ../azure-resource-manager/resource-group-overview.md
[À l’aide de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing/billing-understand-your-bill.md
[Intégrer votre consommation de ressources de Microsoft Azure]: ../billing-usage-rate-card-overview.md
