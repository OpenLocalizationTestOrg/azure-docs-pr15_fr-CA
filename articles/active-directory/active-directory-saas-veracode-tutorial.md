<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Veracode | Microsoft Azure" 
    description="Apprenez à utiliser Veracode avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-veracode"></a>Didacticiel : Intégration d’Azure Active Directory avec Veracode
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Veracode. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Veracode de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Veracode pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Veracode
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-veracode-tutorial/IC802903.png "Scénario")

##<a name="enabling-the-application-integration-for-veracode"></a>L’activation de l’intégration de l’application pour Veracode
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Veracode.

###<a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Veracode, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-veracode-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-veracode-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-veracode-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Veracode**.

    ![Galerie des applications] (./media/active-directory-saas-veracode-tutorial/IC802904.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Veracode**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Veracode] (./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Veracode avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Votre application Veracode attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Veracode** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802907.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Veracode** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802908.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , cliquez sur **suivant**.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-veracode-tutorial/IC802909.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique à Veracode** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802910.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Veracode en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **paramètres**, puis cliquez sur **administration**.

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7.  Cliquez sur l’onglet **SAML** .

8.  Dans la section **Paramètres d’organisation SAML** , effectuez les opérations suivantes :

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Veracode** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur**
    2.  Pour télécharger votre certificat téléchargé, cliquez sur **Fichier**.
    3.  Sélectionnez **Activer l’inscription automatique**.

9.  Dans la section **Paramètres d’enregistrement automatique** , effectuez les opérations suivantes, puis cliquez sur **Enregistrer**:

    ![Administration] (./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")

    1.  En tant **Nouvelle Activation d’utilisateur**, sélectionnez **Aucune Activation requise**.
    2.  En tant **Mises à jour des données utilisateur**, sélectionnez les **Données de préférence de l’utilisateur Veracode**.
    3.  Pour plus de **Détails d’attribut SAML**, sélectionnez le des options suivantes :
        -   **Rôles d’utilisateur**
        -   **Administration des politiques**
        -   **Réviseur**
        -   **Responsable de la sécurité**
        -   **Exécutif**
        -   **Auteur de la soumission**
        -   **Créateur**
        -   **Tous les Types d’analyse**
        -   **Membres de l’équipe**
        -   **Équipe par défaut**

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-veracode-tutorial/IC802914.png "Configurer l’authentification unique")

11. Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-veracode-tutorial/IC795920.png "Attributs")

12. Pour ajouter les mappages d’attributs requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-veracode-tutorial/IC802906.png "Attributs")

  	| Nom de l’attribut | Valeur de l’attribut |
  	|:---------------|:----------------|
  	| Prénom      | User.GivenName  |
  	| nom       | User.Surname    |
  	| Messagerie          | User.Mail       |

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.

    3.  Dans la zone de texte de **Valeur de l’attribut** , sélectionnez la valeur d’attribut affichée pour cette ligne.

    4.  Cliquez sur **terminé**.

13. Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Veracode, il doivent être configurés dans Veracode.  
Dans le cas de Veracode, la mise en service est une tâche automatique.  
Il n’y a aucun élément d’action pour vous...
  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous ouverture de session unique.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Veracode utilisateur compte outils de création ou API fournies par Veracode à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-veracode-perform-the-following-steps"></a>Pour affecter des utilisateurs à Veracode, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Veracode **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-veracode-tutorial/IC802915.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-veracode-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).