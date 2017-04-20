<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Intacct | Microsoft Azure" 
    description="Apprenez à utiliser Intacct avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Didacticiel : Intégration d’Azure Active Directory avec Intacct
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Intacct.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Intacct
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Intacct pourront ouverture de session unique dans l’application au niveau du site de votre société Intacct (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Intacct
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Scénario")
##<a name="enabling-the-application-integration-for-intacct"></a>L’activation de l’intégration de l’application pour Intacct
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Intacct.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Intacct, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Intacct**.

    ![Galerie des applications] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Intacct**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Intacct avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Intacct** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Intacct** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Intacct URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://Intacct.com/company*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Intacct** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Intacct en tant qu’administrateur.

6.  Cliquez sur l’onglet de la **société** , puis cliquez sur **Informations sur la société**.

    ![Société] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Société")

7.  Cliquez sur l’onglet **sécurité** , puis cliquez sur **Modifier**.

    ![Sécurité] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Sécurité")

8.  Dans la section **authentification unique (SSO)** , effectuez les opérations suivantes :

    ![Ouverture de session unique] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Ouverture de session unique")

    1.  Sélectionnez **Activer l’authentification unique**.
    2.  En tant que **type de fournisseur d’identité**, sélectionnez **SAML 2.0**.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Intacct** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte **URL de l’émetteur** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Intacct** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    5.  Créer un fichier **codé en base 64** de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **certificat**
    7.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Intacct, il doivent être configurés dans Intacct.  
Dans le cas de Intacct, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Intacct** .

2.  Cliquez sur l’onglet de la **société** , puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Utilisateurs")

3.  Cliquez sur l’onglet **Ajouter**

    ![Ajouter] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Ajouter")

4.  Dans la section **Informations utilisateur** , effectuez les opérations suivantes :

    ![Informations utilisateur] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Informations utilisateur")

    1.  Tapez l' **ID utilisateur**, le **nom de famille**, **prénom**, l' **adresse de messagerie**, le **titre** et le **téléphone** d’un compte Azure AD que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Sélectionnez les **privilèges de l’administrateur** d’un compte Azure annonce que vous souhaitez mettre en service.
    3.  Cliquez sur **Enregistrer**.
        
        >[AZURE.NOTE] Le titulaire du compte DAS reçoit un e-mail et suivez un lien pour confirmer leur compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Intacct utilisateur compte outils de création ou API fournies par Intacct à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Pour affecter des utilisateurs à Intacct, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Intacct **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).