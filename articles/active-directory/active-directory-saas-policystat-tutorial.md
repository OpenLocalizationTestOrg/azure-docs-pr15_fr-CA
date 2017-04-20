<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec PolicyStat | Microsoft Azure" 
    description="Apprenez à utiliser PolicyStat avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-policystat"></a>Didacticiel : Intégration de Azure Active Directory avec PolicyStat
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et PolicyStat.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire PolicyStat
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à PolicyStat pourront ouverture de session unique dans l’application à votre site de la société PolicyStat (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour PolicyStat
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-policystat-tutorial/IC808662.png "Scénario")
##<a name="enabling-the-application-integration-for-policystat"></a>L’activation de l’intégration de l’application pour PolicyStat
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour PolicyStat.

###<a name="to-enable-the-application-integration-for-policystat-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour PolicyStat, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-policystat-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-policystat-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **PolicyStat**.

    ![Galerie des applications] (./media/active-directory-saas-policystat-tutorial/IC808627.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **PolicyStat**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![PolicyStat] (./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de PolicyStat avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Votre application PolicyStat attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs] (./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **PolicyStat** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808629.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à PolicyStat** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808630.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application de PolicyStat de l’URL (par exemple : *« https://demo-azure.policystat.com »*), puis cliquez sur **suivant**.

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-policystat-tutorial/IC808631.png "Configurer les paramètres de l’application")

4.  Dans la page **configuration de l’authentification unique à PolicyStat** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC808632.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise PolicyStat en tant qu’administrateur.

6.  Cliquez sur l’onglet **Admin** , puis cliquez sur **Configuration d’ouverture de session unique** dans le volet de navigation gauche.

    ![Menu de l’administrateur] (./media/active-directory-saas-policystat-tutorial/IC808633.png "Menu de l’administrateur")

7.  Dans la section de **configuration** , sélectionnez **Activer la seule intégration de connexion**.

    ![Configuration d’ouverture de session unique] (./media/active-directory-saas-policystat-tutorial/IC808634.png "Configuration d’ouverture de session unique")

8.  Cliquez sur **Configurer les attributs**et ensuite, dans la section **Configurer les attributs** , procédez comme suit :

    ![Configuration d’ouverture de session unique] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration d’ouverture de session unique")

    1.  Dans la zone de texte **d’Attribut de nom d’utilisateur** , tapez **uid**.
    2.  Dans la zone de texte **d’Attribut prénom** , tapez le **prénom**.
    3.  Dans la zone de texte **Dernier attribut de nom** , tapez le **nom**.
    4.  Dans la zone de texte **d’Attribut Email** , tapez **emailaddress**.
    5.  Cliquez sur **Enregistrer les modifications**.

9.  Cliquez sur **Votre IDP métadonnées**et puis, dans la section **Des métadonnées IDP votre** , effectuez les opérations suivantes :

    ![Configuration d’ouverture de session unique] (./media/active-directory-saas-policystat-tutorial/IC808635.png "Configuration d’ouverture de session unique")

    1.  Ouvrez votre fichier de métadonnées téléchargé, copier le contenu et puis la coller dans la zone de texte de **Métadonnées de fournisseur de votre identité**
    2.  Cliquez sur **Enregistrer les modifications**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-policystat-tutorial/IC771723.png "Configurer l’authentification unique")

11. 12. Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributs")

13. Pour ajouter les mappages d’attributs requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributs")

    1.  Cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez **uid**.
    3.  Dans la zone de texte de **Valeur de l’attribut** , sélectionnez **ExtractMailPrefix()**.
    4.  Dans la liste de **publipostage** , sélectionnez **User.mail**.
    5.  Cliquez sur **terminé**.
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à PolicyStat, il doivent être configurés dans PolicyStat.  
PolicyStat prend en charge au moment de l’approvisionnement de l’utilisateur. Cela signifie, vous n’avez pas besoin ajouter manuellement des utilisateurs à PolicyStat.  
Les utilisateurs seront ajoutés automatiquement lors de leur première connexion via l’authentification unique sur.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres PolicyStat utilisateur compte outils de création ou API fournies par PolicyStat à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-policystat-perform-the-following-steps"></a>Pour affecter des utilisateurs à PolicyStat, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **PolicyStat **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-policystat-tutorial/IC808636.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-policystat-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).