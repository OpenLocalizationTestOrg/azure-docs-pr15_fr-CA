
<properties 
    pageTitle="Comment utiliser RemoteApp d’Azure avec des comptes d’utilisateur Office 365 | Microsoft Azure"
    description="Découvrez comment utiliser RemoteApp d’Azure avec des comptes d’utilisateurs Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Comment utiliser RemoteApp d’Azure avec des comptes d’utilisateur Office 365

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Si vous avez un abonnement à Office 365 vous avez Azure Active Directory qui stocke vos noms d’utilisateur et les mots de passe utilisés pour accéder aux services Office 365. Par exemple, lorsque vos utilisateurs activer Office 365 ProPlus qu’ils s’authentifient Azure AD pour vérifier les licences. La plupart des clients souhaite utiliser le même répertoire avec Azure RemoteApp.

Si vous déployez Azure RemoteApp, vous utilisez probablement un abonnement Azure qui est associé à une publicité Azure différente. Pour utiliser votre répertoire Office 365, vous devez déplacer l’abonnement Azure dans ce répertoire.

Pour des informations sur la façon de déployer les applications clientes Office 365, voir [comment utiliser votre abonnement à Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Phase 1 : Enregistrer votre abonnement gratuit d’Office 365 Azure Active Directory
Si vous utilisez le portail classique Azure, procédez comme dans [le livre de votre abonnement gratuit d’Azure Active Directory](https://technet.microsoft.com/library/dn832618.aspx) pour obtenir l’accès administratif à votre annonce Azure via le portail de gestion Azure. À la suite de ce processus doit pouvoir se connecter au portail Azure et consultez votre répertoire – à ce stade vous ne verrez pas beaucoup plus car l’abonnement Azure complète que vous utilisez avec Azure RemoteApp est dans un répertoire différent.

N’oubliez pas le nom et le mot de passe du compte d’administrateur que vous avez créé à cette étape : ils seront nécessaires à la Phase 2.

Si vous utilisez le portail d’Azure, Découvrez [comment vous inscrire et activer une libre Azure Active Directory à l’aide du portail Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Phase 2 : Modifier l’annonce Azure associé à votre abonnement Azure.
Nous allons modifier votre abonnement Azure à partir de son répertoire courant dans le répertoire Office 365 que nous avons travaillé avec à la Phase 1.

Suivez les instructions de la rubrique [modification le locataire Azure Active Directory dans Azure RemoteApp](remoteapp-changetenant.md). Une attention particulière aux étapes suivantes :

- Étape #1 : Si vous avez déployé Azure RemoteApp (ARA) dans cet abonnement, assurez-vous que vous supprimez tous les comptes d’utilisateur AD Azure à partir de toutes les collections de ARA tout d’abord, avant d’essayer autre chose. Sinon, vous pouvez envisager la suppression de toutes les collections existantes.
- Étape #2 : Il s’agit d’une étape essentielle. Vous devez utiliser un compte Microsoft (par exemple, @outlook.com) en tant qu’un administrateur de Service sur l’abonnement ; c’est parce que nous ne pouvons pas avoir des comptes d’utilisateurs à partir de l’annonce existante sur Azure lié à la souscription – dans ce cas, nous ne pourrez pas les déplacer vers une autre annonce Azure.
- Étape #4 : Lors de l’ajout d’un répertoire existant, le système vous demande de vous connecter avec le compte administrateur pour ce répertoire. Assurez-vous d’utiliser le compte administrateur de la Phase 1.
- Étape #5 : Modifier le répertoire parent de l’abonnement à votre répertoire d’Office 365. Le résultat final doit être que sous Paramètres -> abonnements votre abonnement indique le répertoire Office 365. 
![Modifiez le répertoire parent de l’abonnement](./media/remoteapp-o365user/settings.png)
 

À ce stade, votre abonnement Azure RemoteApp est associé à votre Office 365 AD Azure ; Vous pouvez utiliser les comptes utilisateur Office 365 avec Azure RemoteApp !




