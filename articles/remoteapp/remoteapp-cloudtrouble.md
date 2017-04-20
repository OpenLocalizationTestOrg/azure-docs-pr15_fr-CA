
<properties
    pageTitle="Dépannage des collections de nuage RemoteApp - création | Microsoft Azure"
    description="Découvrez comment résoudre les problèmes de création de collection de nuage RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-remoteapp-cloud-collections"></a>Résoudre les problèmes de créer des collections de nuage RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Si vous rencontrez des problèmes pour créer une collection de nuage, vérifiez les informations suivantes.

## <a name="your-image-is-invalid"></a>Votre image n’est pas valide ##
Si vous voyez un message du type « GoldImageInvalid » lorsque vous attendez Azure pour provisionner votre collection, cela signifie que l’image de votre modèle ne répond pas à la [définis par les exigences de l’image](remoteapp-imagereqs.md). Ainsi, accédez lire ces [exigences](remoteapp-imagereqs.md), corriger votre image, essayez de créer à nouveau de votre collection.

## <a name="common-errors-seen-in-the-azure-management-portal"></a>Erreurs courantes dans le portail de gestion d’Azure

    DNS server could not be reached
    ProvisioningTimeout

Collections en nuage souvent Échec lors de la création, grâce à vous utilisez des images personnalisées.  Si vous consultez une des erreurs ci-dessus et si vous utilisez une image personnalisée pour créer la collection, vérifiez les éléments suivants :

- Assurez-vous que l’image personnalisée que vous avez téléchargé répond aux exigences de l’image.
- Le plus souvent le problème courant est que l’image n’a pas correctement préparée avec Sysprep.  
- Vérifiez que l’image de démarrage dans Hyper-V ou d’essayer de créer une VM IAAS directement dans votre abonnement Azure à l’aide de l’image. Si la machine virtuelle ne parvient pas à démarrer et pas lancer, puis cela indique généralement que l’image personnalisée n’a pas été préparé correctement.  Vérifiez que l’image personnalisée a été créée suite à la procédure pour créer une image de modèle personnalisé de RemoteApp

Si vous utilisez l’une des images de Microsoft inclus dans votre abonnement, essayez de créer à nouveau la collection. Si le problème persiste contactez services de support Microsoft.

    PlatformImageTrialModeOnly

Si vous voyez cette erreur cela signifie généralement que vous été mis à niveau vers un compte payé mais vous essayez d’utiliser une image fournis par Microsoft qui est uniquement valide pendant le mode d’évaluation du service. Dans ce cas, essayez de créer à nouveau votre collection de nuage, mais veillez à spécifier l’image correcte.
