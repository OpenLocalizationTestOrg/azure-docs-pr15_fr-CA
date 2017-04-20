<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Facebook au travail | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Facebook au travail."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-facebook-at-work"></a>Didacticiel : Intégration d’Azure Active Directory avec Facebook au travail

L’objectif de ce didacticiel est de vous montrer comment intégrer Facebook au travail avec Azure Active Directory (AD Azure).

Intégration de Facebook au travail avec AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à Facebook au travail 
- Vous pouvez configurer automatiquement le compte pour les utilisateurs qui ont accès à Facebook au travail
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé sur Facebook au travail (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central 

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).


## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec des étoiles de CS, vous devez les éléments suivants :

- Un abonnement Azure AD
- Facebook au travail avec authentification unique activée

Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 


## <a name="adding-facebook-at-work-from-the-gallery"></a>Ajout de Facebook au travail à partir de la galerie
Pour configurer l’intégration de Facebook au travail dans Azure annonce, vous devez ajouter Facebook au travail à partir de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter de Facebook au travail à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Facebook au travail**.

7. Dans le volet de résultats, sélectionnez **Facebook au travail**, puis cliquez sur **Terminer** pour ajouter l’application.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

Cette section explique comment permettre aux utilisateurs de s’authentifier à Facebook au travail avec leur compte Azure Active Directory, à l’aide de fédération basée sur le protocole SAML.

**Pour configurer AD Azure SSO avec Facebook au travail, effectuez les opérations suivantes :**

1.  Après l’ajout de Facebook au travail dans Azure portal classique, cliquez sur **Configuration de l’authentification unique**.

2.  Dans l’écran **Configurer l’application URL** , entrez l’URL où les utilisateurs signera dans votre Facebook au travail de votre application. Il s’agit de votre Facebook au travail locataire URL (exemple : https://example.facebook.com/). Une fois que vous avez terminé, cliquez sur **suivant**.

3.  Dans une fenêtre de navigateur web différent, ouvrez une session dans votre Facebook sur le site de la société en tant qu’administrateur.

4. Suivez les instructions à l’adresse suivante pour configurer Facebook au travail, à utiliser AD Azure comme fournisseur d’identité : [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)

5.  Une fois terminée, retournez dans les fenêtres de navigateur montrant l’Azure portal classique, cliquez sur la case à cocher pour confirmer la que fin de la procédure, puis cliquez sur **suivant** et **Terminer**.


## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Configuration automatique des utilisateurs à Facebook au travail

Annonce Azure prend en charge la possibilité de synchroniser automatiquement les détails du compte d’utilisateur affecté à Facebook au travail. La synchronisation automatique permet de Facebook au travail pour obtenir les données dont il a besoin pour autoriser des utilisateurs pour l’accès, avant leur tentative de connexion à pour la première fois. Il met également en des utilisateurs à partir de Facebook au travail lors de l’accès a été révoqué dans Azure AD.

Mise en service automatique permet de configurer en cliquant sur **configurer la mise en service de compte** dans la fenêtre de portail Azure classique.

Pour plus d’informations sur la façon de configurer la mise en service automatique, reportez-vous à la section [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)


## <a name="assigning-users-to-facebook-at-work"></a>Affectation d’utilisateurs à Facebook au travail

Pour mis en service DAS pour permettre aux utilisateurs de voir Facebook au travail sur leur panneau d’accès, il doivent être attribués accès au sein du portail classique Azure.

**Pour affecter des utilisateurs à Facebook au travail :**

1.  Sur la page de démarrage Facebook au travail dans Azure portal classique, cliquez sur **attribuer des comptes**.

2.  Dans le menu **Afficher** , sélectionnez si vous souhaitez affecter un utilisateur ou un groupe sur Facebook au travail et cliquez sur le bouton de coche.

3.  Dans la liste, sélectionnez les utilisateurs ou le groupe auquel vous souhaitez affecter Facebook au travail.

4.  Dans le pied de page, cliquez sur le bouton **affecter** .


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png




