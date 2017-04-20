<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une marge | Microsoft Azure" 
    description="Apprenez à utiliser la marge avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/19/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-slack"></a>Didacticiel : Intégration d’Azure Active Directory avec une marge
  
L’objectif de ce didacticiel doit afficher l’intégration d’Azure et marge.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Une marge de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la marge pourront à l’ouverture de session unique dans l’application à votre site d’entreprise marge (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application de la marge
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-slack-tutorial/IC794980.png "Scénario")

##<a name="enabling-the-application-integration-for-slack"></a>L’activation de l’intégration de l’application de la marge
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application de la marge.

###<a name="to-enable-the-application-integration-for-slack-perform-the-following-steps"></a>Pour activer l’intégration de l’application de la marge, procédez comme suit :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-slack-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-slack-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-slack-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **la marge**.

    ![Galerie des applications] (./media/active-directory-saas-slack-tutorial/IC794981.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **marge**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Scénario] (./media/active-directory-saas-slack-tutorial/IC796925.png "Scénario")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de marge avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **marge** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794982.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous aux utilisateurs de se connecter à la marge** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794983.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Marge URL de connexion** , tapez l’URL de votre marge clients (par exemple : «*https://azuread.slack.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-slack-tutorial/IC794984.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au niveau de la marge** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794985.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société de marge en tant qu’administrateur.

6.  Accédez à **pour Microsoft Azure AD \> paramètres d’équipe**.

    ![Paramètres d’équipe] (./media/active-directory-saas-slack-tutorial/IC794986.png "Paramètres d’équipe")

7.  Dans la section **Paramètres d’équipe** , cliquez sur l’onglet **authentification** , puis cliquez sur **Modifier les paramètres**.

    ![Paramètres d’équipe] (./media/active-directory-saas-slack-tutorial/IC794987.png "Paramètres d’équipe")

8.  Dans la boîte de dialogue **Paramètres d’authentification de SAML** , effectuez les opérations suivantes :

    ![Paramètres de SAML] (./media/active-directory-saas-slack-tutorial/IC794988.png "Paramètres de SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la marge** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte de **Point de terminaison de 2.0 SAML (HTTP)** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la marge** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte **d’Émetteur de fournisseur d’identité** .
    3.  Créer un fichier **codé en base 64** de votre certificat téléchargé.
    
        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat Public** .
    5.  Désactivez l’option **Autoriser les utilisateurs à modifier leur adresse e-mail**.
    6.  Sélectionnez **Autoriser les utilisateurs à choisir leur propres nom d’utilisateur**.
    7.  Comme **l’authentification de votre équipe doit être utilisée par**, sélectionnez **il est facultatif**.
    8.  Cliquez sur **Enregistrer la Configuration**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-slack-tutorial/IC794989.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à la marge, elles doivent être configurés dans la marge.
  
Il n’y a aucun élément d’action pour vous permet de configurer la mise en service de l’utilisateur à la marge.  
Lorsqu’un utilisateur affecté essaie d’ouvrir une session dans la marge, un marge compte est automatiquement créé si nécessaire.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-slack-perform-the-following-steps"></a>Pour affecter des utilisateurs à la marge, procédez comme suit :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **marge **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-slack-tutorial/IC794990.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-slack-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).