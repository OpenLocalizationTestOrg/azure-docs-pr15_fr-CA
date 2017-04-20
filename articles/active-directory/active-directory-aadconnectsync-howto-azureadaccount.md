<properties
    pageTitle="Azure Connect de AD sync : comment gérer le compte de service AD Azure | Microsoft Azure"
    description="Cette rubrique explique comment restaurer le compte de service AD Azure."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, comment faire pour réinitialiser le mot de passe pour la compte du service Connecteur la synchronisation Azure Connect de publicité"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure Connect de AD sync : comment gérer le compte de service AD Azure
Le compte de service utilisé par le connecteur Active Directory Azure est supposé pour être le service gratuit. Si vous devez réinitialiser ses informations d’identification, cette rubrique est pour vous. Par exemple, si un administrateur Global a par erreur réinitialiser le mot de passe du compte de service à l’aide de PowerShell.

## <a name="reset-the-credentials"></a>Réinitialiser les informations d’identification
Si le compte de service défini sur le connecteur Active Directory Azure ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Connectez-vous au serveur de synchronisation Azure Connect de AD et démarrer PowerShell.
2. Exécutez `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount d’applet de commande PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournissent des informations d’identification administrateur Global de publicité Azure.

Cette applet de commande réinitialise le mot de passe pour le compte de service et mettre à jour dans Active Directory Azure et dans le moteur de synchronisation.

## <a name="known-issues-these-steps-can-solve"></a>Ces étapes peuvent résoudre les problèmes connus
Cette section est une liste d’erreurs signalés par les clients qui ont été résolus par une identification de réinitialisation sur le compte de service AD Azure.

-----------
Événement 6900  
Le serveur a rencontré une erreur inattendue lors du traitement d’une notification de modification de mot de passe :  
AADSTS70002 : Erreur validation informations d’identification. AADSTS50054 : L’ancien mot de passe est utilisé pour l’authentification.

----------
Événement 659  
Erreur lors de la configuration de la synchronisation de stratégie de mot de passe de récupération. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException :  
AADSTS70002 : Erreur validation informations d’identification. AADSTS50054 : L’ancien mot de passe est utilisé pour l’authentification.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de vue d’ensemble**

- [Azure Connect de AD sync : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration des identités sur site à Azure Active Directory](active-directory-aadconnect.md)
