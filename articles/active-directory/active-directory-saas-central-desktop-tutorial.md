<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le bureau Central | Microsoft Azure" 
    description="Apprenez à utiliser le bureau Central avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Didacticiel : Intégration d’Azure Active Directory avec le bureau Central

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et bureau Central. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Authentification unique Bureau Central sur abonnement activé / client de bureau Central

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications pour ordinateur de bureau Central
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scénario")
##<a name="enabling-the-application-integration-for-central-desktop"></a>L’activation de l’intégration des applications pour ordinateur de bureau Central

L’objectif de cette section doit décrire comment activer l’intégration des applications pour ordinateur de bureau Central.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Pour activer l’intégration des applications pour ordinateur de bureau Central, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Bureau Central**.

    ![Galerie des applications] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez **Bureau Central**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Bureau central] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Bureau central")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de bureau Central avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 à vos clients de bureau Central.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Bureau Central** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à un bureau Central** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes, puis cliquez sur **suivant**: 

    -   Dans la zone de texte **URL centrale de bureau signe dans** , tapez l’URL de votre client de bureau Central (par exemple : *http://contoso.centraldesktop.com*).
    -   Dans la zone de texte URL de réponse bureau Central, tapez l’URL de votre AssertionConsumerService bureau Central (par exemple : https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] Vous pouvez obtenir la valeur à partir des métadonnées bureau centrale (par exemple : *http://contoso.centraldesktop.com*).

    ![Configurer des URL de l’application] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au bureau Central** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "De configurer l’authentification unique")

5.  Connectez-vous à vos clients de **Bureau Central** .

6.  Allez dans **paramètres**, cliquez sur **Avancé**, puis cliquez sur **Single Sign On**.

    ![Le programme d’installation - Configuration avancée] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Le programme d’installation - Configuration avancée")

7.  Dans la page **Connexion unique sur les paramètres** , procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Paramètres d’authentification unique")

    1.  Sélectionnez **Activer SAML v2 d’authentification unique**.
    2.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte **URL de l’authentification unique** .
    3.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion de l’authentification unique** .
    4.  Dans le portail Azure classique, dans la page **configuration de l’authentification unique au bureau Central** , copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de déconnexion de l’authentification unique** .

8.  Dans la section **Méthode de vérification de Signature de Message** , effectuez les opérations suivantes :

    ![Méthode de vérification de Signature de message] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Méthode de vérification de Signature de message")

    1.  Sélectionnez un **certificat**.
    2.  Dans la liste de **Certificats de l’authentification unique** , sélectionnez **RSH SHA256**.
    3.  Créez un fichier texte à partir du certificat téléchargé et copiez le contenu du fichier texte puis la coller dans le champ de **Certificat d’authentification unique** .  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    4.  Sélectionnez **Afficher un lien vers votre page de connexion SAMLv2**.

9.  Cliquez sur **mise à jour**.

10. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

DAS aux utilisateurs d’être en mesure de vous connecter, il doivent être configurés pour l’application de bureau Central. Cette section décrit comment créer des comptes d’utilisateur DAS dans un bureau Central.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Pour configurer des comptes d’utilisateurs au bureau Central :

1.  Connectez-vous à vos clients de bureau Central.

2.  Accédez à **personnes \> membres internes**.

3.  Cliquez sur **Ajouter des membres internes**.

    ![Personnes] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Personnes")

4.  Dans la zone de texte **Adresse de messagerie de nouveaux membres** , tapez un compte DAS, vous souhaitez mettre en service, puis cliquez sur **suivant**.

    ![Adresses e-mail de nouveaux membres] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Adresses e-mail de nouveaux membres")

5.  Cliquez sur **Ajouter internes ou les membres**.

    ![Ajouter un membre interne] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Ajouter un membre interne")

    >[AZURE.NOTE] Les utilisateurs que vous avez ajouté recevra un e-mail contenant un lien de confirmation qu'ils devront cliquer pour activer le compte.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres bureau Central utilisateur compte outils de création ou d’API fournies par le bureau Central aux comptes d’utilisateurs de disposition DAS

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Pour affecter des utilisateurs au bureau Central, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Bureau Central** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
