<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec envoi | Microsoft Azure" 
    description="Apprenez à utiliser l’envoi avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-envoy"></a>Didacticiel : Intégration d’Azure Active Directory avec envoi
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et d’envoi.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client d’envoi
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à l’envoi pourront à l’ouverture de session unique dans l’application au site de votre société d’envoi (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application d’envoi
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-envoy-tutorial/IC776759.png "Scénario")
##<a name="enabling-the-application-integration-for-envoy"></a>L’activation de l’intégration de l’application d’envoi
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application d’envoi.

###<a name="to-enable-the-application-integration-for-envoy-perform-the-following-steps"></a>Pour activer l’intégration de l’application d’envoi, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-envoy-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-envoy-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-envoy-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-envoy-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **envoi**.

    ![Galerie des applications] (./media/active-directory-saas-envoy-tutorial/IC776760.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **envoi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Envoi] (./media/active-directory-saas-envoy-tutorial/IC776777.png "Envoi")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès d’envoi avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour l’envoi vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page **d’envoi** application integration, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-envoy-tutorial/IC776778.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à l’envoi** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776779.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Des URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Envoy.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-envoy-tutorial/IC776780.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à l’envoi** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\Envoy.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776781.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise d’envoi en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**.

    ![Envoi] (./media/active-directory-saas-envoy-tutorial/IC776782.png "Envoi")

7.  Cliquez sur **société**.

    ![Société] (./media/active-directory-saas-envoy-tutorial/IC776783.png "Société")

8.  Cliquez sur **SAML**.

    ![SAML] (./media/active-directory-saas-envoy-tutorial/IC776784.png "SAML")

9.  Dans la section de configuration de **L’authentification SAML** , effectuez les opérations suivantes :

    ![Authentification SAML] (./media/active-directory-saas-envoy-tutorial/IC776785.png "Authentification SAML")

    >[AZURE.NOTE] La valeur de l’ID d’emplacement HQ est automatiquement généré par l’application.

    1.  Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone de texte **d’empreinte digitale** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à l’envoi** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte **URL SAML HTTP du fournisseur d’identité** .
    3.  Cliquez sur **Enregistrer les modifications**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-envoy-tutorial/IC776786.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour vous permet de configurer l’envoi de la configuration utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter à l’envoi à l’aide du panneau d’accès, envoi vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par envoi.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-envoy-perform-the-following-steps"></a>Pour affecter des utilisateurs à l’envoi, procédez comme suit :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page **d’envoi **application integration, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-envoy-tutorial/IC776787.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-envoy-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).