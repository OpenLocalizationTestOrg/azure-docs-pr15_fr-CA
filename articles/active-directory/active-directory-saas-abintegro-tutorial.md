<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Abintegro | Microsoft Azure" 
    description="Apprenez à utiliser Abintegro avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-abintegro"></a>Didacticiel : Intégration d’Azure Active Directory avec Abintegro

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Abintegro.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Abintegro de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Abintegro pourront ouverture de session unique dans l’application à votre site de la société Abintegro (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Abintegro
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-abintegro-tutorial/IC790076.png "Scénario")
##<a name="enabling-the-application-integration-for-abintegro"></a>L’activation de l’intégration de l’application pour Abintegro

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Abintegro.

###<a name="to-enable-the-application-integration-for-abintegro-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Abintegro, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-abintegro-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-abintegro-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-abintegro-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **abintegro**.

    ![Galerie des applications] (./media/active-directory-saas-abintegro-tutorial/IC790077.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Abintegro**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Abintegro] (./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Abintegro avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Abintegro** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790079.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Abintegro** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790080.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Abintegro signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à Abintegro (par exemple : `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-abintegro-tutorial/IC790081.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Abintegro** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790082.png "Configurer la fonctionnalité d’authentification unique")

5.  Envoyer le metadatafile à l’équipe de prise en charge de Abintegro.

    >[AZURE.NOTE] La configuration de l’authentification unique doit être effectuée par l’équipe de prise en charge Abintegro. Vous recevrez une notification dès que la configuration est terminée.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-abintegro-tutorial/IC790083.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Il n’y a aucun élément d’action pour configurer approvisionnement à Abintegro de l’utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter à Abintegro à l’aide du panneau d’accès, Abintegro vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Abintegro.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-abintegro-perform-the-following-steps"></a>Pour affecter des utilisateurs à Abintegro, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Abintegro **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-abintegro-tutorial/IC790084.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-abintegro-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
