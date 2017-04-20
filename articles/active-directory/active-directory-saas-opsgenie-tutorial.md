<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec OpsGenie | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et OpsGenie."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Didacticiel : Intégration d’Azure Active Directory avec OpsGenie

L’objectif de ce didacticiel est de vous montrer comment intégrer OpsGenie Azure Active Directory (AD Azure).

Intégrant OpsGenie AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à OpsGenie
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à OpsGenie (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec OpsGenie, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un OpsGenie de l’authentification unique activée abonnement


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de OpsGenie à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-opsgenie-from-the-gallery"></a>Ajout de OpsGenie à partir de la galerie
Pour configurer l’intégration de OpsGenie dans AD Azure, vous devez ajouter OpsGenie à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des OpsGenie à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **OpsGenie**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. Dans le volet de résultats, sélectionnez **OpsGenie**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec OpsGenie basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, Azure AD doit connaître l’utilisateur équivalent dans OpsGenie à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans OpsGenie doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans OpsGenie.

Pour configurer et tester AD Azure single sign-on avec OpsGenie, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un OpsGenie de test utilisateur](#creating-a-opsgenie-test-user)** - d’avoir un équivalent de Britta Simon dans OpsGenie qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail Azure classique et configurer l’authentification unique dans votre application de OpsGenie.



**Pour configurer AD Azure SSO avec OpsGenie, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **OpsGenie** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à OpsGenie** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    une barre d’outils. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de OpsGenie selon le modèle suivant : **« https://app.opsgenie.com/auth/login »**.

    > [AZURE.NOTE] Veuillez contacter votre [équipe de support de OpsGenie](mailto:support@opsgenie.com) si vous avez besoin de votre URL de connexion.

    b. Cliquez sur **suivant**.


4. Dans la page **configuration de l’authentification unique à OpsGenie** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur. Nous aurons besoin de ce certificat et cette URL de métadonnées (ID de l’entité, SSO URL de connexion et des URL de connexion) pour configurer l’authentification unique sur le côté de la OpsGenie.

    b. Cliquez sur **suivant**.


5. Ouvrez une autre instance de navigateur et reconnectez-vous au OpsGenie en tant qu’administrateur.

6. Cliquez sur **paramètres**, puis cliquez sur l’onglet ouverture de **Session unique** .
 
    ![OpsGenie unique Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. Pour activer l’authentification unique, sélectionnez **activé**.

    ![Paramètres de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. Dans la section **fournisseur** , cliquez sur l’onglet **Azure Active Directory** .

    ![Paramètres de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. Sur la page de dialogue Azure Active Directory, procédez comme suit :
 
    ![Paramètres de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    une barre d’outils. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à OpsGenie** , copiez la valeur **Unique URL de connexion de Service** et puis la coller dans la zone de texte de **Point de terminaison SAML 2.0** .

    b. Créer un fichier encodé en base 64 de votre certificat téléchargé.      
    
    > [AZURE.NOTE] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Ouvrez votre certificat codé en base 64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **Certificat de X.500** .

    d. Cliquez sur **Enregistrer les modifications**.


6. Dans le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.



![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-opsgenie-test-user"></a>Création d’un utilisateur de test OpsGenie

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OpsGenie. 

1.  Dans une fenêtre de navigateur web, connectez-vous à vos clients de OpsGenie en tant qu’administrateur.

2.  En cliquant sur **utilisateur** dans le panneau de gauche, accédez à la liste des utilisateurs.
   
    ![Paramètres de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  Cliquez sur «**Ajouter un utilisateur**».

3.  Dans la boîte de dialogue **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Paramètres de OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    une barre d’outils. Dans la zone de texte **message** , tapez l’adresse de messagerie de Brian dans Azure Active Directory.

    b. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    c. Cliquez sur **Enregistrer**. 

Brian obtiendra un e-mail avec des instructions pour la configuration de son profil.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à OpsGenie.

![Affecter des utilisateurs][200] 

**Pour faire Britta Simon OpsGenie, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
 
    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **OpsGenie**.

    ![Configurer l’authentification unique](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque OpsGenie dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application OpsGenie.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png
