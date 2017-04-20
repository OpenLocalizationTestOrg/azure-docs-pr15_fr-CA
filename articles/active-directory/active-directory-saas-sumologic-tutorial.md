<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SumoLogic | Microsoft Azure" 
    description="Apprenez à utiliser SumoLogic avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Didacticiel : Intégration de Azure Active Directory avec SumoLogic
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et SumoLogic.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire SumoLogic
  
À la fin de ce didacticiel, les utilisateurs AD Azure vous avez affecté à SumoLogicwill être en mesure de même signe dans l’application à votre SumoLogic l’entreprise site (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour SumoLogic
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sumologic-tutorial/IC778549.png "Scénario")

##<a name="enabling-the-application-integration-for-sumologic"></a>L’activation de l’intégration de l’application pour SumoLogic
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour SumoLogic.

###<a name="to-enable-the-application-integration-for-sumologic-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour SumoLogic, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sumologic-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sumologic-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-sumologic-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sumologic-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **sumologic**.

    ![Galerie des applications] (./media/active-directory-saas-sumologic-tutorial/IC778550.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **SumoLogic**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SumoLogic] (./media/active-directory-saas-sumologic-tutorial/IC778551.png "SumoLogic")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de SumoLogic avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à votre SumoLogictenant.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **SumoLogic** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778552.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à SumoLogic** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778553.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **SumoLogic URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. SumoLogic.com*», puis cliquez sur **suivant**.

    ![Configurer aoo URL] (./media/active-directory-saas-sumologic-tutorial/IC778554.png "Configurer aoo URL")

4.  Dans la page **configuration de l’authentification unique à SumoLogic** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778555.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise SumoLogic en tant qu’administrateur.

6.  Accédez à **gérer \> sécurité**.

    ![Gérer les] (./media/active-directory-saas-sumologic-tutorial/IC778556.png "Gérer les")

7.  Cliquez sur **SAML**.

    ![Paramètres de sécurité globaux] (./media/active-directory-saas-sumologic-tutorial/IC778557.png "Paramètres de sécurité globaux")

8.  Dans la liste **Sélectionner une configuration ou créer un nouveau** , sélectionnez **Azure AD**, puis cliquez sur **configurer**.

    ![Configurer SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778558.png "Configurer SAML 2.0")

9.  Dans la boîte de dialogue **configurer SAML 2.0** , effectuez les opérations suivantes :

    ![Configurer SAML 2.0] (./media/active-directory-saas-sumologic-tutorial/IC778559.png "Configurer SAML 2.0")

    1.  Dans la zone de texte **Nom de la Configuration** , tapez **Azure AD**.
    2.  Sélectionnez le **Mode de débogage**.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à SumoLogic** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à SumoLogic** , copiez la valeur de **l’URL de la demande d’authentification** et puis la coller dans la zone de texte **URL de la demande d’authentification** .
    5.  Créer un fichier **codé en Base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis collez l’intégralité du certificat dans la zone de texte de **Certificat X.509** .
    7.  En tant qu' **Attribut de courrier électronique**, sélectionnez le **sujet de l’utilisation SAML**.
    8.  Sélectionnez **SP initiée par la Configuration de la connexion**.
    9.  Dans la zone de texte **Chemin d’accès de la connexion** , tapez **Azure**.
    10. Cliquez sur **Enregistrer**.

10. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à SumoLogic** , sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sumologic-tutorial/IC778560.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à SumoLogic, il doivent être configurés à SumoLogic.  
Dans le cas de SumoLogic, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **SumoLogic** .

2.  Accédez à **Gérer les \> les utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778561.png "Utilisateurs")

3.  Cliquez sur **Ajouter**.

    ![Utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778562.png "Utilisateurs")

4.  Dans la boîte de dialogue **Nouvel utilisateur** , effectuez les opérations suivantes :

    ![Nouvel utilisateur] (./media/active-directory-saas-sumologic-tutorial/IC778563.png "Nouvel utilisateur")

    1.  Tapez les informations connexes du compte Azure AD que vous souhaitez mettre en service dans les zones de texte **prénom**, **Nom** et **E-mail** .
    2.  Sélectionnez un rôle.
    3.  En tant qu' **état**, sélectionnez **actif**.
    4.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SumoLogic utilisateur compte outils de création ou API fournies par SumoLogic à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-sumologic-perform-the-following-steps"></a>Pour affecter des utilisateurs à SumoLogic, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **SumoLogic** application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-sumologic-tutorial/IC778564.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-sumologic-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).