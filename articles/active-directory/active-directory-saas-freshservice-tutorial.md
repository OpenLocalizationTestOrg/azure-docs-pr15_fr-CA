<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec FreshService | Microsoft Azure" 
    description="Apprenez à utiliser FreshService avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Didacticiel : Intégration d’Azure Active Directory avec FreshService
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et FreshService.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un FreshService de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à FreshService pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour FreshService
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-freshservice-tutorial/IC790807.png "Scénario")
##<a name="enabling-the-application-integration-for-freshservice"></a>L’activation de l’intégration de l’application pour FreshService
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour FreshService.

###<a name="to-enable-the-application-integration-for-freshservice-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour FreshService, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-freshservice-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-freshservice-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-freshservice-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **FreshService**.

    ![Galerie des applications] (./media/active-directory-saas-freshservice-tutorial/IC790808.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **FreshService**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Freshservice] (./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de FreshService avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour FreshService vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **FreshService** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshservice-tutorial/IC790810.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à FreshService** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshservice-tutorial/IC790811.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **FreshService URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application Freshdesk (par exemple : «*http://democompany.freshservice.com/*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-freshservice-tutorial/IC790812.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à FreshService** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshservice-tutorial/IC790813.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise FreshService en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Admin**.

    ![Admin] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

7.  Dans le **Portail client**, cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-freshservice-tutorial/IC790815.png "Sécurité")

8.  Dans la section **sécurité** , effectuez les opérations suivantes :

    ![Ouverture de session unique] (./media/active-directory-saas-freshservice-tutorial/IC790816.png "Ouverture de session unique")

    1.  Commutateur de **OnON d’ouverture de session unique**.
    2.  Sélectionnez **SAML SSO**.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à FreshService** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion SAML** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à FreshService** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    5.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **D’empreinte digitale du certificat de sécurité** .
    
        >[AZURE.TIP]Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshservice-tutorial/IC790817.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à FreshService, il doivent être configurés dans FreshService.  
Dans le cas de FreshService, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **FreshService** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **Admin**.

    ![Admin] (./media/active-directory-saas-freshservice-tutorial/IC790814.png "Admin")

3.  Dans la section **Gestion des utilisateurs** , cliquez sur **les demandeurs**.

    ![Demandeurs] (./media/active-directory-saas-freshservice-tutorial/IC790818.png "Demandeurs")

4.  Cliquez sur **nouveau demandeur**.

    ![Nouveaux demandeurs] (./media/active-directory-saas-freshservice-tutorial/IC790819.png "Nouveaux demandeurs")

5.  Dans la section **Nouveau demandeur** , effectuez les opérations suivantes :

    ![Nouveau demandeur] (./media/active-directory-saas-freshservice-tutorial/IC790820.png "Nouveau demandeur")

    1.  Entrez les attributs de **prénom** et de la **messagerie électronique** d’un compte Azure Active Directory valide que vous souhaitez pour la fourniture dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un e-mail incluant un lien permettant de confirmer le compte avant de devenir active

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres FreshService utilisateur compte outils de création ou API fournies par FreshService à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-freshservice-perform-the-following-steps"></a>Pour affecter des utilisateurs à FreshService, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **FreshService **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-freshservice-tutorial/IC790821.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-freshservice-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).