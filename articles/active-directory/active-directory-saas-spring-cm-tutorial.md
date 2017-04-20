<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec ressort CM | Microsoft Azure" 
    description="Apprenez à utiliser des ressorts CM avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-spring-cm"></a>Didacticiel : Intégration d’Azure Active Directory avec ressort CM
  
L’objectif de ce didacticiel est d’afficher la configuration de l’authentification unique entre Azure Active Directory et SpringCM.
  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un SpringCM de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs Azure Active Directory que vous avez affecté à SpringCM pourront de l’authentification unique à l’aide du panneau accès DAS.

1.  L’activation de l’intégration de l’application pour SpringCM
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scénario")

##<a name="enabling-the-application-integration-for-springcm"></a>L’activation de l’intégration de l’application pour SpringCM
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour SpringCM.

###<a name="to-enable-the-application-integration-for-springcm-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour SpringCM, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **SpringCM**.

    ![Galerie des applications] (./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **SpringCM**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SpringCM] (./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier à SpringCM avec leur compte Azure Active Directory, à l’aide de fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **SpringCM** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797047.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à SpringCM** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797048.png "De configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **SpringCM signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application SpringCM, puis cliquez sur **suivant**. 

    L’URL de l’application est votre client SpringCM (par exemple : *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*) :

    ![Configurer des URL de l’application] (./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au SpringCM** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différents, connectez-vous à votre site d’entreprise **SpringCM** en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Aller à**, cliquez sur **Préférences**, puis, dans la section **Préférences de compte** , cliquez sur **SAML SSO**.

    ![SAML SSO] (./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7.  Dans la section de Configuration de fournisseur d’identité, effectuez les opérations suivantes :

    ![Configuration de fournisseur d’identité] (./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Configuration de fournisseur d’identité")

    1.  Pour télécharger votre certificat Azure Active Directory téléchargé, cliquez sur **Sélectionner un certificat de l’émetteur** ou de **Changer de certificat de l’émetteur**.
    2.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à SpringCM** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    3.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à SpringCM** , copiez la valeur de **l’URL du Service de session Singel** et puis la coller dans la zone de texte **Service Provider (SP) exécutée par le point de terminaison** .
    4.  **SAML activée**, sélectionnez **Activer**.
    5.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configurer la fonctionnalité d’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’Azure Active Directory pour vous connecter à SpringCM, il doivent être configurés dans SpringCM.  
Dans le cas de SpringCM, la mise en service est une tâche manuelle.

>[AZURE.NOTE] Pour plus d’informations, consultez [Création et modification d’un utilisateur SpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user)

###<a name="to-provision-a-user-account-to-springcm-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à SpringCM, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **SpringCM** en tant qu’administrateur.

2.  Cliquez sur **Atteindre**, puis cliquez sur **Carnet d’adresses**.

    ![Création d’utilisateur] (./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Création d’utilisateur")

3.  Cliquez sur **créer un utilisateur**.

4.  Sélectionnez un **rôle d’utilisateur**.

5.  Sélectionnez **Envoyer l’E-mail d’Activation**.

6.  Tapez le prénom, le nom et l’adresse de messagerie d’un compte d’utilisateur valide Azure Active Directory que vous souhaitez mettre en service dans les zones de texte liées.

7.  Ajouter l’utilisateur à un **groupe de sécurité**.

8.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SpringCM utilisateur compte outils de création ou API fournies par SpringCM à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-springcm-perform-the-following-steps"></a>Pour affecter des utilisateurs à SpringCM, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **SpringCM** application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).




