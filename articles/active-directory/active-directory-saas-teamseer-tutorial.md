<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec TeamSeer | Microsoft Azure" 
    description="Apprenez à utiliser TeamSeer avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-teamseer"></a>Didacticiel : Intégration d’Azure Active Directory avec TeamSeer
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et TeamSeer.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire TeamSeer
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à TeamSeer pourront ouverture de session unique dans l’application à votre site de la société TeamSeer (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour TeamSeer
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scénario")

##<a name="enabling-the-application-integration-for-teamseer"></a>L’activation de l’intégration de l’application pour TeamSeer
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour TeamSeer.

###<a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour TeamSeer, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-teamseer-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-teamseer-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **TeamSeer**.

    ![Galerie des applications] (./media/active-directory-saas-teamseer-tutorial/IC789619.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **TeamSeer**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![TeamSeer] (./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de TeamSeer avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **TeamSeer** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à TeamSeer** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **TeamSeer URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://www.teamseer.com/companyid*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à TeamSeer** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise TeamSeer en tant qu’administrateur.

6.  Accédez à **l’administrateur des ressources humaines**.

    ![Administrateur RH] (./media/active-directory-saas-teamseer-tutorial/IC789634.png "Administrateur RH")

7.  Cliquez sur **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-teamseer-tutorial/IC789635.png "Programme d’installation")

8.  Cliquez sur **définir les détails du fournisseur SAML**.

    ![Paramètres de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789636.png "Paramètres de SAML")

9.  Dans la section Détails du fournisseur SAML, effectuez les opérations suivantes :

    ![Paramètres de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789637.png "Paramètres de SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à TeamSeer** , copiez la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL** .
    2.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrir votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat Public de IdP** .

10. Pour terminer la configuration du fournisseur SAML, effectuez les opérations suivantes :

    ![Paramètres de SAML] (./media/active-directory-saas-teamseer-tutorial/IC789638.png "Paramètres de SAML")

    1.  Dans **Test des adresses de messagerie**, tapez adresse de messagerie de l’utilisateur test.
    2.  Dans la zone de texte de **l’émetteur** , tapez l’URL de l’émetteur du fournisseur de services.
    3.  Cliquez sur **Enregistrer**.

11. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à TeamSeer, il doivent être configurés dans ShiftPlanning.  
Dans le cas de TeamSeer, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **TeamSeer** en tant qu’administrateur.

2.  Effectuez les opérations suivantes :

    ![Administrateur RH] (./media/active-directory-saas-teamseer-tutorial/IC789640.png "Administrateur RH")

    1.  Accédez à **Administrateur RH \> les utilisateurs**.
    2.  Cliquez sur **exécuter l’Assistant Nouvel utilisateur**.

3.  Dans la section **Détails de l’utilisateur** , effectuez les opérations suivantes :

    ![Détails de l’utilisateur] (./media/active-directory-saas-teamseer-tutorial/IC789641.png "Détails de l’utilisateur")

    1.  Tapez le **prénom**, **nom de famille**, **nom d’utilisateur (adresse E-mail)** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **suivant**.

4.  Suivez les instructions à l’écran pour ajouter un nouvel utilisateur, puis cliquez sur **Terminer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres TeamSeer utilisateur compte outils de création ou d’API fournies par TeamSeer pour configurer les comptes d’utilisateur AD Azure.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>Pour affecter des utilisateurs à TeamSeer, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **TeamSeer **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-teamseer-tutorial/IC789642.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-teamseer-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).