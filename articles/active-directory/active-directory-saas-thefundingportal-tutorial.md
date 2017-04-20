<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le portail de financement | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et du portail de financement."
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
    ms.date="09/02/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Didacticiel : Intégration d’Azure Active Directory avec le portail de financement

Dans ce didacticiel, vous allez apprendre à intégrer du portail de financement avec Azure Active Directory (AD Azure).

Intégration du portail de financement avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès au portail de financement le
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur le portail de financement (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec du financement de portail, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique **Du portail de financement** sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout du portail de financement de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-the-funding-portal-from-the-gallery"></a>Ajout du portail de financement de la galerie
Pour configurer l’intégration du portail de financement dans AD Azure, vous devez ajouter du portail de financement de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter du portail de financement de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Le portail de financement**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png)

7. Dans le volet résultats, sélectionnez **Le portail de financement**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure du portail de financement basées sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans le portail de financement à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le portail de financement doit être établi.
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de l' **utilisateur** dans le portail de financement.

Pour configurer et tester AD Azure single sign-on avec du portail de financement, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un portail de financement le test utilisateur](#creating-a-the-funding-portal-test-user)** - d’avoir un équivalent de Britta Simon dans le portail de financement qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application du portail de financement.

L’application de portail de financement attend les assertions SAML contient un attribut nommé « externalId1 ». La valeur de « externalId1 » doit être un studentID reconnu. Veuillez configurer la demande de « externalId1 » pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **« Atrributes »** de l’application. La capture d’écran suivante montre un exemple de cela.

![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png) 

**Pour configurer du portail de financement AD Azure SSO, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page **Du portail de financement** d’intégration application, dans le menu de la partie supérieure, cliquez sur **attributs**.
     
    ![Configurer l’authentification unique][5]

2. Dans la boîte de dialogue attributs de jeton SAML, ajoutez l’attribut « externalId1 ».

    une barre d’outils. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur** . 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png)

    b. Dans la zone de texte **Nom de l’attribut** , tapez le nom de l’attribut « externalId1 ».

    c. Dans la liste **Valeur de l’attribut** , sélectionnez l’attribut que vous souhaitez utiliser pour votre implémentation. Par exemple, si vous avez stocké la valeur StudentID dans le ExtensionAttribute1, puis sélectionnez user.extensionattribute1.

    d. Cliquez sur **terminé**. Puis, cliquez sur **Appliquer les modifications**.

1. Dans le menu du haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique][6]

2. Dans le portail classique, sur la page **Du portail de financement** d’intégration application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7] 

3. Dans la page **Comment voulez-vous que les utilisateurs à se connecter sur le portail du financement** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png)

4. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    une barre d’outils. Dans la zone de texte URL de connexion, tapez une URL en utilisant le modèle suivant : `https://<subdomain>.regenteducation.net/`.

    b. Cliquez sur **suivant**.

5. Dans la page **configuration de l’authentification unique sur le portail de financement** , cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png)

6. Pour obtenir l’authentification unique configurée pour votre application, contactez le support technique du portail de financement. Qu’elles peuvent aider avec le canal approprié pour configurer l’authentification unique. Veuillez Notez que vous devez envoyer un courriel et joindre téléchargé le fichier de métadonnées à info@regenteducation.com.

7. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

8. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-the-funding-portal-test-user"></a>Création d’un utilisateur de test du portail de financement

Dans cette section, vous créez un utilisateur appelé Britta Simon dans le portail de financement. Si vous ne connaissez pas l’ajout de Britta Simon dans le portail de financement, travaillez avec l’équipe de prise en charge du financement de portail pour ajouter l’utilisateur de test et activer l’authentification unique. Contactez-le à info@regenteducation.com.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès sur le portail du financement.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon sur le portail du financement, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Le portail de financement**.

    ![Configurer l’authentification unique](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque du portail de financement dans le panneau d’accès, vous devez obtenir automatiquement signé dans votre application du portail de financement.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png
