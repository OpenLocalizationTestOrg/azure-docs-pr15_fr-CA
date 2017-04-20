<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Onit | Microsoft Azure" 
    description="Apprenez à utiliser Onit avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-onit"></a>Didacticiel : Intégration d’Azure Active Directory avec Onit
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Onit.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Onit de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Onit pourront ouverture de session unique dans l’application au niveau du site de votre société Onit (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Onit
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-onit-tutorial/IC791166.png "Scénario")
##<a name="enabling-the-application-integration-for-onit"></a>L’activation de l’intégration de l’application pour Onit
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Onit.

###<a name="to-enable-the-application-integration-for-onit-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Onit, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-onit-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-onit-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-onit-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Onit**.

    ![Galerie des applications] (./media/active-directory-saas-onit-tutorial/IC791167.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Onit**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Onit] (./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Onit avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Onit vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).
  
Votre application Onit attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![L’ouverture de session unique] (./media/active-directory-saas-onit-tutorial/IC791168.png "L’ouverture de session unique")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page **Onit** d’intégration application, dans le menu de la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-onit-tutorial/IC791169.png "Attributs")

2.  Pour ajouter les mappages d’attributs requis, procédez comme suit :

    
  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|nom|User.userPrincipalName|
  	|Messagerie|User.Mail|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    3.  Dans la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

3.  Cliquez sur **appliquer les modifications**.

4.  Dans votre navigateur, cliquez sur **Nouveau** pour ouvrir la boîte de dialogue **Quick Start** à nouveau.

5.  Cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791170.png "Configurer l’authentification unique")

6.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Onit** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791171.png "Configurer l’authentification unique")

7.  Dans la page **Configurer l’URL App** , dans la zone de texte **Onit signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application Onit (par exemple : «*https://ms-sso-test.onit.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-onit-tutorial/IC791172.png "Configurer des URL de l’application")

8.  Dans la page **configuration de l’authentification unique à Onit** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791173.png "Configurer l’authentification unique")

9.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Onit en tant qu’administrateur.

10. Dans le menu du haut, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")

11. Cliquez sur **Edition Corporation**.

    ![Édition entreprise] (./media/active-directory-saas-onit-tutorial/IC791175.png "Édition entreprise")

12. Cliquez sur l’onglet **sécurité** .

    ![Modifier info.] (./media/active-directory-saas-onit-tutorial/IC791176.png "Modifier info.")

13. Sous l’onglet **sécurité** , effectuez les opérations suivantes :

    ![L’ouverture de session unique] (./media/active-directory-saas-onit-tutorial/IC791177.png "L’ouverture de session unique")

    1.  En tant que **Stratégie d’authentification**, sélectionnez **connexion unique et mot de passe**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Onit** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de cible Idp** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Onit** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion Idp** .
    4.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **Idp d’empreintes de certificats (SHA1)** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    5.  Comme **Type d’authentification unique**, sélectionnez **SAML**.
    6.  Dans la zone de **texte du bouton de connexion SSO** , tapez un texte de bouton que vous souhaitez.
    7.  Sélectionnez **connecter avec l’authentification unique : requis pour les utilisateurs/domaines suivants**, tapez l’adresse de messagerie d’un utilisateur test dans la zone de texte associée, puis cliquez sur **mise à jour**.
        ![Édition entreprise] (./media/active-directory-saas-onit-tutorial/IC791178.png "Édition entreprise")

14. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-onit-tutorial/IC791179.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Onit, il doivent être configurés dans Onit.  
Dans le cas de Onit, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Ouverture de session sur le site de votre entreprise **Onit** en tant qu’administrateur.

2.  Cliquez sur **Ajouter un utilisateur**.

    ![Administration] (./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")

3.  Sur la page de la boîte de dialogue **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-onit-tutorial/IC791181.png "Ajouter utilisateur")

    1.  Tapez le **nom** et l' **Adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **créer**.  

        >[AZURE.NOTE] Le propriétaire du compte recevront un message électronique incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Onit utilisateur compte outils de création ou API fournies par Onit à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-onit-perform-the-following-steps"></a>Pour affecter des utilisateurs à Onit, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Onit **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-onit-tutorial/IC791182.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-onit-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).