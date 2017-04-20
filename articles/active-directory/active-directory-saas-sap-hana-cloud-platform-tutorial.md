<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SAP HANA plateforme en nuage | Microsoft Azure" 
    description="Apprenez à utiliser la plateforme en nuage HANA SAP avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-sap-hana-cloud-platform"></a>Didacticiel : Intégration d’Azure Active Directory avec une plate-forme de nuage SAP HANA
  
L’objectif de ce didacticiel est d’afficher l’intégration de Azure et la plateforme en nuage HANA SAP.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un compte de la plateforme en nuage SAP HANA
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à une plate-forme de nuage SAP HANA pourront à l’ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

>[AZURE.IMPORTANT]Vous avez besoin déployer votre application ou vous abonner à une application sur votre compte de plateforme en nuage HANA SAP pour tester l’authentification unique. Dans ce didacticiel, une application est déployée dans le compte.
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour la plateforme en nuage SAP HANA
2.  Configuration de l’authentification unique
3.  Affectation d’un rôle à un utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scénario")
##<a name="enabling-the-application-integration-for-sap-hana-cloud-platform"></a>L’activation de l’intégration des applications pour la plateforme en nuage SAP HANA
  
L’objectif de cette section doit décrire comment activer l’intégration des applications pour la plateforme en nuage HANA SAP.

###<a name="to-enable-the-application-integration-for-sap-hana-cloud-platform-perform-the-following-steps"></a>Pour activer l’intégration des applications pour la plateforme en nuage HANA SAP, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **SAP HANA plateforme en nuage**.

    ![Galerie des applications] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez la **Plateforme en nuage HANA SAP**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![SAP Hana] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de plate-forme de SAP HANA nuage avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à vos clients de SAP HANA plateforme en nuage.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **SAP HANA plateforme en nuage** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la plateforme en nuage HANA SAP** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configurer l’authentification unique")

3.  Dans une fenêtre de navigateur web différent, ouvrez une session sur le Cockpit de plate-forme SAP HANA nuage à https://account. \<hôte de paysage\>.ondemand.com/cockpit (par exemple : *https://account.hanatrial.ondemand.com/cockpit*).

4.  Cliquez sur l’onglet **approbation** .

    ![Faire confiance] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Faire confiance")

5.  Dans la section Gestion de confiance, effectuez les opérations suivantes :

    ![Obtenir des métadonnées] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Obtenir des métadonnées")

    1.  Cliquez sur l’onglet **Fournisseur de services Local** .
    2.  Pour télécharger le fichier de métadonnées de plateforme en nuage HANA SAP, cliquez sur **Obtenir les métadonnées**.

6.  Dans le portail classique Azure Active, dans la page **Configurer l’URL App** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre application de **Plateforme en nuage HANA SAP** . Il s’agit de l’URL spécifique au compte d’une ressource protégée dans votre application de plateforme en nuage HANA SAP. L’URL est basé sur le modèle suivant : *https://\<applicationName\>\<nom de compte\>.\< hôte de paysage\>.ondemand.com/\<chemin d’accès\_à\_protégé\_ressource\> * (par exemple : *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]Il s’agit de l’URL dans votre application de plateforme en nuage HANA SAP qui oblige l’utilisateur à authentifier.

    2.  Ouvrir le fichier de métadonnées de plateforme en nuage SAP HANA téléchargé et recherchez la balise **ns3:AssertionConsumerService** .
    3.  Copiez la valeur de l’attribut **d’emplacement** et puis la coller dans la zone de texte **URL de réponse plate-forme SAP HANA Cloud** .

7.  Dans la page **configuration de l’authentification unique au niveau de la plateforme en nuage HANA SAP** , pour télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configurer l’authentification unique")

8.  Dans le Cockpit de plate-forme SAP HANA Cloud, dans la section **Fournisseur de services Local** , procédez comme suit :

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Gestion d’approbation")

    1.  Cliquez sur **Modifier**.
    2.  En tant que **Type de Configuration**, sélectionnez **personnalisé**.
    3.  En tant que **Nom du fournisseur Local**, laissez la valeur par défaut.
    4.  Pour générer une **Clé de signature** et une paire de clés de **Signature de certificat** , cliquez sur **Générer la paire de clés**.
    5.  En tant que **Principal de Propagation**, sélectionnez **désactivé**.
    6.  **Force l’authentification**, sélectionnez **désactivé**.
    7.  Cliquez sur **Enregistrer**.

