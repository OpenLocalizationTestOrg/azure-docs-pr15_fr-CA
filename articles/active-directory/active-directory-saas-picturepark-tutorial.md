<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Picturepark | Microsoft Azure" 
    description="Apprenez à utiliser Picturepark avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Didacticiel : Intégration de Azure Active Directory avec Picturepark
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Picturepark.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Picturepark
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Picturepark pourront ouverture de session unique dans l’application à votre site de la société Picturepark (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Picturepark
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scénario")

##<a name="enabling-the-application-integration-for-picturepark"></a>L’activation de l’intégration de l’application pour Picturepark
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Picturepark.

###<a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Picturepark, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-picturepark-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-picturepark-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Picturepark**.

    ![Galerie des applications] (./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Picturepark**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Picturepark] (./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Picturepark avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Picturepark vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)...

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Picturepark** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Picturepark** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **Picturepark signe d’URL** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.picturepark.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Picturepark** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Picturepark en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Outils d’administration**, puis cliquez sur **Console de gestion**.

    ![Console de gestion] (./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console de gestion")

7.  Cliquez sur **authentification**, puis cliquez sur **fournisseurs d’identité**.

    ![Authentification] (./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentification")

8.  Dans la section de **configuration de fournisseur d’identité** , effectuez les opérations suivantes :

    ![Configuration de fournisseur d’identité] (./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuration de fournisseur d’identité")

    1.  Cliquez sur **Ajouter**.
    2.  Tapez un nom pour votre configuration.
    3.  Sélectionnez **définir par défaut**.
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Picturepark** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte **URI de l’émetteur** .
    5.  Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone **d’Impression de Thumb émetteur de confiance** .  

        >[AZURE.TIP]Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Cliquez sur **JoinDefaultUsersGroup**.
    7.  Pour définir l’attribut **d’adresse électronique** dans la zone de texte de **demande** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
        ![Configuration de] (./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration de")
    8.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Picturepark, il doivent être configurés dans Picturepark.  
Dans le cas de Picturepark, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Picturepark** .

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Outils d’administration**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795067.png "Utilisateurs")

3.  Sous l’onglet **vue d’ensemble des utilisateurs** , cliquez sur **Nouveau**.

    ![Gestion des utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gestion des utilisateurs")

4.  Dans la boîte de dialogue **Créer un utilisateur** , effectuez les opérations suivantes :

    ![Création d’utilisateur] (./media/active-directory-saas-picturepark-tutorial/IC795069.png "Création d’utilisateur")

    1.  Type de la : **adresse électronique**, **mot de passe**, **Confirmer le mot de passe**, **prénom**, **nom**, **société**, **pays**, **code postal**, **Ville** d’Azure Active Directory utilisateur valide vous souhaitez disposition ot dans les zones de texte liées.
    2.  Sélectionnez une **langue**.
    3.  Cliquez sur **créer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Picturepark utilisateur compte outils de création ou API fournies par Picturepark à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>Pour affecter des utilisateurs à Picturepark, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Picturepark **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-picturepark-tutorial/IC795070.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-picturepark-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).