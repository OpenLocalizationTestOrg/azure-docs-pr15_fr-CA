<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - sécurisé | Microsoft Azure"
    description="Découvrez comment utiliser TOPdesk - Secure avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Didacticiel : Intégration d’Azure Active Directory avec TOPdesk - sécurisé
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et TOPdesk - sécurisé.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un TOPdesk - sécurisée de l’authentification unique activée abonnement
  
À la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à TOPdesk - sécurisé pourront à l’ouverture de session unique dans l’application à votre TOPdesk - site de société sécurisés (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour TOPdesk - sécurisé
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scénario")

##<a name="enabling-the-application-integration-for-topdesk---secure"></a>L’activation de l’intégration de l’application pour TOPdesk - sécurisé
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour TOPdesk - sécurisé.

###<a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour TOPdesk - sécurisé, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **TOPdesk - sécurisé**.

    ![Galerie des applications] (./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **TOPdesk - sécurisé**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![TOPdesk - sécurisé] (./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - sécurisé")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier sur TOPdesk - Secure avec leur compte Azure annonce à l’aide de fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour TOPdesk - sécurisé nécessaire de charger un fichier icône. Pour obtenir le fichier icône, contactez l’équipe de support TOPdesk.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - sécurisée** en tant qu’administrateur.

2.  Dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

3.  Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

4.  Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

5.  Dans la section **sécurité** de la section de configuration de **connexion SAML** , effectuez les opérations suivantes :

    ![Paramètres techniques] (./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "Paramètres techniques")

    1.  Cliquez sur **Télécharger** pour télécharger le fichier de métadonnées publiques et enregistrez-le localement sur votre ordinateur.
    2.  Ouvrez le fichier de métadonnées et recherchez le nœud **AssertionConsumerService** .
        ![Service de consommateur d’assertion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Service de consommateur d’assertion")
    3.  Copiez la valeur de **AssertionConsumerService** .  

        >[AZURE.NOTE] Plus loin dans ce didacticiel, vous devez la valeur dans la section **Configurer des URL App** .

6.  Dans une fenêtre de navigateur web différent, ouvrez une session dans votre **Azure portal classique** en tant qu’administrateur.

7.  Sur la page intégration d’application **TOPdesk - sécurisé** , cliquez sur **de configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de session unique** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Configurer l’authentification unique")

8.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à TOPdesk - sécurisé** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Configurer l’authentification unique")

9.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "Configurer des URL de l’application")

    1.  Dans la zone de texte **TOPdesk - Secure sur URL de connexion** , tapez l’URL utilisée par vos utilisateurs pour vous connecter à votre TOPdesk - application sécurisée (par exemple : «*https://qssolutions.topdesk.net*»).
    2.  Dans la zone de texte **TOPdesk – URL de réponse publique** , collez la **TOPdesk - URL de AssertionConsumerService sécurisé** (par exemple : «*https://qssolutions.topdesk.net/tas/public/login/saml*»)
    3.  Cliquez sur **suivant**.

10. Dans la page **configuration de l’authentification unique à TOPdesk - sécurisé** , cliquez sur **télécharger les métadonnées**pour télécharger votre fichier de métadonnées et puis enregistrez le fichier localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Configurer l’authentification unique")

11. Pour créer un fichier de certificat, effectuez les opérations suivantes :

    ![Certificat] (./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "Certificat")

    1.  Ouvrez le fichier de métadonnées téléchargés.
    2.  Développez le nœud de **RoleDescriptor** qui a un **xsi : type** de **chargé : ApplicationServiceType**.
    3.  Copiez la valeur du nœud **X509Certificate** .
    4.  Enregistrer la valeur de **X509Certificate** copiée localement sur votre ordinateur dans un fichier.

12. Dans votre TOPdesk - site sécurisé de société, dans le menu **TOPdesk** , cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "Paramètres")

13. Cliquez sur **paramètres de connexion**.

    ![Paramètres de connexion] (./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "Paramètres de connexion")

14. Développer le menu **Paramètres de connexion** , puis cliquez sur **Général**.

    ![Général] (./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Général")

15. Dans la section **publique** , cliquez sur **Ajouter**.

    ![Ajouter] (./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Ajouter")

16. Sur la page de la boîte de dialogue **assistant de configuration de SAML** , effectuez les opérations suivantes :

    ![Assistant de Configuration SAML] (./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "Assistant de Configuration SAML")

    1.  Pour charger votre fichier de métadonnées téléchargés, sous des **Métadonnées de fédération**, cliquez sur **Parcourir**.
    2.  Pour charger votre fichier de certificat, sous **Certificats (RSA)**, cliquez sur **Parcourir**.
    3.  Pour télécharger le fichier de logo que vous avez obtenu auprès de l’équipe de prise en charge par TOPdesk, sous l' **icône du Logo**, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **d’attribut de nom d’utilisateur** , tapez **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    5.  Dans la zone de texte **nom complet** , tapez un nom pour votre configuration.
    6.  Cliquez sur **Enregistrer**.

17. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour permettre aux utilisateurs d’AD Azure pour vous connecter à TOPdesk - sécurisé, elles doivent être configurés dans TOPdesk - sécurisé.  
Dans le cas de TOPdesk - sécurisée, mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à votre site d’entreprise **TOPdesk - sécurisée** en tant qu’administrateur.

2.  Dans le menu du haut, cliquez sur **TOPdesk \> nouveau \> prise en charge de fichiers \> opérateur**.

    ![Opérateur] (./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Opérateur")

3.  Dans la boîte de dialogue **Nouvel opérateur** , effectuez les opérations suivantes :

    ![Opérateur new] (./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "Opérateur new")

    1.  Cliquez sur l’onglet Général.
    2.  Dans la zone de texte **nom** de la section **Général** , tapez le nom d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    3.  Dans la section **emplacement** , sélectionnez un **Site** pour le compte.
    4.  Dans la zone de texte **Nom de connexion** de la section **Connexion de TOPdesk** , tapez un nom de connexion de l’utilisateur.
    5.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autre TOPdesk - outils de création de compte utilisateur sécurisé ou des API fournies par TOPdesk - sécurisé pour dimensionner les comptes d’utilisateur de DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Pour affecter des utilisateurs à TOPdesk - sécurisé, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **TOPdesk - sécurisé **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).