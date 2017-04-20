<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Optimizely | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Optimizely."
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Didacticiel : Intégration d’Azure Active Directory avec Optimizely

Dans ce didacticiel, vous allez apprendre à intégrer Optimizely Azure Active Directory (AD Azure).

Intégrant Optimizely AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Optimizely
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à Optimizely (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec Optimizely, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique **Optimizely** sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de Optimizely à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-optimizely-from-the-gallery"></a>Ajout de Optimizely à partir de la galerie
Pour configurer l’intégration des Optimizely dans AD Azure, vous devez ajouter Optimizely à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des Optimizely à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Optimizely**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. Dans le volet de résultats, sélectionnez **Optimizely**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure Optimizely basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans Optimizely à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Optimizely doit être établi.
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans Optimizely.

Pour configurer et tester AD Azure single sign-on avec Optimizely, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un Optimizely de test utilisateur](#creating-an-optimizely-test-user)** - d’avoir un équivalent de Britta Simon dans Optimizely qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application de Optimizely.

Application de Optimizely attend les assertions SAML contient un attribut nommé « courrier électronique ». La valeur de « courrier électronique » doit être un e-mail Optimizely reconnu authentifié par Active Directory Azure. Veuillez configurer la demande de « courrier électronique » de cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **« Atrributes »** de l’application. La capture d’écran suivante montre un exemple de cela. 


![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**Pour configurer AD Azure SSO avec Optimizely, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **Optimizely** , dans le menu de la partie supérieure, cliquez sur **attributs**.
     
    ![Configurer l’authentification unique][5]

2. Dans la boîte de dialogue attributs de jeton SAML, ajoutez l’attribut « courrier électronique ».

    une barre d’outils. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter un attribut utilisateur** . 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b. Dans la zone de texte **Nom de l’attribut** , tapez l’attribut nom « courrier électronique ».

    c. Dans la liste **Valeur de l’attribut** , sélectionnez la valeur de l’attribut « userprincipalname » ou toute valeur contenant un e-mail reconnu par AD Azure et Optimizely.

    d. Cliquez sur **terminé**.
3. Dans le menu du haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique][6]
4. Dans le portail classique, sur la page intégration d’application **Optimizely** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7] 

5. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Optimizely** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez :`https://app.optimizely.net/contoso`

    b. Dans la zone **identificateur** , tapez :`urn:auth0:optimizely:contoso`

    c. Cliquez sur **suivant**. 


    > [AZURE.NOTE] Les valeurs de **l’identificateur** et **l’URL de connexion** sont uniquement des espaces réservés pour les valeurs réelles. Vous trouverez des instructions pour lors de l’acquisition des valeurs réelles de Optimizely plus loin dans ce didacticiel.

7. Dans la page **configuration de l’authentification unique à Optimizely** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Copiez l' **URL du Service d’authentification unique**.

8. Pour obtenir l’authentification unique configurée pour votre application, contactez votre responsable de compte Optimizely et fournir les informations suivantes :

    - Votre certificat téléchargé 
    - L’URL du Service d’authentification unique
 
    En réponse à votre courrier électronique, Optimizely vous donne le signe d’URL (l’authentification unique initiée par SP) et les valeurs d’identificateur (ID d’entité Service fournisseur).

9. Revenir à la boîte de dialogue **Configurer les paramètres d’application** et puis procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l' **URL de l’authentification unique initiée par SP** fourni par Optimizely.

    b. Dans la zone **identificateur** , tapez l' **ID d’entité de Service fournisseur** fourni par Optimizely.

    c. Cliquez sur **suivant**.

10. Dans la page **configuration de l’authentification unique à Optimizely** , effectuez les opérations suivantes :
    
    ![Azure AD unique Single Sign-On][10]

    une barre d’outils. Sélectionnez la confirmation de la configuration d’ouverture de session unique.

    b. Cliquez sur **suivant**.

11. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]

12. Dans une fenêtre différente, ouverture de session sur votre application Optimizely.
13. Cliquez sur le compte de nom dans le coin supérieur droit, puis sur **Paramètres du compte**.

    ![Azure AD unique Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. Dans l’onglet compte, la case **Activer l’authentification** sous Single Sign On dans la section **vue d’ensemble** .

    ![Azure AD unique Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.
Dans la liste utilisateurs, sélectionnez **Brian Simon**.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-optimizely-test-user"></a>Création d’un utilisateur de test Optimizely

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Optimizely.

1. Sur la page d’accueil, sélectionnez l’onglet des **collaborateurs**
2. Cliquez sur **Nouveau collaborateur** pour ajouter un nouveau collaborateur pour le projet.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Renseignez l’adresse de messagerie et de leur assigner un rôle. Cliquez sur **inviter**.


    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Ils reçoivent une invitation par courrier électronique. À l’aide de l’adresse de messagerie. ils devront se connecter à Optimizely.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès à Optimizely.

![Affecter des utilisateurs][200] 

**Pour faire Britta Simon Optimizely, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Optimizely**.

    ![Configurer l’authentification unique](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque Optimizely dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application Optimizely.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png
