<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une journée de travail | Microsoft Azure" 
    description="Apprenez à utiliser la journée de travail avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-workday"></a>Didacticiel : Intégration d’Azure Active Directory avec une journée de travail
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et de la journée de travail. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans une journée de travail
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application de la journée de travail
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Configuration d’approvisionnement de l’utilisateur

![Scénario] (./media/active-directory-saas-workday-tutorial/IC782919.png "Scénario")

##<a name="enabling-the-application-integration-for-workday"></a>L’activation de l’intégration de l’application de la journée de travail
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application de la force de vente.

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Pour activer l’intégration de l’application de la journée de travail, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-workday-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Bibliothèque de l’Application**, cliquez sur **Ajouter une application**et puis cliquez sur **Ajouter une application de mon entreprise à utiliser**.

    ![Ce que vous voulez faire ?] (./media/active-directory-saas-workday-tutorial/IC700995.png "Ce que vous voulez faire ?")

5.  Dans la **zone Rechercher**, tapez la **journée de travail**.

    ![Journée de travail] (./media/active-directory-saas-workday-tutorial/IC701021.png "Journée de travail")

6.  Dans le volet de résultats, sélectionnez la **journée de travail**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Journée de travail] (./media/active-directory-saas-workday-tutorial/IC701022.png "Journée de travail")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de la journée de travail avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez créer un certificat codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page intégration d’application **journée de travail** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782920.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à une journée de travail** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782921.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-workday-tutorial/IC782957.png "Configurer des URL de l’application")

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à se connecter à une journée de travail selon le modèle suivant :`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b.  Dans la zone de texte **URL de réponse journée de travail** , tapez l’URL de réponse de journée de travail selon le modèle suivant :`https://impl.workday.com/<tenant>/login-saml.htmld`

    >[AZURE.NOTE]Votre URL de réponse doit être un sous-domaine (par exemple : www, wd2, wd3, wd3-impl, wd5, wd5-impl). 
    >À l’aide de quelque chose comme «*http://www.myworkday.com*» fonctionne, mais n’est pas le cas de «*http://myworkday.com*». 
 
4.  Dans la page **configuration de l’authentification unique à une journée de travail** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782922.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre journée de travail d’entreprise en tant qu’administrateur.

6.  Accédez à **Menu \> atelier**.

    ![Atelier] (./media/active-directory-saas-workday-tutorial/IC782923.png "Atelier")

7.  Aller à **l’Administration du compte**.

    ![Administration des comptes] (./media/active-directory-saas-workday-tutorial/IC782924.png "Administration des comptes")

8.  Accédez à **Modifier la configuration de clients – sécurité**.

    ![Modifier la sécurité des clients] (./media/active-directory-saas-workday-tutorial/IC782925.png "Modifier la sécurité des clients")

