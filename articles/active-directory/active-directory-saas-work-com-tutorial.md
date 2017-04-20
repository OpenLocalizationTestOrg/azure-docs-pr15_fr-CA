<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Work.com | Microsoft Azure" 
    description="Apprenez à utiliser Work.com avec Azure Active Directory pour activer l’ouverture de session unique, automatisée mise en service et bien plus encore !." 
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

#<a name="tutorial-azure-active-directory-integration-with-workcom"></a>Didacticiel : Intégration d’Azure Active Directory avec Work.com
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Work.com.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un Work.com de l’authentification unique activée abonnement
  
Après la fin de ce didacticiel, les utilisateurs de DAS à qui vous avez affecter Work.com accès sera en mesure de même signe dans l’application à votre site de la société Work.com (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Work.com
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-work-com-tutorial/IC794105.png "Scénario")

##<a name="enabling-the-application-integration-for-workcom"></a>L’activation de l’intégration de l’application pour Work.com
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Work.com.

###<a name="to-enable-the-application-integration-for-workcom-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Work.com, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-work-com-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-work-com-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-work-com-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-work-com-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Work.com**.

    ![Galerie des applications] (./media/active-directory-saas-work-com-tutorial/IC794106.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Work.com**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Work.com] (./media/active-directory-saas-work-com-tutorial/IC794107.png "Work.com")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Work.com avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat pour Work.com.com.

>[AZURE.NOTE] Pour configurer l’authentification unique, vous devez configurer un nom de domaine personnalisé Work.com encore. Vous devez définir au moins un nom de domaine, le nom de votre domaine de test, et le déployer dans toute l’organisation.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous à vos clients de Work.com en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

3.  Dans le volet de navigation de gauche, dans la section **administrer** , cliquez sur **Gestion de domaine** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-work-com-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été correctement configuré, assurez-vous qu’il s’agit de le «**Étape 4 déployée vers des utilisateurs**» et passez en revue votre «**Mes paramètres de domaine**».

    ![Domaine déployée pour l’utilisateur] (./media/active-directory-saas-work-com-tutorial/IC784377.png "Domaine déployée pour l’utilisateur")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre portail classique Azure.

6.  Sur la page intégration d’application **Work.com **, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794109.png "Configurer l’authentification unique")

7.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Work.com** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794110.png "Configurer l’authentification unique")

8.  Sur la page **Configurer l’URL App** , dans la zone de texte **Work.com signe d’URL** , tapez l’URL utilisée par vos utilisateurs à se connecter à votre application de Work.com (par exemple : » *http://company.my.salesforce.com*«), puis cliquez sur **suivant**: 

    ![Configurer des URL de l’application] (./media/active-directory-saas-work-com-tutorial/IC794111.png "Configurer des URL de l’application")

9.  Dans la page **configuration de l’authentification unique à Work.com** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794112.png "Configurer l’authentification unique")

10. Connectez-vous à vos clients Work.com.

11. Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

12. Développez le menu **Sécurité** , puis cliquez sur **Paramètres d’authentification unique**.

    ![Paramètres d’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794113.png "Paramètres d’authentification unique")

13. Sur la page de la boîte de dialogue **Paramètres d’authentification unique** , procédez comme suit :

    ![SAML activé] (./media/active-directory-saas-work-com-tutorial/IC781026.png "SAML activé")

    1.  Sélectionnez **SAML activé**.
    2.  Cliquez sur **Nouveau**.

14. Dans la section **Paramètres d’authentification unique SAML** , effectuez les opérations suivantes :

    ![Paramètre ouverture de session unique de le SAML] (./media/active-directory-saas-work-com-tutorial/IC794114.png "Paramètre ouverture de session unique de le SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.  

        >[AZURE.NOTE] Fournir une valeur pour **nom** remplit automatiquement la zone de texte **Nom de l’API** .

    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Work.com** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    3.  Pour télécharger le certificat téléchargé, cliquez sur **Parcourir**.
    4.  Dans la zone de texte **Id de l’entité** , tapez **https://salesforce-work.com**.
    5.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de fédération à partir de l’objet utilisateur**.
    6.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité dans l’élément NameIdentfier de l’instruction de l’objet**.
    7.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Work.com** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité** .
    8.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Work.com** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL déconnexion du fournisseur d’identité** .
    9.  **Service fournisseur initiée par demande de liaison**, sélectionnez **HTTP Post**.
    10. Cliquez sur **Enregistrer**.

15. Dans votre portail classique de Work.com, dans le volet de navigation de gauche, cliquez sur **Gestion de domaine** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-work-com-tutorial/IC794115.png "Mon domaine")

16. Sur la page de **Mon domaine** , dans la section **Personnalisation de Page de connexion** , cliquez sur **Modifier**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-work-com-tutorial/IC767826.png "Page de connexion de personnalisation")

17. Dans la page de **Personnalisation de Page de connexion** , dans la section **Service d’authentification** , le nom de vos **Paramètres d’authentification unique SAML** s’affiche. Sélectionnez-le, puis cliquez sur **Enregistrer**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-work-com-tutorial/IC784366.png "Page de connexion de personnalisation")

18. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-work-com-tutorial/IC794116.png "Configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Pour Azure Active Directory aux utilisateurs d’être en mesure de vous connecter, il doivent être configurés à Work.com.  
Dans le cas de Work.com, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Ouverture de session sur le site de votre entreprise Work.com en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-work-com-tutorial/IC794108.png "Programme d’installation")

3.  Accédez à **Gérer les utilisateurs \> les utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC784369.png "Gérer les utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC794117.png "Tous les utilisateurs")

5.  Dans la section utilisateur modifier, effectuez les opérations suivantes :

    ![Modification de l’utilisateur] (./media/active-directory-saas-work-com-tutorial/IC794118.png "Modification de l’utilisateur")

    1.  Tapez le **Nom**, **Alias**, **E-mail**, attributs **Username** et **surnom** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Sélectionnez le **rôle**, de **licence de l’utilisateur** et de **profil**.
    3.  Cliquez sur **Enregistrer**.  

        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory obtenez un e-mail incluant un lien permettant de confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Work.com utilisateur compte outils de création ou API fournies par Work.com à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-workcom-perform-the-following-steps"></a>Pour affecter des utilisateurs à Work.com, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration Work.com application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-work-com-tutorial/IC794119.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-work-com-tutorial/IC767830.png "Oui")
  
Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé à Work.com.com.
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).