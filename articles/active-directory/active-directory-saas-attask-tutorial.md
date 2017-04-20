<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec @Task| Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et @Task."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-task"></a>Didacticiel : Intégration d’Azure Active Directory avec@Task

Ce didacticiel vise à vous montrer comment intégrer @Task avec Azure Active Directory (AD Azure).  
Intégration de @Task avec AD Azure vous fournit les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à@Task
- Vous pouvez activer vos utilisateurs obtenir la signature sur @Task (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec @Task, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un @Task connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de trois blocs de construction principaux :

1. Ajout de @Task à partir de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-task-from-the-gallery"></a>Ajout de @Task à partir de la galerie
Pour configurer l’intégration de @Task dans Azure annonce, vous devez ajouter @Task de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des @Task de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2] 

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3] 

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4] 

6. Dans la zone Rechercher, tapez **@Task**.

    ![Applications][5] 

7. Dans le volet résultats, sélectionnez **@Task**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][30] 



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique

L’objectif de cette section est de vous montrer comment configurer et tester AD Azure single sign-on avec @Task basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir ce que l’utilisateur équivalent dans @Task à un utilisateur dans AD Azure est. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans @Task doit être établi.   
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans @Task.
 
Pour configurer et tester AD Azure single sign-on avec @Task, vous avez besoin compléter les éléments suivants :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un @Tasktest utilisateur](#creating-a-halogen-software-test-user) ** - ont un équivalent de Britta Simon dans @Taskthat est lié à la représentation sous forme de publicité Azure de lui.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre @Task application.

**Pour configurer AD Azure single sign-on avec @Task, les étapes suivantes :**

1. Dans le portail Azure classique, sur le **@Task** intégration de l’application, cliquez sur **configurer SSO** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Sur le **comment vous souhaitez que les utilisateurs à se connecter à @Task ** page, sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][7] 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application][8] 
 
     une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre @Task application (par exemple :*https://<Tenant name>.attask-ondemand.com*).

     b. Cliquez sur **suivant**.

4. Sur le **configurer l’authentification unique à @Task ** page et cliquez sur **télécharger les métadonnées**, enregistrez le fichier de métadonnées localement sur votre ordinateur, puis cliquez sur **suivant**.

    ![Nouveautés d’Azure AD Connect][9] 



1. Ouverture de session sur votre @Task site de la société en tant qu’administrateur.

2. Accédez à **Configuration de l’authentification unique**.


1. Dans la boîte de dialogue **Single Sign-On** , effectuez les opérations suivantes

    ![Configurer l’authentification unique][23]

    une barre d’outils. Sous **Type**, sélectionnez **SAML 2.0**.

    b. Sélectionnez **l’ID du fournisseur de Service**.

    c. Sur le portail Azure classique, copiez l' **URL de connexion d’accès à distance**et puis la coller dans la zone de texte **URL du portail de connexion** .

    d. Sur le portail Azure classique, copiez l' **URL du Service Sign-Out unique**et puis la coller dans la zone de texte **URL de Sign-Out** .

    e. Sur le portail Azure classique, copiez l' **URL de modification de mot de passe**et puis la coller dans la zone de texte **URL de modification de mot de passe** .

    f. Cliquez sur **Enregistrer**.

6. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![Nouveautés d’Azure AD Connect][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Nouveautés d’Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.  

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
 
    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
 
8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
  
    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-an-task-test-user"></a>Création d’un @Task utilisateur test

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans @Task.


**Pour créer un utilisateur appelé Britta Simon dans @Task, les étapes suivantes :**

1. Ouvrez une session sur votre @Task site de la société en tant qu’administrateur.

2. Dans le menu du haut, cliquez sur **personnes**.

3. Cliquez sur **Nouveau**. 

4. Dans la boîte de dialogue nouvelle personne, effectuez les opérations suivantes :

    ![Créer un @Task utilisateur test][21] 

    une barre d’outils. Dans la zone de texte **prénom** , tapez « Brian ».

    b. Dans la zone de texte **Nom** , tapez « Simon ».

    c. Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de Britta de Simon dans Azure Active Directory.

    d. Cliquez sur **Ajouter une personne**.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à @Task.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à @Task, les étapes suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **@Task**.

    ![Affecter des utilisateurs][202] 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur le @Task en mosaïque dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre @Task application.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






