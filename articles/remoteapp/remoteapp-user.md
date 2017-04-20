<properties
    pageTitle="Ajouter un utilisateur à votre collection Azure RemoteApp | Microsoft Azure"
    description="Apprenez à ajouter des utilisateurs à votre collection Azure RemoteApp"
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

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Comment faire pour ajouter un utilisateur à votre collection Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Avant que vos utilisateurs peuvent voir et utiliser vos applications dans Azure RemoteApp, vous devez leur accorder l’accès à votre collection. C’est la partie facile : sous l’onglet **Accès de l’utilisateur** , entrez les informations de compte pour l’utilisateur, puis cliquez sur la case à cocher.

Les informations de compte avez-vous besoin ? Qui dépend du type de collection et vous avez créé (cloud ou hybride) et si vous utilisez Office 365 ProPlus dans cette collection.

## <a name="supported-user-identities"></a>Identité des utilisateurs pris en charge

Les différents types de collection (cloud et hybride) prend en charge diverses identités d’utilisateur pour l’accès aux applications.  

Pour une collection hybride de RemoteApp, vous devez configurer une infrastructure de domaine Active Directory sur site et un locataire Azure Active Directory grâce à l’intégration de l’annuaire (et éventuellement single sign-on). En outre, vous devez créer certains objets Active Directory dans le répertoire local.  

Pour une collection de nuage de RemoteApp, tout utilisateur qui a Azure Active Directory prend en charge les identités peut être accordée les accès utilisateur aux RemoteApp pour inclure les Accounts de Microsoft.  Consultez le tableau ci-dessous.

Utilisateurs de Microsoft Office 365 sont utilisateurs Azure Active Directory. S’ils ont des hybrides d’Azure Active Directory, annuaire synchronisé des comptes, ils peuvent accéder utilisateur dans un déploiement hybride de RemoteApp.   

Vous pouvez utiliser ce tableau comme une référence rapide pour lequel identité est pris en charge dans votre collection et quelles sont les exigences d’Active Directory.

|Comptes d’utilisateurs |Nuage   |Hybride|
|--------------|--------|------|
|Compte Microsoft|     Oui|    N°|
|Azure Active Directory (AD Azure)| | |
|Azure cloud annonce uniquement    |Oui    |N° |
|ADsync avec la synchronisation de mot de passe  |Oui    |Oui    |
|ADsync sans la synchronisation de mot de passe|  Oui |N° |
|ADsync avec AD FS  |Oui    |Oui    |
|[3 rd-party Azure pris en charge les fournisseurs d’identité](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (exemple Ping) |Oui    |Oui|
|Plusieurs facteurs d’authentification    |Oui    |Oui    |

Découvrez [plus d’informations](remoteapp-ad.md) sur la configuration d’Active Directory pour RemoteApp.


> [AZURE.NOTE] Les utilisateurs Azure Active Directory doivent être entre le locataire associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l’onglet **paramètres** dans le portail. Voir [Modifier le locataire Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d’informations.)

## <a name="office-365-proplus-user-account-information"></a>Informations sur le compte Office 365 ProPlus utilisateur
Si vous utilisez l’image du modèle Office 365 ProPlus dans votre collection, *ou* si vous avez créé une image personnalisée qui utilise Office 365, vous pouvez uniquement ajouter des utilisateurs Azure Active Directory qui ont des abonnements d’Office 365 pour le domaine par défaut de votre abonnement. Pour plus d’informations, reportez-vous [à l’aide d’Office 365 avec Azure RemoteApp](remoteapp-o365.md) .
