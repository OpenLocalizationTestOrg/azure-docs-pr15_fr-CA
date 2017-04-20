<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Clarizen | Microsoft Azure" 
    description="Apprenez à utiliser Clarizen avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Didacticiel : Intégration d’Azure Active Directory avec Clarizen

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Clarizen.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Clarizen de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Clarizen pourront ouverture de session unique dans l’application à votre site de la société Clarizen (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Clarizen
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-clarizen-tutorial/IC784679.png "Scénario")
##<a name="enabling-the-application-integration-for-clarizen"></a>L’activation de l’intégration de l’application pour Clarizen

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Clarizen.

###<a name="to-enable-the-application-integration-for-clarizen-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Clarizen, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clarizen-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clarizen-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-clarizen-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clarizen-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Clarizen**.

    ![Galerie des applications] (./media/active-directory-saas-clarizen-tutorial/IC784680.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Clarizen**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clarizen] (./media/active-directory-saas-clarizen-tutorial/IC784681.png "Clarizen")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Clarizen avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Clarizen** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Clarizen** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784683.png "Configurer l’authentification unique")

3.  Dans la page **configuration de l’authentification unique à Clarizen** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784684.png "Configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différents, connectez-vous à votre site d’entreprise **Clarizen** en tant qu’administrateur (par exemple : *https://app2.clarizen.com/Clarizen/Pages/Service/Login.aspx*).

5.  Cliquez sur votre nom d’utilisateur, puis cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-clarizen-tutorial/IC784685.png "Paramètres")

6.  Cliquez sur l’onglet **Paramètres globaux** et puis, en regard **Authentification de fédérés**, cliquez sur **Modifier**.

    ![Paramètres globaux] (./media/active-directory-saas-clarizen-tutorial/IC786906.png "Paramètres globaux")

7.  Dans la boîte de dialogue **Authentification de fédéré** , effectuez les opérations suivantes :

    ![Fédérés d’authentification] (./media/active-directory-saas-clarizen-tutorial/IC785892.png "Fédérés d’authentification")

    1.  Cliquez sur **Télécharger** pour télécharger votre certificat téléchargé.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Clarizen** , copier la valeur de **l’URL de Service unique ouverture de session** et puis la coller dans la zone de texte **URL de connexion** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configurer unique de déconnexion à Clarizen** , copier la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de Sign-out** .
    4.  Sélectionnez **utiliser POST**.
    5.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clarizen-tutorial/IC784688.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Clarizen, il doivent être configurés dans Clarizen.  
Dans le cas de Clarizen, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Clarizen** en tant qu’administrateur.

2.  Cliquez sur **personnes**.

    ![Personnes] (./media/active-directory-saas-clarizen-tutorial/IC784689.png "Personnes")

3.  Cliquez sur **inviter l’utilisateur**.

    ![Inviter des utilisateurs] (./media/active-directory-saas-clarizen-tutorial/IC784690.png "Inviter des utilisateurs")

4.  Sur la page de la boîte de dialogue inviter des personnes, effectuez les opérations suivantes :

    ![Inviter des personnes] (./media/active-directory-saas-clarizen-tutorial/IC784691.png "Inviter des personnes")

    1.  Dans la zone de texte **message** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **inviter**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un e-mail et suivez un lien pour confirmer leur compte avant d’être active.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-clarizen-perform-the-following-steps"></a>Pour affecter des utilisateurs à Clarizen, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Clarizen **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-clarizen-tutorial/IC784692.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-clarizen-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
