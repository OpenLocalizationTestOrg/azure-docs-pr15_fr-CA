<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Coupa | Microsoft Azure" 
    description="Apprenez à utiliser Coupa avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-coupa"></a>Didacticiel : Intégration d’Azure Active Directory avec Coupa

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Coupa.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Coupa de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Coupa pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Coupa
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-coupa-tutorial/IC791897.png "Scénario")
##<a name="enabling-the-application-integration-for-coupa"></a>L’activation de l’intégration de l’application pour Coupa

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Coupa.

###<a name="to-enable-the-application-integration-for-coupa-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Coupa, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-coupa-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-coupa-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-coupa-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Coupa**.

    ![Galerie des applications] (./media/active-directory-saas-coupa-tutorial/IC791898.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Coupa**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Coupa] (./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Coupa avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Coupa vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Ouverture de session sur le site de votre entreprise Coupa en tant qu’administrateur.

2.  Accédez à **le programme d’installation \> contrôle de la sécurité**.

    ![Contrôles de sécurité] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Contrôles de sécurité")

3.  Pour télécharger le fichier de métadonnées Coupa sur votre ordinateur, cliquez sur **télécharger et importer les métadonnées SP**.

    ![Métadonnées de Coupa SP] (./media/active-directory-saas-coupa-tutorial/IC791901.png "Métadonnées de Coupa SP")

4.  Dans une autre fenêtre de navigateur, ouvrez une session sur le portail classique Azure.

5.  Sur la page intégration d’application **Coupa** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurer l’authentification unique")

6.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Coupa** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurer l’authentification unique")

7.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Coupa (par exemple : «*http://company.Coupa.com*»).
    2.  Ouvrez votre fichier de métadonnées Coupa téléchargé, puis copiez l' **Index de AssertionConsumerService/URL**.
    3.  Dans la zone de texte **URL de réponse Coupa** , collez la valeur de **l’Index de AssertionConsumerService/URL** .
    4.  Cliquez sur **suivant**.

8.  Dans la page **configuration de l’authentification unique à Coupa** , cliquez sur **télécharger les métadonnées**pour télécharger votre fichier de métadonnées et puis enregistrez le fichier localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurer l’authentification unique")

9.  Sur le site de la société Coupa, accédez à **le programme d’installation \> contrôle de la sécurité**.

    ![Contrôles de sécurité] (./media/active-directory-saas-coupa-tutorial/IC791900.png "Contrôles de sécurité")

10. Dans la section **se connecter en utilisant les informations d’identification de Coupa** , effectuez les opérations suivantes :

    ![Connectez-vous à l’aide des informations d’identification de la Coupa] (./media/active-directory-saas-coupa-tutorial/IC791906.png "Connectez-vous à l’aide des informations d’identification de la Coupa")

    1.  Sélectionnez **se connecter à l’aide de SAML**.
    2.  Cliquez sur **Parcourir** pour charger votre fichier de métadonnées téléchargé Azure Active.
    3.  Cliquez sur **Enregistrer**.

11. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Coupa, il doivent être configurés dans Coupa.  
Dans le cas de Coupa, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Coupa** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **le programme d’installation**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791908.png "Utilisateurs")

3.  Cliquez sur **créer**.

    ![Créer des utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791909.png "Créer des utilisateurs")

4.  Dans la section **Création d’utilisateur** , effectuez les opérations suivantes :

    ![Détails de l’utilisateur] (./media/active-directory-saas-coupa-tutorial/IC791910.png "Détails de l’utilisateur")

    1.  Tapez le **Login**, **prénom**, **Nom**, **ID de connexion unique**, les attributs de **messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **créer**.

    >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un e-mail avec un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Coupa utilisateur compte outils de création ou API fournies par Coupa à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-coupa-perform-the-following-steps"></a>Pour affecter des utilisateurs à Coupa, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Coupa **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-coupa-tutorial/IC791911.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-coupa-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
