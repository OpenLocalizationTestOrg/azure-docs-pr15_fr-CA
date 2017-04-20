<properties
   pageTitle="Résolution des problèmes : Élément de « Active Directory » est manquant ou n’est pas disponible | Microsoft Azure "
   description="Que faire lorsque l’élément de menu Active Directory n’apparaît pas dans le portail de gestion Azure."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Résolution des problèmes : Élément de « Active Directory » est manquant ou n’est pas disponible

La plupart des instructions pour l’utilisation des services et des fonctionnalités Active Directory de Azure commencent avec « Aller vers le portail de gestion Azure et cliquez sur **Active Directory**. » Mais que faire si l’élément de menu ou d’extension de Active Directory ne s’affiche pas ou si elle est marquée **N’est pas disponible**? Cette rubrique est conçue pour vous aider. Il décrit les conditions de **Active Directory** ne s’affiche pas ou n’est pas disponible et explique comment procéder.

## <a name="active-directory-is-missing"></a>Active Directory est manquant

En général, un élément de **Active Directory** s’affiche dans le menu de navigation de gauche. Les instructions de procédures d’Azure Active Directory supposent que cet élément est dans l’affichage.

![Capture d’écran : Active Directory dans Azure](./media/active-directory-troubleshooting/typical-view.png)

Lorsqu’une des conditions suivantes est vraie, l’élément Active Directory s’affiche dans le menu de navigation de gauche. Dans le cas contraire, l’élément n’apparaît pas.

* L’utilisateur actuel connecté avec un compte Microsoft (anciennement appelé un de Windows Live ID).

    OU

* Le locataire Azure a un répertoire et que le compte actuel est un administrateur d’annuaire.

    OU

* Le locataire Azure a au moins un espace de noms de contrôle d’accès AD de Azure (ACS). Pour plus d’informations, consultez [Namespace de contrôle d’accès](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OU

* Le locataire Azure a au moins un fournisseur d’authentification à plusieurs facteurs Azure. Pour plus d’informations, consultez [Administration de fournisseurs d’authentification selon plusieurs facteurs Azure](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Pour créer un espace de noms du contrôle d’accès ou d’un fournisseur d’authentification à facteurs multiples, cliquez sur **+ Nouveau** > **Application Services** > **Active Directory**.

Pour obtenir des droits d’administration dans un répertoire, demandez à un administrateur affecter un rôle d’administrateur à votre compte. Pour plus d’informations, consultez [affectation rôles d’administrateur](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory n’est pas disponible

Lorsque vous cliquez sur **+ Nouveau** > les**Services d’application**, un élément de **Active Directory** apparaît. En particulier, l’élément de Active Directory s’affiche lorsque les fonctionnalités Active Directory, par exemple le répertoire, de contrôle d’accès ou de fournisseur d’authentification selon plusieurs facteurs, sont disponibles à l’utilisateur actuel.

Toutefois, pendant le chargement de la page, l’élément est grisé et est marqué comme **Non disponible**. Il s’agit d’un état temporaire. Si vous attendez quelques secondes, l’élément devienne disponible. Si le délai est prolongé, l’actualisation de la page web souvent résout le problème.

![Capture d’écran : Active Directory n’est pas disponible](./media/active-directory-troubleshooting/not-available.png)
