<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory fichiers plat | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et fichiers plat."
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


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Didacticiel : Intégration d’Azure Active Directory fichiers plat

L’objectif de ce didacticiel est de vous montrer comment intégrer les fichiers plat avec Azure Active Directory (AD Azure).  
Intégration des fichiers plat avec AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès aux fichiers plat 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur fichiers plat (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure Active Directory

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec fichiers plat, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique des fichiers plat sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de fichiers à partir de la galerie plat 
2. Configuration et test AD Azure authentification unique


## <a name="adding-flatter-files-from-the-gallery"></a>Ajout de fichiers à partir de la galerie plat
Pour configurer l’intégration des fichiers plat dans AD Azure, vous devez ajouter des fichiers plat à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des fichiers plat à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Fichiers plat**.


    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. Dans le volet résultats, sélectionnez **Fichiers plat**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester Azure AD-session unique avec les fichiers plat en fonction d’un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans les fichiers plat à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans les fichiers plat doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans les fichiers plat.
 
Pour configurer et tester AD Azure SSO avec fichiers plat, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un fichier plat tester l’utilisateur](#creating-a-halogen-software-test-user)** - à un équivalent de Britta Simon plat fichiers comportent qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique AD Azure et configurer l’authentification unique dans votre application de fichiers plat. Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64. Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Pour configurer l’authentification unique pour les fichiers plat, vous avez besoin d’un domaine enregistré. Si vous n’avez pas un domaine enregistré encore, contact vos fichiers plat prend en charge l’équipe via [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Pour configurer AD Azure SSO avec fichiers plat, effectuez les opérations suivantes :**

1. Dans le portail classique AD Azure, sur la page intégration d’application **Plat de fichiers** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à des fichiers plat** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] Fichiers plat utilise la même URL de connexion de l’authentification unique pour tous les clients : [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. Dans la page **configuration de l’authentification unique à plat de fichiers** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


1. Ouverture de session sur votre application de fichiers plat en tant qu’administrateur.

2. Cliquez sur le tableau de bord. 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Cliquez sur **paramètres**, puis effectuez les opérations suivantes sur l’onglet de la **société** : 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    une barre d’outils. Sélectionnez **utiliser SAML 2.0 pour l’authentification**.

    b. Cliquez sur **configurer SAML**.



2. Dans la boîte de dialogue **Configuration de SAML** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    une barre d’outils. Dans la zone de texte domaine, tapez votre domaine enregistré.

    > [AZURE.NOTE] Si vous n’avez pas un domaine enregistré encore, contact vos fichiers plat prend en charge l’équipe via [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. Dans l’Azure portal classique, sur la configuration unique de session à la boîte de dialogue fichiers plat, copt l’URL du Service ouverture de session unique et puis la coller dans la zone de texte URL du fournisseur d’identité.

    c.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

    >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    d.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat d’identité du fournisseur FlatterFiles** .

    e. Cliquez sur **mise à jour**.

6. Dans le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Création d’un utilisateur de test de fichiers plat

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans les fichiers plat.

**Pour créer un utilisateur appelé Britta Simon dans les fichiers plat, effectuez les opérations suivantes :**

1. Ouverture de session pour le site de votre société de **Fichiers plat** en tant qu’administrateur.

2. Dans le volet de navigation de gauche, cliquez sur **paramètres**, puis cliquez sur **l’onglet**d' utilisateurs.

    ![Cfreate un utilisateur fichiers plat](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Cliquez sur **Ajouter un utilisateur**. 

4. Dans la boîte de dialogue **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Cfreate un utilisateur fichiers plat](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.

    b. Dans la zone de texte **Nom** , tapez **Simon**. 

    c. Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de Brian dans Azure portal classique.

    d. Cliquez sur **Envoyer**.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès aux fichiers plat.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à plat de fichiers, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Fichiers plat**.

    ![Affecter des utilisateurs](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque plat de fichiers dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application de fichiers plat.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






