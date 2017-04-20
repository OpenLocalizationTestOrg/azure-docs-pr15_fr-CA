<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory boîte | Microsoft Azure" 
    description="Découvrez comment utiliser zone avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Didacticiel : Intégration d’Azure Active Directory boîte


  
L’objectif de ce didacticiel doit afficher l’intégration d’Azure et de zone.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client test dans la zone
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à la zone pourront à l’ouverture de session unique dans l’application au niveau du site de votre société boîte (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour la zone
2.  Configuration de l’authentification unique
3.  Configuration utilisateur et configuration de groupe
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-box-tutorial/IC769537.png "Scénario")



##<a name="enabling-the-application-integration-for-box"></a>L’activation de l’intégration de l’application pour la zone
  
L’objectif de cette section est à expliquent comment activer l’intégration de l’application pour la zone.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour la zone, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-box-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-box-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **boîte**.

    ![Galerie des applications] (./media/active-directory-saas-box-tutorial/IC701023.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez la **zone**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Zone] (./media/active-directory-saas-box-tutorial/IC701024.png "Zone")



##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier à zone avec leur compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML. Dans le cadre de cette procédure, vous devez télécharger les métadonnées à Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **zone** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769538.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la zone** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769539.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de clients de zone** , tapez l’URL de votre zone cliente (par exemple : https://<mydomainname>. box.com), puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au niveau de la zone** , pour télécharger vos métadonnées, cliquez sur **télécharger les métadonnées**, le fichier de données local sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC669824.png "De configurer l’authentification unique")

5.  Transférer ce fichier de métadonnées à la zone de l’équipe de support. L’équipe de support a configure de l’authentification unique pour vous.

6.  Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configuration de session unique** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-box-tutorial/IC769540.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
L’objectif de cette section est à expliquent comment activer la mise en service de comptes d’utilisateurs Active Directory pour la zone.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1. Dans le portail Azure classique, sur la page intégration d’application **zone** , cliquez sur **configurer approvisionnement de l’utilisateur** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** . 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769541.png "Activer la mise en service automatique de l’utilisateur")

2. Dans la boîte de dialogue **Activer l’approvisionnement à la zone de l’utilisateur** , cliquez sur **Activer l’approvisionnement de l’utilisateur**. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769544.png "Activer la mise en service automatique de l’utilisateur")

3. Dans la page **connexion à accorder l’accès à la boîte** , fournir les informations d’identification requises, puis cliquez sur **Autoriser**. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769546.png "Activer la mise en service automatique de l’utilisateur")


4. Cliquez sur **accorder l’accès à la zone** pour autoriser cette opération et revenir à l’Azure portal classique. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769549.png "Activer la mise en service automatique de l’utilisateur")


5. Dans la page **Options de mise en service** , les cases à cocher des **types d’objet à disposition** permettent de choisir ou non des objets de groupe sont mis en service pour la zone en plus des objets de l’utilisateur.  Pour plus d’informations, consultez « Affectant les utilisateurs et les groupes de section » ci-dessous.


6. Pour terminer la configuration, cliquez sur le bouton terminé. 

    ![Activer la mise en service automatique de l’utilisateur] (./media/active-directory-saas-box-tutorial/IC769551.png "Activer la mise en service automatique de l’utilisateur")



##<a name="assigning-a-test-user"></a>Affectation d’un utilisateur test
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Pour affecter des utilisateurs à la zone, effectuez les opérations suivantes :

1. Dans le portail Azure classique, créez un compte de test.

2. La **zone **application integration page, cliquez sur **affecter des utilisateurs**. 

    ![Affecter des utilisateurs] (./media/active-directory-saas-box-tutorial/IC769552.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation. 

    ![Oui] (./media/active-directory-saas-box-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé pour la zone.

Dans un premier temps de vérification, vous pouvez vérifier le statut de l’approvisionnement, en cliquant sur le tableau de bord dans la D sur la page d’intégration d’application zone sur Azure portal classique.

![Tableau de bord] (./media/active-directory-saas-box-tutorial/IC769553.png "Tableau de bord")

Un cycle de configuration d’utilisateur terminé est indiqué par un statut liés :

![Statut d’intégration] (./media/active-directory-saas-box-tutorial/IC769555.png "Statut d’intégration")


Dans les clients de votre zone, les utilisateurs synchronisés sont répertoriés sous **Gérer les utilisateurs** dans la **Console d’administration**.

![Statut d’intégration] (./media/active-directory-saas-box-tutorial/IC769556.png "Statut d’intégration")


##<a name="assigning-users-and-groups"></a>Affectation d’utilisateurs et groupes

La **zone > les utilisateurs et les groupes** onglet dans Azure portal classique vous permet de spécifier quels utilisateurs et groupes doivent avoir accès à la boîte. Affectation d’un utilisateur ou un groupe entraîne les actions suivantes se produise :

* AD Azure permet à l’utilisateur affecté (soit par affectation directe ou l’appartenance au groupe) pour authentifier à la zone. Si un utilisateur n’est pas affecté, Azure AD ne permettra pas à vous connecter à la boîte et renverra une erreur sur la page de connexion AD Azure.

* Une mosaïque de l’application pour la zone est ajoutée à de l’utilisateur [Lanceur d’applications](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Si la mise en service automatique est activée, les utilisateurs et/ou les groupes sont ajoutés à la file d’attente de mise en service pour être configuré automatiquement.

    * Si seuls les objets utilisateur ont été configurés pour être mis en service, alors tous les utilisateurs directement affectées sont placés dans la file d’attente de mise en service, et tous les utilisateurs qui sont membres des groupes affectés seront placés dans la file d’attente de mise en service. 
    
    * Si les objets de groupe ont été configurés pour être mis en service, tous les objets de groupe affecté sont mis en service pour la zone, ainsi que tous les utilisateurs qui sont membres de ces groupes. L’appartenance à un groupe et d’utilisateur est conservés lors de sont écrites dans la zone.
    
Vous pouvez utiliser la **attributs > Single Sign-On** onglet pour configurer les attributs de l’utilisateur (ou demandes) sont présentées à la zone au cours de l’authentification basée sur les SAML et le **attributs > Provisioning** onglet pour configurer les attributs d’utilisateur et de groupe flux des depuis Active Directory Azure à la zone au cours de la mise en service des opérations. Consultez les ressources ci-dessous pour plus d’informations.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Personnalisation des affirmations émises dans le jeton SAML](active-directory-saml-claims-customization.md)
* [Mise en service : Personnaliser les mappages d’attributs](active-directory-saas-customizing-attribute-mappings.md)
* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
