<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Citrix GoToMeeting | Microsoft Azure" 
    description="Découvrez comment utiliser Citrix GoToMeeting avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Didacticiel : Intégration d’Azure Active Directory avec Citrix GoToMeeting  
S’applique à : Azure

>[AZURE.TIP]Pour obtenir des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=522412).

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Citrix GoToMeeting. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Citrix GoToMeeting

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application de Citrix GoToMeeting
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Configuration de] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration de")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>L’activation de l’intégration de l’application de Citrix GoToMeeting

L’objectif de cette section doit décrire comment activer l’intégration de l’application de Citrix GoToMeeting.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Pour activer l’intégration de l’application de Citrix GoToMeeting, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de la galerie] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Ajouter une application à partir de la galerie")

6.  Dans la **zone Rechercher**, tapez **GoToMeeting de Citrix**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Dans le volet résultats, sélectionnez **Citrix GoToMeeting**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Citrix GoToMeeting avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à vos clients de Citrix GoToMeeting.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Sur la page intégration d’application **Citrix GoToMeeting** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer une seule suite de signe** .

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Activer l’ouverture de session unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Citrix GoToMeeting** , sélectionnez **Microsoft Azure AD Single Sign-On**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "De configurer l’authentification unique")


3. Dans la page **Configurer les paramètres d’application** , cliquez sur **suivant**. 

    ![Activer l’ouverture de session unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Activer l’ouverture de session unique")

4.  Dans la page **configuration de l’authentification unique à Citrix GoToMeeting** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "De configurer l’authentification unique")

5.  Dans une autre fenêtre de navigateur, connectez-vous à votre centre d’organisation Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Cliquez sur l’onglet **Fournisseur d’identité** et puis procédez comme suit :  

    ![Programme d’installation SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Programme d’installation SAML")

    une barre d’outils. Sélectionnez **manuel**

    
    b. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Citrix GoToMeeting** , copier la valeur de **l’URL de la Page connexion** et puis la coller dans la zone de texte **URL de la page connexion** . 

    
    c. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Citrix GoToMeeting** , copiez la valeur de **l’URL de la Page Sign-Out** et puis la coller dans la zone de texte **URL de la page de déconnexion** .

    
    d. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Citrix GoToMeeting** , copiez la valeur de **l’ID de l’entité** et puis la coller dans la zone de texte **ID de l’entité fournisseur identité** .

   
    e. Pour télécharger votre certificat téléchargé, cliquez sur **Télécharger le certificat**.

    
    f. Cliquez sur **Enregistrer**.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "De configurer l’authentification unique")


7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.

    ![Programme d’installation SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Programme d’installation SAML")





##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

L’objectif de cette section doit décrire comment activer la mise en service de comptes d’utilisateurs Active Directory à Citrix GoToMeeting.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, sur la page intégration d’application **Citrix GoToMeeting** , cliquez sur **configurer approvisionnement de l’utilisateur** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

    ![Configurer configuration des utilisateurs] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configurer configuration des utilisateurs")

2.  Dans la page **paramètres et les informations d’identification de l’administrateur** , effectuez les opérations suivantes :

    ![Configurer configuration des utilisateurs] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configurer configuration des utilisateurs")

    une barre d’outils. Dans la zone de texte **Nom d’utilisateur Admin Citrix GoToMeeting** , tapez le nom d’utilisateur d’un administrateur.

    
    b. Dans la zone **Mot de passe de Citrix GoToMeeting Admin** , mot de passe de l’administrateur.

    
    c. Cliquez sur **suivant**.

3.  Dans la page de **Confirmation** , cliquez sur la coche pour enregistrer votre configuration.

4.  Cliquez sur le bouton **Valider** pour vérifier votre configuration.


##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Pour affecter des utilisateurs à Citrix GoToMeeting, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Citrix GoToMeeting** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Oui")

Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé pour la boîte de dépôt pour les entreprises.

Dans un premier temps de vérification, vous pouvez vérifier le statut de l’approvisionnement, en cliquant sur le tableau de bord dans la D sur la page d’intégration d’application **Citrix GoToMeeting** dans Azure portal classique.

![Tableau de bord] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Tableau de bord")

Un cycle de configuration d’utilisateur terminé est indiqué par un statut liés :

![Statut d’intégration] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Statut d’intégration")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès.

Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](https://msdn.microsoft.com/library/dn308586).
