<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Clever | Microsoft Azure" 
    description="Apprenez à utiliser Clever avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-clever"></a>Didacticiel : Intégration d’Azure Active Directory avec Clever

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Clever. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client intelligent

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Clever pourront ouverture de session unique dans l’application à votre site d’entreprise intelligente (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Clever
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-clever-tutorial/IC798977.png "Scénario")
##<a name="enabling-the-application-integration-for-clever"></a>L’activation de l’intégration de l’application pour Clever

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Clever.

###<a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Clever, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-clever-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-clever-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Clever**.

    ![Galerie des applications] (./media/active-directory-saas-clever-tutorial/IC798978.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Clever**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Intelligente] (./media/active-directory-saas-clever-tutorial/IC798979.png "Intelligente")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Clever avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Votre application intelligente attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs] (./media/active-directory-saas-clever-tutorial/IC798980.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Clever** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC784682.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Clever** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798981.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Connexion intelligente d’URL** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application intelligente (par exemple : *https://clever.com/in/azsandbox*), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-clever-tutorial/IC798982.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Clever** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798983.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site d’entreprise intelligente en tant qu’administrateur.

6.  Dans la barre d’outils, cliquez sur **Connexion instantanée**.

    ![Connexion instantanée] (./media/active-directory-saas-clever-tutorial/IC798984.png "Connexion instantanée")

7.  Sur la page de **Connexion instantanée** , effectuez les opérations suivantes :

    ![Connexion instantanée] (./media/active-directory-saas-clever-tutorial/IC798985.png "Connexion instantanée")

    1.  Tapez l' **URL de connexion**.  

        >[AZURE.NOTE] L' **URL de connexion** est une valeur personnalisée.
Vous pouvez obtenir la valeur réelle de votre équipe de prise en charge intelligente.

    2.  En tant que **Système d’identité**, sélectionnez **ADFS**.
    3.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-clever-tutorial/IC798986.png "Configurer l’authentification unique")

9.  Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-clever-tutorial/IC795920.png "Attributs")

10. Pour ajouter les mappages d’attributs requis, procédez comme suit :

    ![attributs de jeton SAML] (./media/active-directory-saas-clever-tutorial/IC795921.png "attributs de jeton SAML")

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|clever.Student.Credentials.district\_nom d’utilisateur|User.userPrincipalName|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    3.  Dans la zone de texte de **Valeur de l’attribut** , sélectionnez la valeur d’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

11. Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Clever, il doivent être configurés dans Clever.  
Dans le cas de Clever, mise en service est une tâche manuelle qui doit être effectuée par votre équipe de prise en charge intelligente.

>[AZURE.NOTE] Vous pouvez utiliser les autres outils de création de compte utilisateur intelligente ou API fournies par Clever à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-clever-perform-the-following-steps"></a>Pour affecter des utilisateurs à Clever, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Clever **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-clever-tutorial/IC798987.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-clever-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
