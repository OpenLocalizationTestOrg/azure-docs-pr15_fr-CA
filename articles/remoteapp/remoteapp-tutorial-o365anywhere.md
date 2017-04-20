<properties
   pageTitle="Obtenir la même expérience Office 365 sur n’importe quel périphérique avec Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment partager n’importe quelle application Office 365 avec vos utilisateurs à l’aide de RemoteApp d’Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Obtenir la même expérience Office 365 sur n’importe quel périphérique avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Cet article couvre le déploiement d’Office 365 sur n’importe quel périphérique de votre société. Les utilisateurs peuvent obtenir les mêmes capacités et l’expérience de l’interface utilisateur Android, Apple et Windows.

Pour ce faire, nous sera à l’aide de RemoteApp d’Azure par d’hébergement sur des machines virtuelles accessibles en échelle dans Azure et les utilisateurs peuvent se connecter à Office 365. Cet ensemble de machines virtuelles, nous appelons une collection « nuage ».

## <a name="create-a-cloud-collection"></a>Créer une collection de nuage

Tout d’abord une fois que vous avez créé un compte Azure, accédez à **RemoteApp** en cliquant sur le lien sur le côté gauche.
![Affichage de RemoteApp Azure sur le portail Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Puis continuez en cliquant sur **Nouveau** en bas et « création rapide » une collection. Fournir un nom, la région, l’abonnement, le plan et l’image « Proffesional Office 2013 » que nous fournissons.
![Boîte de dialogue Créer](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Une fois que vous avez terminé le processus de création de la collection du formulaire doit démarrer. Cela peut prendre une heure ou de l’opération.

![En attente](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Une fois le processus terminé, il doit ressembler à ceci. Si vous cliquez sur la **publication** , nous pouvons voir que la plupart des applications Office ont déjà été publiées pour nous.
![Collection créée](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Applications publiées](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

À ce stade vous pouvez également ajouter plus d’utilisateurs qui ont accès à cette collection en cliquant sur **L’accès des utilisateurs**.
![Configurer l’accès utilisateur](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Maintenant nous allons essayer de vous connecter à Office 365 !

## <a name="connect-to-office-365"></a>Se connecter à Office 365

Nous pour [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/)de tête sur, faites défiler vers le bas et cliquez sur **télécharger les clients** pour installer le client Azure RemoteApp sur le périphérique que vous êtes sur. Les captures d’écran ci-dessous sont pour Windows.

Une fois le démarrage de l’application vous devez vous connecter avec votre compte Microsoft (anciennement appelé un « ID Live »), utilisez le même que celui que votre compte Azure pour l’instant. Lorsque vous êtes connecté dans, vous doit afficher une notification de nouveau invitations, cliquez sur il et vous devez voir une liste comme ci-dessous. Accepter l’invitation correspondant à votre adresse de courriel du propriétaire du compte Azure.

![Nouvelle invitation](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Quoi il ressemble lorsqu’il existe des invitations de nouveau.

![Accepter une application](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Dès que vous acceptez l’invitation, que vous devriez voir toutes les applications Office dans le client Azure RemoteApp.

![Liste des applications](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Lorsque vous cliquez sur ces que l’application doit démarrer sur l’ordinateur virtuel Azure et vous doivent être tous ensemble ! Profitez !

![démarrage](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
