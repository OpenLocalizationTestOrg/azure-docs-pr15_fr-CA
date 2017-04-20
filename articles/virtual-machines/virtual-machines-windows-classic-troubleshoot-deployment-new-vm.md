<properties
   pageTitle="Résoudre les problèmes de déploiement classique Windows VM | Microsoft Azure"
   description="Résoudre les problèmes de déploiement classique lorsque vous créez une machine virtuelle Windows dans Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="JiangChen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>

<tags
  ms.service="virtual-machines-windows"
  ms.workload="na"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="09/06/2016"
  ms.author="cjiang"/>

# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-windows-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement classique avec création d’une machine virtuelle Windows dans Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Journaux d’audit collecte

Pour démarrer la résolution des problèmes, collecte des journaux d’audit pour identifier l’erreur associée à ce problème.

Dans le portail Azure, cliquez sur **Parcourir** > **machines virtuelles** > *votre machine Windows* > **paramètres** > **les journaux d’Audit**.

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** Si le système d’exploitation est Windows généralisée, et elle est téléchargée et/ou capturé avec le paramètre généralisé, il n’y avoir des erreurs. De même, si le système d’exploitation est Windows spécialisés, elle est téléchargée et/ou capturé avec le paramètre spécialisé, puis n’aura pas toutes les erreurs.

**Erreurs de téléchargement :**

**N<sup>1</sup>:** Si le système d’exploitation est Windows généralisée, et il est téléchargé comme spécialisés, vous obtiendrez une erreur de temporisation de mise en service avec la machine virtuelle bloquée au niveau de l’écran OOBE.

**N<sup>2</sup>:** Si le système d’exploitation est Windows spécialisé, et il est téléchargé comme généralisée, vous obtiendrez une erreur d’échec de mise en service avec la machine virtuelle bloquée au niveau de l’écran OOBE, car la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe.

**Résolution :**

Pour résoudre ces deux erreurs, télécharger le disque dur virtuel d’origine, disponibles sur-prem, avec le même paramètre que celui du système d’exploitation (généralisée/spécialisé). Pour télécharger comme généralisée, n’oubliez pas d’exécuter tout d’abord de sysprep. Pour plus d’informations, reportez-vous à la section [Création et téléchargement d’un disque dur virtuel du serveur de Windows Azure](virtual-machines-windows-classic-createupload-vhd.md) .

**Capturer les erreurs :**

**N<sup>3</sup>:** Si le système d’exploitation est Windows généralisée, et il est capturé en tant que spécialisé, vous obtiendrez une erreur de temporisation de mise en service, car la machine virtuelle d’origine n’est pas utilisable car il est marqué comme généralisé.

**N<sup>4</sup>:** Si le système d’exploitation est Windows spécialisé, et elle est capturée comme généralisée, vous obtiendrez une erreur d’échec de mise en service, car la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur d’origine, nom d’utilisateur et mot de passe. En outre, l’original VM n’est pas utilisable car il est marqué comme spécialisés.

**Résolution :**

Pour résoudre ces deux erreurs, supprimez l’image en cours à partir du portail et [capturer de nouveau depuis les disques durs virtuels en cours](virtual-machines-windows-classic-capture-image.md) avec le même paramètre que celui du système d’exploitation (généralisée/spécialisé).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problème : Personnalisé / Galerie / image du marché ; Échec d’allocation
Cette erreur se produit dans les situations, lorsque la nouvelle demande de machine virtuelle est envoyée à un cluster qui n’a pas d’espace libre pour s’adapter à la demande, ou ne reconnaît pas la taille de mémoire virtuelle demandée. Il n’est pas possible de combiner les différentes séries d’ordinateurs virtuels dans le même service de cloud. Par conséquent, si vous souhaitez créer une nouvelle machine virtuelle de ce que votre service cloud peut prendre en charge une taille différente, la demande de calcul échouera.

Selon les contraintes du service cloud que vous permet de créer la nouvelle machine virtuelle, vous pouvez rencontrer une erreur due à une des deux situations.

**Provoquer 1 :** Le service en nuage est épinglé à un cluster spécifique, ou il est lié à un groupe d’affinité et épinglée, par conséquent, un cluster spécifique par conception. Les ressources de calcul de nouveaux demande dans ce groupe d’affinité sont essayés dans le même cluster où sont hébergés les ressources existantes. Toutefois, le même cluster peut prend en charge de la taille de mémoire virtuelle demandée ou non avoir le manque d’espace, ce qui entraîne une erreur d’allocation. Il a la valeur true si les nouvelles ressources sont créées via un service en nuage ou via un service cloud existant.

**Résolution 1 :**

- Créer un nouveau service de cloud et l’associer à une région ou sur un réseau virtuel en fonction de la région.
- Créer une nouvelle machine virtuelle dans le nouveau service de cloud.
  Si vous obtenez une erreur lorsque vous tentez de créer un nouveau service en nuage, réessayer ultérieurement, ou modifier la région pour le service en nuage.

> [AZURE.IMPORTANT] Si vous tentez de créer une machine virtuelle dans un service cloud existant mais n’a pas pu et deviez créer un nouveau service de cloud pour votre nouvelle machine virtuelle, vous pouvez choisir de consolider vos ordinateurs virtuels dans le même service de cloud. Pour ce faire, supprimez les ordinateurs virtuels dans le service cloud existant et les acquérir à nouveau à partir de leurs disques dans le nouveau service de cloud. Toutefois, il est important de se rappeler que le nouveau service de cloud aura un nouveau nom et l’adresse IP virtuelle, vous devez mettre à jour ces pour toutes les dépendances qui utilisent ces informations pour le service cloud existant.

**Cause 2 :** Le service en nuage est associé à un réseau virtuel qui est lié à un groupe d’affinité, afin qu’il est épinglé à un cluster spécifique par conception. Toutes les ressources de calcul nouvelle demande dans ce groupe d’affinité sont donc essayé dans le même cluster où sont hébergés les ressources existantes. Toutefois, le même cluster peut prend en charge de la taille de mémoire virtuelle demandée ou non avoir le manque d’espace, ce qui entraîne une erreur d’allocation. Il a la valeur true si les nouvelles ressources sont créées via un service en nuage ou via un service cloud existant.

**Résolution 2 :**

- Créer un nouveau réseau virtuel régional.
- Créez la nouvelle machine virtuelle dans le nouveau réseau virtuel.
- [Se connecter votre réseau virtuel existant](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) vers le nouveau réseau virtuel. Reportez-vous à la section plus d’informations sur [les réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Vous pouvez également [migrer votre réseau virtuel par affinité de groupe pour un réseau virtuel](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis créez la nouvelle machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une VM Windows arrêté ou que vous redimensionnez une VM Windows existant dans Azure, consultez [résoudre les problèmes de déploiement classique avec redémarrage ou le redimensionnement d’un ordinateur virtuel de Windows existant dans Azure](windows/classic/virtual-machines-windows-classic-restart-resize-error-troubleshooting.md).
