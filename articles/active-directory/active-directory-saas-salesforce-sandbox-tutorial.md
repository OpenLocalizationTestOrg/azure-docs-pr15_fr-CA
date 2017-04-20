<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une force de vente Sandbox | Microsoft Azure"
    description="Apprenez à utiliser des Sandbox de la force de vente avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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
    ms.date="10/28/2016" 
    ms.author="jeedes" />


#<a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Didacticiel : Intégration d’Azure Active Directory avec Sandbox de la force de vente
>[AZURE.TIP]Pour obtenir des commentaires, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=521878).
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Sandbox de la force de vente.  
Sandbox vous donnent la possibilité de créer plusieurs copies de votre entreprise dans les environnements distincts pour diverses raisons, telles que le développement, de test et de formation, sans compromettre les données et les applications de votre organisation de production de force de vente.  
Pour plus d’informations, consultez [Vue d’ensemble du Sandbox](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)
  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un sandbox dans Salesforce.com
  
Si vous n’avez encore un sandbox valide dans Salesforce.com, vous devez contacter la force de vente.
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour le Sandbox de la force de vente
2.  Configuration de l’authentification unique
3.  L’activation de votre domaine
4.  Configuration d’approvisionnement de l’utilisateur
5.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scénario")
##<a name="enabling-the-application-integration-for-salesforce-sandbox"></a>L’activation de l’intégration de l’application pour le Sandbox de la force de vente
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour le sandbox de la force de vente.

###<a name="to-enable-the-application-integration-for-salesforce-sandbox-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour le sandbox de la force de vente, procédez comme suit :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Bibliothèque de l’Application**, cliquez sur **Ajouter une application**et puis cliquez sur **Ajouter une application de mon entreprise à utiliser**.

    ![Ce que vous voulez faire ?] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Ce que vous voulez faire ?")

5.  Dans la **zone Rechercher**, tapez **Sandbox de la force de vente**.

    ![Galerie des applications] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Galerie des applications")

6.  Dans le volet de résultats, sélectionnez **Sandbox de la force de vente**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Sandbox de la force de vente] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Sandbox de la force de vente")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de la force de vente avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Sandbox de la force de vente** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à des Sandbox de force de vente** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Sandbox de la force de vente] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Sandbox de la force de vente")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant `http://company.my.salesforce.com`, puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "Configurer des URL de l’application")

4. Si vous avez déjà configuré de l’authentification unique pour une autre instance de force de vente Sandbox dans votre répertoire, vous devez également configurer l' **identificateur** pour avoir la même valeur que l' **ouverture de session des URL**. Le champ **d’identificateur** peut être trouvé en cochant la case **Afficher les paramètres avancés** dans la page **Configurer l’application URL** de la boîte de dialogue.

4.  Dans la page **configuration de l’authentification unique au Sandbox de la force de vente** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le sandbox de votre force de vente en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "Programme d’installation")

7.  Dans le volet de navigation de gauche, cliquez sur les **Contrôles de sécurité**, puis cliquez sur **Paramètres d’authentification unique**.

    ![Paramètres d’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Paramètres d’authentification unique")

8.  Dans la section Paramètres d’authentification unique, procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Paramètres d’authentification unique")

    une barre d’outils.  Sélectionnez **SAML activé**.
    
    b.  Cliquez sur **Nouveau**.

9.  Dans la section Paramètres d’authentification unique SAML, effectuez les opérations suivantes :

    ![Paramètres d’authentification unique SAML] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "Paramètres d’authentification unique SAML")

    une barre d’outils.  Dans la zone de texte Nom, tapez le nom de la configuration (par exemple : *SPSSOWAAD\_Test*).
    
    b.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au Sandbox de la force de vente** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    
    c.  Dans la zone de texte **Id de l’entité** , tapez **https://test.salesforce.com** , il s’agit de la première instance de Sandbox de la force de vente que vous ajoutez à votre répertoire. Si vous avez déjà ajouté une instance du Sandbox de la force de vente, puis pour le type **d’ID de l’entité** dans l' **URL de connexion**, qui doit être dans ce format :`http://company.my.salesforce.com`
    
    d.  Cliquez sur **Parcourir** pour charger du certificat téléchargé.
    
    e.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de fédération à partir de l’objet utilisateur**.
    
    f.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité se trouve dans l’élément NameIdentifier de l’instruction de l’objet**.
    
    g.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au Sandbox de la force de vente** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité** .
    
    h.  SFDC ne prend pas en charge SAML déconnexion.  Pour résoudre ce problème, collez 'https://login.windows.net/common/wsfederation?wa=wsignout1.0' dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    
    i.  **Service fournisseur initiée par demande de liaison**, sélectionnez **HTTP POST**.
    
    j. Cliquez sur **Enregistrer**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Configurer l’authentification unique")

