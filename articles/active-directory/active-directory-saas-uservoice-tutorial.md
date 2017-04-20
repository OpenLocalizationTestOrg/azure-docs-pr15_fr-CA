<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec UserVoice | Microsoft Azure" 
    description="Découvrez comment utiliser UserVoice avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Didacticiel : Intégration de Azure Active Directory avec UserVoice
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et UserVoice.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire UserVoice
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à UserVoice pourront ouverture de session unique dans l’application au niveau du site de votre société UserVoice (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour UserVoice
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-uservoice-tutorial/IC777514.png "Scénario")

##<a name="enabling-the-application-integration-for-uservoice"></a>L’activation de l’intégration de l’application pour UserVoice
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour UserVoice.

###<a name="to-enable-the-application-integration-for-uservoice-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour UserVoice, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-uservoice-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-uservoice-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-uservoice-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-uservoice-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **UserVoice**.

    ![Galerie des applications] (./media/active-directory-saas-uservoice-tutorial/IC777513.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **UserVoice**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![UserVoice] (./media/active-directory-saas-uservoice-tutorial/IC777810.png "UserVoice")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de UserVoice avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour UserVoice vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **UserVoice** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777515.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à UserVoice** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777516.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **UserVoice URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. UserVoice.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-uservoice-tutorial/IC777517.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à UserVoice** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\UserVoice.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777518.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise UserVoice en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur paramètres et sélectionnez portail Web dans le menu.

    ![Paramètres] (./media/active-directory-saas-uservoice-tutorial/IC777519.png "Paramètres")

7.  Sous l’onglet du **portail Web** , dans la section **authentification de l’utilisateur** , cliquez sur **Modifier** pour ouvrir la page de la boîte de dialogue **Modifier l’authentification des utilisateurs**

    ![Portail Web] (./media/active-directory-saas-uservoice-tutorial/IC777520.png "Portail Web")

8.  Sur la page de la boîte de dialogue **Modifier l’authentification utilisateur** , effectuez les opérations suivantes :

    ![Modifier l’authentification de l’utilisateur] (./media/active-directory-saas-uservoice-tutorial/IC777521.png "Modifier l’authentification de l’utilisateur")

    1.  Cliquez sur **Single Sign-On (SSO)**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à UserVoice** , copier la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **SSO à distance Sign-In** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à UserVoice** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la **zone de texte Sign-Out à distance de l’authentification unique**.
    4.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **en cours d ' empreintes digitales SHA1 du certificat** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    5.  Cliquez sur **Enregistrer les paramètres d’authentification**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-uservoice-tutorial/IC777522.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à UserVoice, il doivent être configurés dans UserVoice.  
Dans le cas de UserVoice, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **UserVoice** .

2.  Accéder aux **paramètres**.

    ![Paramètres] (./media/active-directory-saas-uservoice-tutorial/IC777811.png "Paramètres")

3.  Cliquez sur **Général**.

4.  Cliquez sur **les autorisations et les Agents**.

    ![Les agents et les autorisations] (./media/active-directory-saas-uservoice-tutorial/IC777812.png "Les agents et les autorisations")

5.  Cliquez sur **administrateurs de l’ajout**.

    ![Administrateurs de l’ajout] (./media/active-directory-saas-uservoice-tutorial/IC777813.png "Administrateurs de l’ajout")

6.  Dans la boîte de dialogue **Inviter des administrateurs** , procédez comme suit :

    ![Inviter des administrateurs] (./media/active-directory-saas-uservoice-tutorial/IC777814.png "Inviter des administrateurs")

    1.  Dans le TextBox E-mails, tapez l’adresse de messagerie du compte que vous souhaitez mettre en service, puis cliquez sur **Ajouter**.
    2.  Cliquez sur **inviter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres UserVoice utilisateur compte outils de création ou API fournies par UserVoice à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-uservoice-perform-the-following-steps"></a>Pour affecter des utilisateurs à UserVoice, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **UserVoice **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-uservoice-tutorial/IC777523.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-uservoice-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).