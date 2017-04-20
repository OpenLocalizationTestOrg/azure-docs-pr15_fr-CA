<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant BAMBOU HR | Microsoft Azure" 
    description="Apprenez à utiliser des ressources humaines de bambou avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Didacticiel : Azure Active Directory intégrant BAMBOU RH

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et BambooHR.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un BambooHR de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à BambooHR pourront ouverture de session unique dans l’application à votre site de la société BambooHR (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour BambooHR
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")
##<a name="enabling-the-application-integration-for-bamboohr"></a>L’activation de l’intégration de l’application pour BambooHR

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour BambooHR.

###<a name="to-enable-the-application-integration-for-bamboohr-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour BambooHR, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **BambooHR**.

    ![Galerie des applications] (./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **BambooHR**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![BambooHR] (./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de BambooHR avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **BambooHR** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Scénario] (./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Scénario")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à BambooHR** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **BambooHR URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application BambooHR (par exemple : https://company.bamboohr.com), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à BambooHR** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise BambooHR en tant qu’administrateur.

6.  Sur la page d’accueil, effectuez les opérations suivantes :

    ![L’ouverture de session unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "L’ouverture de session unique")

    1.  Cliquez sur **applications**.
    2.  Dans le menu applications de gauche, cliquez sur **Single Sign-On**.
    3.  Cliquez sur **SAML Single Sign-On**.

7.  Dans la section **SAML Single Sign-On** , effectuez les opérations suivantes :

    ![SAML Single Sign-On] (./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "SAML Single Sign-On")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à BambooHR** , copiez la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL de connexion de l’authentification unique** .
    2.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    3.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat X.509**
    4.  Cliquez sur **Enregistrer**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à BambooHR, il doivent être configurés dans BambooHR.  
Dans le cas de BambooHR, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre site de **BambooHR** en tant qu’administrateur.

2.  Dans la barre d’outils dans la partie supérieure, cliquez sur **paramètres**.

    ![Définition] (./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Définition")

3.  Cliquez sur **vue d’ensemble**.

4.  Dans le volet de navigation de gauche, accédez à **sécurité \> les utilisateurs**.

5.  Tapez l’adresse de l’e-mail, le nom et le mot de passe utilisateur d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.

6.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres BambooHR utilisateur compte outils de création ou API fournies par BambooHR à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-bamboohr-perform-the-following-steps"></a>Pour affecter des utilisateurs à BambooHR, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **BambooHR **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
