<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Litmos | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Litmos."
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


# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Didacticiel : Intégration d’Azure Active Directory avec Litmos

L’objectif de ce didacticiel est de vous montrer comment intégrer Litmos Azure Active Directory (AD Azure).  
Intégrant Litmos AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à Litmos 
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à Litmos (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : Azure Active Directory 

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec Litmos, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Litmos sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de trois blocs de construction principaux :

1. Ajout de Litmos à partir de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-litmos-from-the-gallery"></a>Ajout de Litmos à partir de la galerie
Pour configurer l’intégration de Litmos dans AD Azure, vous devez ajouter Litmos à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des Litmos à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Litmos**.

    ![Applications][5]

7. Dans le volet de résultats, sélectionnez **Litmos**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec Litmos basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans Litmos à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Litmos doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans Litmos.
 
Pour configurer et tester AD Azure single sign-on avec Litmos, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un Litmos de test utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un équivalent de Britta Simon dans Litmos qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique AD Azure et configurer l’authentification unique dans votre application de Litmos.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Dans le cadre de la configuration, vous devez personnaliser les **Attributs de jeton SAML** pour votre application de Litmos.  

![Azure AD unique Single Sign-On][17] 

**Pour configurer AD Azure SSO avec Litmos, effectuez les opérations suivantes :**

1. Dans le portail classique AD Azure, sur la page d’intégration **Litmos** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Litmos** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.
 
    ![Azure AD unique Single Sign-On][7] 


1. Ouverture de session sur le site de votre entreprise Litmos (par exemple : *https://azureapptest.litmos.com/account/Login*) en tant qu’administrateur.

    ![Azure AD unique Single Sign-On][21] 


1. Dans la barre de navigation sur le côté gauche, cliquez sur **comptes**.

    ![Azure AD unique Single Sign-On][22] 


1. Cliquez sur l’onglet **intégration** .

    ![Azure AD unique Single Sign-On][23] 


1. Dans l’onglet **intégration** , **3ème partie intégrations**faites défiler, puis cliquez sur onglet de **SAML 2.0** .

    ![Azure AD unique Single Sign-On][24] 

1. Copier la valeur sous **endoiint le SAML pour litmos est :**.

    ![Azure AD unique Single Sign-On][26] 


3. Dans le portail classique Azure, sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Azure AD unique Single Sign-On][8] 
 
    une barre d’outils. Dans la zone **identificateur** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de Litmos (par exemple : *https://azureapptest.litmos.com/account/Login*).
     
    b. Dans la zone de texte **URL de la réponse** , collez la valeur que vous avez copié à partir de l’application Litmos à l’étape précédente.

    c. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique à Litmos** , effectuez les opérations suivantes :

    ![Azure AD unique Single Sign-On][2] 

    une barre d’outils. Cliquez sur Télécharger le certificat et puis enregistrez le fichier sur votre ordinateur.


1. Dans votre application de **Litmos** , effectuez les opérations suivantes :

    ![Azure AD unique Single Sign-On][25] 

    une barre d’outils. Cliquez sur **Activer SAML**.

    b. Créer un fichier **codé en base 64** de votre certificat téléchargé.  

    >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    c. Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **Certificat X.509 de SAML** .

    d. Cliquez sur **Enregistrer les modifications**.


6. Sur le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
  
    ![Azure AD unique Single Sign-On][11]


20. Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** . 

    ![Configurer l’authentification unique][12]


24. Dans la boîte de dialogue **Ajouter un attribut utilisateur** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique][14]

  	| Nom de l’attribut | Valeur de l’attribut |
  	| ---            | ---             |
  	| Messagerie          | User.Mail       |
  	| Prénom      | User.GivenName  |
  	| Nom       | User.Surname    |

    Pour chaque ligne de données dans le tableau ci-dessus, effectuez les opérations suivantes :
   
    une barre d’outils. Cliquez sur **Ajouter un attribut de l’utilisateur**. 

    ![Configurer l’authentification unique][15]


    une barre d’outils. Dans la zone de texte **Nom de l’attribut** , tapez le **Nom de l’attribut** indiqué pour cette ligne.

    b. Sélectionnez la **Valeur de l’attribut** indiqué pour cette ligne.

    c. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur** .


25. Cliquez sur **appliquer les modifications**. 

    ![Configurer l’authentification unique][16]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.  

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans le **clasic Azure portal**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  

    une barre d’outils. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
 
    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
 
8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
  
    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

  
 
### <a name="creating-a-litmos-test-user"></a>Création d’un utilisateur de test Litmos

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Litmos.  
Prend en charge de l’application Litmos juste-à-temps provisionnement. Ainsi, un compte d’utilisateur est automatiquement créé les si nécessaire pendant une tentative d’accéder à l’application à l’aide du panneau d’accès.

**Pour créer un utilisateur appelé Britta Simon Litmos, effectuez les opérations suivantes :**


1. Ouverture de session sur le site de votre entreprise Litmos (par exemple : *https://azureapptest.litmos.com/account/Login*) en tant qu’administrateur.

    ![Azure AD unique Single Sign-On][21] 


1. Dans la barre de navigation sur le côté gauche, cliquez sur **comptes**.

    ![Azure AD unique Single Sign-On][22] 


1. Cliquez sur l’onglet **intégration** .

    ![Azure AD unique Single Sign-On][23] 


1. Dans l’onglet **intégration** , **3ème partie intégrations**faites défiler, puis cliquez sur onglet de **SAML 2.0** .

    ![Azure AD unique Single Sign-On][24] 

1. Sélectionnez **les utilisateurs Autogenerate :**.

    ![Azure AD unique Single Sign-On][27] 


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à Litmos.

![Affecter des utilisateurs][200] 

**Pour faire Britta Simon Litmos, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Litmos**.

    ![Affecter des utilisateurs][202] 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque Litmos dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application Litmos.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png





