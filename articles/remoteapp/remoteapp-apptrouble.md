<properties 
    pageTitle="Résolution des problèmes de RemoteApp Azure - échecs de lancement et de connexion d’Application | Microsoft Azure" 
    description="Découvrez comment résoudre les problèmes de démarrage et de connexion aux applications dans Azure RemoteApp." 
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
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Résoudre les problèmes de RemoteApp Azure - échecs de lancement et de connexion d’Application 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Applications hébergées dans Azure RemoteApp peuvent échouer pour plusieurs raisons différentes de lancement. Cet article explique les différentes raisons et les utilisateurs peuvent recevoir lorsque des messages d’erreur lors du lancement des applications. Il parle également les échecs de connexion. (Mais cet article ne décrit pas de problèmes lors de la signature dans le client Azure RemoteApp.)  

Lisez la suite pour plus d’informations sur les messages d’erreur courants en raison d’échecs de connexion d’exécution et d’application.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Nous nous réunissons configurer... Réessayez dans 10 minutes.

Cette erreur signifie Qu'azure RemoteApp est mise à l’échelle pour répondre à la nécessité de la capacité de vos utilisateurs. En arrière-plan plus Azure VM de RemoteApp instances est créées pour gérer les besoins de vos utilisateurs. En général, cela prend environ cinq minutes mais peut prendre jusqu'à 10 minutes. Parfois, cela ne se produit pas assez rapidement et les ressources nécessaires immédiatement. Par exemple un scénario 9 AM où de nombreux utilisateurs ont besoin d’utiliser votre application dans Azure RemoteAppn en même temps. Si c’est le cas, nous pouvons activer **mode de capacité** sur le back-end. Pour ce faire ouvrez un ticket de Support d’Azure, et ou envoyez-nous un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Veillez à inclure votre ID d’abonnement dans la demande.  

![Nous recevons configurer](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Ne peut pas reconnexion automatique à vos applications, Veuillez relancer votre application  

Ce message d’erreur est souvent si vous utilisiez Azure RemoteApp et puis mettez votre ordinateur en veille plus de 4 heures et puis a éveillé votre PC et le client d’Azure RemoteApp tente automatiquement se reconnecter et de délai d’attente a été dépassé.  Demandez aux utilisateurs d’accéder à l’application et essayez de l’ouvrir à partir du client Azure RemoteApp.

![Pourrait pas la reconnexion automatique à vos applications](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problèmes avec le profil temporaire 

Cette erreur se produit lors de l’échec du montage de votre profil utilisateur (disque de profil d’utilisateur) et l’utilisateur reçoit un profil temporaire.  Les administrateurs doivent naviguer à la collection dans le portail Azure et accédez à l’onglet **Sessions** et essayez de **Déconnecter** l’utilisateur. Cela va forcer la saturation du journal sur la session de l’utilisateur -, puis demandez à l’utilisateur de tenter de lancer une application à nouveau. En cas d’échec contacter le support technique Azure et ou envoyez-nous un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>RemoteApp Azure a cessé de fonctionner

Ce message d’erreur signifie que le client Azure RemoteApp rencontre un problème et doit être redémarré. Demander aux utilisateurs de fermer : sélectionnez **Fermer le programme** et puis exécutez à nouveau le client Azure RemoteApp.  Si le problème continue d’ouvrir et intervention Azure et ou envoyez-nous un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![RemoteApp Azure a cessé de fonctionner](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Une erreur s’est produite lors de la connexion Bureau à distance a été l’accès à cette ressource. Essayez de vous reconnecter ou contactez votre administrateur système

Il s’agit d’un message d’erreur générique - contacter le support technique Azure et ou envoyez-nous un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) afin que nous puissions étudier. 

![Message d’Azure RemoteApp générique](./media/remoteapp-apptrouble/ra-apptrouble4.png) 