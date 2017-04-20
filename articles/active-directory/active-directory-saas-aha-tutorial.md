<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant Aha ! | Microsoft Azure" 
    description="Découvrez comment utiliser Aha ! avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-aha"></a>Didacticiel : Azure Active Directory intégrant Aha !

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Aha !  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un AH bon ! l’ouverture de session unique activé l’abonnement

À la fin de ce didacticiel, les utilisateurs AD Azure vous avez affecté à Aha ! seront en mesure d’ouverture de session unique dans l’application à votre Aha ! site de la société (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Aha !
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-aha-tutorial/IC798944.png "Scénario")
##<a name="enabling-the-application-integration-for-aha"></a>L’activation de l’intégration de l’application pour Aha !

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Aha !.

###<a name="to-enable-the-application-integration-for-aha-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Aha !, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-aha-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-aha-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-aha-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-aha-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Aha !**.

    ![Galerie des applications] (./media/active-directory-saas-aha-tutorial/IC798945.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Aha !**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Trompent !] (./media/active-directory-saas-aha-tutorial/IC802746.png "Trompent !")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès d’Aha ! avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur le **Aha !** intégration de l’application, cliquez sur **configurer SSO** pour ouvrir la boîte de dialogue **Configuration de session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798946.png "Configurer l’authentification unique")

2.  Sur le **comment vous souhaitez que les utilisateurs à se connecter à Aha !** page, sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798947.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans le **Aha ! URL de connexion** zone de texte, tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre Aha ! Application (par exemple : «*https://company.aha.io/session/new*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-aha-tutorial/IC798948.png "Configurer des URL de l’application")

4.  Sur le **configurer l’ouverture de session unique à Aha !** page pour télécharger votre fichier de métadonnées, cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798949.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans votre Aha ! site de la société en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-aha-tutorial/IC798950.png "Paramètres")

7.  Cliquez sur **compte**.

    ![Profil] (./media/active-directory-saas-aha-tutorial/IC798951.png "Profil")

8.  Cliquez sur **sécurité et l’ouverture de session unique**.

    ![Sécurité et authentification unique] (./media/active-directory-saas-aha-tutorial/IC798952.png "Sécurité et authentification unique")

9.  Dans la section de **L’ouverture de session unique** , en tant que **Fournisseur d’identité**, sélectionnez **SAML2.0**.

    ![Sécurité et authentification unique] (./media/active-directory-saas-aha-tutorial/IC798953.png "Sécurité et authentification unique")

10. Sur la page de configuration **De l’authentification unique** , procédez comme suit :

    ![L’ouverture de session unique] (./media/active-directory-saas-aha-tutorial/IC798954.png "L’ouverture de session unique")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Pour **configurer l’utilisation**, sélectionnez le **Fichier de métadonnées**.
    3.  Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Parcourir**.
    4.  Cliquez sur **mise à jour**.

11. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-aha-tutorial/IC798955.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à Aha !, ils doivent être mis en service dans Aha !.  
Dans le cas d’Aha !, mise en service est une tâche automatique.  
Il n’y a aucun élément d’action pour vous.
  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous ouverture de session unique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre Aha ! outils de création de comptes utilisateur ou des API fournies par Aha ! Pour configurer des comptes d’utilisateur DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-aha-perform-the-following-steps"></a>Pour affecter des utilisateurs à Aha !, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur le **trompent !** intégration de l’application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-aha-tutorial/IC798956.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-aha-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
