<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Zoho Mail | Microsoft Azure" 
    description="Découvrez comment utiliser Zoho Mail avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.date="09/09/2016" 
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Didacticiel : Intégration d’Azure Active Directory avec Zoho Mail
  
L’objectif de ce didacticiel doit afficher l’intégration d’Azure et Zoho Mail.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client de messagerie de Zoho
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la messagerie de Zoho pourront ouverture de session unique dans l’application au niveau du site de votre société Zoho Mail (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour la messagerie de Zoho
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scénario")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>L’activation de l’intégration de l’application pour la messagerie de Zoho
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour la messagerie de Zoho.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le courrier Zoho, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Zoho Mail**.

    ![Galerie des applications] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Zoho courrier**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Messagerie de Zoho] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Messagerie de Zoho")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier à Zoho messagerie avec son compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Zoho courrier** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la messagerie de Zoho** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurer des URL de l’application")

    une barre d’outils. Dans la zone de texte **Zoho Mail signe d’adresse URL** , tapez l’URL de votre en utilisant le modèle suivant :`http://<company name>.ZohoMail.com`

    b. Cliquez sur **suivant**.


4.  Dans la page **configuration de l’authentification unique au message de Zoho** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société Zoho courrier en tant qu’administrateur.

6.  Ouvrez le **Panneau de configuration**.

    ![Le panneau de configuration] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Le panneau de configuration")

7.  Cliquez sur l’onglet **Authentification de SAML** .

    ![Authentification SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Authentification SAML")

8.  Dans la section des **Détails d’authentification SAML** , effectuez les opérations suivantes :

    ![Détails d’authentification SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Détails d’authentification SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au message de Zoho** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au message de Zoho** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au message de Zoho** , copiez la valeur de **l’URL de modification de mot de passe** et puis la coller dans la zone de texte **URL de modification de mot de passe** .
    4.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **clé publique** .
    6.  Comme l' **algorithme**, sélectionnez **RSA**.
    7.  Cliquez sur **OK**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à la messagerie de Zoho, elles doivent être configurés dans la messagerie de Zoho.  
Dans le cas de messages de Zoho, mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **Zoho courrier** en tant qu’administrateur.

2.  Accédez à **Panneau de \> de messagerie et de documents**.

3.  Accédez à **Détails de l’utilisateur \> Ajouter utilisateur**.

    ![Ajouter utilisateur] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Ajouter utilisateur")

4.  Dans la boîte de dialogue **Ajouter des utilisateurs** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Ajouter utilisateur")

    1.  Tapez le **prénom**, le **Nom**, le **ID de courrier électronique**, le **mot de passe** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **OK**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un e-mail avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Zoho courrier utilisateur compte outils de création ou API fournies par courrier de Zoho à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Pour affecter des utilisateurs à messagerie de Zoho, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Zoho courrier **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).