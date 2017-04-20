<properties
   pageTitle="Mettre à jour votre collection Azure RemoteApp | Microsoft Azure"
   description="Apprenez à mettre à jour votre collection Azure RemoteApp"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Mise à jour d’une collection dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Il arrivera un moment, inévitablement, lorsque vous avez besoin pour mettre à jour les applications ou l’image dans votre collection Azure RemoteApp. Si vous utilisez l’une des images inclus dans votre abonnement Azure RemoteApp, dans la collection un nuage ou un hybride, toutes les mises à jour de sont gérées par RemoteApp Azure lui-même, vous pouvez compter.

Toutefois, si vous utilisez une image personnalisée (que vous avez créé de toutes pièces ou que vous avez créé en modifiant une de nos images), vous êtes responsable de la maintenance de l’image et les applications. Si vous avez besoin mettre à jour votre image ou l’une des applications qu’il contient, vous devez créer une nouvelle version mise à jour de l’image et remplacer l’image existante dans votre collection avec cette nouvelle image mis à jour.

Par conséquent, comment faire à propos de la mise à jour de votre collection ? Il est relativement simple :

1. Mettre à jour l’image que vous avez utilisé dans votre collection. Appliquer tous les correctifs ou mises à jour nécessaires et enregistrez-le sous un nouveau nom.
2. [Télécharger](remoteapp-uploadimage.md) ou [Importer des](remoteapp-image-on-azurevm.md) images à RemoteApp.
3. Maintenant, sur la page de la collection, cliquez sur **mise à jour**.
4. Choisissez la nouvelle image dans la liste de **l’Image du modèle** .
4. Voici la partie délicate : vous devez décider comment traiter tous les utilisateurs qui utilisent une application dans la collection. Vous disposez des choix suivants :
    - **Permettre aux utilisateurs de 60 minutes après la mise à jour**. Dès que la mise à jour est terminée, Azure RemoteApp affiche un message à tous les utilisateurs actives leur donnant à enregistrer leur travail et fermer la session et reconnectez-vous. Après 60 minutes, tous les utilisateurs actifs qui n’ont pas été déconnecté seront automatiquement fermées. Les utilisateurs peuvent se connecter immédiatement sur.
    - **Déconnecter des utilisateurs à immédiatement**. Dès que la mise à jour est terminée, déconnectez tous les utilisateurs automatiquement sans aucun avertissement. Si vous choisissez cette option, les utilisateurs peuvent perdre des données. Toutefois, ils peuvent se reconnecter à l’application immédiatement.

1. Cliquez sur la case à cocher pour démarrer la mise à jour.
