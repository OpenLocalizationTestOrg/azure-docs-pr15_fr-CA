<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel Igloo | Microsoft Azure" 
    description="Apprenez à utiliser des logiciels de Igloo avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="10/20/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Didacticiel : Intégration d’Azure Active Directory avec le logiciel Igloo
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et logiciel de Igloo.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Une [Logiciel de Igloo](http://www.igloosoftware.com/) de session unique abonnement activé
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté au logiciel de Igloo pourront à l’ouverture de session unique dans l’application au niveau du site de votre société Igloo logiciel (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour le logiciel de Igloo
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-igloo-software-tutorial/IC783961.png "Scénario")
##<a name="enabling-the-application-integration-for-igloo-software"></a>L’activation de l’intégration de l’application pour le logiciel de Igloo
  
L’objectif de cette section est à expliquent comment activer l’intégration de l’application pour les logiciels de Igloo.

###<a name="to-enable-the-application-integration-for-igloo-software-perform-the-following-steps"></a>Pour activer l’intégration des applications pour Igloo Software, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-igloo-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-igloo-software-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-igloo-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Igloo logiciel**.

    ![Galerie des applications] (./media/active-directory-saas-igloo-software-tutorial/IC783962.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez le **Logiciel de Igloo**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Igloo] (./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier au logiciel de Igloo avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à vos clients de bureau Central.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Logiciel de Igloo** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter au logiciel de Igloo** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Igloo logiciel URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.igloocommunities.com/?signin*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-igloo-software-tutorial/IC773625.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique chez Igloo Software** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre société de logiciels d’Igloo en tant qu’administrateur.

6.  Ouvrez le **Panneau de configuration**.

    ![Le panneau de configuration] (./media/active-directory-saas-igloo-software-tutorial/IC799949.png "Le panneau de configuration")

7.  Dans l’onglet **adhésion** , cliquez sur **Paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-igloo-software-tutorial/IC783968.png "Paramètres de connexion")

8.  Dans la section Configuration de SAML, cliquez sur **Configuration de l’authentification SAML**.

    ![Configuration de SAML] (./media/active-directory-saas-igloo-software-tutorial/IC783969.png "Configuration de SAML")

9.  Dans la section de **Configuration générales** , effectuez les opérations suivantes :

    ![Configuration générale] (./media/active-directory-saas-igloo-software-tutorial/IC783970.png "Configuration générale")

    1.  Dans la zone de texte **Nom de la connexion** , tapez un nom personnalisé pour votre configuration.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique chez Igloo Software** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion IdP** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique chez Igloo Software** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion IdP** .
    4.  En tant **réponse de fermeture de session et le Type de demande HTTP**, sélectionnez **Valider**.
    5.  Créez un fichier texte à partir du certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Supprimer la première ligne et la dernière ligne de la version de fichier de texte de votre certificat, copiez le texte restant du certificat et puis la coller dans la zone de texte du **Certificat Public** .

10. Dans **réponse et la Configuration de l’authentification**, procédez comme suit :

    ![Configuration d’authentification et réponse] (./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Configuration d’authentification et réponse")

    1.  En tant que **Fournisseur d’identité**, sélectionnez **Microsoft ADFS**.
    2.  **Identificateur de Type**, sélectionnez **Adresse E-mail**.
    3.  Dans la zone de texte **d’Attribut Email** , tapez **emailaddress**.
    4.  Dans la zone de texte **Nom du premier attribut** , tapez **givenname**.
    5.  Dans la zone de texte **Dernier attribut de nom** , tapez le **nom de famille**.

11. Effectuez les opérations suivantes pour terminer la configuration :

    ![Création de l’utilisateur sur la connexion] (./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Création de l’utilisateur sur la connexion")

    1.  Que la **Création de l’utilisateur sur la connexion**, sélectionnez **créer un nouvel utilisateur sur votre site lorsqu’ils se connectent**.
    2.  Sous **paramètres de connexion**, sélectionnez **bouton utilisation SAML sur l’écran de « Connexion »**.
    3.  Cliquez sur **Enregistrer**.

12. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour pouvoir configurer l’approvisionnement de l’utilisateur pour le logiciel de Igloo.  
Lorsqu’un utilisateur affecté essaie de se connecter au logiciel de Igloo l’aide du panneau d’accès, Igloo logiciel vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par le logiciel Igloo.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-igloo-software-perform-the-following-steps"></a>Pour affecter des utilisateurs à Igloo Software, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Logiciel de Igloo **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-igloo-software-tutorial/IC783974.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-igloo-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).