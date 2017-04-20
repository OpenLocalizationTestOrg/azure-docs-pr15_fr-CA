<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Bime | Microsoft Azure" 
    description="Apprenez à utiliser Bime avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bime"></a>Didacticiel : Intégration de Azure Active Directory avec Bime

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Bime.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Bime

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Bime pourront ouverture de session unique dans l’application au niveau du site de votre société Bime (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Bime
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bime-tutorial/IC775552.png "Scénario")
##<a name="enabling-the-application-integration-for-bime"></a>L’activation de l’intégration de l’application pour Bime

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Bime.

###<a name="to-enable-the-application-integration-for-bime-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Bime, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bime-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-bime-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bime-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Bime**.

    ![Galerie des applications] (./media/active-directory-saas-bime-tutorial/IC775553.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Bime**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bime] (./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Bime avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Bime vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Bime** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC771709.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Bime** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775555.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Bime URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Bimeapp.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bime-tutorial/IC775556.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Bime** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\Bime.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775557.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Bime en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **administration**, puis sur **compte**.

    ![Admin] (./media/active-directory-saas-bime-tutorial/IC775558.png "Admin")

7.  Sur la page de configuration du compte, effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775559.png "Configurer l’authentification unique")

    1.  Sélectionnez **Activer le SAML**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Bime** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion d’accès à distance** .
    3.  Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone de texte **D’empreinte digitale du certificat** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    4.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-bime-tutorial/IC775560.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Bime, il doivent être configurés dans Bime.  
Dans le cas de Bime, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Bime** .

2.  Dans la barre d’outils, cliquez sur **administration**, puis sur **utilisateurs**.

    ![Admin] (./media/active-directory-saas-bime-tutorial/IC775561.png "Admin")

3.  Dans la **Liste des utilisateurs**, cliquez sur **Ajouter un nouvel utilisateur** (« + »).

    ![Utilisateurs] (./media/active-directory-saas-bime-tutorial/IC775562.png "Utilisateurs")

4.  Sur la page de la boîte de dialogue **Détails de l’utilisateur** , effectuez les opérations suivantes :

    ![Détails de l’utilisateur] (./media/active-directory-saas-bime-tutorial/IC775563.png "Détails de l’utilisateur")

    1.  Entrez le prénom, le nom, la connexion, la messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur Enregistrer.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Bime utilisateur compte outils de création ou API fournies par Bime à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-bime-perform-the-following-steps"></a>Pour affecter des utilisateurs à Bime, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Bime **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-bime-tutorial/IC775564.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-bime-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
