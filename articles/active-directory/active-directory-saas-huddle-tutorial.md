<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant le nombre | Microsoft Azure" 
    description="Découvrez comment utiliser le clin de œil avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-huddle"></a>Didacticiel : Azure Active Directory intégrant clin de œil
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et clin de œil.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un clin de œil de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à clin de œil pourront à l’ouverture de session unique dans l’application au site de votre société clin de œil (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour le clin de œil
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787830.png "Configurer l’authentification unique")
##<a name="enabling-the-application-integration-for-huddle"></a>L’activation de l’intégration de l’application pour le clin de œil
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour le clin de œil.

###<a name="to-enable-the-application-integration-for-huddle-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le clin de œil, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-huddle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-huddle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-huddle-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-huddle-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **le nombre**.

    ![Galerie des applications] (./media/active-directory-saas-huddle-tutorial/IC787831.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez le **clin de œil**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Clin de œil] (./media/active-directory-saas-huddle-tutorial/IC787832.png "Clin de œil")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de clin de œil avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page **clin de œil** d’un intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787833.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous aux utilisateurs de se connecter à clin de œil** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787834.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Nombre de signe d’URL** , tapez l’URL de votre client clin de œil, selon le modèle suivant «*http://company.huddle.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-huddle-tutorial/IC787835.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à clin de œil** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787836.png "Configurer l’authentification unique")

    1.  Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.
    2.  Copiez la valeur de **l’URL de l’émetteur** , la valeur de **l’URL de l’authentification unique SAML** et le certificat téléchargé et puis de les envoyer à l’équipe de support clin de œil.

    >[AZURE.NOTE] L’ouverture de session unique doit être activé par l’équipe de support clin de œil.
Vous recevez une notification lorsque la configuration est terminée.

5.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-huddle-tutorial/IC787837.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à clin de œil, elles doivent être configurés dans le clin de œil.  
En ce qui concerne le clin de œil, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société **clin de œil** en tant qu’administrateur.

2.  Cliquez sur **espace de travail**.

3.  Cliquez sur **personnes \> inviter des personnes**.

    ![Personnes] (./media/active-directory-saas-huddle-tutorial/IC787838.png "Personnes")

4.  Dans la section **créer une nouvelle invitation** , effectuez les opérations suivantes :

    ![Nouvelle Invitation] (./media/active-directory-saas-huddle-tutorial/IC787839.png "Nouvelle Invitation")

    1.  Dans la liste **Choisir une équipe pour inviter des personnes à y participer** , sélectionnez **l’équipe**.
    2.  Tapez l' **Adresse de messagerie** d’un compte DAS valide que vous souhaitez mettre en service dans la zone de texte associée.
    3.  Cliquez sur **inviter**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD recevront un message électronique incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres clin de œil utilisateur compte outils de création ou d’API fournies par le clin de œil à disposition DAS, comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-huddle-perform-the-following-steps"></a>Pour affecter des utilisateurs à clin de œil, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page **clin de œil **d’un intégration d’application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-huddle-tutorial/IC787840.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-huddle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).