9.  Dans la section **URL de Redirection** , procédez comme suit :

    ![URL de redirection] (./media/active-directory-saas-workday-tutorial/IC7829581.png "URL de redirection")

    une barre d’outils. Cliquez sur **Ajouter une ligne**.

    b. Dans la zone de texte **URL de redirection de connexion** et la zone de texte **URL de redirection Mobile** , tapez l' **URL du locataire journée de travail** que vous avez entré dans la page **Configurer l’application URL** du portail Azure classique.
    
    c. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à une journée de travail** , copiez l' **URL du Service Sign-Out unique**et puis la coller dans la zone de texte **URL de redirection de déconnexion** .

    d.  Dans un **environnement** de zone de texte, tapez le nom de l’environnement.  


    >[AZURE.NOTE] La valeur de l’attribut de l’environnement est liée à la valeur de l’URL de clients :
    >
    >-   Si le nom de domaine des clients de la journée de travail URL commence par l’implémentation (par exemple : *https://impl.workday.com/\<clients\>/login-saml2.htmld*), l’attribut de **l’environnement** doit être défini pour la mise en oeuvre.
    >-   Si le nom de domaine commence par autre chose, vous devez contacter la journée de travail pour obtenir la valeur **d’environnement** correspondante.

10. Dans la section **Installation de SAML** , effectuez les opérations suivantes :

    ![Programme d’installation SAML] (./media/active-directory-saas-workday-tutorial/IC782926.png "Programme d’installation SAML")

    une barre d’outils.  Sélectionnez **Activer l’authentification SAML**.

    b.  Cliquez sur **Ajouter une ligne**.

11. Dans la section fournisseurs d’identité SAML, effectuez les opérations suivantes :

    ![Fournisseurs d’identité SAML] (./media/active-directory-saas-workday-tutorial/IC7829271.png "Fournisseurs d’identité SAML")

    une barre d’outils. Dans la zone de texte Nom de fournisseur d’identité, tapez un nom de fournisseur (par exemple : *SPInitiatedSSO*).

    b. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à une journée de travail** , copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte de **l’émetteur** .

    c. Sélectionnez **Activer la déconnexion de Initialted jours ouvrables**.

    d. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à une journée de travail** , copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de la demande de déconnexion** .


    e. Cliquez sur **Le certificat de clé publique identité fournisseur**, puis cliquez sur **créer**. 

    ![Créer] (./media/active-directory-saas-workday-tutorial/IC782928.png "Créer")

    f. Cliquez sur **x509 de créer une clé publique**. 
        
    ![Créer] (./media/active-directory-saas-workday-tutorial/IC782929.png "Créer")


1. Dans la section **X509 d’afficher la clé publique** , effectuez les opérations suivantes : 

    ![X509 d’afficher la clé publique] (./media/active-directory-saas-workday-tutorial/IC782930.png "X509 d’afficher la clé publique") 

    une barre d’outils. Dans la zone de texte **nom** , tapez un nom pour votre certificat (par exemple : *EPI\_SP*).
        
    b. Dans la zone de texte **Valide à partir de** , tapez le valide à partir de la valeur de l’attribut de votre certificat.
    
    c.  Dans la zone de texte **Valide pour** , tapez la valeur de l’attribut de votre certificat valide.
        
    >[AZURE.NOTE] Vous pouvez obtenir le valide à partir de la date et le valide à la date du certificat téléchargé en double-cliquant dessus. Les dates sont répertoriés sous l’onglet **Détails** .

    d. Créer un fichier **codé en Base 64** de votre certificat téléchargé.  

    >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    e.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et puis copiez le contenu de celui-ci.
    
    f.  Dans la zone de texte du **certificat** , collez le contenu du Presse-papiers.
    
    g.  Cliquez sur **OK**.

12.  Effectuez les opérations suivantes : 

    ![Configuration de l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC7829351111.png "Configuration de l’authentification unique")

    une barre d’outils.  Activer la **x509 clé privée**.

    b.  Dans la zone de texte **ID de fournisseur de Service** , tapez **http://www.workday.com**.

    c.  Sélectionnez **Activer SP exécutée par l’authentification SAML**.

    d.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à une journée de travail** , copiez la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL de Service d’authentification unique IdP** .
     
    e. Sélectionnez **compressé pas de demande d’authentification de SP exécutée par le serveur**.

    f. En tant que **Méthode de Signature de la demande d’authentification**, sélectionnez **SHA256**. 
        
    ![Méthode de Signature de demande d’authentification] (./media/active-directory-saas-workday-tutorial/IC782932.png "Méthode de Signature de demande d’authentification") 
 
    g. Cliquez sur **OK**. 
        
    ![OK] (./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

12. Dans le portail Azure classique, dans la page **configuration de l’authentification unique à une journée de travail** , cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782934.png "De configurer l’authentification unique")

13. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-workday-tutorial/IC782935111.png "De configurer l’authentification unique")



##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour obtenir un utilisateur de test mis en service dans la journée de travail, vous devez contacter l’équipe de support de journée de travail.  
L’équipe de support de journée de travail va créer l’utilisateur pour vous.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-workday-perform-the-following-steps"></a>Pour affecter des utilisateurs à la journée de travail, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **journée de travail **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-workday-tutorial/IC782935.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-workday-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).