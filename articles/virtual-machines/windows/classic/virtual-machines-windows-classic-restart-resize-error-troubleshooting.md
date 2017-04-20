<properties
   pageTitle="VM, redémarrage ou redimensionnement | Microsoft Azure"
   description="Résoudre les problèmes de déploiement classique avec redémarrage ou le redimensionnement d’un ordinateur virtuel de Windows existant dans Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-windows"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="required"
   ms.date="09/20/2016"
   ms.devlang="na"
   ms.author="delhan"/>

# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement classique avec redémarrage ou le redimensionnement d’un ordinateur virtuel de Windows existant dans Azure

> [AZURE.SELECTOR]
- [Classique](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
- [Gestionnaire de ressources](../../virtual-machines-windows-restart-resize-error-troubleshooting.md)

Lorsque vous essayez de démarrer un ordinateur virtuel d’Azure arrêté (VM), ou redimensionner une VM Azure existante, l’erreur courant que vous rencontrez est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région ne dispose pas de ressources disponibles ou Impossible de prendre en charge la taille de mémoire virtuelle demandée.
> [AZURE.IMPORTANT] Azure dispose de deux modèles de déploiement différentes pour la création et l’utilisation des ressources : [le Gestionnaire de ressources et classique](../../../resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement classique. Microsoft recommande que la plupart des nouveaux déploiements utilisent le modèle de gestionnaire de ressources.

[AZURE.INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit pour identifier l’erreur associée à ce problème.

Dans le portail Azure, cliquez sur **Parcourir** > **machines virtuelles** > _votre machine Windows_ > **paramètres** > **les journaux d’Audit**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : Erreur lors du démarrage d’un ordinateur virtuel arrêté

Vous essayez de démarrer un ordinateur virtuel arrêté, mais obtenir un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour démarrer la machine virtuelle arrêtée doit être tentée sur le cluster d’origine qui héberge le service cloud. Cependant, le cluster n’a pas d’espace libre disponible pour répondre à la demande.

### <a name="resolution"></a>Résolution

* Créer un nouveau service de cloud et l’associer soit une région ou un réseau virtuel en fonction de la région, mais pas un groupe d’affinité.

* Supprimer la machine virtuelle arrêtée.

* Recréer la machine virtuelle dans le nouveau service cloud à l’aide de disques.

* Démarrer la machine virtuelle recréée.

Si vous obtenez une erreur lorsque vous tentez de créer un nouveau service en nuage, réessayer ultérieurement, ou modifier la région pour le service en nuage.

> [AZURE.IMPORTANT] Le nouveau service de cloud aura un nouveau nom et l’adresse IP virtuelle, vous devez modifier ces informations pour toutes les dépendances qui utilisent ces informations pour le service cloud existant.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : Erreur lors du redimensionnement d’un ordinateur virtuel existant

Vous essayez de redimensionner un ordinateur virtuel existant, mais obtenir un échec d’allocation.

### <a name="cause"></a>Cause

La demande de redimensionnement de la machine virtuelle doit être tentée sur le cluster d’origine qui héberge le service cloud. Cependant, le cluster ne gère pas la taille de mémoire virtuelle demandée.

### <a name="resolution"></a>Résolution

Réduire la taille de mémoire virtuelle demandée, puis relancez la requête de redimensionnement.

* Cliquez sur **Parcourir tous les** > **(classique) des machines virtuelles** > _votre machine virtuelle_ > **paramètres** > **taille**. Pour obtenir la procédure détaillée, voir [redimensionner l’ordinateur virtuel](https://msdn.microsoft.com/library/dn168976.aspx).

Si elle n’est pas possible de réduire la taille de la mémoire virtuelle, procédez comme suit :

  * Créer un nouveau service en nuage, s’assurer qu’il est pas lié à un groupe d’affinité et non associé à un réseau virtuel qui est lié à un groupe d’affinité.

  * Créer une VM nouvelle et plus grande taille, qu’il contient.

Vous pouvez consolider vos ordinateurs virtuels dans le même service de cloud. Si votre service cloud existant est associé à un réseau virtuel en fonction de la région, vous pouvez connecter le nouveau service de cloud au réseau virtuel existant.

Si le service cloud existant n’est pas associé à un réseau virtuel en fonction de la région, vous devez supprimer les ordinateurs virtuels dans le service cloud existant et les recréer dans le nouveau service de nuage à partir de leurs disques. Toutefois, il est important de se rappeler que le nouveau service de cloud aura un nouveau nom et l’adresse IP virtuelle, vous devez mettre à jour ces pour toutes les dépendances qui utilisent ces informations pour le service cloud existant.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes lorsque vous créez une machine virtuelle Windows dans Azure, consultez [résoudre les problèmes de déploiement à la création d’une machine virtuelle Windows dans Azure](../../virtual-machines-windows-troubleshoot-deployment-new-vm.md).
