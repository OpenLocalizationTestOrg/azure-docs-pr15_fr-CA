<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory explique Microsoft | Microsoft Azure" 
    description="Apprenez à utiliser des instructions de Microsoft avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>Didacticiel : Intégration d’Azure Active Directory avec des instructions pour Microsoft

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure Active Directory et les Directions sur Microsoft.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un itinéraire sur abonnement Microsoft

Si vous n’avez un sens fédérés sur encore d’abonnement à Microsoft, une demande de la messagerie "*service@DirectionsOnMicrosoft.com*«.

À la fin de ce didacticiel, les utilisateurs d’Azure Active Directory que vous avez affecté à plus d’informations sur Microsoft pourront à l’ouverture de session unique dans l’application à l’aide de l’authentification unique.

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour plus d’informations sur Microsoft
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scénario")
##<a name="enabling-the-application-integration-for-directions-on-microsoft"></a>L’activation de l’intégration de l’application pour plus d’informations sur Microsoft

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour plus d’informations sur Microsoft.

###<a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour plus d’informations sur Microsoft, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **instructions sur Microsoft**.

    ![Galerie des applications] (./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez les **Directions sur Microsoft**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Scénario] (./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scénario")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier pour plus d’informations sur Microsoft avec leur compte Azure annonce à l’aide de fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’integration application **Directions sur Microsoft** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à plus d’informations sur Microsoft** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Singel de session] (./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel de session")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte URL de connexion, tapez **https://www.directionsonmicrosoft.com/user/login**, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à plus d’informations sur Microsoft** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées pour les instructions sur l’équipe de support technique de Microsoft (*service@DirectionsOnMicrosoft.com*). Pour activer les instructions sur l’équipe de support technique Microsoft trouver votre abonnement site fédéré, inclure des informations sur votre société dans votre e-mail.

    >[AZURE.NOTE] Ouverture de session unique pour obtenir des instructions sur Microsoft doit être activé par les instructions à l’équipe de support technique de Microsoft.
Vous recevrez une notification lors de l’ouverture de session unique a été activé.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Il n’y a aucun élément d’action pour configurer de provisionnement pour plus d’informations sur Microsoft.  
Lorsqu’un utilisateur affecté essaie d’ouvrir une session dans les instructions de Microsoft en utilisant le panneau d’accès, Directions sur Microsoft vérifie l’existence de l’utilisateur. Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par les Directions sur Microsoft.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>Pour affecter des utilisateurs à plus d’informations sur Microsoft, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  La **plus d’informations sur Microsoft **application integration page, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Oui")
