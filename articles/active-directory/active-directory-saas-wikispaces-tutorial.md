<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Wikispaces | Microsoft Azure" 
    description="Découvrez comment utiliser Wikispaces avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Didacticiel : Intégration d’Azure Active Directory avec Wikispaces
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Wikispaces.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Wikispaces de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Wikispaces pourront ouverture de session unique dans l’application au niveau du site de votre société Wikispaces (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Wikispaces
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>L’activation de l’intégration de l’application pour Wikispaces
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Wikispaces.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Wikispaces, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Wikispaces**.

    ![Galerie des applications] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Wikispaces**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Wikispaces avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Wikispaces** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Wikispaces** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Wikispaces URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.wikispaces.net*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Wikispaces** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurer l’authentification unique")

5.  Envoyer le metadatafile à l’équipe de prise en charge Wikispaces.

    >[AZURE.NOTE] La configuration de l’authentification unique doit être effectuée par l’équipe de support Wikispaces. Vous recevrez une notification dès que la configuration est terminée.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Wikispaces, il doivent être configurés dans Wikispaces.  
Dans le cas de Wikispaces, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Wikispaces** en tant qu’administrateur.

2.  Accéder aux **membres**.

    ![Membres] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membres")

3.  Cliquez sur l' **Inviter des personnes**.

    ![Inviter des personnes] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Inviter des personnes")

4.  Dans la section **d’Inviter des personnes** , effectuez les opérations suivantes :

    ![Inviter des personnes] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Inviter des personnes")

    1.  Tapez les **noms d’utilisateur ou adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Envoyer**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory reçoit un message électronique incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Wikispaces utilisateur compte outils de création ou API fournies par Wikispaces à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Pour affecter des utilisateurs à Wikispaces, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Wikispaces **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).