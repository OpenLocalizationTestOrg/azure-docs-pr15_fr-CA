<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant MCM | Microsoft Azure" 
    description="Apprenez à utiliser des MCM avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Didacticiel : Intégration d’Azure Active Directory avec MCM
  
L’objectif de ce didacticiel est de vous montrer comment intégrer MCM Azure Active Directory (AD Azure).

Intégrant MCM AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à MCM
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé à MCM (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec MCM, vous devez les éléments suivants :

- Un abonnement Azure valide
- Une connexion unique MCM sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout des MCM à partir de la galerie
2. Configuration et test AD Azure authentification unique

## <a name="adding-mcm-from-the-gallery"></a>Ajout des MCM à partir de la galerie
Pour configurer l’intégration des MCM dans AD Azure, vous devez ajouter des MCM à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des MCM à partir de la galerie, procédez comme suit :**

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **MCM**.

    ![Galerie des applications] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **MCM**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec MCM basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans MCM à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans MCM doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans MCM.

Pour configurer et tester AD Azure single sign-on avec MCM, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Tester l’utilisateur créant un MCM](#creating-a-mcm-test-user)** - avoir un équivalent de Britta Simon dans MCM qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure
  
Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application MCM.

**Pour configurer AD Azure SSO avec MCM, effectuez les opérations suivantes :**

1.  Dans Azure portal classique, sur la page d’intégration **MCM** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à des MCM** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure AD Single Sign-On")

3.  Sur la page de la boîte de dialogue Configurer les paramètres de l’application, effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurer des URL de l’application")

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez : `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique à MCM** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurer l’authentification unique")

5. Pour obtenir l’authentification unique configurée pour votre application, contactez votre équipe de support MCM. Joindre le fichier de métadonnées téléchargés et les partager avec l’équipe MCM pour configurer l’authentification unique sur leur côté.

6.  Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurer l’authentification unique")

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurer l’authentification unique")


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure

L’objectif de cette section est de créer un utilisateur test dans le portail classique appelé Britta Simon.

![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   

###<a name="creating-a-mcm-test-user"></a>Création d’un utilisateur de test MCM
  
Dans cette section, vous créez un utilisateur appelé Britta Simon dans MCM. Travaillez avec l’équipe de support MCM pour ajouter les utilisateurs de la plate-forme MCM.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres MCM utilisateur compte outils de création ou API fournies par MCM à disposition DAS des comptes d’utilisateurs.


###<a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure
  
L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en autorisant son accès à MCM.
    
![Affecter des utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Affecter des utilisateurs")

**Pour affecter des Britta Simon à MCM, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter des utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Affecter des utilisateurs")

2. Dans la liste des applications, sélectionnez **MCM**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. Dans le menu du haut, cliquez sur **utilisateurs**.
    
    ![Affecter des utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Affecter des utilisateurs")

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.
    
    ![Affecter des utilisateurs] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Affecter des utilisateurs")


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la mosaïque MCM dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application MCM.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)