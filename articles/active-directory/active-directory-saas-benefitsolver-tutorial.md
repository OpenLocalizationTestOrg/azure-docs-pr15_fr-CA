<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Benefitsolver | Microsoft Azure"
    description="Apprenez à utiliser Benefitsolver avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>Didacticiel : Intégration d’Azure Active Directory avec Benefitsolver

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Benefitsolver.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Benefitsolver de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Benefitsolver pourront ouverture de session unique dans l’application à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Benefitsolver
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scénario")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>L’activation de l’intégration de l’application pour Benefitsolver

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Benefitsolver.

###<a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Benefitsolver, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Benefitsolver**.

    ![Galerie des applications] (./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Benefitsolver**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Benefitssolver] (./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Benefitsolver avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Votre application Benefitsolver attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **Benefitsolver** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Benefitsolver** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer les paramètres de l’application] (./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configurer les paramètres de l’application")

    1.  Dans la zone de texte **URL de connexion** , tapez **http://azure.benefitsolver.com**.
    2.  Dans la zone de texte **URL de réponse** , tapez **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**.  


    3.  Cliquez sur **suivant**.

4.  Dans la page **configuration de l’authentification unique à Benefitsolver** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configurer l’authentification unique")

5.  Envoyer le fichier de métadonnées téléchargé à votre équipe de support Benefitsolver.

    >[AZURE.NOTE] Votre équipe de support de Benefitsolver est la véritable configuration de l’authentification unique.
Vous recevez une notification lors de l’authentification unique a été activée pour votre abonnement.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configurer l’authentification unique")

7.  Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributs")

8.  Pour ajouter les mappages d’attributs requis, procédez comme suit :

    ![Attributs] (./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributs")

  	|Nom de l’attribut|Valeur de l’attribut|
  	|---|---|
  	|ClientID|Vous devez obtenir cette valeur à partir de votre équipe de support Benefitsolver.|
  	|ClientKey|Vous devez obtenir cette valeur à partir de votre équipe de support Benefitsolver.|
  	|LogoutURL|Vous devez obtenir cette valeur à partir de votre équipe de support Benefitsolver.|
  	|N° employé|Vous devez obtenir cette valeur à partir de votre équipe de support Benefitsolver.|

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    3.  Dans la zone de texte de **Valeur de l’attribut** , sélectionnez la valeur d’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

9.  Cliquez sur **appliquer les modifications**.

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Benefitsolver, il doivent être configurés dans Benefitsolver.  
Dans le cas de Benefitsolver, les données sur les employés sont dans votre application remplie via un fichier de recensement de votre système de ressources humaines (en général quotidiennement).  

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Benefitsolver utilisateur compte outils de création ou API fournies par Benefitsolver à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>Pour affecter des utilisateurs à Benefitsolver, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Benefitsolver **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
