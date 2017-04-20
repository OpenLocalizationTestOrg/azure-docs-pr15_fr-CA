<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panopto | Microsoft Azure" 
    description="Apprenez à utiliser Panopto avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Didacticiel : Intégration d’Azure Active Directory avec Panopto
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Panopto.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Panopto
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Panopto pourront ouverture de session unique dans l’application à votre site de la société Panopto (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Panopto
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Scénario")
##<a name="enabling-the-application-integration-for-panopto"></a>L’activation de l’intégration de l’application pour Panopto
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Panopto.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Panopto, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Panopto**.

    ![Galerie de Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Galerie de Appkication")

7.  Dans le volet de résultats, sélectionnez **Panopto**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Panopto avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Panopto** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777667.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Panopto** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777668.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Panopto URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Panopto.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Panopto** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777669.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Panopto en tant qu’administrateur.

6.  Dans la barre d’outils sur la gauche, cliquez sur **système**, puis cliquez sur **Fournisseurs d’identité**.

    ![Système] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Système")

7.  Cliquez sur **Ajouter un fournisseur**.

    ![Fournisseurs d’identité] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Fournisseurs d’identité")

8.  Dans la section fournisseur SAML, effectuez les opérations suivantes :

    ![Configuration de SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configuration de SaaS")

    1.  Dans la liste **Type de fournisseur** , sélectionnez **SAML20**
    2.  Dans la zone de texte **Nom de l’Instance** , tapez un nom pour l’instance.
    3.  Dans la zone de texte **Description claire** , tapez une description conviviale.
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Panopto** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    5.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Panopto** , copiez la valeur de **l’URL de l’authentification unique SAML** et puis la coller dans la zone de texte **Url de la Page rebond** .
    6.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    7.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte **Clé publique**
    8.  Cliquez sur **Enregistrer**.
        ![Enregistrer] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Enregistrer")

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-panopto-tutorial/IC777674.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour configurer approvisionnement à Panopto de l’utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter à Panopto à l’aide du panneau d’accès, Panopto vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Panopto.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Panopto utilisateur compte outils de création ou d’API fournies par Panopto pour configurer les comptes d’utilisateur AD Azure.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Pour affecter des utilisateurs à Panopto, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Panopto **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).