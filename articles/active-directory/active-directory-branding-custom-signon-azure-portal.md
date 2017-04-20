<properties
pageTitle="Personnaliser votre page de connexion dans l’aperçu Azure Active Directory | Microsoft Azure"
description="Découvrez comment ajouter une marque à la page de connexion Azure de l’entreprise"
services="active-directory"
documentationCenter=""
authors="curtand"
manager="femila"
editor=""/>

<tags
ms.service="active-directory"
ms.workload="identity"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Ajouter la marque de votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise

Pour éviter toute confusion, de nombreuses sociétés souhaitent appliquer une apparence cohérente sur tous les sites Web et les services qu’ils gèrent. Aperçu de Active Directory Azure offre cette possibilité en vous permettant de personnaliser l’apparence de la page de connexion avec votre logo d’entreprise et les jeux de couleurs personnalisés. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) La page de connexion est la page qui s’affiche lorsque vous vous connectez à Office 365 ou d’autres applications web qui utilisent Active Directory Azure comme fournisseur d’identité. Vous interagissez avec cette page pour entrer vos informations d’identification.

Si vous souhaitez afficher la marque de votre société, des couleurs et autres éléments personnalisables sur cette page, consultez les images suivantes de comprendre la différence entre les deux expériences.

La capture d’écran montre et l’exemple de la page de connexion à Office 365 sur un ordinateur de bureau **avant** une personnalisation suivants :

![Office 365-page de connexion avant de personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La capture d’écran montre et l’exemple de la page de connexion à Office 365 sur un ordinateur de bureau **après** une personnalisation suivants :

![Office 365-page de connexion après la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personnalisation de la page de connexion

En général, si vous avez besoin d’accès par navigateur à vos applications de nuage et les services que votre organisation s’abonne à, vous utilisez la page de connexion.

Si vous avez appliqué les modifications à votre page de connexion, il peut prendre une heure pour que les modifications s’affichent.

Une marque-page de connexion s’affiche uniquement lorsque vous accédez à un service avec une URL spécifique de clients tels que https://outlook.com/**contoso**.com ou https://mail. **Contoso**. com.

Lorsque vous visitez un service avec des URL spécifiques non-clients (par exemple : https://mail.office365.com), une marque non-page de connexion s’affiche. Dans ce cas, votre marque s’affiche une fois que vous avez entré votre nom d’utilisateur, ou vous avez sélectionné une mosaïque de l’utilisateur.

> [AZURE.NOTE]
>
- Votre nom de domaine doit apparaître comme « Actif » dans la partie de **domaines** du portail Azure dans lequel vous avez configuré de personnalisation. Pour plus d’informations, voir [Ajouter des noms de domaine personnalisé](active-directory-domains-add-azure-portal.md).
- Personnalisation de la page de connexion ne sont reportés à la page de Microsoft de connexion consommateur. Si vous vous connectez avec un compte Microsoft, vous pouvez consulter une liste de propriétaires de mosaïques d’utilisateur affichées par AD Azure, mais la personnalisation de votre organisation ne s’applique pas à la page ouverture de session de compte Microsoft.

Sur votre page de connexion, la case à cocher **Conserver ma connexion** permet à un utilisateur de rester connecté lorsqu’ils fermer et rouvrir leur navigateur. 

   ![Je souhaite rester connecté](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Il n’affecte pas la durée de vie de session. Vous pouvez masquer la case à cocher sur la page de connexion Azure Active Directory.
Si la case à cocher est affichée dépend de la définition de **me laisser connecté désactivé**.

   ![Je souhaite rester connecté](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Pour masquer la case à cocher, configurez ce paramètre sur **Oui**. 

> [AZURE.NOTE] Certaines fonctionnalités de SharePoint Online et Office 2010 dépendent des utilisateurs à cette case à cocher. Si vous configurez ce paramètre masqué, vos utilisateurs peuvent voir des invites supplémentaires et inattendues à l’ouverture de session.




**Pour ajouter la marque de votre répertoire de l’entreprise :**

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Sur la blade **d’utilisateurs et des groupes** , sélectionnez la **marque de la société**.

4. Sur le **les utilisateurs et groupes - marque de la société** lame, sélectionnez la commande **Modifier** .

    ![Modifier la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modifier les éléments que vous souhaitez personnaliser. Tous les éléments sont facultatifs.

6. Cliquez sur **Enregistrer**.

Il peut prendre une heure pour que les modifications apportées à la page de connexion de personnalisation qui s’affiche.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter la marque de l’entreprise spécifique à la langue](active-directory-branding-localize-azure-portal.md)
