<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Brightspace par Desire2Learn | Microsoft Azure" 
    description="Apprenez à utiliser des Brightspace par Desire2Learn avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Didacticiel : Intégration d’Azure Active Directory avec Brightspace par Desire2Learn

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Brightspace par Desire2Learn.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Brightspace par Desire2Learn de session unique activé l’abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Brightspace par Desire2Learn pourront ouverture de session unique dans l’application à votre Brightspace par le site de la société Desire2Learn (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application de Brightspace par Desire2Learn
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798957.png "Scénario")
##<a name="enabling-the-application-integration-for-brightspace-by-desire2learn"></a>L’activation de l’intégration de l’application de Brightspace par Desire2Learn

L’objectif de cette section doit décrire comment activer l’intégration de l’application de Brightspace par Desire2Learn.

###<a name="to-enable-the-application-integration-for-brightspace-by-desire2learn-perform-the-following-steps"></a>Pour activer l’intégration de l’application de Brightspace par Desire2Learn, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Brightspace par Desire2Learn**.

    ![Bibliothèque d’apllication] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798958.png "Bibliothèque d’apllication")

7.  Dans le volet résultats, sélectionnez le **Brightspace par Desire2Learn**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Brightspace par Desire2Learn] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC799321.png "Brightspace par Desire2Learn")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Brightspace par Desire2Learn avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Brightspace par Desire2Learn** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798959.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Brightspace par Desire2Learn** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798960.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798961.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre **Brightspace par Desire2Learn** (par exemple : *https://partnershowcase.desire2learn.com/Shibboleth.sso/Login?entityID=https://sts.windows-ppe.net/5caf9349-fd93-4a74-b064-0070f65bfb49/&target=https%3A%2F%2Fpartnershowcase.desire2learn.com%2Fd2l%2FshibbolethSSO%2Faspinfo.asp*).
    2.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique à Brightspace par Desire2Learn** , pour télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**et puis enregistrez les métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798962.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre Brightspace par l’équipe de prise en charge Desire2Learn.

    >[AZURE.NOTE] Votre Brightspace par l’équipe de prise en charge de Desire2Learn est la véritable configuration de l’authentification unique.
Vous recevez une notification lors de l’authentification unique a été activée pour votre abonnement.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798963.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Brightspace par Desire2Learn, il doivent être configurés dans Brightspace par Desire2Learn.  
Dans le cas de Brightspace par Desire2Learn, les comptes d’utilisateur doivent être créés par votre Brightspace par l’équipe de prise en charge Desire2Learn.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre Brightspace par les outils de création de compte utilisateur Desire2Learn ou API fournies par Brightspace par Desire2Learn à disposition Azure Active Directory les comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-brightspace-by-desire2learn-perform-the-following-steps"></a>Pour affecter des utilisateurs à Brightspace par Desire2Learn, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Brightspace par Desire2Learn **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC798964.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-brightspace-desire2learn-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
