<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory SAP NetWeaver | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de SAP NetWeaver."
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


# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>Didacticiel : Intégration d’Azure Active Directory SAP NetWeaver

Dans ce didacticiel, vous allez apprendre à intégrer de SAP NetWeaver avec Azure Active Directory.

Intégration de SAP NetWeaver avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à SAP NetWeaver
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé-on à SAP NetWeaver (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec SAP NetWeaver, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique SAP NetWeaver sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de SAP NetWeaver dans la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-sap-netweaver-from-the-gallery"></a>Ajout de SAP NetWeaver dans la galerie
Pour configurer l’intégration de SAP NetWeaver dans AD Azure, vous devez ajouter NetWeaver de SAP à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des SAP NetWeaver dans la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **SAP NetWeaver**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_01.png)

7. Dans le volet de résultats, sélectionnez **SAP NetWeaver**, puis cliquez sur **Terminer** pour ajouter l’application.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez AD Azure SSO basée sur un utilisateur de test appelé « Brian Simon » de SAP NetWeaver.

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans SAP NetWeaver à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans SAP NetWeaver doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans SAP NetWeaver.

Pour configurer et tester AD Azure single sign-on avec SAP NetWeaver, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un SAP NetWeaver tester l’utilisateur](#creating-a-sap-netweaver-test-user)** - à ont un équivalent de Britta Simon dans SAP NetWeaver qui est lié à la représentation sous forme de publicité Azure de lui.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application de SAP NetWeaver.


**Pour configurer AD Azure SSO avec SAP NetWeaver, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page d’intégration **SAP NetWeaver** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à SAP NetWeaver** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application SAP NetWeaver selon le modèle suivant : **https://\<votre instance de société de SAP NetWeaver\>**.
    
    b. Dans la zone **identificateur** , tapez l’URL en utilisant le modèle suivant : **https://\<votre instance de société de SAP NetWeaver\>**.

    c. Dans la zone de texte **URL de réponse** , tapez l’URL en utilisant le modèle suivant : **https://\<votre instance de société de SAP NetWeaver\>/sap/saml2/sp/acs/100**.

    > [AZURE.NOTE] Vous pourrez trouver toutes ces valeurs dans le doucument des métadonnées de fédération fourni par votre partenaire de SAP NetWeaver.

    d. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique à SAP NetWeaver** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_05.png)

    une barre d’outils. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, contactez votre partenaire SAP NetWeaver et de leur présenter les éléments suivants :

    • Les **métadonnées** de téléchargé

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

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-sap-netweaver-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-an-sap-netweaver-test-user"></a>Création d’un utilisateur de test de SAP NetWeaver

Dans cette section, vous créez un utilisateur appelé Britta Simon dans SAP NetWeaver. Travaillez avec votre partenaire de SAP NetWeaver pour ajouter les utilisateurs de la plate-forme SAP NetWeaver.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en autorisant son accès à SAP NetWeaver.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à SAP NetWeaver, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SAP NetWeaver**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sap-netweaver-tutorial/tutorial_sapnetweaver_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque dans le panneau d’accès SAP NetWeaver, vous devez obtenir automatiquement signé sur votre application SAP NetWeaver.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sap-netweaver-tutorial/tutorial_general_205.png