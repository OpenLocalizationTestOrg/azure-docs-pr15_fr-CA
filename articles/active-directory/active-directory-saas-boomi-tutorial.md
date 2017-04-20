<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Boomi | Microsoft Azure" 
    description="Apprenez à utiliser Boomi avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-boomi"></a>Didacticiel : Intégration d’Azure Active Directory avec Boomi

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Boomi.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Boomi de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Boomi pourront ouverture de session unique dans l’application au niveau du site de votre société Boomi (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Boomi
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-boomi-tutorial/IC791134.png "Scénario")
##<a name="enabling-the-application-integration-for-boomi"></a>L’activation de l’intégration de l’application pour Boomi

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Boomi.

###<a name="to-enable-the-application-integration-for-boomi-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Boomi, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-boomi-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-boomi-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-boomi-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-boomi-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Boomi**.

    ![Galerie des applications] (./media/active-directory-saas-boomi-tutorial/IC790822.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Boomi**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Boomi] (./media/active-directory-saas-boomi-tutorial/IC790823.png "Boomi")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Boomi avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Boomi** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790824.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Boomi** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de réponse Boomi** , tapez votre **URL de connexion de AtomSphere Boomi** (par exemple : «*https://platform.boomi.com/sso/AccountName/saml*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-boomi-tutorial/IC790826.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Boomi** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790827.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Boomi en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur votre nom de la société, **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-boomi-tutorial/IC790828.png "Programme d’installation")

7.  Cliquez sur **les Options de l’authentification unique**.

    ![Options d’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC790829.png "Options d’authentification unique")

8.  Dans la section **Options d’ouverture de session unique** , effectuez les opérations suivantes :

    ![Options d’ouverture de session unique] (./media/active-directory-saas-boomi-tutorial/IC790830.png "Options d’ouverture de session unique")

    1.  Sélectionnez **Activer SAML Single Sign-On**.
    2.  Cliquez sur **Importer**pour charger du certificat téléchargé.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Boomi** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité** .
    4.  En tant **Fédération code emplacement**, sélectionnez **Id de fédération est en élément NameID du sujet**.
    5.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-boomi-tutorial/IC775560.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Boomi, il doivent être configurés dans Boomi.  
Dans le cas de Boomi, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Boomi** en tant qu’administrateur.

2.  Accédez à **Gestion des utilisateurs \> les utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-boomi-tutorial/IC790831.png "Utilisateurs")

3.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter utilisateur] (./media/active-directory-saas-boomi-tutorial/IC790832.png "Ajouter utilisateur")

4.  Sur la page de la boîte de dialogue **Ajouter des rôles utilisateur** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-boomi-tutorial/IC790833.png "Ajouter utilisateur")

    1.  Tapez le **prénom**, le **Nom** et **courrier électronique** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur OK.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Boomi utilisateur compte outils de création ou API fournies par Boomi à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-boomi-perform-the-following-steps"></a>Pour affecter des utilisateurs à Boomi, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Boomi **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-boomi-tutorial/IC790834.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-boomi-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
