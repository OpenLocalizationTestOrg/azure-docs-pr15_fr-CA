<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ScreenSteps | Microsoft Azure" 
    description="Apprenez à utiliser ScreenSteps avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Didacticiel : Intégration d’Azure Active Directory avec ScreenSteps
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et ScreenSteps.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire ScreenSteps
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à ScreenSteps pourront ouverture de session unique dans l’application au niveau du site de votre société ScreenSteps (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour ScreenSteps
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-screensteps-tutorial/IC778516.png "Scénario")
##<a name="enabling-the-application-integration-for-screensteps"></a>L’activation de l’intégration de l’application pour ScreenSteps
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour ScreenSteps.

###<a name="to-enable-the-application-integration-for-screensteps-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour ScreenSteps, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-screensteps-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-screensteps-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-screensteps-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **ScreenSteps**.

    ![Galerie des applications] (./media/active-directory-saas-screensteps-tutorial/IC778517.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **ScreenSteps**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ScreenSteps] (./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de ScreenSteps avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **ScreenSteps** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778519.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ScreenSteps** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778520.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **ScreenSteps URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. ScreenSteps.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-screensteps-tutorial/IC778521.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à ScreenSteps** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778522.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise ScreenSteps en tant qu’administrateur.

6.  Cliquez sur **Gestion des comptes**.

    ![Gestion des comptes] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestion des comptes")

7.  Cliquez sur **l’authentification à distance**.

    ![Authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778524.png "Authentification à distance")

8.  Cliquez sur **Créer point de terminaison de l’authentification**.

    ![Authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778525.png "Authentification à distance")

9.  Dans la section **créer un point de terminaison de l’authentification** , procédez comme suit :

    ![Créer un point de terminaison de l’authentification] (./media/active-directory-saas-screensteps-tutorial/IC778526.png "Créer un point de terminaison de l’authentification")

    1.  Dans la zone **titre** , tapez un titre.
    2.  Dans la liste **Mode** , sélectionnez **SAML**.
    3.  Cliquez sur **créer**.

10. Dans la section **Authentification de point de terminaison distant** , effectuez les opérations suivantes :

    ![Point de terminaison de l’authentification à distance] (./media/active-directory-saas-screensteps-tutorial/IC778527.png "Point de terminaison de l’authentification à distance")

    1.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à ScreenSteps** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion d’accès à distance** .
    2.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à ScreenSteps** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL vous déconnecter** .
    3.  Cliquez sur **Choisir un fichier**avant de télécharger le certificat téléchargé.
    4.  Cliquez sur **mise à jour**.

11. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-screensteps-tutorial/IC778542.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à **ScreenSteps**, il doivent être configurés dans **ScreenSteps**.  
Dans le cas de **ScreenSteps**, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-screensteps-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à ScreenSteps, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **ScreenSteps** .

2.  Cliquez sur **Gestion des comptes**.

    ![Gestion des comptes] (./media/active-directory-saas-screensteps-tutorial/IC778523.png "Gestion des comptes")

3.  Cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778544.png "Utilisateurs")

4.  Cliquez sur **créer un utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778545.png "Tous les utilisateurs")

5.  Dans la liste **Rôle de l’utilisateur** , sélectionnez un rôle pour l’utilisateur.

6.  Dans la section rôle d’utilisateur, tapez «**prénom**, **nom**, **E-mail**, **connexion**, **mot de passe** et **Confirmation du mot de passe**» d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.

    ![Nouvel utilisateur] (./media/active-directory-saas-screensteps-tutorial/IC778546.png "Nouvel utilisateur")

7.  Dans la section groupes, sélectionnez le **Groupe d’authentification (SAML)**, puis cliquez sur **Créer un utilisateur**.

    ![Groupes] (./media/active-directory-saas-screensteps-tutorial/IC778547.png "Groupes")

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres ScreenSteps utilisateur compte outils de création ou API fournies par ScreenSteps à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-screensteps-perform-the-following-steps"></a>Pour affecter des utilisateurs à ScreenSteps, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **ScreenSteps **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC773094.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Affecter des utilisateurs] (./media/active-directory-saas-screensteps-tutorial/IC778548.png "Affecter des utilisateurs")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).