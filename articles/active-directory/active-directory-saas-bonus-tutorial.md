<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Bonus.ly | Microsoft Azure" 
    description="Apprenez à utiliser Bonus.ly avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Didacticiel : Intégration de Azure Active Directory avec Bonus.ly

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Bonus.ly. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client test dans Bonus.ly

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Bonus.ly
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bonus-tutorial/IC773679.png "Scénario")
##<a name="enabling-the-application-integration-for-bonusly"></a>L’activation de l’intégration de l’application pour Bonus.ly

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Bonus.ly.

###<a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Bonus.ly, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-bonus-tutorial/IC773680.png "Activer l’ouverture de session unique")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-bonus-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bonus-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Bonus.ly**.

    ![Galerie des applications] (./media/active-directory-saas-bonus-tutorial/IC773681.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Bonus.ly**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Bonus.ly avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Bonus.ly vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Bonus.ly** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Bonus.ly** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773683.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL du locataire Bonus.ly** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Bonus.ly*», puis cliquez sur **suivant**: 

    ![Configurer des URL de l’application] (./media/active-directory-saas-bonus-tutorial/IC773684.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Bonus.ly** , cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat localement en tant que **c:\\Bonusly.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773685.png "De configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, connectez-vous à vos clients **Bonus.ly** .

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**et sélectionnez **les intégrations et les applications**.

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  Sous **L ' ouverture de session unique**, sélectionnez **SAML**.

8.  Sur la page de dialogue **SAML** , effectuez les opérations suivantes :

    ![Bonusly] (./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Bonus.ly** , copier la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de cible IdP SSO** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Bonus.ly** , copiez la valeur de **l’ID d’émetteur** et puis la coller dans la zone de texte **IdP émetteur** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Bonus.ly** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion IdP** .
    4.  Copier la valeur de **l’empreinte numérique** du certificat exporté et puis la coller dans la zone de texte **D’empreinte digitale du certificat** .

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

9.  Cliquez sur **Enregistrer**.

10. Sur le portail classique de Microsoft Azure, sélectionnez la confirmation de la configuration, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bonus-tutorial/IC773689.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Bonus.ly, il doivent être configurés dans Bonus.ly.  
Dans le cas de Bonus.ly, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Dans une fenêtre de navigateur web, connectez-vous à vos clients Bonus.ly.

2.  Cliquez sur **paramètres**

    ![Paramètres] (./media/active-directory-saas-bonus-tutorial/IC781041.png "Paramètres")

3.  Cliquez sur l’onglet **utilisateurs et primes** .

    ![Les utilisateurs et les primes] (./media/active-directory-saas-bonus-tutorial/IC781042.png "Les utilisateurs et les primes")

4.  Cliquez sur **Gérer les utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-bonus-tutorial/IC781043.png "Gérer les utilisateurs")

5.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter utilisateur] (./media/active-directory-saas-bonus-tutorial/IC781044.png "Ajouter utilisateur")

6.  Dans la boîte de dialogue **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-bonus-tutorial/IC781045.png "Ajouter utilisateur")

    1.  Tapez le «**E-mail**, **prénom**, **nom de famille**» d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte DAS recevra un e-mail contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Bonus.ly utilisateur compte outils de création ou API fournies par Bonus.ly à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>Pour affecter des utilisateurs à Bonus.ly, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration Bonus.ly application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-bonus-tutorial/IC773690.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-bonus-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
