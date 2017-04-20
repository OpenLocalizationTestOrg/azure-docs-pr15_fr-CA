<properties
    pageTitle="Le Gestionnaire de licences Azure RemoteApp | Microsoft Azure"
    description="Apprenez comment fonctionnement la gestion des licences dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Comment le Gestionnaire de licences fonctionne dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Si vous avez défini votre service Azure RemoteApp, créé vos modèles et que vous êtes prêt à publier des applications à vos utilisateurs. Mais il existe encore une dernière chose à savoir : le Gestionnaire de licences. Simplement comment fonctionne licence pour RemoteApp et pour les applications que vous le partager par le biais de RemoteApp ?

RemoteApp ne nécessite pas les licences Windows ou les licences d’accès client du Bureau à distance. Votre abonnement prend en charge le côté RemoteApp lui-même. (Consultez les détails de la [tarification](https://azure.microsoft.com/pricing/details/remoteapp)).

Si vous utilisez une des images qui est inclus dans votre abonnement, vous pouvez partager des applications installées sur cette image sans avoir besoin d’une licence distincte. Par exemple, si l’image du modèle Windows Server 2012 R2 vous permet de créer votre collection, vous pouvez partager System Center Endpoint Protection avec vos utilisateurs. Les seules exceptions à cette règle sont Office 365 ProPlus, qui requiert un abonnement distinct, et Office 2013, qui ne peut pas être partagée dans une collection de production.

Si vous souhaitez utiliser l’image du modèle Office 365 qui accompagne RemoteApp, vous devez disposer d’un plan d’Office 365 ProPlus *existant* . Il en est de même pour n’importe quelle application Office 365 que vous publiez à l’aide d’un modèle personnalisé. Vous devez activer les applications avec votre abonnement. Cela est vrai pour les abonnements d’essai et payées. Si vous souhaitez utiliser l’image du modèle Office 365 au cours de l’essai, *et vous n’êtes pas déjà abonné*, accédez à la page d’Office 365 à [s’inscrire](https://go.microsoft.com/fwlink/p/?LinkID=403802) pour un abonnement d’essai. Consultez [comment RemoteApp et Bureau fonctionnent ensemble ?](remoteapp-o365.md) pour plus d’informations.

Si, au cours de la période d’évaluation, vous ne souhaitez obtenir un abonnement d’évaluation d’Office 365, utilisez le 2013 d’Office Professionnel Plus modèle d’image qui est fourni avec RemoteApp. Cette image de modèle peut uniquement être utilisée pendant 30 jours et ne peut pas être convertie en une collection payante.

Pour les autres applications, vous devez vous assurer que vous disposez de la licence de partager l’application.

Qui justifie, droit ? Vous pouvez publier n’importe quelle application que vous êtes légalement habilité à partager. Et vous devez vous assurer que vous êtes vraiment autorisé à partager vos programmes.

Veuillez noter que vous ne pouvez pas utiliser un accord de licence d’accès client ou de licence en Volume dans une collection de nuage. Vous *pouvez* d’utiliser un accord de licence en Volume pour activer des applications dans votre collection hybride (à l’exception d’Office). Vous devez uniquement les installer sur votre image du modèle à partir du média de licence en Volume. Suivez les informations du fournisseur de l’application pour installer les licences dans un environnement de bureau à distance.
