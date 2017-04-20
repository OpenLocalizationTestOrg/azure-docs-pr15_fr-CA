<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory Zoom | Microsoft Azure" 
    description="Découvrez comment utiliser le Zoom avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-zoom"></a>Didacticiel : Intégration d’Azure Active Directory Zoom
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Zoom.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client de Zoom
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Zoom pourront à l’ouverture de session unique dans l’application au niveau du site de votre société Zoom (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md)
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour le Zoom
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-zoom-tutorial/IC784693.png "Scénario")

##<a name="enabling-the-application-integration-for-zoom"></a>L’activation de l’intégration des applications pour le Zoom
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour le Zoom.

###<a name="to-enable-the-application-integration-for-zoom-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le Zoom, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-zoom-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-zoom-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-zoom-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Zoom**.

    ![Galerie des applications] (./media/active-directory-saas-zoom-tutorial/IC784694.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Zoom**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Effectuer un zoom avant] (./media/active-directory-saas-zoom-tutorial/IC784695.png "Effectuer un zoom avant")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Zoom avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’integration application **d’effectuer un zoom avant** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784696.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous aux utilisateurs de se connecter à Zoom** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784697.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Zoom URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.zoom.us*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au niveau de Zoom** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784699.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre société de Zoom en tant qu’administrateur.

6.  Cliquez sur l’onglet **De l’authentification unique** .

    ![L’ouverture de session unique] (./media/active-directory-saas-zoom-tutorial/IC784700.png "L’ouverture de session unique")

7.  Cliquez sur l’onglet **Contrôle de la sécurité** et passez les paramètres **De l’authentification unique** .

8.  Dans la section Single Sign-On, effectuez les opérations suivantes :

    ![L’ouverture de session unique] (./media/active-directory-saas-zoom-tutorial/IC784701.png "L’ouverture de session unique")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de Zoom** , copier la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL de la page connexion** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de Zoom** , copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de la page de déconnexion** .
    3.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **certificat d’identité du fournisseur**
    5.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de Zoom** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    6.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-zoom-tutorial/IC784702.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à Zoom, elles doivent être configurés en Zoom.  
Dans le cas de Zoom, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **Zoom** en tant qu’administrateur.

2.  Cliquez sur l’onglet **Gestion des comptes** , puis cliquez sur **Gestion des utilisateurs**.

3.  Dans la section Gestion des utilisateurs, cliquez sur **Ajouter des utilisateurs**.

    ![Gestion des utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784703.png "Gestion des utilisateurs")

4.  Dans la page **Ajouter des utilisateurs** , effectuez les opérations suivantes :

    ![Ajouter des utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784704.png "Ajouter des utilisateurs")

    1.  En tant que **Type d’utilisateur**, sélectionnez de **base**.
    2.  Dans la zone de texte des **E-mails** , tapez l’adresse de messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    3.  Cliquez sur **Ajouter**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres utilisateur compte création outils de Zoom ou l’API fournie par Zoom à disposition DAS, comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-zoom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Zoom, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’integration application **d’effectuer un zoom avant **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-zoom-tutorial/IC784705.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-zoom-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).