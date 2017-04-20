<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory suite Adaptive | Microsoft Azure"
    description="Découvrez comment utiliser Adaptive Suite avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Didacticiel : Intégration d’Azure Active Directory suite Adaptive

L’objectif de ce didacticiel doit afficher l’intégration d’Azure et Suite Adaptive.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Suite Adaptive

À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la Suite d’Adaptive pourront à l’ouverture de session unique dans l’application au niveau du site de votre société Adaptive Suite (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour la Suite Adaptive
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Scénario")
##<a name="enabling-the-application-integration-for-adaptive-suite"></a>L’activation de l’intégration de l’application pour la Suite Adaptive

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour la Suite Adaptive.

###<a name="to-enable-the-application-integration-for-adaptive-suite-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour la Suite Adaptive, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Suite Adaptive**.

    ![Galerie des applications] (./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Adaptive Suite**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Suite Adaptive] (./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Suite Adaptive")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier à Adaptive Suite avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Adaptive Suite vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Adaptive Suite** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la Suite d’Adaptive** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , dans la zone de texte **URL de réponse** , tapez l’URL de votre en utilisant le modèle suivant "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE =*«, puis cliquez sur **suivant**.

    >[AZURE.NOTE] Vous pouvez obtenir cette valeur à partir de la page de **Paramètres d’authentification unique SAML** adaptative de la Suite.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique à Adaptive Suite** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site d’entreprise Suite adaptative en tant qu’administrateur.

6.  Accédez à **l’administrateur**.

    ![Admin] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

7.  Dans la section **utilisateurs et rôles** , cliquez sur **Gérer les paramètres d’authentification unique SAML**.

    ![Gérer les paramètres d’authentification unique de SAML] (./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gérer les paramètres d’authentification unique de SAML")

8.  Dans la page **Paramètres de l’authentification unique SAML** , effectuez les opérations suivantes :

    ![Paramètres d’authentification unique de SAML] (./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Paramètres d’authentification unique de SAML")

    1.  Dans la zone de texte **nom de fournisseur d’identité** , tapez un nom pour votre configuration.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à la Suite d’Adaptive** , copier la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **ID de l’entité fournisseur d’identité** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à la Suite d’Adaptive** , copier la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte **URL de l’authentification unique fournisseur d’identité** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à la Suite d’Adaptive** , copier la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte **URL de fermeture de session personnalisé** .
    5.  Pour télécharger votre certificat téléchargé, cliquez sur **Choisir un fichier**.
    6.  En tant qu' **id utilisateur SAML**, sélectionnez **Adaptive de perspectives sur nom d’utilisateur**.
    7.  En tant qu' **emplacement des id utilisateur SAML**, sélectionnez **id utilisateur dans NameID du sujet**.
    8.  En tant que **format de SAML NameID**, sélectionnez **adresse E-mail**.
    9.  **Activer les SAML**, sélectionnez **Autoriser le SSO SAML et direct Adaptive perspectives sur la connexion**.
    10. Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à Adaptive Suite, elles doivent être configurés dans la Suite Adaptive.  
Dans le cas de Suite Adaptive, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **Suite adaptative** en tant qu’administrateur.

2.  Accédez à **l’administrateur**.

    ![Admin] (./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Admin")

3.  Dans la section **utilisateurs et rôles** , cliquez sur **Ajouter un utilisateur**.

    ![Ajouter utilisateur] (./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Ajouter utilisateur")

4.  Dans la section **Utilisateur** , effectuez les opérations suivantes :

    ![Soumettre] (./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Soumettre")

    1.  Tapez le **nom de** **connexion**, **E-mail**, **mot de passe** d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Sélectionnez un **rôle**.
    3.  Cliquez sur **Envoyer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Suite Adaptive utilisateur compte outils de création ou API fournies par Adaptive Suite aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-adaptive-suite-perform-the-following-steps"></a>Pour affecter des utilisateurs à la Suite Adaptive, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Adaptive Suite **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
