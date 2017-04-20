<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec IdeaScale | Microsoft Azure" 
    description="Apprenez à utiliser IdeaScale avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Didacticiel : Intégration d’Azure Active Directory avec IdeaScale
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et IdeaScale.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un IdeaScale de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à IdeaScale pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour IdeaScale
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-ideascale-tutorial/IC790838.png "Scénario")
##<a name="enabling-the-application-integration-for-ideascale"></a>L’activation de l’intégration de l’application pour IdeaScale
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour IdeaScale.

###<a name="to-enable-the-application-integration-for-ideascale-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour IdeaScale, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-ideascale-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-ideascale-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-ideascale-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **IdeaScale**.

    ![Galerie des applications] (./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **IdeaScale**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![IdeaScale] (./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de IdeaScale avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour IdeaScale vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **IdeaScale** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à IdeaScale** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **IdeaScale signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de IdeaScale (par exemple : «*https://company.IdeaScale.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à IdeaScale** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise IdeaScale en tant qu’administrateur.

6.  Accédez aux **paramètres de la Communauté**.

    ![Paramètres de la Communauté] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Paramètres de la Communauté")

7.  Accédez à **sécurité \> unique de paramètres de connexion**.

    ![Paramètres de la fonctionnalité d’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790848.png "Paramètres de la fonctionnalité d’authentification unique")

8.  En tant que **Type de SSO**, sélectionnez **SAML 2.0**.

    ![Type de connexion unique] (./media/active-directory-saas-ideascale-tutorial/IC790849.png "Type de connexion unique")

9.  Dans la boîte de dialogue **Paramètres de connexion unique** , effectuez les opérations suivantes :

    ![Paramètres de la fonctionnalité d’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790850.png "Paramètres de la fonctionnalité d’authentification unique")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à IdeaScale** , copier la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **ID de l’entité IdP SAML** .
    2.  Copie le contenu de votre fichier de métadonnées téléchargé et puis collez-les dans la zone de texte de **Métadonnées IdP de SAML** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à IdeaScale** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de réussite de déconnexion** .
    4.  Cliquez sur **Enregistrer les modifications**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à IdeaScale, il doivent être configurés dans IdeaScale.  
Dans le cas de IdeaScale, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **IdeaScale** en tant qu’administrateur.

2.  Accédez aux **paramètres de la Communauté**.

    ![Paramètres de la Communauté] (./media/active-directory-saas-ideascale-tutorial/IC790847.png "Paramètres de la Communauté")

3.  Accédez à **paramètres de base de \> gestion de membre**.

4.  Cliquez sur **Ajouter des membres**.

    ![Gestion des membres] (./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gestion des membres")

5.  Dans la section Ajouter un nouveau membre, effectuez les opérations suivantes :

    ![Ajouter un nouveau membre] (./media/active-directory-saas-ideascale-tutorial/IC790853.png "Ajouter un nouveau membre")

    1.  Dans la zone **Adresses de messagerie** , tapez l’adresse de messagerie d’un compte DAS valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer les modifications**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un e-mail avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres IdeaScale utilisateur compte outils de création ou API fournies par IdeaScale à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-ideascale-perform-the-following-steps"></a>Pour affecter des utilisateurs à IdeaScale, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **IdeaScale **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-ideascale-tutorial/IC790854.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-ideascale-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).