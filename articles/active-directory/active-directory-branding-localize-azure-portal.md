<properties
pageTitle="Ajouter la marque de votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise spécifique à la langue | Microsoft Azure"
description="Apprendre à ajouter une société spécifique du langage personnalisation des images et du texte à une page de connexion Azure"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Ajouter la marque de votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise spécifique à la langue

Pour éviter toute confusion, de nombreuses sociétés souhaitent appliquer une apparence cohérente sur tous les sites Web et les services qu’ils gèrent. Aperçu de Active Directory Azure offre cette possibilité en vous permettant de personnaliser l’apparence de la page de connexion avec votre logo d’entreprise et les jeux de couleurs personnalisés. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) La page de connexion est la page qui s’affiche lorsque vous vous connectez à Office 365 ou d’autres applications web qui utilisent Active Directory Azure comme fournisseur d’identité. Vous interagissez avec cette page pour entrer vos informations d’identification.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personnalisation de la page de connexion pour une autre langue

Vous pouvez ajouter des éléments de langage spécifiques à votre page de connexion personnalisée uniquement si vous avez déjà créé une page de connexion personnalisée comme décrit dans [Ajout de marque à votre page de connexion de l’entreprise](active-directory-branding-custom-signon-azure-portal.md). Vous pouvez configurer une page de connexion par répertoire avec un ensemble par défaut des éléments personnalisables. Une fois que vous avez configuré l’ensemble par défaut des éléments de page, vous pouvez configurer plusieurs versions pour différents paramètres régionaux. Vous pouvez également mélanger et correspondent aux différents éléments. Par exemple, vous pouvez :

- Créer une **image de la page de connexion** qui fonctionne pour toutes les cultures, puis créer des versions pour l’anglais et le Français par défaut. Lorsque vous définissez vos navigateurs à l’un de ces deux langues, l’image spécifique à la langue s’affiche, tandis que l’illustration par défaut s’affiche pour toutes les autres langues.

- Configurer plusieurs logos pour votre organisation (par exemple, les versions japonais ou l’hébreu).

Nous vous conseillons de conserver le nombre de variantes de langue faible, pour des raisons de performances et de maintenance.

**Pour ajouter la marque de votre répertoire de l’entreprise :**

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour le répertoire.

2.  Sélectionnez des **services supplémentaires**, entrez les **utilisateurs et les groupes** dans la zone de texte et puis sélectionnez **entrée**.

    ![Gestion des utilisateurs ouverture](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. Sur la blade **d’utilisateurs et des groupes** , sélectionnez la **marque de la société**.

4. Sur le **les utilisateurs et groupes - personnalisation de la société** lame, sélectionnez la commande **Ajouter la langue** .

    ![Ajouter des éléments de personnalisation spécifiques à une langue](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modifier les éléments que vous souhaitez personnaliser. Tous les éléments sont facultatifs.

6. Cliquez sur **Enregistrer**.

Il peut prendre une heure pour que les modifications apportées à la page de connexion de personnalisation qui s’affiche.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter la marque de votre page de connexion de l’entreprise](active-directory-branding-custom-signon-azure-portal.md)
