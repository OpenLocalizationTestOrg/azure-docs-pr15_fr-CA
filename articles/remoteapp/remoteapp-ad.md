
<properties 
    pageTitle="Azure AD + Active Directory requise pour Azure RemoteApp | Microsoft Azure" 
    description="Apprenez à configurer Active Directory pour fonctionner avec Azure RemoteApp." 
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



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + Active Directory requise pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.


Pour votre collection de hybride Azure RemoteApp ou pour une collection de cloud qui vous voulez vous fédérer à l’aide de la connexion d’Active Directory, vous devez effectuer les opérations suivantes.

### <a name="connect-azure-ad-and-active-directory"></a>Connexion AD Azure et Active Directory

Si vous souhaitez connecter vos clients AD Azure et vos environnements de Active Directory sur site, utilisez connexion d’Active Directory. Il faudra vous uniquement [4 clics](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) pour connecter les deux annuaires.

Remarque - synchronisation d’annuaire est requis pour les collections hybride.

### <a name="make-sure-your-domaincom-match"></a>Assurez-vous que votre "@domain.com" correspond à
Avant de commencer, assurez-vous que l’UPN pour la forêt locale correspond au suffixe de votre domaine Active Directory Azure. 

Après avoir configuré le suffixe de domaine dans Active Directory Azure, tous les utilisateurs ouvrant une session dans Azure RemoteApp seront connecteront en tant que “user@ <the suffix you set up>». Assurez-vous que les utilisateurs peuvent également connecter avec le même user@suffix dans le domaine local. Dans certains cas vous pouvez configurer un nom de domaine dans Azure AD lors de la spécification d’un suffixe de domaine différent pour l’utilisateur montant Dans ce cas, vos utilisateurs ne seront pas en mesure de se connecter à des ordinateurs liés à un domaine ou des ressources par le biais de RemoteApp d’Azure.

Par exemple, si vous configurez votre suffixe UPN dans DAS contoso.com, mais certains utilisateurs locaux et d’Active Directory sont configurés pour se connecter avec @contoso.uk, puis ces utilisateurs ne sera pas en mesure de vous connecter correctement à la collection de ARA. Les utilisateurs UPN dans DAS et AD doivent être le même pour la connexion est possible »

### <a name="create-objects-for-azure-remoteapp"></a>Créer des objets pour Azure RemoteApp
Vous devez également créer des objets Active Directory suivant sur site :

- Un compte de service pour fournir l’accès aux ressources du domaine pour les programmes RemoteApp par l’association RDSH les points de terminaison sur le domaine local.
- Une unité d’organisation (OU) pour contenir les objets ordinateur RemoteApp. Utilisation de l’unité d’organisation est recommandée (mais pas obligatoire) pour isoler les comptes et les stratégies que vous utiliserez avec RemoteApp.

Vous avez besoin de ces deux objets lorsque vous créez votre collection RemoteApp, veillez à effectuer ces étapes, au préalable.