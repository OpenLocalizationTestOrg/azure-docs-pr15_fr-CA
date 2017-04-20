<properties
    pageTitle="Vérifiez le lecteur D: d’un ordinateur virtuel un disque de données | Microsoft Azure"
    description="Décrit comment modifier des lettres de lecteur d’un ordinateur virtuel de Windows afin que vous puissiez utiliser le lecteur D: comme un lecteur de données."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilisez le lecteur d comme un lecteur de données sur un ordinateur virtuel de Windows 

Si votre application doit utiliser le lecteur D pour stocker des données, suivez ces instructions pour utiliser une autre lettre de lecteur pour le disque temporaire. N’utilisez jamais le disque temporaire pour stocker les données dont vous avez besoin.

Si vous redimensionnez ou **Stop (Deallocate)** un ordinateur virtuel, ceci peut déclencher le placement de la machine virtuelle à un hyperviseur de nouveau. Un événement d’entretien planifié ou peut également déclencher ce placement. Dans ce scénario, le disque temporaire sera réaffecté à la première lettre de lecteur disponible. Si vous avez une application nécessitant spécifiquement le lecteur d, vous devez procédez comme suit pour temporairement déplacer pagefile.sys, connectez un nouveau disque de données et lui attribuer la lettre D et replacez le fichier pagefile.sys sur le disque temporaire. Une fois terminé, Azure pas ramène le lecteur D: si la machine virtuelle se déplace vers un autre hyperviseur.

Pour plus d’informations sur comment Azure utilise le disque temporaire, reportez-vous à la section [comprendre le lecteur temporaire Microsoft Azure machines virtuelles en fonctionnement](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="attach-the-data-disk"></a>Connectez le disque de données

Vous devez tout d’abord, attachez le disque de données à l’ordinateur virtuel. 

- Pour utiliser le portail, consultez [comment joindre un disque de données dans le portail Azure](virtual-machines-windows-attach-disk-portal.md)
- Pour utiliser le portail classique, consultez [comment joindre un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md). 


## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Déplacer temporairement pagefile.sys sur lecteur C

1. Se connecter à l’ordinateur virtuel. 

2. Cliquez sur le menu **Démarrer** et sélectionnez **système**.

3. Dans le menu de gauche, sélectionnez **paramètres système avancés**.

4. Dans la section **performances** , sélectionnez **paramètres**.

5. Sélectionnez l’onglet **Avancé** .

5. Dans la section **mémoire virtuelle** , sélectionnez **Modifier**.

6. Sélectionnez le lecteur **C** , puis cliquez sur **Taille gérée par le système** et puis cliquez sur **définir**.

7. Sélectionnez le lecteur **D** , puis cliquez sur **aucun fichier d’échange** et puis cliquez sur **définir**.

8. Cliquez sur Appliquer. Vous obtiendrez un avertissement que l’ordinateur doit être redémarré pour que les modifications soient prises en compte.

9. Redémarrez l’ordinateur virtuel.




## <a name="change-the-drive-letters"></a>Modifier les lettres de lecteur 

1. Une fois le redémarrage de la machine virtuelle, connectez-vous de nouveau à la machine virtuelle.

2. Cliquez sur le menu **Démarrer** , tapez **diskmgmt.msc** et appuyez sur ENTRÉE. Gestion de disque démarre.

3. Avec le bouton droit sur **D**, le lecteur de stockage temporaire et sélectionnez **Modifier la lettre de lecteur et les chemins d’accès**.

4. Sous la lettre de lecteur, sélectionnez le lecteur **G** et puis cliquez sur **OK**. 

5. Avec le bouton droit sur le disque de données et sélectionnez **Modifier la lettre de lecteur et les chemins d’accès**.

6. Sous la lettre de lecteur, sélectionnez le lecteur **D** et puis cliquez sur **OK**. 

7. Avec le bouton droit sur **G**, le lecteur de stockage temporaire et sélectionnez **Modifier la lettre de lecteur et les chemins d’accès**.

8. Sous la lettre de lecteur, sélectionnez le lecteur **E** et puis cliquez sur **OK**. 

> [AZURE.NOTE] Si votre machine virtuelle dispose d’autres disques ou les lecteurs, utilisez la même méthode pour réaffecter les lettres de lecteur des autres disques et des lecteurs. Vous souhaitez que la configuration du disque à être :  
>- Lecteur C: disque du système d’exploitation du  
>- D: disque de données de  
>- E: disque temporaire



## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile.sys de déplacement vers le disque de stockage temporaire 

1. Cliquez sur le menu **Démarrer** , sélectionnez **système**

2. Dans le menu de gauche, sélectionnez **paramètres système avancés**.

3. Dans la section **performances** , sélectionnez **paramètres**.

4. Sélectionnez l’onglet **Avancé** .

5. Dans la section **mémoire virtuelle** , sélectionnez **Modifier**.

6. Sélectionnez le lecteur du système d’exploitation **C** et cliquez sur **aucun fichier d’échange** et puis cliquez sur **définir**.

7. Sélectionnez le lecteur de stockage temporaire **E** et puis cliquez sur **Taille gérée par le système** et puis cliquez sur **définir**.

8. Cliquez sur **Appliquer**. Vous obtiendrez un avertissement que l’ordinateur doit être redémarré pour que les modifications soient prises en compte.

9. Redémarrez l’ordinateur virtuel.




## <a name="next-steps"></a>Étapes suivantes
- Vous pouvez augmenter le stockage disponible pour votre ordinateur virtuel en [attachant un disque de données supplémentaires](virtual-machines-windows-attach-disk-portal.md).



