
<properties
    pageTitle="Télécharger une image personnalisée pour Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment télécharger une image personnalisée pour Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="ericor" />



# <a name="upload-a-custom-image-for-azure-remoteapp"></a>Télécharger une image personnalisée pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Maintenant que vous avez créé l’image de votre modèle personnalisé ou que vous avez mis à jour avec les modifications, vous devez télécharger cette image sur votre bibliothèque d’images Azure RemoteApp. Suivez ces étapes.


## <a name="before-you-start"></a>Avant de commencer

1.      Vérifiez que votre image personnalisée respecte les [exigences de l’image](remoteapp-imagereqs.md) et les [exigences de l’application](remoteapp-appreqs.md).
2.      Installez le [module PowerShell de Azure](../powershell-install-configure.md).

## <a name="step-by-step-on-how-to-upload-custom-image"></a>Étape par étape sur la façon de télécharger l’image personnalisée

1.      Ouvrir le portail de gestion Azure et accédez à la page de RemoteApp.
2.      Sous l’onglet **images du modèle** , cliquez sur **Télécharger** en bas de la page.
4.      Entrez un nom convivial pour votre image et spécifiez l’emplacement de compte de stockage. Assurez-vous que l’emplacement est l’emplacement de votre collection de RemoteApp ou un emplacement où vous souhaitez créer un.
5.      Lorsque vous y êtes invité, téléchargez le script sur votre ordinateur local.
6.      Copier les paramètres de la commande dans la zone de texte dans le Presse-papiers.
7.      Ouvrez une fenêtre Windows PowerShell avec des privilèges élevés.
8.      À partir de la fenêtre Windows PowerShell avec élévation de privilèges, naviguez dans le même répertoire que celui où vous avez téléchargé le script.
9.      Coller la commande copiée et appuyez sur **entrée**.

    Commence le processus de téléchargement et de durée peut dépendre de nombreux facteurs, notamment votre vitesse de réseau et de la taille de l’image

11.    Si votre téléchargement échoue en raison d’interruption du réseau ou de choses que, vous pouvez toujours reprendre le processus de téléchargement que vous avez commencé. Pour reprendre un téléchargement, exécutez le script en utilisant la même ligne de commande.

> [AZURE.WARNING] Ne modifiez jamais le script de téléchargement. Vérifications spécifiques ont été implémentées pour vous assurer que l’image satisfait les exigences de l’image et les exigences de l’application.

## <a name="common-problems"></a>Problèmes courants

- Assurez-vous que vous utilisez Windows PowerShell, pas Azure PowerShell. Vous devez installer le module PowerShell de Azure car certains modules sont nécessaires pendant le processus de téléchargement.
- Ne jamais modifier le script, les validations sont pour vous faciliter la tâche.
- Si le fichier de disque dur virtuel obtient verrouillé au moment du chargement, copiez le fichier ou déplacez-le vers un nouveau téléchargement de l’emplacement et essayez à nouveau. Il peut y avoir un processus Windows qui empêche le téléchargement.  
