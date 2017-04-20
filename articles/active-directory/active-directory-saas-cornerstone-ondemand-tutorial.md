<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec pierre angulaire à la demande | Microsoft Azure" 
    description="Apprenez à utiliser pierre angulaire à la demande avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-cornerstone-ondemand"></a>Didacticiel : Intégration d’Azure Active Directory avec pierre angulaire à la demande

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et pierre angulaire à la demande.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire pierre angulaire à la demande

À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à pierre angulaire à la demande pourront ouverture de session unique dans l’application au niveau du site de votre société à la demande pierre angulaire (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour la pierre angulaire à la demande
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781593.png "Scénario")
##<a name="enabling-the-application-integration-for-cornerstone-ondemand"></a>L’activation de l’intégration de l’application pour la pierre angulaire à la demande

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour la pierre angulaire à la demande.

###<a name="to-enable-the-application-integration-for-cornerstone-ondemand-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour la pierre angulaire à la demande, procédez comme suit :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **pierre angulaire à la demande**.

    ![Galerie des applications] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781594.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Pierre angulaire à la demande**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Pierre angulaire à la demande] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781595.png "Pierre angulaire à la demande")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de pierre angulaire à la demande avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Pierre angulaire à la demande** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781596.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à pierre angulaire à la demande** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781597.png "Microsoft Azure AD Single Sign-On")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte de **Pierre angulaire à la demande URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.csod.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781598.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à pierre angulaire à la demande** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781599.png "Configurer l’authentification unique")

5.  Envoyer que les éléments suivants à la pierre angulaire à l’équipe de support :

    1.  Le certificat téléchargé
    2.  La valeur de **L’URL de connexion à distance**
    3.  La valeur **d’URL de déconnexion à distance** .

    >[AZURE.NOTE] L’ouverture de session unique doit être configurée par l’équipe de support de pierre angulaire à la demande.
Vous recevez une notification de l’équipe de prise en charge lorsque la configuration est terminée.

6.  Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configuration de session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781600.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à pierre angulaire à la demande, il doivent être configurés en pierre angulaire à la demande.  
Dans le cas de pierre angulaire à la demande, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Envoyer les informations (par exemple : nom, Emial) sur l’utilisateur AD Azure que vous souhaitez fournir à la demande de la pierre angulaire l’équipe de support.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres pierre angulaire à la demande utilisateur compte outils de création ou API fournies par pierre angulaire à la demande aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-cornerstone-ondemand-perform-the-following-steps"></a>Pour affecter des utilisateurs à pierre angulaire à la demande, procédez comme suit :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Pierre angulaire à la demande **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC775564.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Affecter des utilisateurs] (./media/active-directory-saas-cornerstone-ondemand-tutorial/IC781601.png "Affecter des utilisateurs")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
