<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ClickTime | Microsoft Azure" 
    description="Apprenez à utiliser ClickTime avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Didacticiel : Intégration d’Azure Active Directory avec ClickTime

Dans ce didacticiel, vous allez apprendre à intégrer ClickTime Azure Active Directory (AD Azure).

Intégrant ClickTime AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à ClickTime
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signée à ClickTime (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec ClickTime, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique ClickTime sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de ClickTime à partir de la galerie
2. Configuration et test AD Azure authentification unique

##<a name="adding-clicktime-from-the-gallery"></a>Ajout de ClickTime à partir de la galerie

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour ClickTime.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour ClickTime, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **ClickTime**.

    ![Galerie des applications] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **ClickTime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure ClickTime basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans ClickTime à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans ClickTime doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans ClickTime.

Pour configurer et tester AD Azure single sign-on avec ClickTime, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un ClickTime de test utilisateur](#creating-a-clicktime-test-user)** - d’avoir un équivalent de Britta Simon dans ClickTime qui est lié à la représentation sous forme de publicité Azure de sa.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.


### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de ClickTime avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  


>[AZURE.IMPORTANT] Pour être en mesure de configurer l’authentification unique sur vos clients ClickTime, vous devez contacter le support technique de ClickTime pour obtenir cette fonctionnalité est activée en premier.

**Pour configurer AD Azure SSO avec ClickTime, effectuez les opérations suivantes :**

1.  Dans Azure portal classique, sur la page d’intégration **ClickTime** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ClickTime** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "De configurer l’authentification unique")

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    une barre d’outils. Dans la zone de texte **IdentifierL** , tapez l’URL en utilisant le modèle suivant : **https://app.clicktime.com/sp/**
    
    b. Dans la zone de texte **URL de réponse** , tapez l’URL en utilisant le modèle suivant : **https://app.clicktime.com/Login/**

    c. Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique à ClickTime** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "De configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise ClickTime en tant qu’administrateur.

5.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Préférences**, puis cliquez sur **Paramètres de sécurité**.

6.  Dans la section de configuration des **Préférences d’ouverture de session unique** , effectuez les opérations suivantes :

    ![Paramètres de sécurité] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Paramètres de sécurité")

    une barre d’outils.  Sélectionnez **Autoriser** signe à l’aide de Single Sign-On (SSO) avec **AD Azure**.
    
    b.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à ClickTime** , copiez la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte de **Point de terminaison fournisseur identité** .

    c.  Ouvrez le certificat codé en base 64 dans **le bloc-notes**, copiez le contenu et puis la coller dans la zone de texte du **Certificat X.509** .
    
    d.  Cliquez sur **Enregistrer**.

7.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à ClickTime, il doivent être configurés dans ClickTime.  
Dans le cas de ClickTime, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **ClickTime** .

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **société**, puis cliquez sur **utilisateurs**.

    ![Personnes] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Personnes")

3.  Cliquez sur **Ajouter une personne**.

    ![Ajouter la personne] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Ajouter la personne")

4.  Dans la section nouvelle personne, effectuez les opérations suivantes :

    ![Personnes] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Personnes")

    une barre d’outils.  Dans la zone de texte **adresse de messagerie** , tapez l’adresse e-mail de votre compte Azure AD.
    
    b.  Dans la zone de texte **nom complet** , tapez le nom de votre compte Azure AD.  

    >[AZURE.NOTE] Si vous le souhaitez, vous pouvez définir des propriétés supplémentaires de l’objet person.

    c.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ClickTime utilisateur compte outils de création ou d’API fournies par ClickTime pour configurer les comptes d’utilisateur AD Azure.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès à ClickTime.

![Affecter des utilisateurs][200]

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

**Pour faire Britta Simon ClickTime, effectuez les opérations suivantes**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ClickTime**.

    ![Configurer l’authentification unique](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]

## <a name="testing-single-sign-on"></a>Test de l’authentification unique
Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque ClickTime dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application ClickTime.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png