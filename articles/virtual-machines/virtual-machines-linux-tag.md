<properties
   pageTitle="Comment marquer une machine virtuelle Linux | Microsoft Azure"
   description="Obtenir des informations sur le balisage d’une machine virtuelle de Linux créée dans Azure en utilisant le modèle de déploiement du Gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Comment marquer une machine virtuelle Linux dans Azure

Cet article décrit les différentes façons de marquer une machine virtuelle Linux dans Azure via le modèle de déploiement du Gestionnaire de ressources. Les balises sont des paires clé/valeur définies par l’utilisateur qui peuvent être placés directement sur une ressource ou un groupe de ressources. Azure prend actuellement en charge jusqu'à 15 mots-clés par ressource et groupe de ressources. Les balises peuvent être placés sur une ressource au moment de la création ou ajoutés à une ressource existante. Veuillez noter que les balises sont pris en charge pour les ressources créées par le modèle de déploiement du Gestionnaire de ressources uniquement.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marquage avec l’interface CLI Azure

Pour commencer, [installez et configurez l’infrastructure du langage commun Azure](../xplat-cli-azure-resource-manager.md) et assurez-vous que vous êtes en mode de Gestionnaire des ressources (`azure config mode arm`).

Vous pouvez afficher toutes les propriétés pour une donnée Machine virtuelle, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyTestVM

Pour ajouter une nouvelle balise de machine virtuelle par le biais de l’interface CLI d’Azure, vous pouvez utiliser la `azure vm set` commande avec le paramètre de balise **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **– T** dans le `azure vm set` commande.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Maintenant que nous avons appliqué des balises à nos ressources Azure CLI et le portail, examinons les détails de l’utilisation de consulter les balises dans le portail de facturation.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le balisage de vos ressources d’Azure, consultez [ [Vue d’ensemble du Gestionnaire de ressources Azure][] à l’aide de balises][]et pour organiser vos ressources d’Azure.
* Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources d’Azure, consultez [Présentation de votre facture Azure][] et [intégrer votre consommation de ressources de Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Vue d’ensemble de Gestionnaire des ressources Azure]: ../azure-resource-manager/resource-group-overview.md
[À l’aide de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing/billing-understand-your-bill.md
[Intégrer votre consommation de ressources de Microsoft Azure]: ../billing-usage-rate-card-overview.md
