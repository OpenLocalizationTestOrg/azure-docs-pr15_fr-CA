<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AppDynamics | Microsoft Azure" 
    description="Apprenez à utiliser AppDynamics avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Didacticiel : Intégration d’Azure Active Directory avec AppDynamics

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et AppDynamics. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un AppDynamics de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à AppDynamics pourront ouverture de session unique dans l’application à votre site de la société AppDynamics (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour AppDynamics
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-appdynamics-tutorial/IC790209.png "Scénario")
##<a name="enabling-the-application-integration-for-appdynamics"></a>L’activation de l’intégration de l’application pour AppDynamics

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour AppDynamics.

###<a name="to-enable-the-application-integration-for-appdynamics-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour AppDynamics, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-appdynamics-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-appdynamics-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-appdynamics-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **AppDynamics**.

    ![Galerie des applications] (./media/active-directory-saas-appdynamics-tutorial/IC790210.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **AppDynamics**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AppDynamics] (./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de AppDynamics avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **AppDynamics** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790212.png "Configurer la fonctionnalité d’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à AppDynamics** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790213.png "Configurer la fonctionnalité d’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **AppDynamics signe d’URL** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur AppDynamics («*https://companyname.saas.appdynamics.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-appdynamics-tutorial/IC790214.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à AppDynamics** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790215.png "Configurer la fonctionnalité d’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise AppDynamics en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**, puis cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")

7.  Cliquez sur l’onglet **Fournisseur d’authentification** .

    ![Fournisseur d’authentification] (./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Fournisseur d’authentification")

8.  Dans la section de **Fournisseur d’authentification** , effectuez les opérations suivantes :

    ![Configuration de SAML] (./media/active-directory-saas-appdynamics-tutorial/IC790225.png "Configuration de SAML")

    1.  En tant que **Fournisseur d’authentification**, sélectionnez **SAML**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à AppDynamics** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à AppDynamics** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    4.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    5.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **certificat**
    6.  Cliquez sur **Enregistrer**.
        ![Enregistrer] (./media/active-directory-saas-appdynamics-tutorial/IC777673.png "Enregistrer")

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer la fonctionnalité d’authentification unique] (./media/active-directory-saas-appdynamics-tutorial/IC790226.png "Configurer la fonctionnalité d’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à AppDynamics, il doivent être configurés dans AppDynamics.  
Dans le cas de AppDynamics, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Ouvrez une session en tant qu’administrateur dans le site de votre entreprise AppDynamics.

2.  Accédez aux **utilisateurs**, puis cliquez sur **+** pour ouvrir la boîte de dialogue **Créer un utilisateur** .

    ![Utilisateurs] (./media/active-directory-saas-appdynamics-tutorial/IC790229.png "Utilisateurs")

3.  Dans la section **Créer un utilisateur** , effectuez les opérations suivantes :

    ![Création d’utilisateur] (./media/active-directory-saas-appdynamics-tutorial/IC790230.png "Création d’utilisateur")

    1.  Tapez le **nom d’utilisateur**, **nom**, **courrier électronique**, **Nouveau mot de passe**, **Confirmer le nouveau mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AppDynamics utilisateur compte outils de création ou d’API fournies par AppDynamics pour configurer les comptes d’utilisateur AD Azure.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-appdynamics-perform-the-following-steps"></a>Pour affecter des utilisateurs à AppDynamics, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **AppDynamics **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-appdynamics-tutorial/IC790231.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-appdynamics-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
