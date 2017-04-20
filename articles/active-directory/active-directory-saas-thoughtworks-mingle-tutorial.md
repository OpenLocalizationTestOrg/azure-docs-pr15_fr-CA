<properties 
    pageTitle="Didacticiel : Azure Active Directory intégrant Thoughtworks passion | Microsoft Azure" 
    description="Découvrez comment utiliser le Thoughtworks passion avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Didacticiel : Azure Active Directory intégrant Thoughtworks passion
  
L’objectif de ce didacticiel doit afficher l’intégration d’Azure et Thoughtworks leur passion.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire Thoughtworks passion
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour leur passion de Thoughtworks
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scénario")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>L’activation de l’intégration des applications pour leur passion de Thoughtworks
  
L’objectif de cette section est à expliquent comment activer l’intégration de l’application pour Thoughtworks passion.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Thoughtworks passion, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **thoughtworks passion**.

    ![Galerie des applications] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Thoughtworks passion**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Association de ThoughtWorks] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Association de ThoughtWorks")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès Thoughtworks rencontrer avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat pour discuter de Thoughtworks.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page **Thoughtworks passion **d’intégration d’application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à signer Thoughtworks rencontrer** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de clients Thoughtworks leur passion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.mingle.thoughtworks.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Thoughtworks passion** , cliquez sur les métadonnées de téléchargement et puis de l’enregistrer sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "De configurer l’authentification unique")

5.  Connectez-vous au site de votre société **Thoughtworks passion** en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin** , puis cliquez sur **Configuration de l’authentification unique**.

    ![Configuration de l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configuration de l’authentification unique")

7.  Dans la section **Configuration de l’authentification unique** , procédez comme suit :

    ![Configuration de l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configuration de l’authentification unique")

    1.  Pour télécharger le fichier de métadonnées, cliquez sur **Choisir un fichier**.
    2.  Cliquez sur **Enregistrer les modifications**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour les DAS pour permettre aux utilisateurs de se connecter, ils doivent être configurés à l’application Thoughtworks passion, à l’aide de leurs noms d’utilisateur Azure Active Directory.  
Dans le cas de la Thoughtworks passion, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre société Thoughtworks passion en tant qu’administrateur.

2.  Cliquez sur **profil**.

    ![Votre premier projet] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Votre premier projet")

3.  Cliquez sur l’onglet **Admin** , puis cliquez sur **utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Nouvel utilisateur] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "Nouvel utilisateur")

5.  Sur la page de la boîte de dialogue **Nouvel utilisateur** , effectuez les opérations suivantes :

    ![Nouvel utilisateur] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "Nouvel utilisateur")

    1.  Tapez le **nom d’utilisateur** **nom d’affichage**, **Choisissez le mot de passe**, **Confirmer le mot de passe** d’un compte DAS valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  En tant que **type d’utilisateur**, sélectionnez **utilisateur complet**.
    3.  Cliquez sur **créer ce profil**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Thoughtworks passion utilisateur compte outils de création ou d’API fournies par Thoughtworks passion aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Pour affecter des utilisateurs à Thoughtworks passion, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page **Thoughtworks passion** d’intégration d’application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
