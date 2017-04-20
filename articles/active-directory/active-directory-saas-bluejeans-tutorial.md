<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec BlueJeans | Microsoft Azure" 
    description="Apprenez à utiliser BlueJeans avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-ad-integration-with-bluejeans"></a>Didacticiel : L’intégration de publicités Azure avec BlueJeans

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et BlueJeans.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un BlueJeans de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à BlueJeans pourront ouverture de session unique dans l’application au niveau du site de votre société BlueJeans (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour BlueJeans
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bluejeans-tutorial/IC785860.png "Scénario")
##<a name="enabling-the-application-integration-for-bluejeans"></a>L’activation de l’intégration de l’application pour BlueJeans

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour BlueJeans.

###<a name="to-enable-the-application-integration-for-bluejeans-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour BlueJeans, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bluejeans-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-bluejeans-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bluejeans-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **BlueJeans**.

    ![Galerie des applications] (./media/active-directory-saas-bluejeans-tutorial/IC785861.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **BlueJeans**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![BlueJeans] (./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de BlueJeans avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **BlueJeans** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785863.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à BlueJeans** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **BlueJeans URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.BlueJeans.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bluejeans-tutorial/IC785865.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à BlueJeans** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise **BlueJeans** en tant qu’administrateur.

6.  Accédez à **ADMIN \> paramètres de groupe \> sécurité**.

    ![Admin] (./media/active-directory-saas-bluejeans-tutorial/IC785868.png "Admin")

7.  Dans la section **sécurité** , effectuez les opérations suivantes :

    ![Authentification unique SAML] (./media/active-directory-saas-bluejeans-tutorial/IC785869.png "Authentification unique SAML")

    1.  Sélectionnez **authentification unique SAML**.
    2.  Sélectionnez **Activer la mise à disponibilité automatique**.

8.  Passer aux étapes suivantes :

    ![Chemin d’accès du certificat] (./media/active-directory-saas-bluejeans-tutorial/IC785870.png "Chemin d’accès du certificat")

    1.  Cliquez sur **Fichier**et télécharger le certificat téléchargé.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à BlueJeans** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à BlueJeans** , copiez la valeur de **l’URL de modification de mot de passe** et puis la coller dans la zone de texte **URL de modification de mot de passe** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à BlueJeans** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .

9.  Passer aux étapes suivantes :

    ![Enregistrer les modifications] (./media/active-directory-saas-bluejeans-tutorial/IC785874.png "Enregistrer les modifications")

    1.  Dans la zone de texte **nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    2.  Dans la zone de texte **message** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.
    3.  Cliquez sur **Enregistrer les modifications**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à BlueJeans, il doivent être configurés dans BlueJeans.  
Dans le cas de BlueJeans, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **BlueJeans** en tant qu’administrateur.

2.  Accédez à **ADMIN \> gestion des utilisateurs \> Ajouter utilisateur**.

    ![Admin] (./media/active-directory-saas-bluejeans-tutorial/IC785877.png "Admin")

    >[AZURE.IMPORTANT] L’onglet **Ajouter un utilisateur** , n’est disponible que si, dans l' **onglet sécurité**, **activez la mise en service automatique** est désactivée.

3.  Dans la section **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-bluejeans-tutorial/IC785886.png "Ajouter utilisateur")

    1.  Tapez un **Nom d’utilisateur de BlueJeans**, une **adresse de messagerie**, un **ID de réunion de BlueJeans**, un **Modérateur de mot de passe**, un **Nom complet**, la **société** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres BlueJeans utilisateur compte outils de création ou API fournies par BlueJeans à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-bluejeans-perform-the-following-steps"></a>Pour affecter des utilisateurs à BlueJeans, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **BlueJeans **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-bluejeans-tutorial/IC785887.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-bluejeans-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
