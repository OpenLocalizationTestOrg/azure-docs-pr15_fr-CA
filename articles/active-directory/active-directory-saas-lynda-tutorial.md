<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Lynda.com | Microsoft Azure" 
    description="Découvrez comment utiliser Lynda.com avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Didacticiel : Intégration d’Azure Active Directory avec Lynda.com
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Lynda.com.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Lynda.com
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Lynda.com pourront à l’ouverture de session unique dans l’application au niveau du site de votre société Lynda.com (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Lynda.com
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Scénario")
##<a name="enabling-the-application-integration-for-lyndacom"></a>L’activation de l’intégration de l’application pour Lynda.com
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Lynda.com.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Lynda.com, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Lynda.com**.

    ![Galerie des applications] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Lynda.com**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Lynda.com avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

>[AZURE.IMPORTANT]Pour être en mesure de configurer l’authentification unique sur vos clients Lynda.com, vous devez contacter le support technique de Lynda.com pour obtenir cette fonctionnalité est activée en premier.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Lynda.com** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777526.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Lynda.com** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777527.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Lynda.com URL de connexion** , tapez l’URL de votre client Lynda.com (par exemple : *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Lynda.com** , pour télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777529.png "De configurer l’authentification unique")

5.  Envoyez le fichier de métadonnées téléchargés à l’équipe de support Lynda.com. L’équipe de support Lynda.com effectue la configuration de session unique pour vous.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-lynda-tutorial/IC777530.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Il n’y a aucun élément d’action pour configurer Lynda.com de configuration utilisateur.  
Lorsqu’un utilisateur affecté essaie de se connecter Lynda.com à l’aide du panneau d’accès, Lynda.com vérifie l’existence de l’utilisateur.  
Si aucun compte d’utilisateur disponible n’est encore, il est automatiquement créé par Lynda.com.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Lynda.com utilisateur compte outils de création ou API fournies par Lynda.com aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Lynda.com, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Lynda.com **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).