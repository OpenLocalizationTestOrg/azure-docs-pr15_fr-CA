<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Gigya | Microsoft Azure" 
    description="Apprenez à utiliser Gigya avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-gigya"></a>Didacticiel : Intégration d’Azure Active Directory avec Gigya
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Gigya.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Gigya authentification unique activée d’abonnement
  
Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à Gigya pourront ouverture de session unique dans l’application au niveau du site de votre société Gigya (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Gigya
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789512.png "Configurer l’authentification unique")
##<a name="enabling-the-application-integration-for-gigya"></a>L’activation de l’intégration de l’application pour Gigya
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Gigya.

###<a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Gigya, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-gigya-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-gigya-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **Gigya**.

    ![Galerie des applications] (./media/active-directory-saas-gigya-tutorial/IC789513.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **Gigya**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Gigya] (./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Gigya avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Gigya** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789528.png "Configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Gigya** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789529.png "Configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Gigya URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*http://company.gigya.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-gigya-tutorial/IC789530.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à Gigya** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789531.png "Configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise Gigya en tant qu’administrateur.

6.  Accédez à **paramètres \> SAML connexion**, puis cliquez sur le bouton **Ajouter** .

    ![Connexion SAML] (./media/active-directory-saas-gigya-tutorial/IC789532.png "Connexion SAML")

7.  Dans la section **Connexion de SAML** , effectuez les opérations suivantes :

    ![Configuration de SAML] (./media/active-directory-saas-gigya-tutorial/IC789533.png "Configuration de SAML")

    1.  Dans la zone de texte **nom** , tapez un nom pour votre configuration.
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Gigya** , copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte de **l’émetteur** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Gigya** , copier la valeur de **l’URL de Service unique de session** et puis la coller dans la zone de texte **URL de Service unique de session** .
    4.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à Gigya** , copier la valeur de **l’Identificateur de Format du nom** et puis la coller dans la zone de texte **Nom du Format de l’ID** .
    5.  Créer un fichier **codé en base 64** de votre certificat téléchargé.
        
        >[AZURE.TIP]Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    6.  Ouvrez votre certificat codé en base 64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte du **Certificat X.509** .
    7.  Cliquez sur **Enregistrer les paramètres**.

8.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![Configurer l’authentification unique] (./media/active-directory-saas-gigya-tutorial/IC789534.png "Configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à Gigya, il doivent être configurés dans Gigya.  
Dans le cas de Gigya, la mise en service est une tâche manuelle.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Pour configurer un compte d’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **Gigya** en tant qu’administrateur.

2.  Accédez à **Admin \> gérer les utilisateurs**, puis cliquez sur **Inviter des utilisateurs**.

    ![Gérer les utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789535.png "Gérer les utilisateurs")

3.  Dans la boîte de dialogue inviter des utilisateurs, effectuez les opérations suivantes :

    ![Inviter des utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789536.png "Inviter des utilisateurs")

    1.  Dans la zone de texte **message** , tapez l’alias de messagerie d’un compte Azure Active Directory valide que vous souhaitez mettre en service.
    2.  Cliquez sur **inviter l’utilisateur**.
    
        >[AZURE.NOTE] Le titulaire du compte Azure Active Directory recevra un e-mail contenant un lien pour confirmer le compte avant d’être active.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres Gigya utilisateur compte outils de création ou API fournies par Gigya à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-gigya-perform-the-following-steps"></a>Pour affecter des utilisateurs à Gigya, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Gigya **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-gigya-tutorial/IC789537.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-gigya-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).