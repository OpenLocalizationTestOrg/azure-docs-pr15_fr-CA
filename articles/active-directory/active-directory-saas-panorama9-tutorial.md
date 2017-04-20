<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Panorama9 | Microsoft Azure" 
    description="Apprenez à utiliser Panorama9 avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-panorama9"></a>Didacticiel : Intégration d’Azure Active Directory avec Panorama9
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Panorama9.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Panorama9 de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Panorama9 pourront ouverture de session unique dans l’application à votre site de la société Panorama9 (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Panorama9
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-panorama9-tutorial/IC790016.png "Scénario")
##<a name="enabling-the-application-integration-for-panorama9"></a>L’activation de l’intégration de l’application pour Panorama9
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Panorama9.

###<a name="to-enable-the-application-integration-for-panorama9-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Panorama9, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-panorama9-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-panorama9-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-panorama9-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Panorama9**.

    ![Galerie des applications] (./media/active-directory-saas-panorama9-tutorial/IC790017.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Panorama9**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Panorama9] (./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Panorama9 avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Panorama9 vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Panorama9** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790019.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Panorama9** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790020.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Panorama9 signe d’URL** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à Panorama9 (par exemple : «*https://dashboard.panorama9.com/saml/access/3262*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-panorama9-tutorial/IC790021.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au Panorama9** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**et puis l’enregistrer en local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790022.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Panorama9 en tant qu’administrateur.

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **Gérer**, puis cliquez sur **Extensions**.

    ![Extensions] (./media/active-directory-saas-panorama9-tutorial/IC790023.png "Extensions")

7.  Dans la boîte de dialogue **Extensions** , cliquez sur **Single Sign-On**.

    ![L’ouverture de session unique] (./media/active-directory-saas-panorama9-tutorial/IC790024.png "L’ouverture de session unique")

8.  Dans la section **paramètres** , effectuez les opérations suivantes :

    ![Paramètres] (./media/active-directory-saas-panorama9-tutorial/IC790025.png "Paramètres")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Panorama9** , copiez la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL du fournisseur d’identité** .
    2.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **d’empreinte de certificat** .  

        >[AZURE.TIP]Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    3.  Cliquez sur **Enregistrer**.

9.  Sur le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-panorama9-tutorial/IC790026.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Panorama9, il doivent être configurés dans Panorama9.  
Dans le cas de Panorama9, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Panorama9** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **Gérer**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790027.png "Utilisateurs")

3.  Cliquez sur **+**.

4.  Dans la section de données utilisateur, effectuez les opérations suivantes :

    ![Utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790028.png "Utilisateurs")

    1.  Dans la zone de texte **message** , tapez l’adresse de messagerie d’un utilisateur Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Panorama9 utilisateur compte outils de création ou API fournies par Panorama9 à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-panorama9-perform-the-following-steps"></a>Pour affecter des utilisateurs à Panorama9, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Panorama9** application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-panorama9-tutorial/IC790029.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-panorama9-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).