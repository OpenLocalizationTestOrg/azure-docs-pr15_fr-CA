<properties
    pageTitle="Publier une application dans Azure RemoteApp | Microsoft Azure"
    description="Apprenez à publier des applications et des ressources dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Comment publier une application dans RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Après avoir créé votre collection RemoteApp, vous devez publier les applications ou les ressources que vous souhaitez rendre disponible pour vos utilisateurs. Les images du modèle fournis avec votre abonnement ont uniquement quelques applications publiées par défaut - à partager les autres applications, vous devez les publier.

> [AZURE.NOTE] Vous devez mettre à jour une application ? Vous devez [mettre à jour l’image](remoteapp-update.md) tout d’abord.

Dans l’onglet **publication** dans le portail, cliquez sur **Publier**. Vous pouvez ajouter une application à partir du menu **Démarrer** de l’image de votre modèle ou fournir le chemin d’accès vers lequel l’application est installée sur l’image du modèle. Si vous choisissez d’ajouter dans le menu **Démarrer** , cliquez sur l’application de publier à partir de la liste. Si vous choisissez de fournir le chemin d’accès à l’application, entrez un nom pour l’application et le chemin d’accès à l’application. Utiliser des variables dans le chemin d’accès - par exemple, « % lecteur_système % » au lieu de « c:\".

> [AZURE.NOTE] Si vous souhaitez ajouter votre application dans le menu **Démarrer** , vous devez avoir *ajouté cette application à la * *Démarrer* * menu sur votre image modèle.* Dans le cas contraire, RemoteApp verra uniquement quel *est* dans le menu **Démarrer** , et vous serez confondus. 

>Pour vous assurer que votre application se trouve dans le menu **Démarrer** , placez un fichier de raccourci - **.lnk** - à l’intérieur du dossier %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.

> Si vous avez oublié d’ajouter l’application au menu **Démarrer** lors de la création du modèle, cliquez sur Ajouter le chemin d’accès à l’application. (Ou recréer votre image du modèle, mais c’est un peu plus de travail.)


 