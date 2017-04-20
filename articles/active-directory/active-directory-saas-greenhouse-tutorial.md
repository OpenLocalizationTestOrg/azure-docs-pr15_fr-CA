<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec serre | Microsoft Azure" 
    description="Apprenez à utiliser à effet de serre avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>Didacticiel : Intégration d’Azure Active Directory avec à effet de serre
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et à effet de serre.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un à effet de serre seul signe sur abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à effet de serre pourront à l’ouverture de session unique dans l’application au niveau du site de votre société à effet de serre (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour à effet de serre
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scénario")
##<a name="enabling-the-application-integration-for-greenhouse"></a>L’activation de l’intégration de l’application pour à effet de serre
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour à effet de serre.

###<a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour à effet de serre, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **à effet de serre**.

    ![Galerie des applications] (./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **à effet de serre**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![À effet de serre] (./media/active-directory-saas-greenhouse-tutorial/IC790785.png "À effet de serre")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de serre avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **à effet de serre** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790786.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à effet de serre** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790787.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.greenhouse.io*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à effet de serre** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790789.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à l’équipe de Support d’à effet de serre.

    >[AZURE.NOTE] L’ouverture de session unique doit être activé par l’équipe de support à effet de serre.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-greenhouse-tutorial/IC790790.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour se connecter à effet de serre, ils doivent être mis en service dans à effet de serre.  
Dans le cas de serre, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Ouvrez une session en tant qu’administrateur le site de votre société **à effet de serre** .

2.  Dans le menu du haut, cliquez sur **configurer**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Utilisateurs")

3.  Cliquez sur **nouveaux utilisateurs**.

    ![Nouvel utilisateur] (./media/active-directory-saas-greenhouse-tutorial/IC790792.png "Nouvel utilisateur")

4.  Dans la section **Ajouter un nouvel utilisateur** , effectuez les opérations suivantes :

    ![Ajouter un nouvel utilisateur] (./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Ajouter un nouvel utilisateur")

    1.  Dans la zone de texte **les e-mails d’entrée utilisateur** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.
        
        >[AZURE.NOTE] Les détenteurs de comptes Active Directory de Azure recevront un message électronique incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres à effet de serre utilisateur compte outils de création ou API fournies par à effet de serre aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>Pour affecter des utilisateurs à effet de serre, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **à effet de serre **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).