9.  Cliquez sur l’onglet **Fournisseur d’identité approuvé** , puis cliquez sur **Ajouter fournisseur d’identité approuvé**.

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Gestion d’approbation")

    >[AZURE.NOTE]Pour gérer la liste des fournisseurs d’identité approuvée, vous devez choisi le type de configuration personnalisée dans la section fournisseur de services Local. Pour le type de configuration par défaut, vous avez une approbation implicite et non modifiable de l’ID de Service SAP. Pour aucun, vous n’avez pas les paramètres d’approbation.

10. Cliquez sur l’onglet **Général** , puis cliquez sur **Parcourir** pour charger le fichier de métadonnées téléchargés.

    ![Gestion d’approbation] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Gestion d’approbation")

    >[AZURE.NOTE] Après avoir téléchargé le fichier de métadonnées, les valeurs de **l’URL d’ouverture de session unique**, **Les URL de déconnexion unique** et **Certificat de signature** sont remplies automatiquement.

11. Cliquez sur l’onglet **attributs** .

12. Sous l’onglet **attributs** , effectuez les opérations suivantes :

    ![Attributs] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributs")

    1.  En cliquant sur **Les attributs Add Assertion-Based**, ajoutez les attributs basés sur l’assertion suivantes :

        |Attribut d’assertion| Attribut de l’entité de sécurité|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/givenName|   Prénom|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Surname|        nom|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/EmailAddress|Messagerie|

    >[AZURE.NOTE]La configuration des attributs dépend de comment les applications sur HCP sont développées, c'est-à-dire les attributs qu’ils s’attendre dans la réponse SAML et ils accèdent à cet attribut dans le code sous le nom (attribut de l’entité de sécurité).
    >  
    >une barre d’outils.  L' **Attribut par défaut** dans la capture d’écran est uniquement à des fins d’illustration. Il n’est pas nécessaire de faire fonctionner le scénario.  
    >
    >b.  Les noms et valeurs d' **Attribut de l’entité de sécurité** est illustré dans la capture d’écran dépendent de la façon dont l’application est développée. Il est possible que votre application nécessite différents mappages.

13. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la plateforme en nuage HANA SAP** , sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configurer l’authentification unique")
  
Comme une étape facultative, vous pouvez configurer des groupes basés sur une assertion pour votre fournisseur d’identité Active Directory Azure

>[AZURE.NOTE]L’utilisation des groupes sur une plate-forme de nuage HANA SAP vous permet assigner dynamiquement un ou plusieurs utilisateurs à un ou plusieurs rôles dans vos applications SAP HANA plateforme en nuage, déterminées par les valeurs des attributs dans l’assertion SAML 2.0. Par exemple, si l’assertion contient l’attribut "*contrat = temporaire*», vous souhaiterez peut-être tous les utilisateurs à ajouter au groupe «*temporaires*». Le groupe «*temporaire*» peut contenir un ou plusieurs rôles à partir d’une ou plusieurs applications déployées dans votre compte SAP HANA plateforme en nuage.
>  
>Utilisez des groupes basés sur l’assertion si vous souhaitez affecter plusieurs utilisateurs à un ou plusieurs rôles d’applications dans votre compte de plate-forme de SAP HANA nuage masse. Si vous ne souhaitez pas affecter un seul ou un petit le nombre d’utilisateurs à (a) ou les rôles spécifiques, nous vous recommandons de les affecter directement dans l’onglet «**autorisations**» du cockpit SAP HANA plateforme en nuage.

##<a name="assigning-a-role-to-a-user"></a>Affectation d’un rôle à un utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à la plateforme de Cloud HANA SAP, vous devez affecter des rôles dans la plateforme en nuage SAP HANA leur.

###<a name="to-assign-a-role-to-a-user-perform-the-following-steps"></a>Pour attribuer un rôle à un utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre **Plate-forme de nuage HANA SAP** de cockpit.

2.  Effectuez les opérations suivantes :

    ![Autorisations] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Autorisations")

    1.  Cliquez sur **autorisation**.
    2.  Cliquez sur l’onglet **utilisateurs** .
    3.  Dans la zone **utilisateur** , tapez l’adresse de messagerie de l’utilisateur.
    4.  Cliquez sur **affecter** pour affecter l’utilisateur à un rôle.
    5.  Cliquez sur **Enregistrer**.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-sap-hana-cloud-platform-perform-the-following-steps"></a>Pour affecter des utilisateurs à une plate-forme de nuage HANA SAP, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **SAP HANA plateforme en nuage** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).