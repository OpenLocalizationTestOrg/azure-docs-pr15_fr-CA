<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec & franchement | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et & franchement."
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
    ms.date="08/12/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Didacticiel : Intégration d’Azure Active Directory avec & franchement

Ce didacticiel vise à vous montrer comment intégrer et honnête avec Azure Active Directory (AD Azure).

Intégration & franchement avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès au & franchement
- Vous pouvez activer vos utilisateurs obtenir automatiquement connecté au & franchement (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer Active Directory Azure integration avec & franchement, vous devez les éléments suivants :

- Un abonnement Azure AD
- A & franchement connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout & franchement à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-frankly-from-the-gallery"></a>Ajout & franchement à partir de la galerie
Pour configurer l’intégration de & franchement dans AD Azure, vous devez ajouter & franchement à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Ajouter & franchement, à partir de la galerie, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **& franchement**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_01.png)

7. Dans le panneau de résultats, sélectionnez **& franchement**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![L’application de la sélection dans la galerie](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est pour vous montrer comment configurer et test AD Azure unique ouverture de session avec & franchement en fonction d’un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans & franchement à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le & franchement doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de l' **utilisateur** dans le & franchement.

Pour configurer et tester les annonces Azure single sign-on avec & franchement, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un & franchement utilisateur test](#creating-a-&frankly-test-user)** - ont un équivalent de Britta Simon dans le & franchement qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurer l’authentification unique dans votre & franchement application.

**Pour configurer Azure AD simple sign-on avec & franchement, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page intégration d’application **et, franchement** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à & franchement** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_03.png)

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , si vous souhaitez configurer l’application en **IDP initiée par mode**, effectuez les opérations suivantes et cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_04.png)

    une barre d’outils. Dans la zone **identificateur** , tapez une URL en utilisant le modèle suivant :`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant le modèle suivant :`https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

    c. Cliquez sur **suivant**

4. Si vous souhaitez configurer l’application en **mode exécutée par le Service Pack** sur la page de la boîte de dialogue **Configurer les paramètres d’application** , puis cliquez sur **« Afficher les paramètres (facultatifs) avancés »** puis entrez l' **URL de connexion** et cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_05.png)

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez une URL en utilisant le modèle suivant :`https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`

    b. Cliquez sur **suivant**

    > [AZURE.NOTE] Veuillez noter que ce ne sont pas les valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion, un identificateur et une URL de réponse réelle. Contact [help@andfrankly.com](emailTo:help@andfrankly.com) pour obtenir ces valeurs.

5. Sur le **configurer l’authentification unique à & franchement** page, effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_06.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

6. Pour obtenir l’authentification unique configurée pour votre application, contactez votre & franchement l’équipe par le biais de support [help@andfrankly.com](emailTo:help@andfrankly.com). Joindre le fichier de métadonnées téléchargés et partagez-le avec & franchement de l’équipe pour configurer l’authentification unique de leur côté.

7. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

8. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_04.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_05.png)

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_06.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-andfrankly-tutorial/create_aaduser_08.png)

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-frankly-test-user"></a>Créer un & franchement utilisateur test

Dans cette section, vous créez un utilisateur appelé Britta Simon dans le & franchement. Veuillez utiliser & franchement l’équipe par le biais de support [help@andfrankly.com](emailTo:help@andfrankly.com) pour ajouter les utilisateurs de & franchement plate-forme.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès au & franchement.
    
![Affecter des utilisateurs][200]

**Pour affecter des Britta Simon & franchement, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **& franchement**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-andfrankly-tutorial/tutorial_andfrankly_50.png)

1. Dans le menu du haut, cliquez sur **utilisateurs**.
    
    ![Affecter des utilisateurs][203]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur & franchement mosaïque dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre & franchement application.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-andfrankly-tutorial/tutorial_general_205.png
