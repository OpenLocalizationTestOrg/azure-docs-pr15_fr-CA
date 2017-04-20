<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec LogicMonitor | Microsoft Azure" 
    description="Apprenez à utiliser LogicMonitor avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>Didacticiel : Intégration d’Azure Active Directory avec LogicMonitor
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et LogicMonitor.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire LogicMonitor
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour LogicMonitor
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scénario")
##<a name="enabling-the-application-integration-for-logicmonitor"></a>L’activation de l’intégration de l’application pour LogicMonitor
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour LogicMonitor.

###<a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour LogicMonitor, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **logicmonitor**.

    ![Galerie des applications] (./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **LogicMonitor**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![LogicMonitor] (./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de LogicMonitor avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **LogicMonitor **application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à LogicMonitor** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à LogicMonitor \(e, g, : «*http://company.logicmonitor.com*»\), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à LogicMonitor** , cliquez sur **télécharger les métadonnées**et puis de l’enregistrer sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configurer l’authentification unique")

5.  Connectez-vous au site de votre entreprise **LogicMonitor** en tant qu’administrateur.

6.  Dans le menu du haut, cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Paramètres")

7.  Dans bat de la navigation sur le côté gauche, cliquez sur **Single Sign On**

    ![L’ouverture de session unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "L’ouverture de session unique")

8.  Dans la section **paramètres de Single Sign-on (SSO)** , effectuez les opérations suivantes :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Paramètres d’authentification unique")

    1.  Sélectionnez **Activer l’ouverture de session unique**.
    2.  **Affectation de rôle par défaut**, sélectionnez **en lecture seule**.
    3.  Ouvrez le fichier de métadonnées téléchargés dans le bloc-notes et coller le contenu du fichier dans la zone de texte de **Métadonnées de fournisseur d’identité** .
    4.  Cliquez sur **Enregistrer les modifications**.

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
DAS aux utilisateurs d’être en mesure de se connecter, ils doivent être configurés pour l’application de LogicMonitor à l’aide de leurs noms d’utilisateur Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise LogicMonitor en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **paramètres**, puis cliquez sur **les utilisateurs et les rôles**.

    ![Les utilisateurs et les rôles] (./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Les utilisateurs et les rôles")

3.  Cliquez sur **Ajouter**.

4.  Dans la section **Ajouter un compte** , procédez comme suit :

    ![Ajouter un compte] (./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Ajouter un compte")

    1.  Tapez les valeurs de **nom d’utilisateur**, **l’E-mail**, le **mot de passe** et **Retapez le mot de passe** de l’utilisateur d’Azure Active Directory que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Sélectionnez les **rôles**, **Afficher les autorisations** et le **statut**.
    3.  Cliquez sur **Envoyer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres LogicMonitor utilisateur compte outils de création ou d’API fournies par LogicMonitor à disposition Azure Active Directory les comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>Pour affecter des utilisateurs à LogicMonitor, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **LogicMonitor** application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).




