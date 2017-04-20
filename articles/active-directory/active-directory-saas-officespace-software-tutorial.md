<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel OfficeSpace | Microsoft Azure" 
    description="Apprenez à utiliser des logiciels de OfficeSpace avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Didacticiel : Intégration d’Azure Active Directory avec le logiciel OfficeSpace
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et logiciel de OfficeSpace.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un logiciel de OfficeSpace de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté au logiciel de OfficeSpace pourront à l’ouverture de session unique dans l’application au niveau du site de votre société OfficeSpace logiciel (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour le logiciel de OfficeSpace
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scénario")
##<a name="enabling-the-application-integration-for-officespace-software"></a>L’activation de l’intégration de l’application pour le logiciel de OfficeSpace
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour les logiciels de OfficeSpace.

###<a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour les logiciels OfficeSpace, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **OfficeSpace logiciel**.

    ![Galerie des applications] (./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez le **Logiciel de OfficeSpace**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Logiciel de OfficeSpace] (./media/active-directory-saas-officespace-software-tutorial/IC781007.png "Logiciel de OfficeSpace")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier au logiciel de OfficeSpace avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour les logiciels de OfficeSpace vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Logiciel de OfficeSpace** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique = sur] (./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurer l’authentification unique = sur")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter au logiciel de OfficeSpace** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC777767.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **OfficeSpace logiciel de connexion d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application logicielle de OfficeSpace (par exemple : «*https://company.officespacesoftware.com*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique chez OfficeSpace Software** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC793769.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre société de logiciels de OfficeSpace en tant qu’administrateur.

6.  Accédez à **Admin \> connecteurs**.

    ![Admin] (./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Admin")

7.  Cliquez sur **l’autorisation de SAML**.

    ![Connecteurs] (./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connecteurs")

8.  Dans la section **Autorisation de SAML** , effectuez les opérations suivantes :

    ![Configuration de SAML] (./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuration de SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique chez OfficeSpace Software** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **url du fournisseur de déconnexion** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique chez OfficeSpace Software** , copier la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **url de Client idp cible** .
    3.  Copier la valeur de **l’empreinte numérique** du certificat exporté, puis la coller dans la zone de texte **d’empreinte de certificat Client idp** .  

        >[AZURE.TIP]
        Pour plus d’informations, consultez [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI)

    4.  Cliquez sur **Enregistrer les paramètres**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-officespace-software-tutorial/IC777772.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à un logiciel de OfficeSpace, elles doivent être configurés dans le logiciel de OfficeSpace. Dans le cas OfficeSpace, la mise en service est une tâche automatique.  
Il n’y a aucun élément d’action pour vous.  
Les utilisateurs sont automatiquement créés si nécessaire lors de la première tentative de mise sous ouverture de session unique.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres logiciel de OfficeSpace utilisateur compte outils de création ou d’API fournies par le logiciel OfficeSpace aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>Pour affecter des utilisateurs aux logiciels d’OfficeSpace, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Logiciel de OfficeSpace **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).