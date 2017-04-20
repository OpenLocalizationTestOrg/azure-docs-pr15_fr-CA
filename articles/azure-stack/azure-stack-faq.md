<properties
    pageTitle="Forum aux questions sur la pile d’Azure | Microsoft Azure"
    description="Pile Azure les questions fréquemment posées."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Forum aux questions sur la pile d’Azure

## <a name="deployment"></a>Déploiement

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>Je dois mettre mes disques de données avant de démarrer ou de redémarrer une installation ?

Les disques doivent être au format raw. Si vous réinstallez le système d’exploitation, vous devrez vérifier si le pool de stockage ancien est toujours présent et en supprimer à l’aide des étapes suivantes :

1. Ouvrez le Gestionnaire de serveur.
2. Sélectionnez les Pools de stockage.
3. Vérifiez si un pool de stockage est dans la liste.
4. Cliquez droit sur **le pool de stockage** si répertoriés et activer en lecture / écriture.
5. Cliquez droit sur le **disque dur virtuel** (en bas à gauche), puis sélectionnez Supprimer.
6. Cliquez droit sur le **Pool de stockage** et cliquez sur Supprimer.
7. Relancez le script de la pile d’Azure et vérifiez que la vérification du disque passe.

Le cas échéant, le script suivant peut être utilisé :

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Puis-je utiliser tous les disques SSD pour le pool de stockage dans l’installation de VT ?

Cette configuration n’est pas pris en charge dans cette version.  Pour plus d’informations, consultez le [guide de la configuration requise](azure-stack-deploy.md) pour plus d’informations.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Puis-je utiliser des disques de données de NVMe pour le contrôle du concept Microsoft Azure pile ?

Direct des espaces de stockage prend en charge les disques NVMe, Azure pile prend en charge uniquement un sous-ensemble des types de lecteurs et combinaisons possibles pour les espaces de stockage Direct. 

### <a name="how-can-i-reinstall-azure-stack"></a>Comment puis-je réinstaller Azure pile ?
Vous pouvez suivre les étapes décrites dans le [guide de redéploiement](azure-stack-redeploy.md).  

## <a name="tenant"></a>Clients

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Puis-je déployer mes propres images comme un client ?

Oui, tout comme dans Azure, un client peut télécharger des images dans la pile d’Azure, en plus d’utiliser les images à partir de l’administrateur de service. Pour une vue d’ensemble, consultez [Ajout d’une Image de machine virtuelle](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Test de

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Puis-je utiliser imbriqué de la virtualisation pour tester le contrôle du concept Microsoft Azure pile ?

La virtualisation imbriquée n’est pas pris en charge ou testée avec Azure pile technique Preview 2.

## <a name="virtual-machines"></a>Ordinateurs virtuels

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Pile d’Azure prend-il en charge les disques dynamiques pour les machines virtuelles ?

Pile Azure ne reconnaît pas les disques dynamiques.

## <a name="next-steps"></a>Étapes suivantes

[Résolution des problèmes](azure-stack-troubleshooting.md)
