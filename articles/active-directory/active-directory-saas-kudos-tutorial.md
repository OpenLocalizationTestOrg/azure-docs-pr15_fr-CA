<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec chapeau | Microsoft Azure" 
    description="Apprenez à utiliser le chapeau avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-kudos"></a>Didacticiel : Intégration d’Azure Active Directory avec chapeau
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et chapeau.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire chapeau
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à chapeau pourront à l’ouverture de session unique dans l’application au niveau du site de votre société chapeau (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour le chapeau
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-kudos-tutorial/IC787799.png "Scénario")
##<a name="enabling-the-application-integration-for-kudos"></a>L’activation de l’intégration des applications pour le chapeau
  
L’objectif de cette section est à expliquent comment activer l’intégration de l’application pour un chapeau.

###<a name="to-enable-the-application-integration-for-kudos-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le chapeau, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kudos-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-kudos-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-kudos-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-kudos-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **chapeau**.

    ![Galerie des applications] (./media/active-directory-saas-kudos-tutorial/IC787800.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez le **chapeau**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Félicitations] (./media/active-directory-saas-kudos-tutorial/IC787801.png "Félicitations")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de chapeau avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **naguère** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787802.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à un chapeau** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787803.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Naguère signe d’URL** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.kudosnow.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-kudos-tutorial/IC787804.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au chapeau** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787805.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre société de chapeau en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

7.  Cliquez sur **intégrations \> SSO**.

8.  Dans la section **SSO** , effectuez les opérations suivantes :

    ![Authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787807.png "Authentification unique")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au chapeau** , copier la valeur de **l’URL de Service unique ouverture de session** et puis la coller dans la zone de texte **URL de session** .
    2.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP]
        Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **certificat X.509**
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au chapeau** , copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de déconnexion** .
    5.  Dans la zone de texte de **Votre URL de chapeau** , tapez le nom de votre société.
    6.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-kudos-tutorial/IC787808.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à un chapeau, elles doivent être configurés dans le chapeau.  
Dans le cas de naguère, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société de **chapeau** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kudos-tutorial/IC787806.png "Paramètres")

3.  Cliquez sur **administration de l’utilisateur**.

4.  Cliquez sur l’onglet **utilisateurs** , puis cliquez sur **Ajouter un utilisateur**.

    ![Utilisateur Admin] (./media/active-directory-saas-kudos-tutorial/IC787809.png "Utilisateur Admin")

5.  Dans la section **Ajouter un utilisateur** , effectuez les opérations suivantes :

    ![Ajout d’un utilisateur] (./media/active-directory-saas-kudos-tutorial/IC787810.png "Ajout d’un utilisateur")

    1.  Tapez le **prénom**, **Nom**, **courrier électronique** et autres détails d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **créer un utilisateur**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres chapeau utilisateur compte outils de création ou API fournies par chapeau aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-kudos-perform-the-following-steps"></a>Pour affecter des utilisateurs à chapeau, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Félicitations **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-kudos-tutorial/IC787811.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-kudos-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).