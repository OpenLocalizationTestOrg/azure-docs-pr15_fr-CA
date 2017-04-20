<properties
    pageTitle="À propos des images pour les machines virtuelles Windows | Microsoft Azure"
    description="Découvrez comment les images sont utilisés avec les ordinateurs virtuels Windows Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/21/2016"
    ms.author="cynthn"/>

# <a name="about-images-for-windows-virtual-machines"></a>À propos des images pour les machines virtuelles Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]



## <a name="working-with-images"></a>Utilisation d’images

Vous pouvez utiliser le module PowerShell de Azure pour gérer les images disponibles pour votre abonnement Azure. Vous pouvez également utiliser le portail classique Azure pour certaines tâches de l’image, mais la ligne de commande vous donne davantage d’options.


-   **Obtenir toutes les images**:`Get-AzureVMImage`renvoie une liste de toutes les images de votre abonnement actuel : vos images, ainsi que ceux fournis par Azure ou partenaires. Cela signifie que vous risquez d’obtenir une grande liste. Les exemples suivants montrent comment obtenir une liste plus courte.
-   **Obtenir des familles de l’image**:`Get-AzureVMImage | select ImageFamily` Obtient une liste des familles d’image en montrant les chaînes de propriété de **ImageFamily** .
-   **Obtenir toutes les images dans une famille spécifique**:`Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
-   **Rechercher les Images VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` cela fonctionne en filtrant la propriété DataDiskConfiguration, qui s’applique uniquement aux Images de machine virtuelle. Cet exemple filtre également la sortie uniquement le nom d’étiquette et l’image.
-   **Enregistrer une image généralisée**:`Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
-   **Enregistrer une image spécialisée**:`Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
>[Azure.Tip] Le paramètre OSState est obligatoire si vous souhaitez créer une image de machine virtuelle, qui inclut des disques de données, ainsi que le disque du système d’exploitation. Si vous n’utilisez pas le paramètre, l’applet de commande crée une image de système d’exploitation. La valeur du paramètre indique si l’image est généralisée ou spécialisée, selon que le disque du système d’exploitation a été préparé pour une réutilisation.
-   **Supprimer une image**:`Remove-AzureVMImage –ImageName "MyOldVmImage"`


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [créer un ordinateur Windows à l’aide du portail classique](virtual-machines-windows-classic-tutorial.md)

