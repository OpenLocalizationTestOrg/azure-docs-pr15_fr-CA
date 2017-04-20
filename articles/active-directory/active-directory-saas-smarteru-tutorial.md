<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SmarterU | Microsoft Azure" 
    description="Apprenez à utiliser SmarterU avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Didacticiel : Intégration de Azure Active Directory avec SmarterU
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et SmarterU.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire SmarterU
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à SmarterU pourront ouverture de session unique dans l’application à votre site de la société SmarterU (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour SmarterU
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-smarteru-tutorial/IC777320.png "Scénario")

##<a name="enabling-the-application-integration-for-smarteru"></a>L’activation de l’intégration de l’application pour SmarterU
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour SmarterU.

###<a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour SmarterU, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-smarteru-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-smarteru-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-smarteru-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **SmarterU**.

    ![Fallery d’application] (./media/active-directory-saas-smarteru-tutorial/IC777321.png "Fallery d’application")

7.  Dans le volet de résultats, sélectionnez **SmarterU**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![SmarterU] (./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de SmarterU avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **SmarterU** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777323.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à SmarterU** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777324.png "Configurer l’authentification unique")

3.  Page, télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**sur la **configuration de l’authentification unique à SmarterU** , et ensuite le fichier de données localement en tant que **c:\\SmarterUMetaData.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777325.png "Configurer l’authentification unique")

4.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise SmarterU en tant qu’administrateur.

5.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Paramètres du compte**.

    ![Paramètres de compte] (./media/active-directory-saas-smarteru-tutorial/IC777326.png "Paramètres de compte")

6.  Sur la page de configuration du compte, effectuez les opérations suivantes :

    ![Autorisation externe] (./media/active-directory-saas-smarteru-tutorial/IC777327.png "Autorisation externe")

    1.  Sélectionnez **Activer l’autorisation d’externe**.
    2.  Dans la section de **Contrôle de connexion principal** , sélectionnez l’onglet **SmarterU** .
    3.  Dans la section **Connexion de l’utilisateur par défaut** , sélectionnez l’onglet **SmarterU** .
    4.  Sélectionnez **Activer la Okta**.
    5.  Copiez le contenu du fichier de métadonnées téléchargés et puis la coller dans la zone de texte de **Métadonnées de Okta** .
    6.  Cliquez sur **Enregistrer**.

7.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-smarteru-tutorial/IC777328.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à SmarterU, il doivent être configurés dans SmarterU.  
Dans le cas de SmarterU, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **SmarterU** .

2.  Atteindre **les utilisateurs**.

3.  Dans la section utilisateur, effectuez les opérations suivantes :

    ![Nouvel utilisateur] (./media/active-directory-saas-smarteru-tutorial/IC777329.png "Nouvel utilisateur")

    1.  Cliquez sur **+ utilisateur**.
    2.  Tapez les valeurs de l’attribut associé du compte d’utilisateur Active Directory Azure dans les zones de texte suivantes : **principal de messagerie**, **ID employé**, **mot de passe**, **Vérifiez le mot de passe**, **nom**, **nom de famille**.
    3.  Cliquez sur **actif**.
    4.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SmarterU utilisateur compte outils de création ou API fournies par SmarterU à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>Pour affecter des utilisateurs à SmarterU, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **SmarterU **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-smarteru-tutorial/IC777330.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-smarteru-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).