<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Citrix ShareFile | Microsoft Azure" 
    description="Découvrez comment utiliser Citrix ShareFile avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Didacticiel : Intégration de Azure Active Directory avec Citrix ShareFile

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Citrix ShareFile.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client Citrix ShareFile

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Citrix ShareFile pourront ouverture de session unique dans l’application au niveau du site de votre société Citrix ShareFile (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application de Citrix ShareFile
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "Scénario")
##<a name="enabling-the-application-integration-for-citrix-sharefile"></a>L’activation de l’intégration de l’application de Citrix ShareFile

L’objectif de cette section doit décrire comment activer l’intégration de l’application de Citrix ShareFile.

###<a name="to-enable-the-application-integration-for-citrix-sharefile-perform-the-following-steps"></a>Pour activer l’intégration de l’application de Citrix ShareFile, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **ShareFile de Citrix**.

    ![Galerie des applications] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Citrix ShareFile**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Citrix ShareFile] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Citrix ShareFile avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Citrix ShareFile** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Citrix ShareFile** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL Citrix ShareFile connexion sur** , tapez l’URL en utilisant le modèle suivant `https://<tenant-name>.shareFile.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Citrix ShareFile** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![ConfigureSingle de session] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "ConfigureSingle de session")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société **Citrix ShareFile** en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Admin**.

7.  Dans le volet de navigation de gauche, sélectionnez la **Configuration de l’authentification unique**.

    ![Administration des comptes] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "Administration des comptes")

8.  Sur le **Single Sign-On / SAML 2.0 Configuration** page de la boîte de dialogue sous **Les paramètres de base**, effectuez les opérations suivantes :

    ![L’ouverture de session unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "L’ouverture de session unique")

    1.  Cliquez sur **Activer SAML**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Citrix ShareFile** , copiez la valeur de **L’ID d’entité** et collez-le dans le **votre IDP émetteur / ID d’entité** textbox.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Citrix ShareFile** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    4.  Dans le portail Azure classique, de **la configuration de l’authentification unique à Citrix ShareFile** page, copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    5.  Cliquez sur **Modifier** en regard du champ de **Certificat X.509** et télécharger le certificat que vous avez téléchargé à partir du portail classique AD Azure.
        ![Paramètres de base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "Paramètres de base")

9.  Cliquez sur **Enregistrer** sur le portail de gestion Citrix ShareFile.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Citrix ShareFile, ils doivent être mis en service dans Citrix ShareFile.  
Dans le cas de Citrix ShareFile, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Citrix ShareFile** .

2.  Cliquez sur **Gérer les utilisateurs \> gestion des utilisateurs \> + créer employé**.

    ![Créer employé] (./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "Créer employé")

3.  Entrez l' **E-mail**, le **prénom** et le **nom** d’une annonce Azure valide compte que vous souhaitez mettre en service.

    ![Informations de base] (./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "Informations de base")

4.  Cliquez sur **Ajouter un utilisateur**.

    >[AZURE.NOTE] Le titulaire du compte DAS reçoit un e-mail et suivez un lien pour confirmer leur compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Citrix ShareFile utilisateur compte outils de création ou API fournies par Citrix ShareFile aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-citrix-sharefile-perform-the-following-steps"></a>Pour affecter des utilisateurs à Citrix ShareFile, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Citrix ShareFile **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
