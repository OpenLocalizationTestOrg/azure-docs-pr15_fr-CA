<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec BetterWorks | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et BetterWorks."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Didacticiel : Intégration d’Azure Active Directory avec BetterWorks

L’objectif de ce didacticiel est de vous montrer comment intégrer BetterWorks Azure Active Directory (AD Azure).

Intégrant BetterWorks AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à BetterWorks
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à BetterWorks (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec BetterWorks, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique BetterWorks sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de BetterWorks à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-betterworks-from-the-gallery"></a>Ajout de BetterWorks à partir de la galerie
Pour configurer l’intégration de BetterWorks dans AD Azure, vous devez ajouter BetterWorks à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des BetterWorks à partir de la galerie, procédez comme suit :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **BetterWorks**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)

7. Dans le volet de résultats, sélectionnez **BetterWorks**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![L’application de la sélection dans la galerie](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec BetterWorks basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans BetterWorks à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans BetterWorks doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans BetterWorks.

Pour configurer et tester AD Azure single sign-on avec BetterWorks, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un BetterWorks de test utilisateur](#creating-a-betterworks-test-user)** - d’avoir un équivalent de Britta Simon dans BetterWorks qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application de BetterWorks.

Application de BetterWorks attend les assertions SAML dans un format spécifique. Veuillez configurer les demandes suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet «**Atrribute**» de l’application. La capture d’écran suivante montre un exemple de cela. 

![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Pour configurer AD Azure SSO avec BetterWorks, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page **BetterWorks** d’intégration application, dans le menu de la partie supérieure, cliquez sur **attributs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)

2. Dans la boîte de dialogue **attributs de jeton SAML** , pour chaque ligne indiqué dans le tableau ci-dessous, effectuez les opérations suivantes :
    

  	| Nom de l’attribut | Valeur de l’attribut |
  	| --- | --- |    
  	| saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    une barre d’outils. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter des Attribure utilisateur** .

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    
    c. Dans la liste **Valeur de l’attribut** , tapez l’ID de jeton saml affichée pour cette ligne.
    
    d. Cliquez sur **terminé**

3. Dans le menu du haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 

4. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à BetterWorks** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)

5. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , si vous souhaitez configurer l’application en **IDP initiée par mode**, effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)


    une barre d’outils. Dans la zone **identificateur** , tapez l’URL dans le modèle suivant :`https://app.betterworks.com/saml2/metadata/`


    b. Dans la zone de texte **URL de réponse** , tapez l’URL dans le modèle suivant :`https://app.betterworks.com/saml2/acs/`


    c. Cliquez sur **suivant**

6. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , si vous souhaitez configurer l’application en **mode exécutée par le Service Pack**, exécutez le sur les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)

    une barre d’outils.  Sélectionnez **Afficher les avancées des paramètres (facultatif)**.



    b. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de BetterWorks selon le modèle suivant :`https://app.betterworks.com`

    b. Cliquez sur **suivant**.

7. Dans la page **configuration de l’authentification unique à BetterWorks** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)

    une barre d’outils. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

8. Pour obtenir l’authentification unique configurée pour votre application, contactez votre équipe de prise en charge de BetterWorks par <mailto:support@betterworks.com>. Joindre le fichier de métadonnées téléchargés et les partager avec l’équipe de BetterWorks pour configurer l’authentification unique sur leur côté.

9. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

10. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-betterworks-test-user"></a>Création d’un utilisateur de test BetterWorks

Dans cette section, vous créez un utilisateur appelé Britta Simon dans BetterWorks. 

Veuillez contacter l’équipe de support BetterWorks via <mailto:support@betterworks.com> pour ajouter les utilisateurs de la plate-forme BetterWorks.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à BetterWorks.
    
   ![Affecter des utilisateurs][200]

**Pour faire Britta Simon BetterWorks, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **BetterWorks**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)

1. Dans le menu du haut, cliquez sur **utilisateurs**.
    
    ![Affecter des utilisateurs][203]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.
    
    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la mosaïque BetterWorks dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application BetterWorks.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png
