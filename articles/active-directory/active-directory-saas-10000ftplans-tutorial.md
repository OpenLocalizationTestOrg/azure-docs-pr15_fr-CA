<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Novatus | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et des Plans de 10 000 pieds."
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


# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>Didacticiel : Intégration d’Azure Active Directory avec des Plans de 10 000 pieds

L’objectif de ce didacticiel est de vous montrer comment intégrer les Plans de 10 000 pieds avec Azure Active Directory (AD Azure).  
Intégrant des Plans de 10 000 pieds AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure annonce ayant accès aux Plans de 10 000 pieds
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé sur Plans de 10 000 pieds (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec des Plans de 10 000 pieds, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une 10 000 pieds Plans d’authentification unique sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de 10 000 pieds des Plans à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-10000ft-plans-from-the-gallery"></a>Ajout de 10 000 pieds des Plans à partir de la galerie
Pour configurer l’intégration des Plans de 10 000 pieds dans AD Azure, vous devez ajouter des Plans de 10 000 pieds à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des Plans de 10 000 pieds à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **10 000 pieds Plans**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_01.png)

7. Dans le volet résultats, sélectionnez les **Plans de 10 000 pieds**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester Azure AD de l’authentification unique avec 10 000 pieds Plans basés sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans les Plans de 10 000 pieds à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans 10 000 pieds Plans doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de données, **nom d’utilisateur** dans les Plans de 10 000 pieds.

Pour configurer et tester Azure AD single sign-on avec Plans de 10 000 pieds, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Que Création un 10 000 pieds de Plans de test utilisateur](#creating-a-10000ft-plans-test-user)** - d’avoir un équivalent de Britta Simon de certifier qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application de Plans de 10 000 pieds.



**Pour configurer AD Azure SSO avec des Plans de 10 000 pieds, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page **10 000 pieds Plans** d’intégration application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à des Plans de 10 000 pieds** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes, puis sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_04.png) 


    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez `https://app.10000ft.com`.

    b. Dans la zone **identificateur** , tapez `https://app.10000ft.com/saml/metadata`.

    > [AZURE.NOTE] La valeur de **l’identificateur** est différente si vous avez un domaine personnalisé. Si vous avez besoin d’assistance, contactez votre [équipe de support de Plans de 10 000 pieds](mailto:support@10000ft.com).  

    c. Cliquez sur **suivant**


4. Dans la page **Plans de configuration de l’authentification unique à 10 000 pieds** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_05.png) 

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, contactez votre [équipe de support de Plans de 10 000 pieds](mailto:support@10000ft.com), attacher le certificat téléchargé et avec l’URL de l’émetteur, l’URL de l’authentification unique SAML et le signe des URL.

6. Dans le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.  

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test sécurisé de fournir dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-a-10000ft-plans-test-user"></a>Création d’un 10 000 pieds Plans de test utilisateur

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans les Plans de 10 000 pieds. Plans de 10 000 pieds prend en charge le provisionnement juste-à-temps, qui est par défaut activée.

Il n’y a aucun élément d’action pour vous dans cette section. Un nouvel utilisateur est créé lors d’une tentative d’accéder à des Plans de 10 000 pieds s’il n’existe pas encore. 

> [AZURE.NOTE] Si vous avez besoin créer un utilisateur manuellement, vous devez contacter l’équipe de prise en charge de certifier.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès aux Plans de 10 000 pieds.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon aux Plans de 10 000 pieds, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **les Plans de 10 000 pieds**.

    ![Configurer l’authentification unique](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_50.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque de Plans de 10 000 pieds dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application de Plans de 10 000 pieds.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_205.png
