<properties 
    pageTitle="Didacticiel : Azure intégration Active Directory avec xMatters à la demande | Microsoft Azure"
    description="Apprenez à utiliser xMatters à la demande avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/09/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Didacticiel : Azure intégration Active Directory avec xMatters à la demande
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et xMatters à la demande. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client à la demande de xMatters
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la xMatters à la demande pourront à l’ouverture de session unique dans l’application à votre site de la société à la demande xMatters (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour xMatters à la demande
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scénario")

##<a name="enabling-the-application-integration-for-xmatters-ondemand"></a>L’activation de l’intégration des applications pour xMatters à la demande
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour xMatters à la demande.

###<a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour xMatters à la demande, procédez comme suit :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **xMatters à la demande**.

    ![Galerie des applications] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **XMatters à la demande**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![xMatters à la demande] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters à la demande")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de XMatters OnDemand avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **À la demande XMatters** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à XMatters à la demande** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configurer des URL de l’application")

    une barre d’outils. Dans la zone de texte **XMatters à la demande URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant :`https://<tenant-name>.XMattersOnDemandapp.com`

    b. Cliquez sur **suivant**.


4.  Dans la page **configuration de l’authentification unique à XMatters à la demande** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\XMatters OnDemand.cer**.

    >[AZURE.IMPORTANT] Vous devez transférer le certificat à l’équipe de prise en charge xMatters. Le certificat doit être téléchargé par l’équipe de prise en charge de xMatters vous pouvez finaliser la configuration de l’authentification unique.

    ![Ouverture de session unique de la configuration] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Ouverture de session unique de la configuration")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société XMatters OnDemand en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Admin**, puis cliquez sur **Informations sur la société** dans la barre de navigation sur le côté gauche.

    ![Admin] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7.  Dans la page **Configuration de SAML** , effectuez les opérations suivantes :

    ![Configuration de SAML] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "Configuration de SAML")

    1.  Sélectionnez **Activer SAML**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à XMatters à la demande** , copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte **ID de fournisseur d’identité** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à XMatters à la demande** , copier la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **Seul signe d’URL** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à XMatters à la demande** , copier la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de déconnexion unique** .
    5.  Dans la page de détails de la société, en haut, cliquez sur **Enregistrer les modifications**.
        ![Détails de la société] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Détails de la société")

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Ouverture de session unique de la configuration] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Ouverture de session unique de la configuration")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à XMatters OnDemand, qu’ils doivent être mis en service dans XMatters OnDemand.  
Dans le cas de XMatters OnDemand, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **À la demande XMatters** .

2.  Cliquez sur l’onglet **utilisateurs** .

3.  Cliquez sur **Ajouter un utilisateur**.

    ![Utilisateurs] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Utilisateurs")

4.  Sélectionnez **actif**.

5.  Dans la section **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Ajout d’un utilisateur] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Ajout d’un utilisateur")

    1.  Entrez **l’ID utilisateur**, le **prénom**, le **nom**, le **Site** d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres XMatters OnDemand utilisateur compte outils de création ou API fournies par XMatters OnDemand aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>Pour affecter des utilisateurs à XMatters OnDemand, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **À la demande XMatters **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).