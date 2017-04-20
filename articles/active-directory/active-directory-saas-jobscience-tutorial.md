<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Jobscience | Microsoft Azure" 
    description="Apprenez à utiliser Jobscience avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Didacticiel : Intégration d’Azure Active Directory avec Jobscience
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Jobscience.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un abonnement Jobscience de l’authentification unique activée
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Jobscience pourront ouverture de session unique dans l’application à votre site de la société Jobscience (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Jobscience
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scénario")
##<a name="enabling-the-application-integration-for-jobscience"></a>L’activation de l’intégration de l’application pour Jobscience
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Jobscience.

###<a name="to-enable-the-application-integration-for-jobscience-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Jobscience, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-jobscience-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-jobscience-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **jobscience**.

    ![Galerie des applications] (./media/active-directory-saas-jobscience-tutorial/IC784342.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Jobscience**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Jobscience] (./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Jobscience avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Configuration de l’authentification unique pour Jobscience vous demande de récupérer une valeur de l’empreinte d’un certificat.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment récupérer la valeur d’empreinte numérique d’un certificat](http://youtu.be/YKQF266SAxI).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Connectez-vous au site de votre entreprise Jobscience en tant qu’administrateur.

2.  Atteindre **le programme d’installation**.

    ![Programme d’installation] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programme d’installation")

3.  Dans le volet de navigation de gauche, dans la section **administrer** , cliquez sur **Gestion de domaine** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

4.  Pour vérifier que votre domaine a été correctement configuré, assurez-vous qu’il s’agit de le «**Étape 4 déployée vers des utilisateurs**» et passez en revue votre «**Mes paramètres de domaine**».

    ![Domaine déployée pour l’utilisateur] (./media/active-directory-saas-jobscience-tutorial/IC784377.png "Domaine déployée pour l’utilisateur")

5.  Dans une fenêtre de navigateur web différents, vous connecter à votre portail classique Azure.

6.  Sur la page intégration d’application **Jobscience** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configurer l’authentification unique")

7.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Jobscience** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configurer l’authentification unique")

8.  Dans la page **Configurer l’URL App** , dans la zone de texte **Jobscience URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.my.salesforce.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configurer des URL de l’application")

9.  Dans la page **configuration de l’authentification unique à Jobscience** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configurer l’authentification unique")

10. Sur le site de la société Jobscience, cliquez sur les **Contrôles de sécurité**, puis cliquez sur **Paramètres d’authentification unique**.

    ![Contrôles de sécurité] (./media/active-directory-saas-jobscience-tutorial/IC784364.png "Contrôles de sécurité")

11. Dans la section **Paramètres d’authentification unique** , procédez comme suit :

    ![Paramètres d’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC781026.png "Paramètres d’authentification unique")

    1.  Sélectionnez **SAML activé**.
    2.  Cliquez sur **Nouveau**.

12. Dans la boîte de dialogue **SAML simple Sign-On paramètre modifier** , effectuez les opérations suivantes :

    ![Paramètre ouverture de session unique de le SAML] (./media/active-directory-saas-jobscience-tutorial/IC784365.png "Paramètre ouverture de session unique de le SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Jobscience** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur**
    3.  Dans la zone de texte **Id de l’entité** , tapez **https://salesforce-jobscience.com**
    4.  Cliquez sur **Parcourir** pour charger votre certificat AD Azure.
    5.  En tant que **Type d’identité SAML**, sélectionnez **Assertion contient l’ID de fédération à partir de l’objet utilisateur**.
    6.  En tant qu' **Emplacement d’identité SAML**, sélectionnez **identité dans l’élément NameIdentfier de l’instruction de l’objet**.
    7.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Jobscience** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité**
    8.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Jobscience** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL déconnexion du fournisseur d’identité**
    9.  Cliquez sur **Enregistrer**.

13. Dans le volet de navigation de gauche, dans la section **administrer** , cliquez sur **Gestion de domaine** pour développer la section, puis cliquez sur **Mon domaine** pour ouvrir la page de **Mon domaine** . 

    ![Mon domaine] (./media/active-directory-saas-jobscience-tutorial/IC767825.png "Mon domaine")

14. Sur la page de **Mon domaine** , dans la section **Personnalisation de Page de connexion** , cliquez sur **Modifier**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-jobscience-tutorial/IC767826.png "Page de connexion de personnalisation")

15. Dans la page de **Personnalisation de Page de connexion** , dans la section **Service d’authentification** , le nom de vos **Paramètres d’authentification unique SAML** s’affiche. Sélectionnez-le, puis cliquez sur **Enregistrer**.

    ![Page de connexion de personnalisation] (./media/active-directory-saas-jobscience-tutorial/IC784366.png "Page de connexion de personnalisation")

16. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configurer l’authentification unique")
  
Pour obtenir le Service Pack, authentification unique initiée par l’URL de connexion cliquez sur les **paramètres d’authentification unique** , dans la section menu de **Contrôles de sécurité** .

![Contrôles de sécurité] (./media/active-directory-saas-jobscience-tutorial/IC784368.png "Contrôles de sécurité")
  
Cliquez sur le profil d’authentification unique que vous avez créé à l’étape précédente.  
Cette page affiche l’ouverture de session unique sur l’URL de votre société (par exemple, *https://companyname.my.salesforce.com?so=companyid*).
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Jobscience, il doivent être configurés dans Jobscience.  
Dans le cas de Jobscience, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Jobscience** en tant qu’administrateur.

2.  Atteindre le programme d’installation

    ![Programme d’installation] (./media/active-directory-saas-jobscience-tutorial/IC784358.png "Programme d’installation")

3.  Accédez à **Gérer les utilisateurs \> les utilisateurs**.

    ![Utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784369.png "Utilisateurs")

4.  Cliquez sur **nouvel utilisateur**.

    ![Tous les utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784370.png "Tous les utilisateurs")

5.  Dans la boîte de dialogue **Modifier l’utilisateur** , effectuez les opérations suivantes :

    ![Modification de l’utilisateur] (./media/active-directory-saas-jobscience-tutorial/IC784371.png "Modification de l’utilisateur")

    1.  Tapez le prénom, le nom, les alias, les e-mail, les propriétés nom et surnom d’utilisateur de l’utilisateur AD Azure que vous souhaitez mettre en service dans les zones de texte liées.
    2.  Cliquez sur **Enregistrer**.

    >[AZURE.NOTE] Le titulaire du compte Azure AD obtiendra un e-mail contenant un lien pour confirmer le compte avant qu’il soit activé.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Jobscience utilisateur compte outils de création ou API fournies par Jobscience à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-jobscience-perform-the-following-steps"></a>Pour affecter des utilisateurs à Jobscience, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **Jobscience **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-jobscience-tutorial/IC784372.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-jobscience-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).