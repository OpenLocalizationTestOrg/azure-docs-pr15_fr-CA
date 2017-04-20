<properties
   pageTitle="Comment redimensionner un VM Linux | Microsoft Azure"
   description="Comment redimensionner ou mettre à l’échelle vers le bas d’une machine virtuelle Linux, en modifiant la taille de la mémoire virtuelle."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Comment redimensionner un VM Linux

## <a name="overview"></a>Vue d’ensemble 

Une fois que vous devez configurer une machine virtuelle (VM), vous pouvez mettre à l’échelle la machine virtuelle vers le haut ou vers le bas en modifiant la [taille de mémoire virtuelle][vm-sizes]. Dans certains cas, vous devez libérer tout d’abord la machine virtuelle. Cela peut se produire si la nouvelle taille n’est pas disponible sur le cluster de matériel qui héberge l’ordinateur virtuel.

Cet article montre comment redimensionner une VM Linux à l’aide de la [CLI d’Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.


## <a name="resize-a-linux-vm"></a>Redimensionner une VM Linux 

Pour redimensionner une machine virtuelle, procédez comme suit.

1. Exécutez la commande CLI suivante. Cette commande répertorie les tailles de mémoire virtuelle disponibles sur le cluster de matériel dans lequel l’ordinateur virtuel est hébergé.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Si la taille souhaitée est répertoriée, exécutez la commande suivante pour redimensionner la machine virtuelle.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    L’ordinateur virtuel redémarre au cours de ce processus. Après le redémarrage, votre système d’exploitation existant et les disques de données seront remappés. Quoi que ce soit sur le disque temporaire seront perdu.

    Utilisez le `--enable-boot-diagnostics` option permet [les diagnostics de démarrage][boot-diagnostics], pour consigner les erreurs liées au démarrage.

3. Dans le cas contraire, si la taille souhaitée n’est pas répertoriée, exécutez les commandes suivantes pour libérer de la mémoire virtuelle, redimensionner et redémarrez la machine virtuelle.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Libérer de la mémoire virtuelle libère également toutes les adresses IP dynamiques affectées à la machine virtuelle. Les disques du système d’exploitation et les données ne sont pas affectés.
   
## <a name="next-steps"></a>Étapes suivantes

Pour une évolutivité supplémentaire, exécuter plusieurs instances de la machine virtuelle et évoluer. Pour plus d’informations, consultez [mise à l’échelle les ordinateurs Linux dans une échelle de Machine virtuelle définie automatiquement][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md