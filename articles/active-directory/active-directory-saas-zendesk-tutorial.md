<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Zendesk | Microsoft Azure" 
    description="Découvrez comment utiliser Zendesk avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Didacticiel : Intégration d’Azure Active Directory avec Zendesk
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Zendesk.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Zendesk
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Zendesk pourront ouverture de session unique dans l’application au niveau du site de votre société Zendesk (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Zendesk
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scénario")

##<a name="enabling-the-application-integration-for-zendesk"></a>L’activation de l’intégration de l’application pour Zendesk
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Zendesk.

###<a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Zendesk, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-zendesk-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zendesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Zendesk**.

    ![Galerie des applications] (./media/active-directory-saas-zendesk-tutorial/IC773084.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Zendesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zendesk] (./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Zendesk avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Zendesk vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure AD, sur la page intégration d’application **Zendesk** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![L’ouverture de session unique] (./media/active-directory-saas-zendesk-tutorial/IC773086.png "L’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Zendesk** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773087.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configurer des URL de l’application")
  
    une barre d’outils. Dans la zone de texte **Zendesk URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant :`https://<tenant-name>.zendesk.com`

    b. Cliquez sur **suivant**.



4.  Dans la page **configuration de l’authentification unique à Zendesk** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat local sur votre compiter.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC777534.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Zendesk en tant qu’administrateur.

6.  Cliquez sur **Admin**.

7.  Dans le volet de navigation de gauche, cliquez sur **paramètres**, puis cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-zendesk-tutorial/IC773089.png "Sécurité")

8.  Dans la page **sécurité** , cliquez sur l’onglet **administration et les Agents** .

9.  Sélectionnez **Single sign-on (SSO) et SAML**, puis sélectionnez **SAML**.

10. Dans le portail Azure AD, sur la page **configuration de l’authentification unique à Zendesk** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis collez-les dans la zone de texte **URL de SSO SAML** .

11. Dans le portail Azure AD, sur la page **configuration de l’authentification unique à Zendesk** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion à distance** .

    ![L’ouverture de session unique] (./media/active-directory-saas-zendesk-tutorial/IC773090.png "L’ouverture de session unique")

12. Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone de texte **D’empreinte digitale du certificat** .

    >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

13. Cliquez sur **Enregistrer**.

14. Sur le portail Azure AD, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zendesk-tutorial/IC773093.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à **Zendesk**, il doivent être configurés dans **Zendesk**.  
Dans le cas de **Zendesk**, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à Zendesk, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Zendesk** .

2.  Sélectionnez l’onglet de **Liste de clients** .

3.  Sélectionnez l’onglet **utilisateurs** , puis cliquez sur **Ajouter**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-zendesk-tutorial/IC773632.png "Ajouter un utilisateur")

4.  Tapez l’adresse de messagerie d’un compte Azure AD existant que vous souhaitez mettre en service et puis cliquez sur **Enregistrer**.

    ![Nouvel utilisateur] (./media/active-directory-saas-zendesk-tutorial/IC773633.png "Nouvel utilisateur")

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Zendesk utilisateur compte outils de création ou API fournies par Zendesk à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zendesk, effectuez les opérations suivantes :

1.  Dans le portail Azure AD, créez un compte de test.

2.  Sur la page intégration d’application **Zendesk **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-zendesk-tutorial/IC773094.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-zendesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
