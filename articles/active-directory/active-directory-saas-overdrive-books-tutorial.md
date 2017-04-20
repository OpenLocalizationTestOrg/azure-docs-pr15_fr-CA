<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec carnets de Overdrive | Microsoft Azure" 
    description="Apprenez à utiliser des livres de Overdrive avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>Didacticiel : Intégration d’Azure Active Directory Overdrive livres
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et OverDrive.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un OverDrive de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à OverDrive pourront à l’ouverture de session unique dans l’application au niveau du site de votre société OverDrive (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour OverDrive
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scénario")
##<a name="enabling-the-application-integration-for-overdrive"></a>L’activation de l’intégration de l’application pour OverDrive
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour OverDrive.

###<a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour OverDrive, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **OverDrive**.

    ![Galerie des applications] (./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **OverDrive**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![OverDrive] (./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de OverDrive avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **OverDrive** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous aux utilisateurs de se connecter à OverDrive** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **OverDrive URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://mslibrarytest.libraryreserve.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configurer des URL de l’application")

4.  Dans la **configuration de l’authentification unique à OverDrive** page et télécharger le fichier de métadonnées, puis l’envoyer à l’équipe de support OverDrive.

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "De configurer l’authentification unique")

    >[AZURE.NOTE]L’équipe de support OverDrive configure de l’authentification unique pour vous et vous envoie une notification lorsque la configuration est terminée.

5.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour vous permet de configurer la mise en service de l’utilisateur à OverDrive.  
Lorsqu’un utilisateur affecté essaie de se connecter à la vitesse supérieure, un OverDrive compte est automatiquement créé si nécessaire.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres OverDrive utilisateur compte outils de création ou API fournies par OverDrive aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>Pour affecter des utilisateurs à OverDrive, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **OverDrive **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).