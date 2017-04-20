<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec HR2day par Merces | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de HR2day par Merces."
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


# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Didacticiel : Intégration d’Azure Active Directory avec HR2day par Merces

L’objectif de ce didacticiel est de vous montrer comment intégrer des HR2day par Merces avec Azure Active Directory (AD Azure).  
Intégration de HR2day par Merces avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à HR2day par Merces
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé à HR2day par Merces (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec HR2day par Merces, vous devez les éléments suivants :

- Un abonnement Azure AD
- Un HR2day par Merces connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de HR2day à Merces à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Ajout de HR2day à Merces à partir de la galerie
Pour configurer l’intégration de HR2day par Merces dans AD Azure, vous devez ajouter HR2day par Merces à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des HR2day par Merces à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.
 
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **HR2day par Merces**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. Dans le volet résultats, sélectionnez **l’HR2day par Merces**et puis cliquez sur **Terminer** pour ajouter l’application.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure single sign-on avec HR2day par Merces basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans HR2day par Merces à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans HR2day par Merces doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans HR2day par Merces.

Pour configurer et tester AD Azure single sign-on avec HR2day par Merces, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un HR2day par Merces tester l’utilisateur](#creating-a-hr2day-by-merces-test-user)** - à ont un équivalent de Britta Simon dans HR2day par Merces qui est lié à la représentation sous forme de publicité Azure de lui.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de HR2day par Merces avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

Votre HR2day par Merces application attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration d’attributs jeton SAML. La capture d’écran suivante montre un exemple de cela. 

![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Avant de pouvoir configurer l’assertion SAML, vous devez contacter votre équipe via HR2day [servicedesk@merces.nl](mailto:servicedesk@merces.nl) et de demander la valeur de l’attribut d’identificateur unique pour vos clients. Vous avez besoin de cette valeur à toutes les étapes de la section suivante.


**Pour configurer AD Azure SSO avec HR2day par Merces, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **HR2day par Merces** , dans le menu de la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** . 

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. Pour ajouter les mappages d’attributs requis, effectuez les opérations suivantes, effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 


    une barre d’outils. Cliquez sur **Ajouter un attribut de l’utilisateur**.

    b. Dans la zone de texte **Nom de l’attribut** , tapez **« ATTR_LOGINCLAIM »**.

    c. Dans la liste **Valeur de l’attribut** , sélectionnez **Join()**. 

    d. Dans la liste de **chaîne1** , sélectionnez **User.mail**. 

    e. Dans la zone de texte **chaîne2** , tapez l' **identificateur unique** fourni par votre équipe de HR2day. 

    f. Dans la zone **séparateur** , tapez **@**.

    g. Cliquez sur **terminé**.

  
3. Cliquez sur **appliquer les modifications**.


1. Dans le menu du haut, cliquez sur **Démarrage rapide** pour ouvrir la boîte de dialogue **Quick Start** .

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 



1. Cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à HR2day par Merces** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 


    une barre d’outils. Dans la zone de texte URL de connexion, tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre HR2day par application de Merces selon le modèle suivant : **« https://\<nom du locataire\>.force.com/\<nom de l’instance\>»**.

    b. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique à HR2day par Merces** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, contactez votre HR2day par l’équipe de prise en charge de Merces par [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) et joindre le fichier de certificat téléchargé à votre adresse de courriel. Également, fournissez le SAML SSO URL, le signe des URL et l’URL de l’émetteur afin qu’ils peuvent être configurés pour l’intégration de l’authentification unique.


> [AZURE.NOTE] Mentionnez à l’équipe de Merces que cette intégration est nécessaire à l’ID d’entité soit définie avec ce modèle **https://hr2day.force.com/INSTANCENAME**



6. Dans le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Azure AD unique Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.  

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-hr2day-by-merces-test-user"></a>Création d’un HR2day par l’utilisateur de test Merces

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans HR2day par Merces. Travailler avec des HR2day par l’équipe de support Merces pour ajouter des utilisateurs dans le compte de HR2day. 


> [AZURE.NOTE]Si vous avez besoin créer un utilisateur manuellement, vous devez contacter le HR2day par l’équipe de support technique de Merces.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en accordant l’accès à HR2day par Merces.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à HR2day par Merces, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **HR2day par Merces**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur le HR2day en mosaïque Merces dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre HR2day par application de Merces.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png
