<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Relic nouveau | Microsoft Azure" 
    description="Apprenez à utiliser des nouveaux Relic avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-new-relic"></a>Didacticiel : Intégration d’Azure Active Directory avec les nouvelles Relic
  
L’objectif de ce didacticiel est d’afficher la configuration de l’authentification unique entre Azure Active Directory et nouveau Relic.
  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un nouveau Relic de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affecté à nouveau Relic pourront de l’authentification unique à l’aide du panneau accès DAS.

1.  L’activation de l’intégration des applications pour la nouvelle Relic
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-new-relic-tutorial/IC797030.png "Scénario")
##<a name="enabling-the-application-integration-for-new-relic"></a>L’activation de l’intégration des applications pour la nouvelle Relic
  
L’objectif de cette section est à expliquent comment activer l’intégration de l’application de nouvelles Relic.

###<a name="to-enable-the-application-integration-for-new-relic-perform-the-following-steps"></a>Pour activer l’intégration des applications pour la nouvelle Relic, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-new-relic-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-new-relic-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-new-relic-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-new-relic-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez le **Nouveau Relic**.

    ![Galerie des applications] (./media/active-directory-saas-new-relic-tutorial/IC797031.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Nouveau Relic**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Nouveau Relic] (./media/active-directory-saas-new-relic-tutorial/IC797032.png "Nouveau Relic")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier à nouveau Relic avec leur compte Azure Active Directory, à l’aide de fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page d’intégration **Relic nouvelle** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC769534.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à nouveau Relic** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797033.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Nouveau Relic signe d’adresse URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Relic nouvelle et puis cliquez sur **suivant**. 

    L’URL de l’application est l’URL de votre nouvelle Relic clients (par exemple : *https://rpm.newrelic.com*) :

    ![Configurer des URL de l’application] (./media/active-directory-saas-new-relic-tutorial/IC797034.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à nouveau Relic** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797035.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, connectez-vous à votre site de la société **Nouvelle Relic** en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Paramètres du compte**.

    ![Paramètres de compte] (./media/active-directory-saas-new-relic-tutorial/IC797036.png "Paramètres de compte")

7.  Cliquez sur l’onglet **sécurité et l’authentification** , puis cliquez sur l’onglet **ouverture de session unique** .

    ![L’ouverture de session unique] (./media/active-directory-saas-new-relic-tutorial/IC797037.png "L’ouverture de session unique")

8.  Sur la page de dialogue SAML, effectuez les opérations suivantes :

    ![SAML] (./media/active-directory-saas-new-relic-tutorial/IC797038.png "SAML")

    1.  Cliquez sur **Choisir un fichier** pour télécharger votre certificat Azure Active Directory téléchargé.
    2.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à nouveau Relic** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion à distance** .
    3.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à nouveau Relic** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de lancement de déconnexion** .
    4.  Cliquez sur **Enregistrer mes modifications**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-new-relic-tutorial/IC797039.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’Azure Active Directory pour vous connecter à nouveau Relic, ils doivent être configurés en nouveau Relic.  
Dans le cas de Relic de nouveau, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-new-relic-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à nouveau Relic, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **Nouvelle Relic** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **Paramètres du compte**.

    ![Paramètres de compte] (./media/active-directory-saas-new-relic-tutorial/IC797040.png "Paramètres de compte")

3.  Dans le volet **compte** sur le côté gauche, cliquez sur **Résumé**, puis cliquez sur **Ajouter un utilisateur**.

    ![Paramètres de compte] (./media/active-directory-saas-new-relic-tutorial/IC797041.png "Paramètres de compte")

4.  Dans la boîte de dialogue **utilisateurs actifs** , effectuez les opérations suivantes :

    ![Utilisateurs actifs] (./media/active-directory-saas-new-relic-tutorial/IC797042.png "Utilisateurs actifs")

    1.  Dans la zone de texte **message** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service.
    2.  En tant que **rôle** , sélectionnez **utilisateur**.
    3.  Cliquez sur **Ajouter cet utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres nouveau Relic utilisateur compte outils de création ou API fournies par Relic nouvelle disposition DAS comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-new-relic-perform-the-following-steps"></a>Pour affecter des utilisateurs à nouveau Relic, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Relic nouvelle** application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-new-relic-tutorial/IC797043.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-new-relic-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).




