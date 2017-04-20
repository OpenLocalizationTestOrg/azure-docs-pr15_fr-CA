<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec NetDocuments | Microsoft Azure" 
    description="Apprenez à utiliser NetDocuments avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-netdocuments"></a>Didacticiel : Intégration d’Azure Active Directory avec NetDocuments
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et NetDocuments.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire NetDocuments
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à NetDocuments pourront ouverture de session unique dans l’application au niveau du site de votre société NetDocuments (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour NetDocuments
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-netdocuments-tutorial/IC795040.png "Scénario")
##<a name="enabling-the-application-integration-for-netdocuments"></a>L’activation de l’intégration de l’application pour NetDocuments
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour NetDocuments.

###<a name="to-enable-the-application-integration-for-netdocuments-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour NetDocuments, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-netdocuments-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-netdocuments-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-netdocuments-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-netdocuments-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **NetDocuments**.

    ![Galerie des applications] (./media/active-directory-saas-netdocuments-tutorial/IC795041.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **NetDocuments**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![NetDocuments] (./media/active-directory-saas-netdocuments-tutorial/IC795042.png "NetDocuments")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de NetDocuments avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour NetDocuments vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **NetDocuments** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795043.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à NetDocuments** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795044.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-netdocuments-tutorial/IC795045.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application de NetDocuments (par exemple : «*https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=CA-JI1BG3H1*»).
    2.  Dans la zone de texte **URL de réponse NetDocuments** , tapez la même valeur que vous avez tapé dans la la zone de texte **URL de connexion** .  

        >[AZURE.NOTE]Vous pouvez trouver la valeur correcte à la fin de la boîte de dialogue **Identité fédérée** (voir la capture d’écran de l’étape 9).

    3.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique à NetDocuments** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795046.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise NetDocuments en tant qu’administrateur.

6.  Accédez à **l’administrateur**.

7.  Cliquez sur **Ajouter et supprimer des utilisateurs et des groupes**.

    ![Référentiel] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

8.  Cliquez sur **Configuration avancée des options d’authentification**.

    ![Configuration avancée des options d’authentification] (./media/active-directory-saas-netdocuments-tutorial/IC795048.png "Configuration avancée des options d’authentification")

9.  Dans la boîte de dialogue de **L’identité fédérée** , effectuez les opérations suivantes :

    ![Fédérés Identitty] (./media/active-directory-saas-netdocuments-tutorial/IC795049.png "Fédérés Identitty")

    1.  Comme **type de serveur d’identité fédérés**, sélectionnez **Les Services de fédération Active Directory**.
    2.  Cliquez sur **Choisir un fichier**, pour télécharger le fichier de métadonnées téléchargé.
    3.  Cliquez sur **OK**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-netdocuments-tutorial/IC795050.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à NetDocuments, il doivent être configurés dans NetDocuments.  
Dans le cas de NetDocuments, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Ouverture de session sur le site de votre entreprise **NetDocuments** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **Admin**.

    ![Admin] (./media/active-directory-saas-netdocuments-tutorial/IC795051.png "Admin")

3.  Cliquez sur **Ajouter et supprimer des utilisateurs et des groupes**.

    ![Référentiel] (./media/active-directory-saas-netdocuments-tutorial/IC795047.png "Référentiel")

4.  Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie d’un compte Azure Active Directory valide vous souhaitez mettre en service, puis cliquez sur **Ajouter un utilisateur**.

    ![Adresse de courriel] (./media/active-directory-saas-netdocuments-tutorial/IC795053.png "Adresse de courriel")

    >[AZURE.NOTE]Le titulaire du compte Azure Active Directory obtenez un e-mail contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres NetDocuments utilisateur compte outils de création ou API fournies par NetDocuments à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-netdocuments-perform-the-following-steps"></a>Pour affecter des utilisateurs à NetDocuments, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **NetDocuments **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-netdocuments-tutorial/IC795054.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-netdocuments-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).