<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec la Console d’administration Mimecast | Microsoft Azure" 
    description="Apprenez à utiliser la Console d’administration Mimecast avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Didacticiel : Intégration de Azure Active Directory avec la Console d’administration Mimecast
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et la Console d’administration Mimecast.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Une Console d’administration de Mimecast de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la Console d’administration Mimecast pourront ouverture de session unique dans l’application au niveau du site de votre société Mimecast Admin Console (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour la Console d’administration Mimecast
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795008.png "Scénario")
##<a name="enabling-the-application-integration-for-mimecast-admin-console"></a>L’activation de l’intégration des applications pour la Console d’administration Mimecast
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application de la Console d’administration Mimecast.

###<a name="to-enable-the-application-integration-for-mimecast-admin-console-perform-the-following-steps"></a>Pour activer l’intégration de l’application de la Console d’administration Mimecast, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Mimecast Console d’administration**.

    ![Galerie des applications] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795009.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez la **Console d’administration de Mimecast**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Console d’administration de Mimecast] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795010.png "Console d’administration de Mimecast")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier à la Console d’administration Mimecast avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Console d’administration de Mimecast** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795011.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la Console d’administration Mimecast** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795012.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Mimecast Admin Console signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Console d’administration Mimecast (par exemple : « https://webmail-uk.mimecast.com » ou « https://webmail-us.mimecast.com »), puis cliquez sur **suivant**.

    >[AZURE.NOTE] Le signe sur l’URL est spécifique de la région.

    ![Configurer des URL de l’application] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795013.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au niveau de la Console d’administration de Mimecast** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795014.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre Console d’administration de Mimecast en tant qu’administrateur.

6.  Accédez à **Services \> Application**.

    ![Services] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794998.png "Services")

7.  Cliquez sur **profils d’authentification**.

    ![Profils d’authentification] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC794999.png "Profils d’authentification")

8.  Cliquez sur **nouveau profil d’authentification**.

    ![Nouveaux profils de l’authentification] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795000.png "Nouveaux profils de l’authentification")

9.  Dans la section **Profil d’authentification** , effectuez les opérations suivantes :

    ![Profil d’authentification] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795015.png "Profil d’authentification")

    1.  Dans la zone de texte **Description** , tapez un nom pour votre configuration.
    2.  Sélectionnez **appliquer l’authentification SAML pour la Console d’administration de Mimecast**.
    3.  En tant que **fournisseur**, sélectionnez **Azure Active Directory**.
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la Console d’administration de Mimecast** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte **URL de l’émetteur** .
    5.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la Console d’administration de Mimecast** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    6.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au niveau de la Console d’administration de Mimecast** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .  

        >[AZURE.NOTE]La valeur de l’URL de connexion et la valeur d’URL de déconnexion sont pour la Console d’administration Mimecast le même.

    7.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    8.  Ouvrir votre certificat de codé en base 64 dans le bloc-notes, supprimez la première ligne («*--*») et la dernière ligne («*--*»), copiez le contenu restant de celui-ci dans votre Presse-papiers et puis la coller dans la zone de texte du **Certificat d’identité du fournisseur (métadonnées)** .
    9.  Sélectionnez **Autoriser l’ouverture de session unique sur**.
    10. Cliquez sur **Enregistrer**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795016.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à la Console d’administration de Mimecast, il doivent être configurés dans la Console d’administration Mimecast.  
Pour la Console d’administration de Mimecast, la mise en service est une tâche manuelle.
  
Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre **Console d’administration de Mimecast** en tant qu’administrateur.

2.  Accédez à **répertoires \> internes**.

    ![Répertoires] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795003.png "Répertoires")

3.  Cliquez sur **Enregistrer le nouveau domaine**.

    ![Enregistrer le nouveau domaine] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795004.png "Enregistrer le nouveau domaine")

4.  Après avoir créé votre nouveau domaine, cliquez sur **Nouvelle adresse**.

    ![Nouvelle adresse] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795005.png "Nouvelle adresse")

5.  Dans la boîte de dialogue nouvelle adresse, effectuez les opérations suivantes :

    ![Enregistrer] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795006.png "Enregistrer")

    1.  Tapez les attributs **d’Adresse de messagerie**, **Nom Global**, **mot de passe** et **Confirmer le mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE]Vous pouvez utiliser d’autres outils de création de compte utilisateur Mimecast Admin Console ni API fournies par la Console d’administration Mimecast aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-mimecast-admin-console-perform-the-following-steps"></a>Pour affecter des utilisateurs à la Console d’administration Mimecast, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Mimecast Admin Console **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC795017.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-mimecast-admin-console-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).