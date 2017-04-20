<properties
   pageTitle="Services de récupération de chambre forte FAQ | Microsoft Azure"
   description="Cette version de la FAQ prend en charge la version du service de sauvegarde d’Azure. Réponses aux questions fréquemment posées sur l’agent de sauvegarde, sauvegarde et conservation, restauration, sécurité et autres questions courantes sur la solution de sauvegarde d’Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solution de sauvegarde ; service de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Coffre-fort de Services de récupération - Forum aux questions


Cet article fournit des informations spécifiques à la chambre forte de Services de récupération et qu’il complète le [Forum aux questions de sauvegarde Azure](backup-azure-backup-faq.md). Le Forum aux questions sur la sauvegarde d’Azure fournit l’ensemble des questions et réponses concernant le service de sauvegarde d’Azure.  

Vous pouvez poser des questions relatives à la sauvegarde d’Azure dans la section Disqus de cet article ou un article. Vous pouvez également publier des questions sur le service de sauvegarde d’Azure dans le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Coffres-forts de Services de récupération sont sur le Gestionnaire de ressources. Sont des coffres-forts (mode classique) de sauvegarde toujours pris en charge ? <br/>
Oui, des coffres-forts de sauvegarde sont toujours pris en charge. Créer des coffres-forts de sauvegarde dans le [portail de classique](https://manage.windowsazure.com). Créer des Services de récupération des coffres-forts dans [Azure portal](https://portal.azure.com). Il est vivement recommandé vous permet de créer un coffre-fort de services de récupération en tant que toutes les futures améliorations seront toutefois disponible uniquement dans le coffre-fort de Services de récupération.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Puis-je migrer un coffre-fort de sauvegarde dans un coffre-fort de Services de récupération ? <br/>
Malheureusement non, à ce stade ne peut pas migrer le contenu d’un coffre-fort de sauvegarde dans un coffre-fort de Services de récupération. Nous travaillons sur l’ajout de cette fonctionnalité, mais il n’est pas disponible dans le cadre de la version d’évaluation.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Les coffres-forts des Services de récupération prennent en charge VMs classiques ou le Gestionnaire de ressources en fonction des machines virtuelles ? <br/>
Coffres-forts de Services de récupération prend en charge les deux modèles.  Vous pouvez sauvegarder une machine virtuelle créée dans le portail classique (qui sont des ordinateurs virtuels de mode classique), ou une machine virtuelle créée dans le portail Azure (qui sont sur le Gestionnaire de ressources) à un coffre-fort de Services de récupération.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>J’ai sauvegardé mon VMs classiques dans le coffre-fort de la sauvegarde. Je souhaite à présent migrer mes machines virtuelles à partir du mode classique au mode du Gestionnaire de ressources.  Comment puis-je sauvegarder les dans le coffre-fort de services de récupération ?
Sauvegardes de machines virtuelles de classiques dans le coffre-fort de sauvegarde ne sont pas migrer automatiquement au coffre-fort des services de récupération lorsque vous migrez les ordinateurs virtuels de classique au mode du Gestionnaire de ressources. Suivez ces étapes pour la migration des sauvegardes de machines virtuelles :

1. Dans le coffre-fort de sauvegarde, accédez à onglet **Éléments de protection** et sélectionnez la machine virtuelle. Cliquez sur [Arrêter la Protection](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). N’activez pas *Supprimer les données de sauvegarde associé* option **désactivée**.
2. Migrer l’ordinateur virtuel à partir du mode classique au mode du Gestionnaire de ressources. Assurez-vous que le stockage et le réseau correspondant à la machine virtuelle est également migré vers le mode Gestionnaire de ressources.
3. Créer un coffre-fort de services de récupération et de configurer la sauvegarde sur l’ordinateur virtuel ayant migré à l’aide de la **sauvegarde** d’action en haut du tableau de bord en chambre forte. Pour en savoir plus sur [l’activation de sauvegarde en chambre forte de services de récupération](backup-azure-vms-first-look-arm.md)
