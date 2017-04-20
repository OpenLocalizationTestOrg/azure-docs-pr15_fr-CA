<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Sprinklr | Microsoft Azure" 
    description="Apprenez à utiliser Sprinklr avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Didacticiel : Intégration d’Azure Active Directory avec Sprinklr
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Sprinklr.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Sprinklr
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Sprinklr pourront ouverture de session unique dans l’application à votre site de la société Sprinklr (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Sprinklr
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Scénario")

##<a name="enabling-the-application-integration-for-sprinklr"></a>L’activation de l’intégration de l’application pour Sprinklr
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Sprinklr.

###<a name="to-enable-the-application-integration-for-sprinklr-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Sprinklr, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Sprinklr**.

    ![Galerie des applications] (./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Sprinklr**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sprinklr] (./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Sprinklr avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Sprinklr** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782903.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Sprinklr** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782904.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Sprinklr URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. sprinklr.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Sprinklr** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782906.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Sprinklr en tant qu’administrateur.

6.  Accédez à **Administration \> paramètres**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

7.  Accédez à **gérer un partenaire \> ouverture de session unique** sur dans le volet de gauche.

    ![Gérer des partenaires] (./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gérer des partenaires")

8.  Cliquez sur **+ Ajouter des modules d’authentification unique**.

    ![Connexions uniques] (./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Connexions uniques")

9.  Sur la page **d’Ouverture de session unique** , effectuez les opérations suivantes :

    ![Connexions uniques] (./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Connexions uniques")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : *WAADSSOTest*).
    2.  Sélectionnez **activé**.
    3.  Sélectionnez **Utiliser un nouveau certificat SSO**.
    4.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat d’identité du fournisseur** ,
    6.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Sprinklr** , copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte **Id de l’entité** .
    7.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Sprinklr** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité** .
    8.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Sprinklr** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    9.  En tant que **Type d’ID utilisateur SAML**, sélectionnez **Assertion contient l’utilisateur "nom d’utilisateur de s sprinklr.com**.
    10. En tant qu' **Emplacement des ID utilisateur SAML**, sélectionnez **l’ID utilisateur est dans l’élément identificateur de nom de l’instruction de l’objet**.
    11. Fermez **l’enregistrement**.

        ![SAML] (./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sprinklr-tutorial/IC782912.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour les DAS pour permettre aux utilisateurs de se connecter, ils doivent être configurés pour l’accès à l’intérieur de l’application Sprinklr.  
Cette section décrit comment créer des comptes d’utilisateurs de DAS à l’intérieur de Sprinklr.

###<a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur dans Sprinklr, effectuez les opérations suivantes :

1.  Ouvrez une session en tant qu’administrateur dans le site de votre entreprise Sprinklr.

2.  Accédez à **Administration \> paramètres**.

    ![Administration] (./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administration")

3.  Accédez à **gérer le Client \> les utilisateurs** à partir du volet de gauche.

    ![Paramètres] (./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Paramètres")

4.  Cliquez sur **Ajouter un utilisateur**.

    ![Paramètres] (./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Paramètres")

5.  Dans la boîte de dialogue **Modifier l’utilisateur** , effectuez les opérations suivantes :

    ![Modifier l’utilisateur] (./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Modifier l’utilisateur")

    1.  Dans la **messagerie**, le **prénom** et le **Nom** des zones de texte, le type les informations d’un compte d’utilisateur Active Directory Azure vous souhaitez mettre en service.
    2.  Sélectionnez **mot de passe est désactivée**.
    3.  Sélectionnez une **langue**.
    4.  Sélectionnez un **Type d’utilisateur**.
    5.  Cliquez sur **mise à jour**.

    >[AZURE.IMPORTANT] **Mot de passe désactivé** doit être sélectionnée pour permettre à un utilisateur à se connecter via un fournisseur d’identité.

6.  Accédez au **rôle**et puis procédez comme suit :

    ![Rôles de partenaire] (./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Rôles de partenaire")

    1.  Dans la liste **Global** , sélectionnez **tous les\_autorisations**.
    2.  Cliquez sur **mise à jour**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Sprinklr utilisateur compte outils de création ou d’API fournies par Sprinklr pour configurer les comptes d’utilisateur AD Azure.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-sprinklr-perform-the-following-steps"></a>Pour affecter des utilisateurs à Sprinklr, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Sprinklr **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).