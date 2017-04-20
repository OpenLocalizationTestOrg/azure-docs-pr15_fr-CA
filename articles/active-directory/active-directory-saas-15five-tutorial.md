<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec 15Five | Microsoft Azure" 
    description="Apprenez à utiliser 15Five avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-15five"></a>Didacticiel : Intégration d’Azure Active Directory avec 15Five

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et 15Five. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un 15Five de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à 15Five pourront ouverture de session unique dans l’application à votre site de la société 15Five (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour 15Five
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-15five-tutorial/IC784667.png "Scénario")
##<a name="enabling-the-application-integration-for-15five"></a>L’activation de l’intégration de l’application pour 15Five

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour 15Five.

###<a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour 15Five, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-15five-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-15five-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **15Five**.

    ![Galerie des applications] (./media/active-directory-saas-15five-tutorial/IC784668.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **15Five**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![15Five] (./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de 15Five avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **15Five** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784670.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à 15Five** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784671.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **15Five URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.15Five.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-15five-tutorial/IC784672.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à 15Five** , cliquez sur **télécharger les métadonnées**et puis transférer le fichier de métadonnées à l’équipe de prise en charge 15Five.

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784673.png "De configurer l’authentification unique")

    >[AZURE.NOTE] L’ouverture de session unique doit être activé par l’équipe de prise en charge 15Five.

5.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-15five-tutorial/IC784674.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à 15Five, il doivent être configurés dans 15Five.  
Dans le cas de 15Five, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **15Five** en tant qu’administrateur.

2.  Passez à la **société de gestion**.

    ![Gestion d’entreprise] (./media/active-directory-saas-15five-tutorial/IC784675.png "Gestion d’entreprise")

3.  Accédez à **personnes \> ajouter des personnes**.

    ![Personnes] (./media/active-directory-saas-15five-tutorial/IC784676.png "Personnes")

4.  Dans la section Ajouter une nouvelle personne, effectuez les opérations suivantes :

    ![Ajouter une nouvelle personne] (./media/active-directory-saas-15five-tutorial/IC784677.png "Ajouter une nouvelle personne")

    1.  Tapez le **prénom**, le **Nom**, le **titre**, l' **adresse de messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **terminé**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD recevront un message électronique incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres 15Five utilisateur compte outils de création ou d’API fournies par 15Five aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-15five-perform-the-following-steps"></a>Pour affecter des utilisateurs à 15Five, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **15Five **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-15five-tutorial/IC784678.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-15five-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
