<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Innotas | Microsoft Azure"
    description="Apprenez à utiliser Innotas avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-innotas"></a>Didacticiel : Intégration d’Azure Active Directory avec Innotas
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Innotas.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Innotas
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Innotas pourront ouverture de session unique dans l’application à votre site de la société Innotas (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Innotas
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-innotas-tutorial/IC777331.png "Scénario")
##<a name="enabling-the-application-integration-for-innotas"></a>L’activation de l’intégration de l’application pour Innotas
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Innotas.

###<a name="to-enable-the-application-integration-for-innotas-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Innotas, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-innotas-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-innotas-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Innotas**.

    ![Galerie des applications] (./media/active-directory-saas-innotas-tutorial/IC777332.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Innotas**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Innotas] (./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Innotas avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Innotas** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777334.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Innotas** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777335.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Innotas URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Innotas.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-innotas-tutorial/IC777336.png "Configurer des URL de l’application")

4.  Page, télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**sur la **configuration de l’authentification unique à Innotas** , et ensuite le fichier de données localement en tant que **c:\\InnotasMetaData.xml**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777337.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à Innotas l’équipe de support. L’équipe de support a configure de l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configuration de session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-innotas-tutorial/IC777338.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour configurer approvisionnement à Innotas de l’utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter à Innotas à l’aide du panneau d’accès, Innotas vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Innotas.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-innotas-perform-the-following-steps"></a>Pour affecter des utilisateurs à Innotas, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Innotas **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-innotas-tutorial/IC777339.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-innotas-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).