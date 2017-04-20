<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Origami | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Origami."
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


# <a name="tutorial-azure-active-directory-integration-with-origami"></a>Didacticiel : Intégration d’Azure Active Directory avec Origami

Dans ce didacticiel, vous allez apprendre à intégrer Origami Azure Active Directory (AD Azure).

Intégrant Origami AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Origami
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé à Origami (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec Origami, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Origami sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout d’Origami à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-origami-from-the-gallery"></a>Ajout d’Origami à partir de la galerie
Pour configurer l’intégration des origamis dans AD Azure, vous devez ajouter Origami à partir de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter l’Origami à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Origami**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/tutorial_origami_01.png)
7. Dans le volet résultats, sélectionnez **Origami**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/tutorial_origami_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure Origami basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans Origami à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Origami doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans Origami.

Pour configurer et tester AD Azure single sign-on avec Origami, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un Origami tester l’utilisateur](#creating-a-origami-test-user)** - à ont un équivalent de Britta Simon dans Origami qui est lié à la représentation sous forme de publicité Azure de lui.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application d’Origami.


**Pour configurer AD Azure SSO avec Origami, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page d’intégration **Origami** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Origami** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application Origami selon le modèle suivant : **https://live.origamirisk.com/origami/account/login?account=\<nom de la société\> **
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique à Origami** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.



1. Connectez-vous au compte Origami avec des droits d’administrateur.

1. Dans le menu du haut, cliquez sur **Admin**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)
  

1. Sur la page de dialogue seul signe sur le programme d’installation, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/123.png)

    une barre d’outils. Sélectionnez **Activer l’authentification unique**.

    b. Dans le portail Azure classique, copiez l' **URL de l’authentification unique SAML**et puis la coller dans la zone de texte **Du fournisseur d’identité-in URL de la Page** .

    c. Dans le portail classique Azure, copier l' **Authentification unique des URL de SERVICE**et puis la coller dans la zone de texte **URL de la Page Sign-out du fournisseur d’identité** .

    d. Cliquez sur **Parcourir** pour charger le certificat que vous avez téléchargé à partir du portail classique Azure.

    e. Cliquez sur **Enregistrer les modifications**.


6. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
 
    ![Azure AD unique Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-origami-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-origami-test-user"></a>Création d’un utilisateur de test Origami

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Origami. 

1. Connectez-vous au compte Origami avec des droits d’administrateur.

2. Dans le menu du haut, cliquez sur **Admin**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_51.png)

3. Dans la boîte de dialogue **utilisateurs et la sécurité** , cliquez sur **utilisateurs**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_54.png)

4. Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_55.png)

5. Dans la boîte de dialogue Ajouter un nouvel utilisateur, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_56.png)

    une barre d’outils. Dans la zone de texte **Nom d’utilisateur** , tapez le nom d’utilisateur de Britta Simon dans Azure portal classique.

    b. Dans la zone de texte **mot de passe** , tapez un passwotd.

    c. Dans la zone de texte **Confirmer le mot de passe** , retapez le mot de passe.

    d. Dans la zone de texte **nom** , tapez **Brian**.

    e. Dans la zone de texte **Nom** , tapez **Simon**.

    f. Cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_57.png)

1. Affecter des **Rôles de l’utilisateur** et **l’Accès Client** à l’utilisateur. 

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_58.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO par lui accorder son accès à Origami.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à Origami, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Origami**.

    ![Configurer l’authentification unique](./media/active-directory-saas-origami-tutorial/tutorial_origami_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque Origami dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application d’Origami.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-origami-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-origami-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-origami-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-origami-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-origami-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-origami-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-origami-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-origami-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-origami-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-origami-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-origami-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-origami-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-origami-tutorial/tutorial_general_205.png
