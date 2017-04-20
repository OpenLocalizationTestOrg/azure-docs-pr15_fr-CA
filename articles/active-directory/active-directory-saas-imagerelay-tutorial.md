<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ImageRelay | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et ImageRelay."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Didacticiel : Intégration d’Azure Active Directory avec ImageRelay

L’objectif de ce didacticiel est de vous montrer comment intégrer ImageRelay Azure Active Directory (AD Azure).  
Intégrant ImageRelay AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à ImageRelay
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à ImageRelay (SSO) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec ImageRelay, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un ImageRelay de l’authentification unique activée abonnement


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de ImageRelay à partir de la galerie

2. Configuration et test AD Azure authentification unique


## <a name="adding-imagerelay-from-the-gallery"></a>Ajout de ImageRelay à partir de la galerie
Pour configurer l’intégration de ImageRelay dans AD Azure, vous devez ajouter ImageRelay à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des ImageRelay à partir de la galerie, procédez comme suit :**

1. Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **ImageRelay**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. Dans le volet de résultats, sélectionnez **ImageRelay**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec ImageRelay basée sur un utilisateur de test appelé « Brian Simon ».

De session unique fonctionner, Azure AD a besoin d’un compte d’utilisateur qui représente l’utilisateur connexe dans ImageRelay.  En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans ImageRelay doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans ImageRelay.

Pour configurer et tester AD Azure single sign-on avec ImageRelay, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un ImageRelay de test utilisateur](#creating-a-userecho-test-user)** - d’avoir un équivalent de Britta Simon dans ImageRelay qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application de ImageRelay.


**Pour configurer AD Azure SSO avec ImageRelay, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **ImageRelay** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

     ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ImageRelay** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

     ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de ImageRelay (par exemple : *https://fabrikam.ImageRelay.com/*).

    b. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique à ImageRelay** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise ImageRelay en tant qu’administrateur.

1. Dans la barre d’outils dans la partie supérieure, cliquez sur la charge de travail **des utilisateurs et des autorisations** .

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Cliquez sur **créer une nouvelle autorisation**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. De la charge de **l’Authentification unique sur paramètres** , activez la case à cocher de **ce groupe peuvent seulement signe à l’aide de session unique** , puis cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Accéder aux **paramètres du compte**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Accédez à la charge de **l’Authentification unique sur paramètres** .

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Dans la boîte de dialogue **Paramètres de SAML** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    une barre d’outils. Dans le portail classique Azure, copiez l' **URL de Service unique ouverture de session**et puis la coller dans la zone de texte **URL de connexion** .


    b. Dans le portail classique Azure, copiez l' **URL du Service Sign-Out unique**et puis la coller dans la zone de texte **URL de déconnexion** .

    c. En tant que **Format du nom de code**, sélectionnez **urn : oasis : noms : tc : SAML:1.1:nameid-format : emailAddress**.

    
    d. En tant qu' **Options de liaison pour les demandes provenant du fournisseur de services (relais de l’Image)**, sélectionnez **La liaison POST**.
   

    e. Sous **x.509 certificat**, cliquez sur **Certificat de mise à jour**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Ouvrez le certificat téléchargé dans le bloc-notes, copiez le contenu et puis la coller dans la zone de texte du certificat x.509.
  
    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Dans la section **d’Approvisionnement de l’utilisateur juste à temps** , sélectionnez la **Configuration des utilisateurs activer juste à temps**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Sélectionnez le groupe d’autorisations (par exemple, de **Base de l’authentification unique**) qui est autorisé à se connecter via une ouverture de session unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Cliquez sur **Enregistrer**.

6. Dans le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.

    ![Azure AD unique Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-imagerelay-test-user"></a>Création d’un utilisateur de test ImageRelay

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans ImageRelay.

**Pour créer un utilisateur appelé Britta Simon ImageRelay, effectuez les opérations suivantes :**

1. Ouverture de session sur le site de votre entreprise ImageRelay en tant qu’administrateur.

1. Pour les **utilisateurs et les autorisations** , sélectionnez **Créer un utilisateur de l’authentification unique**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Entrez le **courrier électronique**, le **prénom**, **Nom** et **société** de l’utilisateur que vous souhaitez configurer et sélectionnez le groupe d’autorisations (par exemple, l’authentification de base) qui est le groupe qui peut se connecter que par le biais de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Cliquez sur **créer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à ImageRelay.

![Affecter des utilisateurs][200] 

**Pour faire Britta Simon ImageRelay, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ImageRelay**.

    ![Configurer l’authentification unique](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque ImageRelay dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application ImageRelay.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png
