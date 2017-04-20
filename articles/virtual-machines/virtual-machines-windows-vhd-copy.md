<properties
    pageTitle="Créer une copie d’un ordinateur virtuel spécialisé dans Azure | Microsoft Azure"
    description="Apprenez à créer une copie d’une machine virtuelle Windows spécialisé en cours d’exécution dans Azure, dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Créer une copie d’une machine virtuelle Windows spécialisés en cours d’exécution dans Azure 

Cet article vous montre comment utiliser l’outil AZCopy pour créer une copie de ce dernier à partir d’une machine virtuelle Windows spécialisée qui s’exécute dans Azure. Vous pouvez ensuite utiliser la copie du disque dur virtuel pour créer une nouvelle machine virtuelle. 

- Si voulez copier d’un ordinateur virtuel généralisé, voir [comment créer une image de machine virtuelle à partir d’un Azure VM généralisée existant](virtual-machines-windows-capture-image.md).

- Si vous souhaitez télécharger un disque dur virtuel à partir d’un ordinateur virtuel sur site, tels que ceux créés à l’aide de Hyper-V, voir [télécharger un disque dur virtuel de Windows à partir d’une VM sur site vers Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Avant de commencer

Assurez-vous que vous :

- Disposer d’informations sur les **comptes de stockage source et de destination**. Pour la source de machine virtuelle, vous devez les noms de compte et de conteneur de stockage. En règle générale, le nom du conteneur seront des **disques durs virtuels**. Vous devez également disposer d’un compte de stockage de destination. Si vous n’en avez pas déjà, vous pouvez créer à l’aide du portail (**Plus de Services** > comptes de stockage > Ajouter) ou à l’aide de l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Avez Azure [PowerShell 1.0](../powershell-install-configure.md) (ou ultérieure) installé.

- Téléchargé et installé l' [outil de AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Désallouer la machine virtuelle

Désallouer la machine virtuelle, ce qui permet de libérer le disque dur virtuel à copier. 

- **Portail**: cliquez sur **machines virtuelles** > **myVM** > arrêter
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` libère la machine virtuelle nommée **myVM** dans le groupe de ressources **myResourceGroup**.

L' **état** de la machine virtuelle dans le portail Azure change de **arrêté** arrêté **(libérés)**.


## <a name="get-the-storage-account-urls"></a>Obtenir l’URL de compte de stockage

Vous devez les URL des comptes de stockage source et de destination. L’URL ressemble à : `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si vous connaissez déjà le nom de compte et de conteneur de stockage, vous pouvez remplacer les informations entre les crochets pour créer votre URL. 

Vous pouvez utiliser le portail Azure ou Azure Powershell pour obtenir l’URL :

- **Portail**: cliquez sur **davantage de services** > **comptes de stockage**  >  <storage account> votre fichier de disque dur virtuel source et les **objets BLOB** se trouve probablement dans le conteneur de **disques durs virtuels** . Cliquez sur **Propriétés** pour le conteneur et copier le texte **d’URL**. Vous aurez besoin de l’URL de la source et la destination des conteneurs. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` Obtient les informations de l’ordinateur virtuel nommé **myVM** , dans le groupe de ressources **myResourceGroup**. Dans les résultats, recherchez dans la section **profil de stockage** l' **Uri du disque dur virtuel**. La première partie de l’Uri est l’URL pour le conteneur, et la dernière partie est le nom du disque dur virtuel de système d’exploitation de la machine virtuelle.

## <a name="get-the-storage-access-keys"></a>Obtenir les clés d’accès de stockage

Rechercher les touches d’accès rapide pour la source et la destination, comptes de stockage. Pour plus d’informations sur les touches d’accès rapide, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).

- **Portail**: cliquez sur **davantage de services** > **comptes de stockage**  >  <storage account> **Tous les paramètres** > **clés d’accès**. Copiez la clé appelée **key1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` Obtient la clé pour le compte de stockage **mystorageaccount** de stockage dans le groupe de ressources **myResourceGroup**. Copiez la clé appelée **key1**.


## <a name="copy-the-vhd"></a>Copie du disque dur virtuel 

Vous pouvez copier des fichiers entre des comptes de stockage à l’aide de AzCopy. Pour le conteneur de destination, si le conteneur spécifié n’existe pas, il sera créé pour vous. 

Pour utiliser AzCopy, ouvrez une invite de commandes sur votre ordinateur local et accédez au dossier où est installé le AzCopy. Il sera similaire à *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*. 

Pour copier tous les fichiers dans un conteneur, vous utilisez le commutateur **/S** . Cela peut servir à copier du disque dur virtuel du système d’exploitation et tous les disques de données, si elles sont dans le même conteneur. Cet exemple montre comment copier tous les fichiers dans le conteneur **mysourcecontainer** dans le compte de stockage **mysourcestorageaccount** à la **mydestinationcontainer** du conteneur dans le compte de stockage **mydestinationstorageaccount** . Remplacez les noms des comptes de stockage et les conteneurs avec votre propre. Remplacer `<sourceStorageAccountKey1>` et `<destinationStorageAccountKey1>` avec vos propres clés.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si vous voulez uniquement copier d’un disque dur virtuel spécifique dans un conteneur avec plusieurs fichiers, vous pouvez également spécifier le nom du fichier à l’aide du commutateur /Pattern. Dans cet exemple, seul le fichier nommé **myFileName.vhd** sera copié.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Lorsqu’il est terminé, vous obtiendrez un message qui ressemble à ceci :

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Résolution des problèmes

- Lorsque vous utilisez AZCopy, si le message d’erreur « serveur n’a pas pu authentifier la demande. Assurez-vous que la valeur de l’en-tête d’autorisation est formée correctement, y compris de la signature. » et vous utilisez la touche 2 ou la clé de stockage secondaire, essayez d’utiliser la clé de stockage principal ou 1er.


## <a name="next-steps"></a>Étapes suivantes

- Vous pouvez créer une nouvelle machine virtuelle en [joignant la copie du disque dur virtuel sur un ordinateur virtuel comme un disque du système d’exploitation](virtual-machines-windows-create-vm-specialized.md).