##<a name="enabling-your-domain"></a>L’activation de votre domaine
  
Cette section suppose que vous avez déjà créé un domaine.  
Pour plus d’informations, consultez [Définition de votre nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

###<a name="to-enable-your-domain-perform-the-following-steps"></a>Pour activer votre domaine, effectuez les opérations suivantes :

1.  Dans le volet de navigation de gauche, cliquez sur **Gestion de domaine**, puis cliquez sur **mon domaine.**

    ![Mon domaine] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "Mon domaine")

    >[AZURE.NOTE]Veuillez vous assurer que votre domaine a été configuré correctement.

2.  Dans la section **Paramètres de Page de connexion** , cliquez sur **Modifier**, puis, en tant que **Service d’authentification**, le nom SAML seule session du paramètre d’à partir de la section précédente et enfin cliquez sur **Enregistrer**.

    ![Mon domaine] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "Mon domaine")
  
Dès que vous avez un domaine configuré, vos utilisateurs doivent utiliser l’URL de domaine pour se connecter à la sandbox de la force de vente.  
Pour obtenir la valeur de l’URL, cliquez sur le profil d’authentification unique que vous avez créé dans la section précédente.
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
L’objectif de cette section doit décrire comment activer l’approvisionnement de l’utilisateur Active Directory des comptes d’utilisateurs à des Sandbox de force de vente.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Dans le portail de la force de vente, dans la barre de navigation supérieure, sélectionnez votre nom pour développer votre menu utilisateur :

    ![Mes paramètres] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mes paramètres")

2.  Dans le menu utilisateur, sélectionnez **Mes paramètres** pour ouvrir votre page **Mes paramètres** .

3.  Dans le volet gauche, cliquez sur **personnel** pour développer la section personnelle, puis cliquez sur **Réinitialiser mon jeton de sécurité**:

    ![Mes paramètres] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mes paramètres")

4.  Sur la page **Réinitialiser mon jeton de sécurité** , cliquez sur **Réinitialiser le jeton de sécurité** pour demander un e-mail contenant votre code de sécurité de Salesforce.com.

    ![Nouveau jeton] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "Nouveau jeton")

5.  Vérifiez votre boîte de réception d’un message électronique à partir de Salesforce.com avec «**confirmation de sécurité Salesforce.com**» comme sujet.

6.  Consulter cet e-mail et copiez la valeur de jeton de sécurité.

7.  Dans le portail Azure classique, sur la page intégration d’application **salesforce Sandbox** , cliquez sur **configurer approvisionnement de l’utilisateur** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

    ![Configurer configuration des utilisateurs] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "Configurer configuration des utilisateurs")

8.  Dans la page **entrer vos informations d’identification de la force de vente Sandbox pour activer la mise en service automatique de l’utilisateur** , indiquez les paramètres de configuration suivants :

    ![Sandbox de la force de vente] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Sandbox de la force de vente")

    une barre d’outils.  Dans la zone de texte **Nom d’utilisateur Admin Salesforce Sandbox** , tapez un nom de compte de sandbox de force de vente ayant le profil de **l’Administrateur système** dans Salesforce.com affecté.

    b.  Dans la zone de texte **Mot de passe administrateur Salesforce Sandbox** , tapez le mot de passe pour ce compte.

    c.  Dans la zone de texte de **Jeton de sécurité utilisateur** , coller la valeur de jeton de sécurité.

    d.  Cliquez sur **Valider** pour vérifier votre configuration.

    e.  Cliquez sur le bouton **suivant** pour ouvrir la page de **Confirmation** .

9.  Sur la page de **Confirmation** , cliquez sur **Terminer** pour enregistrer votre configuration.
##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-salesforce-sandbox-perform-the-following-steps"></a>Pour affecter des utilisateurs à des Sandbox de la force de vente, procédez comme suit :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Sandbox de la force de vente **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé au Sandbox de la force de vente.
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](https://msdn.microsoft.com/library/dn308586).
