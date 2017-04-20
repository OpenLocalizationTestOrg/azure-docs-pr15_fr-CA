<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Syncplicity | Microsoft Azure" 
    description="Apprenez à utiliser Syncplicity avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Didacticiel : Intégration d’Azure Active Directory avec Syncplicity
  
L’objectif de ce didacticiel est d’afficher la configuration de l’authentification unique entre Azure Active Directory (AD Azure) et Syncplicity.
  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Syncplicity
  
À la fin de ce didacticiel, les utilisateurs AD Azure auquel vous avez affecter Syncplicity l’accès sera en mesure de même signe dans l’application au niveau du site de votre société Syncplicity (service fournisseur initiée signe) ou en utilisant le panneau Azure AD.

1.  L’activation de l’intégration de l’application pour Syncplicity
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-syncplicity-tutorial/IC769524.png "Scénario")

##<a name="enabling-the-application-integration-for-syncplicity"></a>L’activation de l’intégration de l’application pour Syncplicity
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Syncplicity.

###<a name="to-enable-the-application-integration-for-syncplicity-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Syncplicity, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-syncplicity-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-syncplicity-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-syncplicity-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-syncplicity-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Syncplicity**.

    ![Galerie d’applications Syncplicity] (./media/active-directory-saas-syncplicity-tutorial/IC769532.png "Galerie d’applications Syncplicity")

7.  Dans le volet de résultats, sélectionnez **Syncplicity**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Syncplicity] (./media/active-directory-saas-syncplicity-tutorial/IC769533.png "Syncplicity")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
Cette section explique comment permettre aux utilisateurs de s’authentifier à Syncplicity avec leur compte Azure Active Directory, à l’aide de fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Syncplicity** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-syncplicity-tutorial/IC769534.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Syncplicity** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-syncplicity-tutorial/IC769535.png "Microsoft Azure AD Single Sign-On")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Syncplicity URL de connexion** , les utilisateurs de l’URL, utilisez pour vous connecter à votre application de Syncplicity de type cliquez sur **suivant**. 

    L’URL de l’application est votre client Syncplicity (par exemple : *http://company.Syncplicity.com*) :

    ![Configurer des URL de l’application] (./media/active-directory-saas-syncplicity-tutorial/IC769536.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Syncplicity** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-syncplicity-tutorial/IC769543.png "De configurer l’authentification unique")

5.  Connectez-vous à vos clients **Syncplicity** .

6.  Dans le menu du haut, cliquez sur **admin**, sélectionnez **paramètres**, puis cliquez sur **domaine de personnalisé et de l’authentification unique**.

    ![Syncplicity] (./media/active-directory-saas-syncplicity-tutorial/IC769545.png "Syncplicity")

7.  Sur la page de dialogue **Single Sign-On (SSO)** , effectuez les opérations suivantes :

    ![Single Sign-On \(l’authentification unique\)](./media/active-directory-saas-syncplicity-tutorial/IC769550.png "Single Sign-On \(SSO\)")

    1.  Dans la zone de texte **Personnalisé domaine** , tapez le nom de votre domaine.
    2.  Sélectionnez **activé** en tant **qu’état d’ouverture de session unique**.
    3.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à Syncplicity** , copiez la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **Id de l’entité** .
    4.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à Syncplicity** , copier la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL de la page connexion** .
    5.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique à Syncplicity** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de la page déconnexion** .
    6.  Dans le **Certificat d’identité du fournisseur**, cliquez sur **Choisir un fichier**et ensuite télécharger le certificat que vous avez téléchargé à partir du portail classique Azure.
    7.  Cliquez sur **Enregistrer les modifications**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Confirmation] (./media/active-directory-saas-syncplicity-tutorial/IC769554.png "Confirmation")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
DAS aux utilisateurs d’être en mesure de se connecter, ils doivent être configurés pour l’application de Syncplicity. Cette section décrit comment créer des comptes d’utilisateur DAS dans Syncplicity.

###<a name="to-provision-a-user-account-to-syncplicity-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur à Syncplicity, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients de **Syncplicity** (par exemple : *https://company.Syncplicity.com*).

2.  Cliquez sur **Admin** , puis sélectionnez les **comptes d’utilisateurs**.

3.  Cliquez sur **Ajouter un utilisateur**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-syncplicity-tutorial/IC769764.png "Gérer les utilisateurs")

4.  Tapez l' **adresse de messagerie** d’un compte DAS que vous souhaitez configurer et sélectionnez **l’utilisateur** en tant que **rôle**puis cliquez sur **suivant**.

    ![Informations de compte] (./media/active-directory-saas-syncplicity-tutorial/IC769765.png "Informations de compte")

    >[AZURE.NOTE] Le titulaire du compte DAS recevront un message électronique incluant un lien pour confirmer et activer le compte.

5.  Sélectionnez un groupe dans votre société que votre nouvel utilisateur doit devenir membre de, puis cliquez sur **suivant**.

    ![Appartenance au groupe] (./media/active-directory-saas-syncplicity-tutorial/IC769772.png "Appartenance au groupe")

    >[AZURE.NOTE] S’il n’y a aucun groupe répertorié, cliquez sur **suivant**.

6.  Sélectionnez les dossiers que vous souhaitez placer sous contrôle de Syncplicity sur l’ordinateur de l’utilisateur, puis cliquez sur **suivant**.

    ![Dossiers de Syncplicity] (./media/active-directory-saas-syncplicity-tutorial/IC769773.png "Dossiers de Syncplicity")

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Syncplicity utilisateur compte outils de création ou API fournies par Syncplicity à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-syncplicity-perform-the-following-steps"></a>Pour affecter des utilisateurs à Syncplicity, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Syncplicity** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-syncplicity-tutorial/IC769557.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-syncplicity-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).

