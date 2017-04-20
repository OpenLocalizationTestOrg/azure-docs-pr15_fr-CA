<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Freshdesk | Microsoft Azure" 
    description="Apprenez à utiliser Freshdesk avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Didacticiel : Intégration de Azure Active Directory avec Freshdesk
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Freshdesk.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Freshdesk
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Freshdesk pourront ouverture de session unique dans l’application à votre site de la société Freshdesk (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Freshdesk
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-freshdesk-tutorial/IC776761.png "Scénario")
##<a name="enabling-the-application-integration-for-freshdesk"></a>L’activation de l’intégration de l’application pour Freshdesk
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Freshdesk.

###<a name="to-enable-the-application-integration-for-freshdesk-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Freshdesk, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-freshdesk-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-freshdesk-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-freshdesk-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-freshdesk-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Freshdesk**.

    ![Galerie des applications] (./media/active-directory-saas-freshdesk-tutorial/IC776762.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Freshdesk**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Freshdesk] (./media/active-directory-saas-freshdesk-tutorial/IC776763.png "Freshdesk")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Freshdesk avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Freshdesk vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Freshdesk** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776764.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Freshdesk** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776765.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Freshdesk URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. Freshdesk.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-freshdesk-tutorial/IC776766.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au Freshdesk** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et enregistrez-le localement le fichier de certificat en tant que **c:\\Freshdesk.cer**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776767.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Freshdesk en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **Admin**.

    ![Admin] (./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

7.  Dans l’onglet **Paramètres généraux** , cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Sécurité")

8.  Dans la section **sécurité** , effectuez les opérations suivantes :

    ![Ouverture de session unique] (./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Ouverture de session unique")

    1.  **Single Sign On (SSO)**, sélectionnez puis cliquez **sur**.
    2.  Sélectionnez **SAML SSO**.
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Freshdesk** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion SAML** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Freshdesk** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    5.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **D’empreinte digitale du certificat de sécurité** .  

        >[AZURE.TIP]Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    6.  Cliquez sur **Enregistrer**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-freshdesk-tutorial/IC776771.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Freshdesk, il doivent être configurés dans Freshdesk.  
Dans le cas de Freshdesk, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Freshdesk** .

2.  Dans le menu du haut, cliquez sur **Admin**.

    ![Admin] (./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3.  Dans l’onglet **Paramètres généraux** , cliquez sur **Agents**.

    ![Agents] (./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4.  Cliquez sur **nouvel Agent**.

    ![Nouvel Agent] (./media/active-directory-saas-freshdesk-tutorial/IC776774.png "Nouvel Agent")

5.  Dans la boîte de dialogue informations de l’Agent, procédez comme suit :

    ![Informations de l’agent] (./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Informations de l’agent")

    1.  Dans la zone de texte **Nom complet** , tapez le nom du compte Active Directory Azure que vous souhaitez mettre en service.
    2.  Dans la zone **courrier électronique** , tapez l’adresse de messagerie Active Directory Azure du compte Azure AD que vous souhaitez mettre en service.
    3.  Dans la zone **titre** , tapez le titre du compte Azure AD que vous souhaitez mettre en service.
    4.  Sélectionnez le **rôle des Agents**, puis cliquez sur **attribuer**.
    5.  Cliquez sur **Enregistrer**.
    
        >[AZURE.NOTE] Le titulaire du compte Azure AD obtiendra un e-mail contenant un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Freshdesk utilisateur compte outils de création ou API fournies par Freshdesk à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-freshdesk-perform-the-following-steps"></a>Pour affecter des utilisateurs à Freshdesk, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Freshdesk **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-freshdesk-tutorial/IC776776.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-freshdesk-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).