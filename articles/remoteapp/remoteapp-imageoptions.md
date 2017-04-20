<properties
    pageTitle="Créer une image de RemoteApp d’Azure | Microsoft Azure"
    description="En savoir plus sur les options disponibles pour la création d’images pour Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="create-an-azure-remoteapp-image"></a>Créer une image d’Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure utilise des images pour stocker les applications que vous partagez avec vos utilisateurs. (Nous prendre l’image et l’utiliser pour créer des ordinateurs virtuels - que l’accès aux utilisateurs lorsqu’ils se connectent dans Azure RemoteApp.) Pour créer une collection Azure RemoteApp avec un choix d’applications, qu’il s’agisse de nuage ou hybride, commencez par créer une image avec des applications installées. Ensuite, créez une collection qui utilise cette image, affecter des utilisateurs à la collection et publier des applications aux utilisateurs.

Vous disposez de plusieurs options pour la création ou l’utilisation d’images. [L’exigence](remoteapp-imagereqs.md) base pour une image est qu’il exécuter Windows Server 2012 R2 et le rôle de l’hôte de Session à distance Bureau (RDSH) installé. Comment vous procurer qui est où les choses deviennent intéressantes.

Lorsqu’il s’agit d’images, vous disposez des options suivantes :

- Vous pouvez importer et utiliser une [image basée sur une machine virtuelle Azure](remoteapp-image-on-azurevm.md). Cela est utile pour les applications line-of-business qui requièrent des paramètres personnalisés. Vous pouvez personnaliser l’image à utiliser pour l’application.
- Vous pouvez [créer et télécharger une image personnalisée](remoteapp-create-custom-image.md). Cela est utile si vous disposez déjà d’une image que vous utilisez pour le déploiement de Services Bureau à distance sur site.
- Vous pouvez utiliser l’une des [images de modèle](remoteapp-images.md) incluses dans votre abonnement RemoteApp. Ces images sont créées et gérées par l’équipe de RemoteApp et contiennent certaines applications standard (comme la suite Office) que vous pouvez mettre à la disposition de vos utilisateurs. Notez que seule l’image Office 365 Pro Plus peut être utilisé dans un environnement de production.

Que vous obtenez votre image ou comment vous le créez, vous voudrez vous assurer de que comprendre les [exigences de l’application](remoteapp-appreqs.md) pour garantir que votre application fonctionne bien dans RemoteApp. Ensuite, l’étape suivante consiste à créer une collection de [nuage](remoteapp-create-cloud-deployment.md) ou [hybride](remoteapp-create-hybrid-deployment.md) .
