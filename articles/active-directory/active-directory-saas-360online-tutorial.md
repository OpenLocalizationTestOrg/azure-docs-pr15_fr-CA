<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory à 360° en ligne | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et 360° en ligne."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-360-online"></a>Didacticiel : Intégration d’Azure Active Directory à 360° en ligne


L’objectif de ce didacticiel est de vous montrer comment intégrer 360° en ligne avec Azure Active Directory (AD Azure).

Intégration de 360° en ligne avec Active Directory Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à 360° serveur en ligne
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé à 360° en ligne (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec 360° en ligne, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un locataire en ligne 360°


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de 360° en ligne à partir de la galerie
2. Configuration et test AD Azure authentification unique



## <a name="adding-360-online-from-the-gallery"></a>Ajout de 360° en ligne à partir de la galerie
Pour configurer l’intégration de 360° en ligne dans AD Azure, vous devez ajouter 360° en ligne à partir de la galerie à la liste des applications gérées de SaaS.


**Pour ajouter des 360° en ligne à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **360 ° en ligne**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/tutorial_360online_01.png)

7. Dans le volet de résultats, sélectionnez **360 ° en ligne**, puis cliquez sur **Terminer** pour ajouter l’application.
 
    ![Applications](./media/active-directory-saas-360online-tutorial/tutorial_360online_06.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure single sign-on avec 360° en ligne basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir ce que l’utilisateur équivalent dans Online à un utilisateur dans AD Azure est de 360°. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans 360° en ligne doit être établi.


Pour configurer et tester AD Azure single sign-on avec 360° en ligne, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Tester l’utilisateur créant un 360 ° en ligne](#creating-a-360-online-test-user)** - avoir un équivalent de Britta Simon de 360 ° en ligne qui est lié à la représentation sous forme de publicité Azure de lui.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application en ligne de 360°.

**Pour configurer AD Azure SSO avec 360° en ligne, effectuez les opérations suivantes :**


1. Dans l’Azure portal classique, sur la page intégration d’application **en ligne de 360 °** , cliquez sur **configurer SSO** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][13] 

2. Dans la page **Comment voulez-vous que les utilisateurs à signer à 360 ° en ligne** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-360online-tutorial/tutorial_360online_03.png) 

3. Sur la page de la boîte de dialogue **Configurer l’URL App** , effectuez les opérations suivantes, puis sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-360online-tutorial/tutorial_360online_04.png)

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre 360 °, application en ligne en utilisant le modèle suivant :`https://<company name>.public360online.com`

    b. Cliquez sur **suivant**

4. Sur la page de la boîte de dialogue **Configurer l’URL App** , effectuez les opérations suivantes, puis sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-360online-tutorial/tutorial_360online_05.png) 

    une barre d’outils. Cliquez sur **télécharger les métadonnées**et enregistrez-le sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, contactez votre équipe de support en ligne de 360° via [360online@software-innovation.com](mailto:360online@software-innovation.com) et attachez le fichier de métadonnées téléchargé à votre courrier.

6. Dans le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.


![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_03.png) 


4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_04.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la zone de texte **Nom d’utilisateur** , tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_07.png) 


8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-360online-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   




### <a name="creating-a-360-online-test-user"></a>Création d’un utilisateur de test en ligne de 360°

L’objectif de cette section est de créer un utilisateur appelé Britta Simon de 360° en ligne. 

Pour obtenir un utilisateur de 360° créé en ligne, vous devez contacter votre équipe de support en ligne de 360° via [360online@software-innovation.com](mailto:360online@software-innovation.com).


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en autorisant son accès à 360° en ligne.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à 360° en ligne, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
 
    ![Affecter des utilisateurs][201] 


2. Dans la liste des applications, sélectionnez **360 ° en ligne**.

    ![Configurer l’authentification unique](./media/active-directory-saas-360online-tutorial/tutorial_360online_50.png) 


1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur les 360° en ligne mosaïque dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application en ligne de 360°.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)







<!--Image references-->

[1]: ./media/active-directory-saas-360online-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-360online-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-360online-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-360online-tutorial/tutorial_general_04.png

[10]: ./media/active-directory-saas-360online-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-360online-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-360online-tutorial/tutorial_general_08.png
[13]: ./media/active-directory-saas-360online-tutorial/tutorial_general_09.png
[20]: ./media/active-directory-saas-360online-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-360online-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-360online-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-360online-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-360online-tutorial/tutorial_general_205.png
