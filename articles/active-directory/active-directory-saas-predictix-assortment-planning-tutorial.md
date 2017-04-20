<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory planification d’assortiment Predictix | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de la planification d’assortiment Predictix."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Didacticiel : Intégration d’Azure Active Directory planification d’assortiment Predictix

Dans ce didacticiel, vous allez apprendre à intégrer la planification d’assortiment Predictix avec Azure Active Directory (AD Azure).

Intégration de la planification d’assortiment Predictix avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à la planification d’assortiment Predictix
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur Planification d’assortiment Predictix (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec planification d’assortiment Predictix, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une planification d’assortiment Predictix connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Microsoft Azure AD Single Sign-On dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de Predictix planification d’assortiment de la galerie
2. Configuration et test de Microsoft Azure AD Single Sign-On


## <a name="adding-predictix-assortment-planning-from-the-gallery"></a>Ajout de Predictix planification d’assortiment de la galerie
Pour configurer l’intégration de la planification d’assortiment Predictix AD Azure, vous devez ajouter Predictix planification d’assortiment de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter la planification d’assortiment Predictix à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Predictix planification d’assortiment**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_01.png)

7. Dans le volet résultats, sélectionnez **Planification d’assortiment Predictix**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_02.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configuration et test de Microsoft Azure AD Single Sign-On
Dans cette section, vous configurez et testez Microsoft Azure AD Single Sign-On avec planification d’assortiment Predictix basée sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans la planification d’assortiment Predictix à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans la planification d’assortiment Predictix doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de l' **utilisateur** dans la planification d’assortiment Predictix.

Pour configurer et tester Microsoft Azure AD Single Sign-On avec planification d’assortiment Predictix, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - pour tester Microsoft Azure AD Single Sign-On avec Britta Simon.
3. **[Création d’un utilisateur test planification d’assortiment Predictix](#creating-a-predictix-price-reporting-test-user)** - avoir un équivalent de Britta Simon dans la planification d’assortiment Predictix qui est lié à la représentation sous forme de publicité Azure de lui.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon à utiliser Microsoft Azure AD Single Sign-On.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuration Microsoft Azure AD unique Single Sign-On

Dans cette section, vous activez Microsoft Azure AD Single Sign-On dans le portail classique et configurez l’authentification unique dans votre application de planification d’assortiment Predictix.


**Pour configurer Microsoft Azure AD Single Sign-On avec planification d’assortiment Predictix, effectuez les opérations suivantes :**

1. Dans le portail classique, dans la page **Planification d’assortiment Predictix** d’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la planification d’assortiment Predictix** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de planification d’assortiment Predictix selon le modèle suivant : **https://\<société nom-prix\>.ap.predictix.com/sso/request**.
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique à la planification d’assortiment Predictix** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, contactez l’équipe de support de planification d’assortiment Predictix et de leur présenter les éléments suivants :

    • Le certificat téléchargé

    • L' **ID de l’entité**

    • L' **URL de l’authentification unique de SAML**

    • Le **les URL du Service d’authentification unique**

6. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
 
    ![Azure AD unique Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-predictix-assortment-planning-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-predictix-assortment-planning-test-user"></a>Création d’un utilisateur test Predictix planification d’assortiment

Dans cette section, vous créez un utilisateur appelé Britta Simon dans la planification d’assortiment Predictix. Travaillez avec l’équipe de support de planification d’assortiment Predictix pour ajouter les utilisateurs de la plate-forme Predictix planification d’assortiment.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès à la planification d’assortiment Predictix.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à la planification d’assortiment Predictix, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Planification d’assortiment Predictix**.

    ![Configurer l’authentification unique](./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_predictixassortmentplanning_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre configuration de Single Sign-On Microsoft Azure AD à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque de planification d’assortiment Predictix dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application de planification d’assortiment Predictix.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-predictix-assortment-planning-tutorial/tutorial_general_205.png
