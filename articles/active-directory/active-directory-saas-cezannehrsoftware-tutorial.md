<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel de ressources humaines Cezanne | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et le logiciel de ressources humaines Cezanne."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Didacticiel : Intégration d’Azure Active Directory avec le logiciel de ressources humaines Cezanne

L’objectif de ce didacticiel est de vous montrer comment intégrer le logiciel de ressources humaines Cezanne avec Azure Active Directory (AD Azure).

Intégration du logiciel de RH de Cezanne avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure annonce ayant accès au logiciel de ressources humaines Cezanne
- Vous pouvez permettre à vos utilisateurs pour automatiquement obtenir signé au logiciel de ressources humaines Cezanne (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec le logiciel Cezanne HR, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Cezanne HR logiciel sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout d’un logiciel de ressources humaines Cezanne à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Ajout d’un logiciel de ressources humaines Cezanne à partir de la galerie
Pour configurer l’intégration des logiciels de ressources humaines Cezanne dans AD Azure, vous devez ajouter Cezanne HR logiciel à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des logiciels RH de Cezanne à partir de la galerie, procédez comme suit :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.
    
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Cezanne HR logiciel**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. Dans le panneau de résultats, sélectionnez le **Logiciel de ressources humaines Cezanne**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![L’application de la sélection dans la galerie](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester Azure AD-session unique avec le logiciel de ressources humaines Cezanne basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent de Cezanne HR logiciel à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le logiciel de ressources humaines Cezanne doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans le logiciel de ressources humaines Cezanne.

Pour configurer et tester Azure AD de l’authentification unique avec le logiciel de ressources humaines Cezanne, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un logiciel de ressources humaines Cezanne test utilisateur](#creating-a-cezanne-hr-software-test-user)** - d’avoir un équivalent de Britta Simon Cezanne HR logiciel qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application HR Cezanne.

**Pour configurer AD Azure session unique avec le logiciel de ressources humaines Cezanne, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page intégration d’application **Logiciel de ressources humaines Cezanne** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter au logiciel de ressources humaines Cezanne** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez une URL en utilisant le modèle suivant : `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. Dans la zone de texte **URL de réponse** , tapez une URL en utilisant le modèle suivant : `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Cliquez sur **suivant**

    > [AZURE.NOTE] Notez que vous devez mettre à jour ces valeurs avec le signe sur URL réelle et l’URL de la réponse. Pour obtenir ces valeurs, contactez l’équipe de prise en charge logicielle de h Cezanne via <mailto:info@cezannehr.com>.

4. Dans la page **configuration de l’authentification unique Cezanne HR Software** , effectuez les opérations suivantes, puis cliquez sur **suivant**:

    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Dans une fenêtre de navigateur web différent, ouverture de session sur vos clients de Cezanne HR logiciel en tant qu’administrateur.

6. Dans le volet de navigation de gauche, cliquez sur **Configuration du système**. Accédez aux **paramètres de sécurité**. Ensuite, accédez à **Configuration d’ouverture de session unique**.

    ![Configurer le côté de Single Sign-On sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. **Autoriser les utilisateurs à ouvrir une session à l’aide du Service Single Sign-On (SSO)** panneau **SAML 2.0** la case et sélectionnez l’option de **Configuration avancée** , à côté de lui.

    ![Configurer le côté de Single Sign-On sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Cliquez sur le bouton **Ajouter** .

    ![Configurer le côté de Single Sign-On sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Effectuez les opérations suivantes dans la section des **Fournisseurs d’identité SAML 2.0** .

    ![Configurer le côté de Single Sign-On sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    une barre d’outils. Entrez le nom de votre fournisseur d’identité comme **Nom d’affichage**.

    b. L' **Identificateur de l’entité** textbox placer la valeur de **l’ID de l’entité** à partir de l’Assistant de configuration d’application Azure AD.

    c. Modifier la **liaison de SAML** « Post ».

    d. Dans **Point de terminaison Service de jeton de sécurité** textbox placer la valeur de **l’URL de Service unique de session** à partir de l’Assistant de configuration d’application Azure AD.

    e. Entrez 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' dans le **nom de l’attribut d’ID utilisateur**.

    f. Cliquez sur **Télécharger** pour télécharger le certificat téléchargé à partir d’AD Azure.

    g. Cliquez sur le bouton **Ok** . 

10. Cliquez sur le bouton **Enregistrer** .

    ![Configurer le côté de Single Sign-On sur App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

12. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure Portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , tapez **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Création d’un utilisateur de test de logiciel de ressources humaines Cezanne

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à un logiciel de ressources humaines Cezanne, ils doivent être configurés dans le logiciel de ressources humaines Cezanne. Dans le cas Cezanne HR, la mise en service est une tâche manuelle.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Ouvrez une session en tant qu’administrateur dans le site de votre société de logiciels de ressources humaines Cezanne.

2.  Dans le volet de navigation de gauche, cliquez sur **Configuration du système**. Accédez à **Gestion des utilisateurs**. Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "Nouvel utilisateur")

3.  Dans la section de **Détails de la personne** , suivez les étapes ci-dessous :

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "Nouvel utilisateur")

    une barre d’outils. Définir **l’utilisateur interne** comme OFF.

    b. Dans la zone de texte **nom** , tapez **Brian**.  

    c. Dans la zone de texte **Nom** , tapez **Simon**.

    d. Dans la zone **courrier électronique** , tapez l’adresse de messagerie du compte de Britta Simon.

4.  Dans la section **Informations sur le compte** , suivez les étapes ci-dessous :

    ![Nouvel utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "Nouvel utilisateur")

    une barre d’outils. Dans la zone de texte **nom d’utilisateur** , tapez l’adresse e-mail de Britta Simon.

    b. Dans la zone de texte **mot de passe** , tapez le mot de passe du compte de Britta Simon.

    c. Sélectionnez **Professionnel des ressources humaines** en tant que **rôle de sécurité**.

    d. Cliquez sur **OK**.

5. Accédez à l’onglet de **L’ouverture de session unique** et sélectionnez **Ajouter un nouveau** dans la zone **Des identificateurs SAML 2.0** .

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utilisateur")

6. Choisissez votre fournisseur d’identité pour le **Fournisseur d’identité** et dans la zone de texte de **l’Identificateur d’utilisateur**, entrez l’adresse e-mail du compte de Britta Simon.

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utilisateur")
    
7. Cliquez sur le bouton **Enregistrer** .

    ![Utilisateur] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utilisateur")


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès au logiciel de Cezanne HR.
    
![Affecter des utilisateurs][200]

**Pour affecter des Britta Simon Cezanne HR logiciel, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
    
    ![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Cezanne HR logiciel**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. Dans le menu du haut, cliquez sur **utilisateurs**.
    
    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.
    
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.
 
Lorsque vous cliquez sur la mosaïque de Cezanne HR logiciels dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application logicielle de h Cezanne.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
