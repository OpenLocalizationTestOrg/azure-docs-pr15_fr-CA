<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Thirdlight | Microsoft Azure" 
    description="Apprenez à utiliser Thirdlight avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Didacticiel : Intégration de Azure Active Directory avec Thirdlight
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Thirdlight.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Thirdlight de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Thirdlight pourront ouverture de session unique dans l’application à votre site de la société Thirdlight (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Thirdlight
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-thirdlight-tutorial/IC805836.png "Scénario")

##<a name="enabling-the-application-integration-for-thirdlight"></a>L’activation de l’intégration de l’application pour Thirdlight
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Thirdlight.

###<a name="to-enable-the-application-integration-for-thirdlight-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Thirdlight, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thirdlight-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-thirdlight-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-thirdlight-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-thirdlight-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Thirdlight**.

    ![Galerie des applications] (./media/active-directory-saas-thirdlight-tutorial/IC805837.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Thirdlight**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ThirdLight] (./media/active-directory-saas-thirdlight-tutorial/IC805838.png "ThirdLight")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Thirdlight avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Thirdlight vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Thirdlight** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805839.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Thirdlight** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805840.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Thirdlight URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application de Thirdlight (par exemple : «*http://azuresso2.thirdlight.com/*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-thirdlight-tutorial/IC805841.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Thirdlight** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805842.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Thirdlight en tant qu’administrateur.

6.  Accédez à **Configuration \> Administration système**, puis cliquez sur **SAML2**.

    ![Administration système] (./media/active-directory-saas-thirdlight-tutorial/IC805843.png "Administration système")

7.  Dans la section de configuration SAML2, effectuez les opérations suivantes :

    ![SAML Single Sign-On] (./media/active-directory-saas-thirdlight-tutorial/IC805844.png "SAML Single Sign-On")

    1.  Sélectionnez **Activer la SAML2 Single Sign-On**.
    2.  En tant que **Source des métadonnées de IdP**, sélectionnez **Métadonnées IdP de charge à partir de XML**.
    3.  Ouvrir le fichier de métadonnées téléchargé, copier le contenu et puis la coller dans la zone de texte **XML de métadonnées IdP** .
    4.  Cliquez sur **SAML2 d’enregistrer les paramètres**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-thirdlight-tutorial/IC805845.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Thirdlight, il doivent être configurés dans Thirdlight.  
Dans le cas de Thirdlight, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Thirdlight** en tant qu’administrateur.

2.  Accédez à l’onglet **utilisateurs** .

3.  Sélectionnez **les utilisateurs et les groupes**.

4.  Cliquez sur le bouton **Ajouter un nouvel utilisateur** .

5.  Entrez **le nom d’utilisateur, nom ou Description, E-mail, choisissez un paramètre prédéfini ou de nouveaux membres d’un groupe** d’un compte DAS valide que vous souhaitez à la disposition.

6.  Cliquez sur **créer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Thirdlight utilisateur compte outils de création ou API fournies par Thirdlight à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-thirdlight-perform-the-following-steps"></a>Pour affecter des utilisateurs à Thirdlight, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Thirdlight **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-thirdlight-tutorial/IC805846.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-thirdlight-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).