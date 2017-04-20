<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lucidchart | Microsoft Azure" 
    description="Apprenez à utiliser Lucidchart avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>Didacticiel : Intégration d’Azure Active Directory avec Lucidchart
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Lucidchart.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Lucidchart de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Lucidchart pourront ouverture de session unique dans l’application à votre site de la société Lucidchart (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Lucidchart
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scénario")
##<a name="enabling-the-application-integration-for-lucidchart"></a>L’activation de l’intégration de l’application pour Lucidchart
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Lucidchart.

###<a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Lucidchart, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Lucidchart**.

    ![Galerie des applications] (./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Lucidchart**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Lucidchart] (./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Lucidchart avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Lucidchart** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Lucidchart** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **Lucidchart signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Lucidchart (par exemple : «*https://chart2.office.lucidchart.com/saml/sso/azure*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Lucidchart** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de données local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Lucidchart en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **une équipe**.

    ![Équipe] (./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Équipe")

7.  Cliquez sur **Application \> gérer SAML**.

    ![Gérer SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Gérer SAML")

8.  Sur la page de la boîte de dialogue **Paramètres d’authentification de SAML** , effectuez les opérations suivantes :

    1.  Sélectionnez **Activer l’authentification SAML**, puis cliquez sur **facultatif**.
        ![Paramètres d’authentification de SAML] (./media/active-directory-saas-lucidchart-tutorial/IC791192.png "Paramètres d’authentification de SAML")
    2.  Dans la zone de texte **domaine** , entrez votre domaine, puis cliquez sur **Changer de certificat**.
        ![Changer de certificat] (./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Changer de certificat")
    3.  Ouvrez votre fichier de métadonnées téléchargé et copier le contenu puis le coller dans la zone de texte de **Téléchargement de métadonnées** .
        ![Télécharger les métadonnées] (./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Télécharger les métadonnées")
    4.  Sélectionnez **Ajouter automatiquement un nouvel utilisateur à l’équipe**, puis cliquez sur **Enregistrer les modifications**.
        ![Enregistrer les modifications] (./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Enregistrer les modifications")

9.  Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configuration de session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour configurer approvisionnement à Lucidchart de l’utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter à Lucidchart à l’aide du panneau d’accès, Lucidchart vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Lucidchart.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lucidchart, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Lucidchart **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).