<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Cisco Webex | Microsoft Azure" 
    description="Découvrez comment utiliser Cisco Webex avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Didacticiel : Intégration de Azure Active Directory avec Cisco Webex

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Cisco Webex.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client Cisco Webex

À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Cisco Webex pourront à l’ouverture de session unique dans l’application à votre site d’entreprise Cisco Webex (service fournisseur initiée signe) ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Cisco Webex
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scénario")
##<a name="enabling-the-application-integration-for-cisco-webex"></a>L’activation de l’intégration de l’application pour Cisco Webex

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Cisco Webex.

###<a name="to-enable-the-application-integration-for-cisco-webex-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Cisco Webex, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Cisco Webex**.

    ![Galerie des applications] (./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Cisco Webex**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Cisco Webex] (./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Cisco Webex avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un certificat codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Cisco Webex** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Cisco Webex** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **à l’aide d’URL** , tapez l’URL de votre client Cisco Webex (par exemple : *http://contoso.webex.com*).
    2.  Dans la zone de texte **URL de réponse Cisco Webex** , tapez votre **Cisco Webex AssertionConsumerService URL** (par exemple : *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  Dans la page **configuration de l’authentification unique à Cisco Webex** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site d’entreprise Cisco Webex en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Administration du Site**.

    ![Administration du site] (./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Administration du site")

7.  Dans la section **Gestion du Site** , cliquez sur **Configuration de l’authentification unique**.

    ![Configuration de l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "Configuration de l’authentification unique")

8.  Dans la section de Configuration de SSO de Web fédéré, effectuez les opérations suivantes :

    ![Fédéré de Configuration de l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Fédéré de Configuration de l’authentification unique")

    1.  Dans la liste **Protocole de la fédération** , sélectionnez **SAML 2.0**.
    2.  Créer un fichier **codé en Base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et puis copiez le contenu de celui-ci.
    4.  Cliquez sur **Importer les métadonnées SAML**, puis collez le certificat codé en base 64.
    5.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Cisco Webex** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur de SAML (IdP ID)** .
    6.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Cisco Webex** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion de Service de l’authentification client** .
    7.  Dans la liste **Format de NameID** , sélectionnez **adresse E-mail**.
    8.  Dans la zone de texte **AuthnContextClassRef** , tapez **Urn : oasis : noms : tc : SAML:2.0:ac:classes:Password**.
    9.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Cisco Webex** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion du Service client SSO** .
    10. Cliquez sur **mise à jour**.

9.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Cisco Webex** , sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **Terminer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Cisco Webex, ils doivent être mis en service dans Cisco Webex.  
Dans le cas de Cisco Webex, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre client **Cisco Webex** .

2.  Accédez à **Gérer les utilisateurs \> Ajouter utilisateur**.

    ![Ajouter des utilisateurs] (./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Ajouter des utilisateurs")

3.  Dans la section Ajouter un utilisateur, effectuez les opérations suivantes :

    ![Ajouter un utilisateur] (./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Ajouter un utilisateur")

    1.  Comme **Type de compte**, sélectionnez **hôte**.
    2.  Tapez les informations d’un utilisateur AD Azure existant dans les zones de texte suivantes : **prénom, deuxième prénom**, **nom d’utilisateur**, **E-mail**, **mot de passe**, **Confirmer le mot de passe**.
    3.  Cliquez sur **Ajouter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Cisco Webex utilisateur compte outils de création ou d’API fournie par Cisco Webex aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-cisco-webex-perform-the-following-steps"></a>Pour affecter des utilisateurs à Cisco Webex, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Cisco Webex **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
