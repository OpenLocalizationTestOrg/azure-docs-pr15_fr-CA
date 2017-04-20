<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec SimpleNexus | Microsoft Azure" 
    description="Apprenez à utiliser SimpleNexus avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-simplenexus"></a>Didacticiel : Intégration d’Azure Active Directory avec SimpleNexus
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et SimpleNexus.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un SimpleNexus de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à SimpleNexus pourront ouverture de session unique dans l’application à votre site de la société SimpleNexus (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour SimpleNexus
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-simplenexus-tutorial/IC785893.png "Scénario")
##<a name="enabling-the-application-integration-for-simplenexus"></a>L’activation de l’intégration de l’application pour SimpleNexus
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour SimpleNexus.

###<a name="to-enable-the-application-integration-for-simplenexus-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour SimpleNexus, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-simplenexus-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-simplenexus-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-simplenexus-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-simplenexus-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **nexus simple**.

    ![Galerie des applications] (./media/active-directory-saas-simplenexus-tutorial/IC785894.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **SimpleNexus**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Connexion simple] (./media/active-directory-saas-simplenexus-tutorial/IC809578.png "Connexion simple")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de SimpleNexus avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **SimpleNexus** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785896.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à SimpleNexus** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785897.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **SimpleNexus URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://simplenexus.com/CompanyName\_connexion*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-simplenexus-tutorial/IC786904.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à SimpleNexus** , cliquez sur **télécharger les métadonnées**et puis transférer le fichier de métadonnées à l’équipe de prise en charge SimpleNexus.

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785899.png "Configurer l’authentification unique")

    >[AZURE.NOTE] L’ouverture de session unique doit être activé par l’équipe de prise en charge SimpleNexus.

5.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-simplenexus-tutorial/IC785900.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à SimpleNexus, il doivent être configurés dans SimpleNexus.  
Dans le cas de SimpleNexus, mise en service est une tâche manuelle est effectuée par l’administrateur.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres SimpleNexus utilisateur compte outils de création ou API fournies par SimpleNexus à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-simplenexus-perform-the-following-steps"></a>Pour affecter des utilisateurs à SimpleNexus, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **SimpleNexus **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-simplenexus-tutorial/IC785901.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-simplenexus-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).