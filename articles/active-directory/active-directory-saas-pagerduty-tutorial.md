<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Pagerduty | Microsoft Azure" 
    description="Apprenez à utiliser Pagerduty avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Didacticiel : Intégration de Azure Active Directory avec Pagerduty
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Pagerduty.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Pagerduty
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Pagerduty pourront ouverture de session unique dans l’application à votre site de la société Pagerduty (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Pagerduty
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-pagerduty-tutorial/IC778528.png "Scénario")
##<a name="enabling-the-application-integration-for-pagerduty"></a>L’activation de l’intégration de l’application pour Pagerduty
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Pagerduty.

###<a name="to-enable-the-application-integration-for-pagerduty-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Pagerduty, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-pagerduty-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-pagerduty-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-pagerduty-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Pagerduty**.

    ![Galerie des applications] (./media/active-directory-saas-pagerduty-tutorial/IC778529.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Pagerduty**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PagerDuty] (./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Pagerduty avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Pagerduty** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778531.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Pagerduty** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778532.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Pagerduty URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Pagerduty.com*», puis cliquez sur **suivant**.

    ![Configurer application url] (./media/active-directory-saas-pagerduty-tutorial/IC778533.png "Configurer application url")

4.  Dans la page **configuration de l’authentification unique à Pagerduty** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778534.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Pagerduty en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Paramètres du compte**.

    ![Paramètres de compte] (./media/active-directory-saas-pagerduty-tutorial/IC778535.png "Paramètres de compte")

7.  Cliquez sur **l ' ouverture de session unique**.

    ![L’ouverture de session unique] (./media/active-directory-saas-pagerduty-tutorial/IC778536.png "L’ouverture de session unique")

8.  Dans la page Activer Single Sign-on (SSO), effectuez les opérations suivantes :

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-pagerduty-tutorial/IC778537.png "Activer l’ouverture de session unique")

    1.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    2.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat X.509**
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Pagerduty** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Pagerduty** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    5.  Sélectionnez **Activer l’ouverture de session unique**.
    6.  Cliquez sur **Enregistrer les modifications**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-pagerduty-tutorial/IC778538.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Pagerduty, il doivent être configurés dans Pagerduty.  
Dans le cas de Pagerduty, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Pagerduty** .

2.  Dans le menu du haut, cliquez sur **utilisateurs**.

3.  Cliquez sur **Ajouter des utilisateurs**.

    ![Ajouter des utilisateurs] (./media/active-directory-saas-pagerduty-tutorial/IC778539.png "Ajouter des utilisateurs")

4.  Dans la boîte de dialogue **inviter votre équipe** , tapez **prénom et le nom** et l’adresse de **messagerie** de l’utilisateur AD Azure que vous souhaitez configurer et cliquez sur **Ajouter**, puis cliquez sur **Envoyer d’invite**.

    ![Inviter votre équipe] (./media/active-directory-saas-pagerduty-tutorial/IC778540.png "Inviter votre équipe")

    >[AZURE.NOTE] Tous les utilisateurs ajoutés recevra une invitation pour créer un compte PagerDuty.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Pagerduty utilisateur compte outils de création ou API fournies par Pagerduty à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-pagerduty-perform-the-following-steps"></a>Pour affecter des utilisateurs à Pagerduty, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Pagerduty **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-pagerduty-tutorial/IC778541.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-pagerduty-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).