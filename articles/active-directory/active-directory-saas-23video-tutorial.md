<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory 23 vidéo | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de la vidéo 23."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>Didacticiel : Intégration d’Azure Active Directory 23 vidéo

L’objectif de ce didacticiel est de vous montrer comment intégrer vidéo 23 Azure Active Directory (AD Azure).  
Intégration de 23 vidéo avec AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à la vidéo 23 
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé vidéo 23 (Single Sign-On) avec leur compte Azure AD

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec vidéo 23, vous devez les éléments suivants :

- Un abonnement Azure AD
- Un 23 vidéo connexion unique activé l’abonnement


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de vidéo 23 à partir de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-23-video-from-the-gallery"></a>Ajout de vidéo 23 à partir de la galerie
Pour configurer l’intégration de vidéo 23 dans AD Azure, vous devez ajouter une vidéo 23 à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter une vidéo de 23 à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **23 vidéo**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **la vidéo 23**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][25]

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec 23 vidéo basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans 23 vidéo à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans 23 vidéo doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans 23 vidéo.
 
Pour configurer et tester AD Azure single sign-on avec vidéo 23, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’une vidéo 23 tester l’utilisateur](#creating-a-23-video-test-user)** - à ont un équivalent de Britta Simon dans 23 vidéo qui est lié à la représentation sous forme de publicité Azure de lui.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application vidéo 23.

**Pour configurer AD Azure SSO avec 23 vidéo, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **vidéo 23** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à signer à 23 vidéo** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][7] 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Azure AD unique Single Sign-On][8] 
 
     une barre d’outils. Dans la zone de texte **URL de la réponse** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre site de vidéo 23 (par exemple : *https://britta-simon.23Video.com/saml/login*).

     > [AZURE.NOTE] Intégration d’Active Directory à l’aide de SAML 2.0 est disponible pour tous les utilisateurs de vidéo 23. Veuillez contacter le Support à [support@23company.com](mailto:support@23company.com) si vous devez les métadonnées associées.

     b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique à 23 vidéo** , effectuez les opérations suivantes :

    ![Azure AD unique Single Sign-On][9] 

    une barre d’outils. Cliquez sur Télécharger le certificat et puis enregistrez le fichier sur votre ordinateur.

    b. Contactez votre équipe de support vidéo 23 via [support@23company.com](mailto:support@23company.com), leur fournir le certificat téléchargé, l' **URL de l’émetteur**, l' **URL de Service unique ouverture de session**, l' **URL de Sign-Out unique**et demandez-lui de configurer l’authentification unique pour votre application vidéo 23. 

    c. Cliquez sur **suivant**.


6. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
  
    ![Azure AD unique Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_09.png)  

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_05.png)  

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_06.png) 
 
    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_07.png) 
 
8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-23video-tutorial/create_aaduser_08.png) 
  
    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-23-video-test-user"></a>Création d’un utilisateur de test vidéo 23

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans 23 vidéo.

**Pour créer un utilisateur appelé Britta Simon dans 23 vidéo, effectuez les opérations suivantes :**

1. Ouvrez une session sur votre site d’entreprise vidéo 23 en tant qu’administrateur.

1. Accéder aux **paramètres**.


2. Dans la section **utilisateurs** , cliquez sur **configurer**.

    ![Affecter des utilisateurs][400]

3. Cliquez sur **Ajouter un nouvel utilisateur**. 

    ![Affecter des utilisateurs][401]

4. Dans la section **Inviter quelqu'un à rejoindre ce site** , effectuez les opérations suivantes :

    ![Affecter des utilisateurs][402]

    une barre d’outils. Dans la zone **adresses de messagerie** , tapez l’adresse de courriel de Britta Simon dans Azure AD.

    b. Cliquez sur **Ajouter l’utilisateur**.   


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO par lui accorder son accès à 23 vidéo.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à 23 vidéo, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **vidéo 23**.

    ![Affecter des utilisateurs][202] 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque 23 de la vidéo dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application vidéo 23.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-23video-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-23video-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-23video-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-23video-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_01.png
[25]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_02.png

[6]: ./media/active-directory-saas-23video-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_03.png
[8]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_04.png
[9]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_05.png
[10]: ./media/active-directory-saas-23video-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-23video-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-23video-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-23video-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-23video-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_07.png
[203]: ./media/active-directory-saas-23video-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-23video-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-23video-tutorial/tutorial_general_205.png

[400]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_10.png
[401]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_11.png
[402]: ./media/active-directory-saas-23video-tutorial/tutorial_23video_12.png




