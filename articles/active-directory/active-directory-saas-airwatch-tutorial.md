<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AirWatch | Microsoft Azure" 
    description="Apprenez à utiliser AirWatch avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Didacticiel : Intégration d’Azure Active Directory avec AirWatch

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et AirWatch.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un AirWatch de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à AirWatch pourront ouverture de session unique dans l’application au niveau du site de votre société AirWatch (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour AirWatch
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>L’activation de l’intégration de l’application pour AirWatch

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour AirWatch.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour AirWatch, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **AirWatch**.

    ![Galerie des applications] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **AirWatch**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de AirWatch avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **AirWatch** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à AirWatch** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **AirWatch signe d’URL** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application AirWatch (par exemple : «*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à AirWatch** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise AirWatch en tant qu’administrateur.

6.  Dans le volet de navigation de gauche, cliquez sur **comptes**, puis cliquez sur **administrateurs**.

    ![Administrateurs] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Administrateurs")

7.  Développer le menu **paramètres** , puis cliquez sur **Services d’annuaire**.

    ![Paramètres] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Paramètres")

8.  Cliquez sur l’onglet **utilisateur** , de l’objet textfield de **Base DN** , tapez votre nom de domaine, puis cliquez sur **Enregistrer**.

    ![Utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utilisateur")

9.  Cliquez sur l’onglet **serveur** .

    ![Serveur] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Serveur")

10. Effectuez les opérations suivantes :

    ![Télécharger] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Télécharger")

    1.  En tant que **Type de répertoire**, sélectionnez **Aucun**.
    2.  Sélectionnez **utiliser SAML pour l’authentification**.
    3.  Pour télécharger le certificat téléchargé, cliquez sur **Télécharger**.

11. Dans la section **Request** , effectuez les opérations suivantes :

    ![Demande] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Demande")

    1.  **Type de demande de liaison**, sélectionnez **Valider**.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Airwatch** , copier la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **d’Identité unique signe sur URL du fournisseur** .
    3.  En tant que **Format de NameID**, sélectionnez **Adresse E-mail**.
    4.  Cliquez sur **Enregistrer**.

12. Cliquez à nouveau sur l’onglet **utilisateur** .

    ![Utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utilisateur")

13. Dans la section **attributs** , effectuez les opérations suivantes :

    ![Attribut] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attribut")

    1.  Dans la zone **Identificateur d’objet** , tapez **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Dans la zone de texte **nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Dans la zone de texte **Nom complet** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Dans la zone de texte **nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Dans la zone de texte **Nom** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Dans la zone de texte **message** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Cliquez sur **Enregistrer**.

14. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à AirWatch, il doivent être configurés dans AirWatch.  
Dans le cas de AirWatch, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **AirWatch** en tant qu’administrateur.

2.  Dans le volet de navigation sur le côté gauche, cliquez sur **comptes**, puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utilisateurs")

3.  Dans le menu **utilisateurs** , cliquez sur **Affichage de la liste**, puis cliquez sur **Ajouter \> ajouter un utilisateur**.

    ![Ajouter utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Ajouter utilisateur")

4.  Dans la boîte de dialogue **Ajouter / modifier l’utilisateur** , effectuez les opérations suivantes :

    ![Ajouter utilisateur] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Ajouter utilisateur")

    1.  Tapez le **nom d’utilisateur**, **mot de passe**, **Confirmer le mot de passe**, **prénom**, **Nom**, **Adresse de messagerie** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AirWatch utilisateur compte outils de création ou API fournies par AirWatch à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Pour affecter des utilisateurs à AirWatch, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **AirWatch **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
