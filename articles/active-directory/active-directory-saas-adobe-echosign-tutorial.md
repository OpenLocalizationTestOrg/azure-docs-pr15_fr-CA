<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Adobe EchoSign | Microsoft Azure" 
    description="Découvrez comment utiliser Adobe EchoSign avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-adobe-echosign"></a>Didacticiel : Intégration d’Azure Active Directory avec Adobe EchoSign

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Adobe EchoSign.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Une EchoSign d’Adobe unique d’authentification abonnement activé

À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Adobe EchoSign pourront à l’ouverture de session unique dans l’application à votre site de la société Adobe EchoSign (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Adobe EchoSign
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "Scénario")
##<a name="enabling-the-application-integration-for-adobe-echosign"></a>L’activation de l’intégration de l’application pour Adobe EchoSign

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Adobe EchoSign.

###<a name="to-enable-the-application-integration-for-adobe-echosign-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Adobe EchoSign, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **EchoSign d’Adobe**.

    ![Galerie des applications] (./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Adobe EchoSign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Adobe EchoSign] (./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Adobe EchoSign avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Adobe EchoSign** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Adobe EchoSign** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Adobe EchoSign signe d’adresse URL** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.echosign.com/*» et puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Adobe EchoSign** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société Adobe EchoSign en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **le compte**et puis, dans le volet de navigation sur la matrice de gauche, cliquez sur **Paramètres du SAML** sous **Paramètres du compte**.

    ![Compte] (./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "Compte")

7.  Dans la section Paramètres de SAML, effectuez les opérations suivantes :

    ![Paramètres de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "Paramètres de SAML")

    1.  En tant que **Mode de SAML**, sélectionnez **SAML obligatoire**.
    2.  Sélectionnez **Autoriser des administrateurs de compte EchoSign pour se connecter à l’aide de leurs informations d’identification de EchoSign**.
    3.  **Création de l’utilisateur**, sélectionnez **Ajouter automatiquement des utilisateurs authentifiés par le biais de SAML**.

8.  Déplacer, effectuez les opérations suivantes :

    ![Paramètres de SAML] (./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "Paramètres de SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Adobe EchoSign** , copiez la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **ID de l’entité IdP** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Adobe EchoSign** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion IdP** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Adobe EchoSign** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion IdP** .
    4.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o) 
 5.  Ouvrir votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte **IdP certificat** 6.  Cliquez sur **Enregistrer les modifications**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à Adobe EchoSign, il doivent être configurés dans Adobe EchoSign.  
Dans le cas de Adobe EchoSign, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre site de la société **Adobe EchoSign** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **compte**et puis, dans le volet de navigation sur la matrice de gauche, cliquez sur **utilisateurs et groupes**, puis cliquez sur **créer un nouvel utilisateur**.

    ![Compte] (./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "Compte")

3.  Dans la section **Créer un nouvel utilisateur** , effectuez les opérations suivantes :

    ![Création d’utilisateur] (./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "Création d’utilisateur")

    1.  Tapez **l’Adresse de messagerie**, le **prénom** et le **Nom** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **créer un utilisateur**.

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un e-mail contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Adobe EchoSign utilisateur compte outils de création ou API fournies par Adobe EchoSign aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-adobe-echosign-perform-the-following-steps"></a>Pour affecter des utilisateurs à Adobe EchoSign, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **EchoSign d’Adobe **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
