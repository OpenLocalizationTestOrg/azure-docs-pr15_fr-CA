<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Cherwell | Microsoft Azure" 
    description="Apprenez à utiliser Cherwell avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Didacticiel : Intégration d’Azure Active Directory avec Cherwell

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Cherwell. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Cherwell de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Cherwell pourront ouverture de session unique dans l’application au niveau du site de votre société Cherwell (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Cherwell
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scénario")
##<a name="enabling-the-application-integration-for-cherwell"></a>L’activation de l’intégration de l’application pour Cherwell

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Cherwell.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Cherwell, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Dans le volet de résultats, sélectionnez **Cherwell**, puis cliquez sur **Terminer** pour ajouter l’application.
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Cherwell avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Cherwell** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Cherwell** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurer des URL de l’application")

    une barre d’outils.  Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à ouvrir une session sur votre **Cherwell** (par exemple : *https://\<nom de la société\>.cherwellondemand.com/cherwellclient*).

    b.  Cliquez sur **suivant**

4.  Dans la page **configuration de l’authentification unique à Cherwell** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurer l’authentification unique")

    une barre d’outils.  Cliquez sur **Télécharger le certificat**et enregistrez le certificat localement sur votre ordinateur.

    b.  Copiez l' **URL du fournisseur d’identité**.

    c.  Copiez l' **URL du Service d’authentification unique**.

    d.  Cliquez sur **suivant**.

5.  Envoyer le certificat téléchargé, l' **URL du fournisseur d’identité** et l' **URL de Service unique de session** à votre équipe de support Cherwell.

    >[AZURE.NOTE] Votre équipe de support de Cherwell est la véritable configuration de l’authentification unique.
Vous recevez une notification lors de l’authentification unique a été activée pour votre abonnement.

6.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Cherwell, il doivent être configurés dans Cherwell.  
Dans le cas de Cherwell, les comptes d’utilisateur doivent être créés par votre équipe de support Cherwell.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Cherwell utilisateur compte outils de création ou d’API fournies par Cherwell à disposition Azure Active Directory les comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Pour affecter des utilisateurs à Cherwell, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Cherwell** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
