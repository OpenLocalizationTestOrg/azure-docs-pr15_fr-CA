<properties
   pageTitle="Créer le nom de domaine complet d’un ordinateur virtuel dans Azure portal | Microsoft Azure"
   description="Apprenez à créer un nom de domaine complet ou nom de domaine complet pour un gestionnaire de ressources en fonction de machine virtuelle dans le portail Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal"></a>Créer un nom de domaine complet dans le portail Azure
Lorsque vous créez une machine virtuelle (VM) dans [Azure portal](https://portal.azure.com) en utilisant le modèle de déploiement du Gestionnaire de ressources, une ressource IP publique pour l’ordinateur virtuel est créée automatiquement. Cette adresse IP permet d’accéder à distance à la machine virtuelle. Bien que le portail ne crée pas un [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)ou le nom de domaine complet, par défaut, vous pouvez ajouter un fois que la machine virtuelle est créée. Cet article explique les étapes pour créer un nom DNS ou le nom de domaine complet.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Vous pouvez maintenant connecter à distance à la machine virtuelle à l’aide de ce nom DNS tel qu’avec `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre ordinateur virtuel possède un nom DNS et IP publique, vous pouvez déployer commun des infrastructures d’application ou des services tels que nginx, MongoDB, Docker, etc..

Vous pouvez également en savoir plus sur [l’utilisation du Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) pour obtenir des conseils sur la création de vos déploiements Azure.