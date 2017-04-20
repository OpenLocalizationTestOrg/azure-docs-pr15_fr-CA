<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec RightAnswers | Microsoft Azure" 
    description="Apprenez à utiliser RightAnswers avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>Didacticiel : Intégration d’Azure Active Directory avec RightAnswers
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et RightAnswers. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un RightAnswers de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à RightAnswers pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour RightAnswers
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-rightanswers-tutorial/IC802925.png "Scénario")
##<a name="enabling-the-application-integration-for-rightanswers"></a>L’activation de l’intégration de l’application pour RightAnswers
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour RightAnswers.

###<a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour RightAnswers, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-rightanswers-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-rightanswers-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-rightanswers-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **RightAnswers**.

    ![Galerie des applications] (./media/active-directory-saas-rightanswers-tutorial/IC802926.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **RightAnswers**, puis cliquez sur **Terminer** pour ajouter l’application.
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de RightAnswers avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **RightAnswers** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-rightanswers-tutorial/IC802927.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à RightAnswers** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-rightanswers-tutorial/IC802928.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de RightAnswers (par exemple : *https://fortify.rightanswers.com/portal/ss/*), puis cliquez sur **suivant**.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-rightanswers-tutorial/IC802929.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique à RightAnswers** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-rightanswers-tutorial/IC802930.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre équipe de support RightAnswers.

    >[AZURE.NOTE] Votre équipe de support de RightAnswers est la véritable configuration de l’authentification unique.
Vous recevez une notification lors de l’authentification unique a été activée pour votre abonnement.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-rightanswers-tutorial/IC802931.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à RightAnswers, il doivent être configurés dans RightAnswers.  
Dans le cas de RightAnswers, la mise en service est une tâche automatique.  
Il n’y a aucun élément d’action pour vous.
  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous ouverture de session unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres RightAnswers utilisateur compte outils de création ou API fournies par RightAnswers à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>Pour affecter des utilisateurs à RightAnswers, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **RightAnswers **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-rightanswers-tutorial/IC802932.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-rightanswers-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).