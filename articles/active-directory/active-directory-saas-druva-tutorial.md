<properties 
    pageTitle="Didacticiel : Azure Active Directory integration intégrant Druva | Microsoft Azure" 
    description="Apprenez à utiliser Druva avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-integration-with-druva"></a>Didacticiel : Azure Active Directory integration intégrant Druva

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Druva.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Druva de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Druva pourront ouverture de session unique dans l’application au niveau du site de votre société Druva (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Druva
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-druva-tutorial/IC795084.png "Scénario")
##<a name="enabling-the-application-integration-for-druva"></a>L’activation de l’intégration de l’application pour Druva

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Druva.

###<a name="to-enable-the-application-integration-for-druva-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Druva, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-druva-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-druva-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-druva-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Druva**.

    ![Galerie des applications] (./media/active-directory-saas-druva-tutorial/IC795085.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Druva**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Druva] (./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Druva avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

Votre application Druva attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs de jeton SAML] (./media/active-directory-saas-druva-tutorial/IC795087.png "Attributs de jeton SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Druva** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795027.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Druva** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795088.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Druva signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application Druva (par exemple : «*https://cloud.druva.com/home/*»), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-druva-tutorial/IC795089.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Druva** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795090.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Druva en tant qu’administrateur.

6.  Accédez à **Gérer les \> les paramètres**.

    ![Paramètres] (./media/active-directory-saas-druva-tutorial/IC795091.png "Paramètres")

7.  Dans la boîte de dialogue Paramètres d’authentification unique, procédez comme suit :

    ![Osaïque paramètres d’authentification] (./media/active-directory-saas-druva-tutorial/IC795092.png "Osaïque paramètres d’authentification")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Druva** , copier la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion du fournisseur de code** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Druva** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL du fournisseur de code de déconnexion** .
    3.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte **ID de fournisseur de certificat**
    5.  Pour ouvrir la page **paramètres** , cliquez sur **Enregistrer**.

8.  Dans la page **paramètres** , cliquez sur **Générer le jeton de l’authentification unique**.

    ![Paramètres] (./media/active-directory-saas-druva-tutorial/IC795093.png "Paramètres")

9.  Dans la boîte de dialogue **Ouverture de session d’authentification jeton unique** , effectuez les opérations suivantes :

    ![Jeton d’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795094.png "Jeton d’authentification unique")

    1.  Cliquez sur **Copier**.
    2.  Cliquez sur **Fermer**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-druva-tutorial/IC795095.png "Configurer l’authentification unique")

11. Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-druva-tutorial/IC795096.png "Attributs")

12. Pour ajouter les mappages d’attributs requis, procédez comme suit :

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|synchronisée\_auth\_jeton|<*valeur du Presse-papiers*>|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    3.  Dans la zone de texte de **Valeur de l’attribut** , tapez la valeur d’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

13. Cliquez sur **appliquer les modifications**.
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Druva, il doivent être configurés dans Druva.  
Dans le cas de Druva, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Druva** en tant qu’administrateur.

2.  Accédez à **Gérer les \> les utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795097.png "Gérer les utilisateurs")

3.  Cliquez sur **Créer nouveau**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795098.png "Gérer les utilisateurs")

4.  Dans la boîte de dialogue Créer un nouvel utilisateur, effectuez les opérations suivantes :

    ![Créer NewUser] (./media/active-directory-saas-druva-tutorial/IC795099.png "Créer NewUser")

    1.  Tapez l’adresse de messagerie et le nom d’un compte d’utilisateur Active Directory de Azure valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **créer un utilisateur**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Druva utilisateur compte outils de création ou API fournies par Druva à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-druva-perform-the-following-steps"></a>Pour affecter des utilisateurs à Druva, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Druva **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-druva-tutorial/IC795100.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-druva-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
