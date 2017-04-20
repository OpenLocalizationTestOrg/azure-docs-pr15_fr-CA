<properties
   pageTitle="Résoudre les problèmes de déploiement de Linux VM-RM | Microsoft Azure"
   description="Résoudre les problèmes de déploiement de gestionnaire de ressources, lorsque vous créez une machine virtuelle Linux dans Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/09/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement de gestionnaire de ressources à la création d’une machine virtuelle Linux dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit pour identifier l’erreur associée à ce problème. Les liens suivants contiennent des informations détaillées sur le processus à suivre.

[Résoudre les problèmes de déploiement de groupe de ressources avec Azure Portal](../resource-manager-troubleshoot-deployments-portal.md)

[Auditer les opérations effectuées avec le Gestionnaire de ressources](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si le système d’exploitation est généralisée de Linux, et il est téléchargée et/ou capturé avec le paramètre généralisé, il n’y avoir des erreurs. De la même façon, si le système d’exploitation Linux spécialisé, elle est téléchargée et/ou capturé avec le paramètre spécialisé, puis n’aura pas toutes les erreurs.

**Erreurs de téléchargement :**

**N<sup>1</sup>:** Si le système d’exploitation est généralisée de Linux, et il est téléchargé comme spécialisés, vous obtiendrez une erreur de temporisation de mise en service, car la machine virtuelle est bloquée au niveau de la phase de mise en service.

**N<sup>2</sup>:** Si le système d’exploitation est spécialisé de Linux, et il est téléchargé comme généralisée, vous obtiendrez une erreur d’échec de mise en service, car la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe.

**Résolution :**

Pour résoudre ces deux erreurs, télécharger le disque dur virtuel d’origine, disponibles sur-prem, avec le même paramètre que celui du système d’exploitation (généralisée/spécialisé). Pour télécharger comme généralisée, n’oubliez pas d’exécuter - deprovision tout d’abord.

**Capturer les erreurs :**

**N<sup>3</sup>:** Si le système d’exploitation est généralisée de Linux, et il est capturé en tant que spécialisé, vous obtiendrez une erreur de temporisation de mise en service, car la machine virtuelle d’origine n’est pas utilisable car il est marqué comme généralisé.

**N<sup>4</sup>:** Si le système d’exploitation est spécialisé de Linux, et elle est capturée comme généralisée, vous obtiendrez une erreur d’échec de mise en service, car la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe. En outre, l’original VM n’est pas utilisable car il est marqué comme spécialisés.

**Résolution :**

Pour résoudre ces deux erreurs, supprimez l’image en cours à partir du portail et [capturer de nouveau depuis les disques durs virtuels en cours](virtual-machines-linux-capture-image.md) avec le même paramètre que celui du système d’exploitation (généralisée/spécialisé).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problème : Personnalisé / Galerie / image du marché ; Échec d’allocation
Cette erreur se produit dans les situations, lorsque la nouvelle demande de machine virtuelle est épinglée à un cluster qui ne supporte pas la taille de la mémoire virtuelle qui est demandée, ou n’a pas d’espace libre pour s’adapter à la demande.

**Provoquer 1 :** Le cluster ne peut pas prendre en charge la taille de mémoire virtuelle demandée.

**Résolution 1 :**

- Renouvelez la demande à l’aide d’une taille plus petite de la machine virtuelle.
- Si la taille de la machine virtuelle demandée ne peut pas être modifiée :
  - Arrêtez tous les ordinateurs virtuels dans l’ensemble de la disponibilité.
  Cliquez sur **groupes de ressources** > *votre groupe de ressources* > **ressources** > *la valeur de votre disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  - Après l’arrêt de tous les ordinateurs virtuels, créer la nouvelle machine virtuelle dans la taille souhaitée.
  - Commencez la nouvelle machine virtuelle, puis sélectionnez chacun des ordinateurs virtuels arrêtés et cliquez sur **Démarrer**.

**Cause 2 :** Le cluster n’a pas de libérer des ressources.

**Résolution 2 :**

- Retentez l’opération ultérieurement.
- Si la nouvelle machine virtuelle peut être la partie d’un ensemble différent de disponibilité
  - Créer une nouvelle machine virtuelle dans une autre disponibilité (dans la même région).
  - Ajouter la nouvelle machine virtuelle au même réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une VM Linux arrêté ou que vous redimensionnez une VM Linux existants dans Azure, consultez le [Gestionnaire de ressources de résolution des problèmes de déploiement avec redémarrage ou le redimensionnement d’un ordinateur virtuel de Linux existant dans Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).
