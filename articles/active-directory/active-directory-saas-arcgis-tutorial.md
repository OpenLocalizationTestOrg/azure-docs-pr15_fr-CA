<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec ArcGIS | Microsoft Azure" 
    description="Apprenez à utiliser ArcGIS avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Didacticiel : Intégration de Azure Active Directory avec ArcGIS

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et ArcGIS. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un ArcGIS de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à ArcGIS pourront ouverture de session unique dans l’application au niveau du site de votre société ArcGIS (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour ArcGIS
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scénario")
##<a name="enabling-the-application-integration-for-arcgis"></a>L’activation de l’intégration de l’application pour ArcGIS

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour ArcGIS.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour ArcGIS, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **ArcGIS**.

    ![Bibliothèque d’Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Bibliothèque d’Applcation")

7.  Dans le volet de résultats, sélectionnez **ArcGIS**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de ArcGIS avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **ArcGIS** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ArcGIS** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **ArcGIS URL de connexion** , saisissez l’URL utilisées par vos utilisateurs à ouvrir une session en utilisant le modèle suivant «*https://company.maps.arcgis.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à ArcGIS** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise ArcGIS en tant qu’administrateur.

6.  Cliquez sur **Modifier les paramètres**.

    ![Modifier les paramètres] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Modifier les paramètres")

7.  Cliquez sur **sécurité**.

    ![Sécurité] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sécurité")

8.  Sous **Connexions de l’entreprise**, cliquez sur le **Fournisseur d’identité définie**.

    ![Connexions de l’entreprise] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Connexions de l’entreprise")

9.  Sur la page de configuration du **Fournisseur d’identité définie** , effectuez les opérations suivantes :

    ![Définir le fournisseur d’identité] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Définir le fournisseur d’identité")

    1.  Dans la zone de texte Nom, tapez le nom de votre organisation.
    2.  Pour les **que métadonnées pour le fournisseur d’identité entreprise seront fournies à l’aide de**, sélectionnez **Un fichier**.
    3.  Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Choisir un fichier**.
    4.  Cliquez sur **définir le fournisseur d’identité**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Pour permettre aux utilisateurs d’AD Azure pour vous connecter à ArcGIS, il doivent être configurés dans ArcGIS.  
Dans le cas de ArcGIS, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **ArcGIS** .

2.  Cliquez sur **Inviter des membres**.

    ![Inviter des membres] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Inviter des membres")

3.  Sélectionnez **Ajouter de membres automatiquement sans envoi d’un e-mail**, puis cliquez sur **suivant**.

    ![Ajouter automatiquement des membres] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Ajouter automatiquement des membres")

4.  Sur la page de la boîte de dialogue **des membres** , procédez comme suit :

    ![Ajout et révision] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Ajout et révision")

    1.  Entrez le **prénom**, le **Nom** et **courrier électronique** d’un compte DAS valide que vous souhaitez à la disposition.
    2.  Cliquez sur **Ajouter et passer en revue**.

5.  Vérifiez les données que vous avez saisi, puis cliquez sur **Ajouter des membres**.

    ![Ajouter un membre] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Ajouter un membre")

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres ArcGIS utilisateur compte outils de création ou API fournies par ArcGIS à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Pour affecter des utilisateurs à ArcGIS, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **ArcGIS **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
