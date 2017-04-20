<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory 8 x 8 virtuel Office | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et 8 x 8 bureau virtuel."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Didacticiel : Intégration d’Azure Active Directory avec Office virtuelle de 8 x 8

L’objectif de ce didacticiel est de vous montrer comment intégrer le bureau virtuel de 8 x 8 avec Azure Active Directory (AD Azure).

Intégration de 8 x 8 bureau virtuel avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès au bureau virtuel de 8 x 8
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur 8 x 8 Virtual Office (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec 8 x 8 Virtual Office, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une 8 x 8 Virtual Office connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est pour vous permettre de tester Microsoft Azure AD Single Sign-On dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de 8 x 8 Virtual Office à partir de la galerie
2. Configuration et test de Microsoft Azure AD Single Sign-On


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Ajout de 8 x 8 Virtual Office à partir de la galerie
Pour configurer l’intégration de 8 x 8 Virtual Office dans AD Azure, vous devez ajouter 8 x 8 Virtual Office à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des 8 x 8 Virtual Office à partir de la galerie, procédez comme suit :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **8 x 8 Virtual Office**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. Dans le volet de résultats, sélectionnez le **bureau virtuel de 8 x 8**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![L’application de la sélection dans la galerie](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configuration et test de Microsoft Azure AD Single Sign-On
L’objectif de cette section est de vous montrer comment configurer et tester Microsoft Azure AD Single Sign-On avec 8 x 8 Qu'office virtuel basé sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir ce que l’utilisateur équivalent virtuel Office à un utilisateur dans AD Azure est de 8 x 8. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans 8 x 8 bureau virtuel doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans un bureau virtuel de 8 x 8.

Pour configurer et tester Microsoft Azure AD Single Sign-On avec 8 x 8 Virtual Office, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration Microsoft Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - pour tester Microsoft Azure AD Single Sign-On avec Britta Simon.
3. **[Création d’un utilisateur de test de bureau virtuel 8 x 8](#creating-a-8x8-virtual-office-test-user)** - avoir un équivalent de Britta Simon bureau virtuel 8 x 8 qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon à utiliser Microsoft Azure AD Single Sign-On.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configuration Microsoft Azure AD unique Single Sign-On

Dans cette section, vous activez Microsoft Azure AD Single Sign-On dans le portail classique et configurez l’authentification unique dans votre application de bureau virtuel 8 x 8.

**Pour configurer Microsoft Azure AD Single Sign-On avec 8 x 8 Virtual Office, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page intégration d’application **Office du virtuel 8 x 8** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous aux utilisateurs de se connecter à un bureau virtuel de 8 x 8** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    une barre d’outils. Dans la zone de texte **URL de réponse** , tapez :`https://sso.8x8.com/saml2`

    b. Cliquez sur **suivant**

4. Dans la page **configurer l’authentification unique au bureau virtuel de 8 x 8** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Ouverture de session sur vos clients de bureau virtuel 8 x 8 en tant qu’administrateur.
6. Sélectionnez **Gestionnaire de compte de bureau virtuel** sur le panneau de l’Application.

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Sélectionnez compte **d’entreprise** à gérer et cliquez sur le bouton de **Connexion** .

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Cliquez sur onglet **comptes** de la liste du menu.

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. Cliquez sur **Single Sign On** dans la liste des comptes.

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Sélectionnez **Session monocristallines** sous méthode d’authentification et cliquez sur **SAML**.

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Copiez **SAML SSO URL**, **unique à l’aide des URL de Service** et **émetteur** de publicité Azure à **signer dans l’URL**, **Déconnectez-vous d’URL** et **les URL de l’émetteur** dans le bureau virtuel de 8 x 8. 

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Configurer sur le côté de l’application](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Cliquez sur le bouton **navigateur** pour télécharger le certificat que vous avez téléchargé à partir d’AD Azure.

13. Cliquez sur le bouton **Enregistrer** .

14. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

15. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans le portail classique appelé Britta Simon.
    
![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Création d’un utilisateur de test de bureau virtuel 8 x 8

L’objectif de cette section est de créer un utilisateur appelé Britta Simon bureau virtuel de 8 x 8. 8 x 8 virtuel Office prend en charge le provisionnement juste-à-temps, qui est par défaut activée.

Il n’y a aucun élément d’action pour vous dans cette section. Un nouvel utilisateur est créé lors d’une tentative d’accéder à 8 x 8 Virtual Office s’il n’existe pas encore. 

> [AZURE.NOTE] Si vous avez besoin créer un utilisateur manuellement, vous devez contacter l’équipe de prise en charge du bureau virtuel 8 x 8.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès au bureau virtuel de 8 x 8.
    
![Affecter des utilisateurs][200]

**Pour affecter des Britta Simon à 8 x 8 Virtual Office, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **bureau virtuel de 8 x 8**.

    ![Configurer l’authentification unique](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. Dans le menu du haut, cliquez sur **utilisateurs**.
    
    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est pour tester votre configuration de l’ouverture de session unique Microsoft Azure AD à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque du bureau virtuel de 8 x 8 dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application de bureau virtuel 8 x 8.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
