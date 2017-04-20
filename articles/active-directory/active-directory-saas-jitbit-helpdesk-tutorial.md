<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk | Microsoft Azure" 
    description="Apprenez à utiliser Jitbit Helpdesk avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Didacticiel : Intégration d’Azure Active Directory avec Jitbit Helpdesk
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Jitbit Helpdesk.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Jitbit Helpdesk
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Jitbit Helpdesk pourront à l’ouverture de session unique dans l’application au niveau du site de votre société Jitbit Helpdesk (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour Jitbit Helpdesk
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scénario")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>L’activation de l’intégration des applications pour Jitbit Helpdesk
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Jitbit Helpdesk.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Jitbit Helpdesk, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Jitbit technique**.

    ![Galerie des applications] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez le **Service d’assistance Jitbit**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Jitbit Helpdesk avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML. .  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Pour être en mesure de configurer l’authentification unique sur vos clients Jitbit Helpdesk, vous devez contacter le support technique de Jitbit Helpdesk pour obtenir cette fonctionnalité est activée en premier.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Du support technique de Jitbit** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter au service d’assistance Jitbit** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Jitbit assistance URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Jitbit.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au service d’assistance Jitbit** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\Jitbit Helpdesk.cer**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre site de la société Jitbit Helpdesk en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  Cliquez sur **Paramètres généraux**.

    ![Les utilisateurs, les sociétés et les autorisations] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Les utilisateurs, les sociétés et les autorisations")

8.  Dans la section de configuration de **paramètres d’authentification** , effectuez les opérations suivantes :

    ![Paramètres d’authentification] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Paramètres d’authentification")

    1.  Sélectionnez **Activer SAML 2.0 unique ouverture de session** connexion à l’aide de Single Sign-On (SSO) avec **OneLogin**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au service d’assistance Jitbit** , copiez la valeur **Service Provider (SP) exécutée par le point de terminaison** et puis la coller dans la zone de texte **URL de point de terminaison** .
    3.  Créer un fichier **codé en base 64** de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé en base 64 et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat X.509**
    5.  Cliquez sur **Enregistrer les modifications**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Jitbit Helpdesk, qu’ils doivent être mis en service dans Jitbit Helpdesk.  
Dans le cas de Jitbit Helpdesk, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Du support technique de Jitbit** .

2.  Dans le menu du haut, cliquez sur **Administration**.

    ![Administration] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  Cliquez sur **les utilisateurs, les sociétés et les autorisations**.

    ![Les utilisateurs, les sociétés et les autorisations] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Les utilisateurs, les sociétés et les autorisations")

4.  Cliquez sur **Ajouter un utilisateur**.

    ![Ajouter un utilisateur] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Ajouter un utilisateur")

5.  Dans la section créer, tapez les données du compte Azure AD vous souhaitez mettre en service dans les zones de texte suivantes : **nom d’utilisateur**, **courriel**, **prénom**, deuxième **Prénom**

    ![Créer] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Créer")

6.  Cliquez sur **créer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Jitbit Helpdesk utilisateur compte outils de création ou API fournies par Jitbit Helpdesk aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Jitbit Helpdesk, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Du support technique de Jitbit **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).