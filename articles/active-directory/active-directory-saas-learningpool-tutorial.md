<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Learningpool | Microsoft Azure" 
    description="Apprenez à utiliser Learningpool avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-learningpool"></a>Didacticiel : Intégration d’Azure Active Directory avec Learningpool
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Learningpool.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Learningpool de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Learningpool pourront ouverture de session unique dans l’application à votre site de la société Learningpool (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Learningpool
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scénario")
##<a name="enabling-the-application-integration-for-learningpool"></a>L’activation de l’intégration de l’application pour Learningpool
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Learningpool.

###<a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Learningpool, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-learningpool-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-learningpool-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Learningpool**.

    ![Galerie des applications] (./media/active-directory-saas-learningpool-tutorial/IC795073.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Learningpool**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Learningpool] (./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Learningpool avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.
  
Votre application Learningpool attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** .  
La capture d’écran suivante montre un exemple de cela.

![Attributs de jeton SAML] (./media/active-directory-saas-learningpool-tutorial/IC795074.png "Attributs de jeton SAML")

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page **Learningpool** d’intégration application, dans le menu de la partie supérieure, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** .

    ![Attributs] (./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributs")

2.  Pour ajouter les mappages d’attributs requis, procédez comme suit :

    ###  

  	|Nom de l’attribut                |Valeur de l’attribut            |
  	|------------------------------|---------------------------|

     urn : oid:1.2.840.113556.1.4.221 | User.userPrincipalName
  	|-------------------------------|--------------------------|  
     urn : oid:2.5.4.42|User.GivenName   
  	|urn : oid:0.9.2342.19200300.100.1.3|User.Mail
  	|urn : oid:2.5.4.4|User.Surname

    1.  Pour chaque ligne de données dans le tableau ci-dessus, cliquez sur **Ajouter un attribut de l’utilisateur**.
    2.  Dans la zone de texte **Nom de l’attribut** , tapez le nom d’attribut affiché pour cette ligne.
    3.  Dans la liste **Valeur de l’attribut** , sélectionnez la valeur d’attribut affichée pour cette ligne.
    4.  Cliquez sur **terminé**.

3.  Cliquez sur **appliquer les modifications**.

4.  Dans votre navigateur, cliquez sur **Nouveau** pour ouvrir la boîte de dialogue **Quick Start** à nouveau.

5.  Cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de session unique** .

    ![Configurer Singel de session] (./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configurer Singel de session")

6.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Learningpool** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configurer l’authentification unique")

7.  Sur la page **Configurer l’URL App** , dans la zone de texte **Learningpool signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Learningpool (par exemple : https://parliament.preview.learningpool.com/auth/shibboleth/index.php), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configurer des URL de l’application")

8.  Dans la page **configuration de l’authentification unique à Learningpool** , cliquez sur **télécharger les métadonnées**pour télécharger vos métadonnées et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configurer l’authentification unique")

9.  Transférer ce fichier de métadonnées à votre équipe de Support de Learningpool.

    >[AZURE.NOTE]L’ouverture de session unique doit être activé par l’équipe de prise en charge Learningpool.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Learningpool, il doivent être configurés dans Learningpool.
  
Il n’y a aucun élément d’action pour configurer approvisionnement à Learningpool de l’utilisateur.  
Les utilisateurs doivent être créés par votre équipe de support Learningpool.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Learningpool utilisateur compte outils de création ou API fournies par Learningpool à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>Pour affecter des utilisateurs à Learningpool, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Learningpool **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-learningpool-tutorial/IC795081.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-learningpool-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).