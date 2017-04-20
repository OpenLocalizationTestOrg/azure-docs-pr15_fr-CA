<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec InsideView | Microsoft Azure" 
    description="Apprenez à utiliser InsideView avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Didacticiel : Intégration d’Azure Active Directory avec InsideView
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et InsideView.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire InsideView
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à InsideView pourront ouverture de session unique dans l’application à votre site de la société InsideView (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour InsideView
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Scénario")
##<a name="enabling-the-application-integration-for-insideview"></a>L’activation de l’intégration de l’application pour InsideView
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour InsideView.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour InsideView, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **InsideView**.

    ![Galerie des applications] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **InsideView**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de InsideView avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **InsideView** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à InsideView** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de réponse InsideView** , tapez l’URL de votre InsideView l’authentification unique (par exemple : `https://my.insideview.com/iv/<STS Name>/login.iv`), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à InsideView** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise InsideView en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **administration**, **Paramètres de SingleSignOn**et puis cliquez sur **Ajouter un SAML**.

    ![Authentification unique SAML paramètres] (./media/active-directory-saas-insideview-tutorial/IC794135.png "Authentification unique SAML paramètres")

7.  Dans la section **Ajouter un nouveau SAML** , effectuez les opérations suivantes :

    ![Ajouter un nouveau SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Ajouter un nouveau SAML")

    1.  Dans la zone de texte **Nom de SharePoint Team Services** , tapez un nom pour votre configuration.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à InsideView** , copiez la valeur de **Point de terminaison initiée par Service Provider (SP)** et puis la coller dans la zone de texte de **Point de terminaison Unsolicated SamlP/WS-Fed** .
    3.  Créer un fichier **codé en base 64** de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrir votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **Certificat STS**
    5.  Dans la zone de texte **Crm de mappage d’Id d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  Dans la zone de texte **Mappage de courrier électronique Crm** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Dans la zone de texte **Crm mappage de nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  Dans la zone de texte **mappage de nom de Crm** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à InsideView, il doivent être configurés dans InsideView.  
Dans le cas de InsideView, la mise en service est une tâche manuelle.
  
Pour les utilisateurs ou les contacts créés dans InsideView, contactez votre responsable de réussite client ou envoyer un e-mail à**support@insideview.com**

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres InsideView utilisateur compte outils de création ou d’API fournies par InsideView pour configurer les comptes d’utilisateur AD Azure.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Pour affecter des utilisateurs à InsideView, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **InsideView **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).