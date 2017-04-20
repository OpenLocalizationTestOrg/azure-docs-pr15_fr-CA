<properties
   pageTitle="VM, redémarrage ou redimensionnement | Microsoft Azure"
   description="Résolution des problèmes de déploiement de gestionnaire de ressources de redémarrage ou le redimensionnement d’un ordinateur virtuel de Linux existant dans Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="Deland-Han"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
   ms.service="virtual-machines-linux"
   ms.topic="support-article"
   ms.tgt_pltfrm="vm-linux"
   ms.devlang="na"
   ms.workload="required"
   ms.date="09/09/2016"
   ms.author="delhan"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-restarting-or-resizing-an-existing-linux-virtual-machine-in-azure"></a>Résolution des problèmes de déploiement de gestionnaire de ressources de redémarrage ou le redimensionnement d’un ordinateur virtuel de Linux existant dans Azure

Lorsque vous essayez de démarrer un ordinateur virtuel d’Azure arrêté (VM), ou redimensionner une VM Azure existante, l’erreur courant que vous rencontrez est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région ne dispose pas de ressources disponibles ou Impossible de prendre en charge la taille de mémoire virtuelle demandée.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit pour identifier l’erreur associée à ce problème. Les liens suivants contiennent des informations détaillées sur le processus :

[Résolution des problèmes de déploiement de groupe de ressources avec Azure Portal](../resource-manager-troubleshoot-deployments-portal.md)

[Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md)

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : Erreur lors du démarrage d’un ordinateur virtuel arrêté

Vous essayez de démarrer un ordinateur virtuel arrêté, mais obtenir un échec d’allocation.

### <a name="cause"></a>Cause

La demande pour démarrer la machine virtuelle arrêtée doit être tentée sur le cluster d’origine qui héberge le service cloud. Cependant, le cluster n’a pas d’espace libre disponible pour répondre à la demande.

### <a name="resolution"></a>Résolution

*   Arrêtez tous les ordinateurs virtuels dans l’ensemble de la disponibilité et redémarrez chaque ordinateur virtuel.

  1. Cliquez sur **groupes de ressources** > _votre groupe de ressources_ > **ressources** > _la valeur de votre disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Après l’arrêt de tous les ordinateurs virtuels, sélectionnez chacune des ordinateurs virtuels arrêtés et cliquez sur Démarrer.

*   Renouvelez la demande de redémarrage ultérieurement.

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : Erreur lors du redimensionnement d’un ordinateur virtuel existant

Vous essayez de redimensionner un ordinateur virtuel existant, mais obtenir un échec d’allocation.

### <a name="cause"></a>Cause

La demande de redimensionnement de la machine virtuelle doit être tentée sur le cluster d’origine qui héberge le service cloud. Cependant, le cluster ne gère pas la taille de mémoire virtuelle demandée.

### <a name="resolution"></a>Résolution

* Renouvelez la demande à l’aide d’une taille plus petite de la machine virtuelle.

* Si la taille de la machine virtuelle demandée ne peut pas être modifiée :

  1. Arrêtez tous les ordinateurs virtuels dans l’ensemble de la disponibilité.

    * Cliquez sur **groupes de ressources** > _votre groupe de ressources_ > **ressources** > _la valeur de votre disponibilité_ > **Machines virtuelles** > _votre machine virtuelle_ > **Arrêter**.

  2. Après l’arrêt de tous les ordinateurs virtuels, redimensionnez la VM souhaitée pour une plus grande taille.
  3. Sélectionnez la machine virtuelle redimensionnée et cliquez sur **Démarrer**puis redémarrez chacun des ordinateurs virtuels arrêtés.

## <a name="next-steps"></a>Étapes suivantes

Si vous rencontrez des problèmes lorsque vous créez une VM Linux nouveau dans Azure, consultez [résoudre les problèmes de déploiement à la création d’une machine virtuelle Linux dans Azure](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md).
