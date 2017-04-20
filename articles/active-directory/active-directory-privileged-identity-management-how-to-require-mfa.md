<properties
   pageTitle="Comment requérir une authentification multifacteur | Microsoft Azure"
   description="Découvrez comment requièrent une authentification multifacteur (AMF) pour les identités privilégiées avec l’extension Azure privilégié identité gestion d’Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Comment requérir l’AMF dans la gestion des identités Azure AD privilégié

Nous vous recommandons de dont vous avez besoin d’une authentification multifacteur (AMF) pour tous les administrateurs. Cela réduit le risque d’une attaque en raison d’un mot de passe compromis.

Vous pouvez exiger que les utilisateurs effectuer une stimulation de l’AMF lorsqu’ils se connectent. Le billet de blog [AMF pour Office 365 et AMF pour Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) compare ce qui est inclus dans un abonnement Office et Azure, avec les fonctionnalités contenues dans l’offre de Microsoft Azure une authentification multifactorielle.

Vous pouvez également exiger que les utilisateurs effectuer une stimulation AMF leur activation d’un rôle dans Azure AD PIM. De cette façon, si l’utilisateur n’a pas terminé une stimulation AMF lors de leur signature, ils devront faire par PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Nécessitant AMF dans Azure AD privilégié la gestion des identités

Lorsque vous gérez des identités PIM en tant qu’un administrateur de rôle privilégié, vous pouvez voir les alertes qui vous recommandons AMF pour les comptes privilégiés. Cliquez sur l’alerte de sécurité dans le tableau de bord PIM, et une nouvelle lame s’ouvre avec une liste des comptes d’administration qui ne nécessitent pas AMF.  Vous pouvez exiger AMF en sélectionnant plusieurs rôles, puis en cliquant sur le bouton **Fix** , ou vous pouvez cliquez sur les ellipses en regard des rôles individuels et puis cliquez sur le bouton **Fix** .

> [AZURE.IMPORTANT] Droit de la souris maintenant, Azure AMF ne fonctionne qu’avec les travaux ou les comptes de l’établissement, pas les comptes Microsoft (généralement un compte personnel qui est utilisé pour vous connecter à des services de Microsoft comme Skype, Xbox, Outlook.com, etc..). De ce fait, toute personne à l’aide d’un compte Microsoft ne peut pas être un administrateur admissible, car ils ne peuvent pas utilisent AMF pour activer leurs rôles. Si ces utilisateurs doivent continuer à gérer les charges de travail à l’aide d’un compte Microsoft, élever les administrateurs permanente pour le moment.

En outre, vous pouvez modifier l’exigence de l’AMF pour un rôle spécifique en cliquant sur celui-ci dans la section rôles du tableau de bord PIM. Ensuite, cliquez sur **paramètres** dans la lame de rôle, puis en sélectionnant **Activer** sous authentification à plusieurs facteurs.

## <a name="how-azure-ad-pim-validates-mfa"></a>Comment Azure AD PIM valide AMF

Il existe deux options pour validation AMF lorsqu’un utilisateur Active un rôle.

L’option la plus simple est de compter sur Azure AMF pour les utilisateurs qui sont activation d’un rôle de privilège. Pour cela, vérifiez d’abord que ces utilisateurs sont sous licence, le cas échéant et enregistrement Azure AMF. Plus d’informations sur la procédure à suivre est [mise en route avec authentification à plusieurs facteurs Azure dans le nuage](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md). Il est recommandé, mais pas obligatoire, que vous configurez Azure AD pour appliquer AMF pour ces utilisateurs lorsqu’ils se connectent. C’est parce que les contrôles de l’AMF seront effectuées par PIM de publicité Azure lui-même.

Si les utilisateurs s’authentifient sur site vous pouvez également avoir votre fournisseur d’identité est responsable de l’AMF. Par exemple, si vous avez configuré Active Directory Federation Services pour exiger l’authentification par carte à puce avant d’accéder à des annonces Azure, [sécurisation des ressources de cloud avec authentification à plusieurs facteurs Azure et AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md) inclut des instructions pour la configuration d’ADFS pour envoyer des demandes à AD Azure. Lorsqu’un utilisateur essaie d’activer un rôle, Azure AD PIM accepte qu’AMF a déjà été validé pour l’utilisateur une fois qu’il a reçu les affirmations appropriées.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
