<properties
   pageTitle="Azure AD Connect : Fonctionnalités dans Aperçu | Microsoft Azure"
   description="Cette rubrique décrit dans plus de fonctionnalités détail qui se trouvent dans l’aperçu dans Azure Connect d’Active Directory."
   services="active-directory"
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
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Plus de détails sur les fonctionnalités de visualisation
Cette rubrique décrit comment utiliser les fonctionnalités actuellement dans l’aperçu.

## <a name="group-writeback"></a>Écriture différée du groupe
L’option pour l’écriture différée du groupe dans les fonctionnalités en option vous permettra d’écriture différée **Office 365 groupes** d’une forêt avec Exchange installé. Il s’agit d’un groupe qui est toujours gravé dans le nuage. Si vous avez Exchange sur site vous pouvez réécrire ces groupes dans locaux ainsi des utilisateurs avec une boîte aux lettres de Exchange sur site peuvent envoyer et recevoir des e-mails de ces groupes.

Vous trouverez plus d’informations sur Office 365 groupes et comment les utiliser [ici](http://aka.ms/O365g).

Ce groupe sera représenté sous la forme d’un groupe de distribution dans sur site les services AD DS. Votre serveur d’Exchange sur site doit être mise à jour cumulative Exchange 2013 8 (publié en mars 2015) ou 2016 Exchange à reconnaître ce nouveau type de groupe.

**Notes lors de la visualisation**

- L’attribut de carnet d’adresses n’est actuellement pas rempli dans l’aperçu. Sans cet attribut, le groupe ne sera pas visible dans la liste d’adresses globale. Pour remplir cet attribut le plus simple est d’utiliser l’applet de commande PowerShell de Exchange `update-recipient`.
- Seules les forêts avec le schéma Exchange sont des cibles valides pour les groupes. Si aucun Exchange a été détecté, l’écriture différée du groupe ne sera pas possible d’activer.
- Uniquement les déploiements d’entreprise basée sur une forêt Exchange sont actuellement pris en charge. Si vous avez plus d’une organisation Exchange sur les sites, vous devrez une solution sur site GALSync ces groupes apparaissent dans vos autres forêts.
- La fonctionnalité d’écriture différée de groupe ne gère pas actuellement les groupes de sécurité ou des groupes de distribution.

>[AZURE.NOTE] Un abonnement Azure AD Premium est requis pour l’écriture différée du groupe.

## <a name="user-writeback"></a>Écriture différée de l’utilisateur
> [AZURE.IMPORTANT] La fonctionnalité d’aperçu d’écriture différée utilisateur a été supprimée dans la mise à jour août 2015 Azure Connect d’Active Directory. Si vous l’avez activé, vous devez désactiver cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes
Continuer l' [installation personnalisée de Azure Connect d’Active Directory](./connect/active-directory-aadconnect-get-started-custom.md).

Pour en savoir plus sur [l’intégration des identités avec Azure Active Directory local](active-directory-aadconnect.md).
