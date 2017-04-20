<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Zscaler deux | Microsoft Azure" 
    description="Découvrez comment utiliser deux Zscaler avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Didacticiel : Intégration de Azure Active Directory avec Zscaler deux

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et deux ZScaler.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un deux ZScaler de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à deux ZScaler pourront ouverture de session unique dans l’application à votre site d’entreprise ZScaler deux (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour les deux ZScaler
2.  Configuration de l’authentification unique
3.  Configuration des paramètres de proxy
4.  Configuration d’approvisionnement de l’utilisateur
5.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configurer l’authentification unique")

##<a name="enabling-the-application-integration-for-zscaler-two"></a>L’activation de l’intégration des applications pour les deux ZScaler
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour ZScaler deux.

###<a name="to-enable-the-application-integration-for-zscaler-two-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour deux ZScaler, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Deux ZScaler**.

    ![Galerie des applications] (./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Deux ZScaler**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![ZScaler deux] (./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler deux")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de deux ZScaler avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à vos clients ZScaler deux.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration d’application **ZScaler deux** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à deux ZScaler** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **ZScaler deux signe d’adresse URL** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de deux ZScaler et puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configurer des URL de l’application")

    >[AZURE.NOTE] Vous pouvez obtenir la valeur réelle de votre environnement de votre équipe de support ZScaler deux si vous en avez besoin.

4.  Dans la page **configuration de l’authentification unique à deux ZScaler** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise ZScaler deux en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")

7.  **Gérer les administrateurs et les rôles**, cliquez sur **Gérer les utilisateurs et l’authentification**.

    ![Gérer les utilisateurs et l’authentification] (./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Gérer les utilisateurs et l’authentification")

8.  Dans la section **Choisir les Options d’authentification pour votre organisation** , effectuez les opérations suivantes :

    ![Authentification] (./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentification")

    1.  Sélectionnez **à l’aide de l’authentification SAML Single Sign-On**.
    2.  Cliquez sur **configurer les paramètres d’authentification unique de le SAML**.

9.  Sur la page de la boîte de dialogue **Configurer SAML seule session paramètres** , effectuez les opérations suivantes, puis cliquez sur **terminé**:

    ![L’ouverture de session unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "L’ouverture de session unique")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à deux ZScaler** , copiez la valeur de **l’URL de la demande d’authentification** et puis la coller dans la zone de texte **URL du portail SAML auquel les utilisateurs sont envoyées pour l’authentification** .
    2.  Dans la zone de texte **nom de connexion qui contient l’attribut** , tapez **NameID**.
    3.  Pour télécharger votre certificat téléchargé, cliquez sur **Zscaler pem**.
    4.  Sélectionnez **Activer le provisionnement automatique SAML**.

10. Sur la page de la boîte de dialogue **Configuration de l’authentification utilisateur** , effectuez les opérations suivantes :

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")

    1.  Cliquez sur **Enregistrer**.
    2.  Cliquez sur **Activer maintenant**.

11. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à deux ZScaler** , sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **Terminer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configurer l’authentification unique")

##<a name="configuring-proxy-settings"></a>Configuration des paramètres de proxy

###<a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Pour configurer les paramètres de proxy dans Internet Explorer

1.  Démarrez **Internet Explorer**.

2.  Sélectionnez **options Internet** dans le menu **Outils** pour ouvrir la boîte de dialogue **Options Internet** .

    ![Options Internet] (./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Options Internet")

3.  Cliquez sur l’onglet **connexions** .

    ![Connexions] (./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connexions")

4.  Cliquez sur **paramètres** pour ouvrir la boîte de dialogue **Paramètres du réseau local** .

5.  Dans la section serveur Proxy, procédez comme suit :

    ![Serveur proxy] (./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Serveur proxy")

    1.  Sélectionnez utiliser un serveur proxy pour votre réseau local.
    2.  Dans la zone Adresse, tapez **gateway.zscalerone.net**.
    3.  Dans la zone Port, tapez **80**.
    4.  Sélectionnez **Ignorer le serveur proxy pour les adresses locales**.
    5.  Cliquez sur **OK** pour fermer la boîte de dialogue **paramètres du réseau Local (LAN)** .

6.  Cliquez sur **OK** pour fermer la boîte de dialogue **Options Internet** .

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à deux ZScaler, elles doivent être configurés à deux ZScaler.  
Dans le cas de deux ZScaler, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Zscaler** .

2.  Cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")

3.  Cliquez sur **Gestion des utilisateurs**.

    ![Ajouter] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Ajouter")

4.  Dans l’onglet **utilisateurs** , cliquez sur **Ajouter**.

    ![Ajouter] (./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Ajouter")

5.  Dans la section Ajouter un utilisateur, effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Ajouter utilisateur")

    1.  Tapez le **nom d’utilisateur** **Nom utilisateur**, **mot de passe**, **Confirmer le mot de passe**et sélectionnez le **service** d’un compte DAS valide que vous souhaitez mettre en service et de **groupes** .
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ZScaler deux utilisateur compte outils de création ou API fournies par deux ZScaler aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-zscaler-two-perform-the-following-steps"></a>Pour affecter des utilisateurs à deux ZScaler, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration d’application **ZScaler deux** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).