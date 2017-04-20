<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory zone de LMS | Microsoft Azure" 
    description="Apprenez à utiliser des canevas LMS avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Didacticiel : Intégration d’Azure Active Directory zone de LMS

L’objectif de ce didacticiel doit afficher l’intégration d’Azure et la zone de dessin.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client de la zone de dessin

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la zone de dessin pourront ouverture de session unique dans l’application au niveau du site de votre société Canvas (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour la toile
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scénario")
##<a name="enabling-the-application-integration-for-canvas"></a>L’activation de l’intégration de l’application pour la toile

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour la toile.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour la toile, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **zone de dessin**.

    ![Galerie des applications] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez la **zone de dessin**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Zone de dessin")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier à la zone de dessin avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour la toile vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **canevas** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la zone de dessin** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte des **Canvas URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant `https://<tenant-name>.instructure.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au niveau de la zone de dessin** , cliquez sur **Télécharger le certificat**pour télécharger votre certificat et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre société de zone de dessin en tant qu’administrateur.

6.  Accédez à **cours \> les comptes gérés \> Microsoft**.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zone de dessin")

7.  Dans le volet de navigation de gauche, sélectionnez **l’authentification**, puis cliquez sur **Ajouter une nouvelle configuration de SAML**.

    ![Authentification] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Authentification")

8.  Sur la page intégration actuelle, effectuez les opérations suivantes :

    ![Intégration actuelle] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Intégration actuelle")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la zone de dessin** , copiez la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **ID de l’entité IdP** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la zone de dessin** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion sur** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la zone de dessin** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion à** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la zone de dessin** , copiez la valeur de **l’URL de modification de mot de passe** et puis la coller dans la zone de texte **Lien de modification de mot de passe** .
    5.  Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone de texte **D’empreinte digitale du certificat** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Dans la liste des **Attributs de connexion** , sélectionnez **NameID**.
    7.  Dans la liste **Identificateur de Format** , sélectionnez **emailAddress**.
    8.  Cliquez sur **Enregistrer les paramètres d’authentification**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à la zone de dessin, elles doivent être configurés dans la zone de dessin.  
Pour la toile, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients de **zone de dessin** .

2.  Accédez à **cours \> les comptes gérés \> Microsoft**.

    ![Zone de dessin] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Zone de dessin")

3.  Cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Utilisateurs")

4.  Cliquez sur **Ajouter un nouvel utilisateur**.

    ![Utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Utilisateurs")

5.  Dans la zone Ajouter une page de la boîte de dialogue Nouvel utilisateur, effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Ajouter utilisateur")

    1.  Dans la zone de texte **Nom complet** , tapez le nom de l’utilisateur.
    2.  Dans la zone de texte **message** , tapez l’adresse de messagerie de l’utilisateur.
    3.  Dans la zone **connexion** , tapez l’adresse de messagerie de l’utilisateur AD Azure.
    4.  Sélectionnez **l’utilisateur à propos de la création de ce compte de messagerie**.
    5.  Cliquez sur **Ajouter un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres zone utilisateur compte outils de création ou d’API fournies par la zone de travail aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Pour affecter des utilisateurs à la zone de dessin, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **zone de dessin **, cliquez sur **affecter des utilisateurs**.

    ![Affectation d’utilisateurs] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Affectation d’utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
