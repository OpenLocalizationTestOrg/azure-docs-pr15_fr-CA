<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec Kintone | Microsoft Azure" 
    description="Apprenez à utiliser Kintone avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/01/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-kintone"></a>Didacticiel : Intégration de Azure Active Directory avec Kintone
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Kintone.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Kintone de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Kintone pourront ouverture de session unique dans l’application à votre site de la société Kintone (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Kintone
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-kintone-tutorial/IC785859.png "Scénario")
##<a name="enabling-the-application-integration-for-kintone"></a>L’activation de l’intégration de l’application pour Kintone
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Kintone.

###<a name="to-enable-the-application-integration-for-kintone-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Kintone, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-kintone-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-kintone-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Kintone**.

    ![Galerie des applications] (./media/active-directory-saas-kintone-tutorial/IC785867.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Kintone**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Kintone] (./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Kintone avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Kintone** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785872.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Kintone** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785873.png "Configurer l’authentification unique")

3.  Sur la page **Configurer l’URL App** , dans la zone de texte **Kintone signe d’URL** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.kintone.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-kintone-tutorial/IC785875.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Kintone** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785878.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise **Kintone** en tant qu’administrateur.

6.  Cliquez sur **paramètres**.

    ![Paramètres] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

7.  Cliquez sur **les utilisateurs et l’Administration système**.

    ![Les utilisateurs et l’Administration système] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Les utilisateurs et l’Administration système")

8.  Sous **Administration système \> sécurité** cliquez sur **connexion**.

    ![Ouverture de session] (./media/active-directory-saas-kintone-tutorial/IC785881.png "Ouverture de session")

9.  Cliquez sur **Activer le SAML authentification**.

    ![Authentification SAML] (./media/active-directory-saas-kintone-tutorial/IC785882.png "Authentification SAML")

10. Dans la section authentification de SAML, effectuez les opérations suivantes :

    ![Authentification SAML] (./media/active-directory-saas-kintone-tutorial/IC785883.png "Authentification SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Kintone** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Kintone** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion** .
    3.  Cliquez sur **Parcourir** pour charger votre certificat téléchargé.
    4.  Cliquez sur **Enregistrer**.

11. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-kintone-tutorial/IC785884.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Kintone, il doivent être configurés dans Kintone.  
Dans le cas de Kintone, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Kintone** en tant qu’administrateur.

2.  Cliquez sur **paramètre**.

    ![Paramètres] (./media/active-directory-saas-kintone-tutorial/IC785879.png "Paramètres")

3.  Cliquez sur **les utilisateurs et l’Administration système**.

    ![Administration système & utilisateur] (./media/active-directory-saas-kintone-tutorial/IC785880.png "Administration système & utilisateur")

4.  **Administration des utilisateurs**, cliquez sur **les utilisateurs et les départements**.

    ![Service & utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785888.png "Service & utilisateurs")

5.  Cliquez sur **nouvel utilisateur**.

    ![Nouveaux utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785889.png "Nouveaux utilisateurs")

6.  Dans la section **Utilisateur** , effectuez les opérations suivantes :

    ![Nouveaux utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785890.png "Nouveaux utilisateurs")

    1.  Tapez un **Nom complet**, **Nom de connexion**, **Nouveau mot de passe**, **Confirmer le mot de passe**, **Adresse de messagerie** et autres détails d’un compte DAS valide que vous souhaitez mettre en service dans la texboxes connexes.
    2.  Cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Kintone utilisateur compte outils de création ou API fournies par Kintone à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-kintone-perform-the-following-steps"></a>Pour affecter des utilisateurs à Kintone, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Kintone **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-kintone-tutorial/IC785891.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-kintone-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).