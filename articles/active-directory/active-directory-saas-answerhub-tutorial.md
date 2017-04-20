<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec AnswerHub | Microsoft Azure" 
    description="Apprenez à utiliser AnswerHub avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Didacticiel : Intégration d’Azure Active Directory avec AnswerHub

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) de l’authentification unique activée abonnement

Après la fin de ce didacticiel, les utilisateurs AD Azure que vous avez affecté à AnswerHub pourront ouverture de session unique dans l’application à votre site de la société AnswerHub (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour AnswerHub
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scénario")

##<a name="enabling-the-application-integration-for-answerhub"></a>L’activation de l’intégration de l’application pour AnswerHub

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour AnswerHub.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour AnswerHub, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **AnswerHub**.

    ![Galerie des applications] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Galerie des applications")

7.  Dans le volet de résultats, sélectionnez **AnswerHub**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de AnswerHub avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous êtes obligé de créer un fichier de certificat de codé en base 64.  
Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page d’intégration **AnswerHub** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à AnswerHub** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **AnswerHub URL de connexion** , tapez l’URL de votre en utilisant le modèle suivant «*https://company.answerhub.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique à AnswerHub** , pour télécharger votre certificat, cliquez sur **Télécharger le certificat**, puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "De configurer l’authentification unique")

5.  Dans une fenêtre de navigateur web différent, ouvrez une session dans le site de votre entreprise AnswerHub en tant qu’administrateur.
    >[AZURE.NOTE] Si vous avez besoin d’aide pour la configuration d’AnswerHub, contacter [l’équipe de AnswerHub](mailto:success@answerhub.com. ).








6.  Aller à **l’Administration**.

7.  Cliquez sur l’onglet **utilisateurs et groupes** .

8.  Dans le volet de navigation sur le côté gauche, dans la section **Paramètres sociaux** , cliquez sur **Le programme d’installation de SAML**.

9.  Cliquez sur onglet **IDP Config** .

10. Sous l’onglet **Configuration de IDP** , effectuez les opérations suivantes :

    ![Programme d’installation SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Programme d’installation SAML")

    1.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à AnswerHub** , copiez la valeur de **L’URL de connexion à distance** et puis la coller dans la zone de texte **URL de connexion IDP** .
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à AnswerHub** , copiez la valeur **d’URL de déconnexion à distance** et puis la coller dans la zone de texte **URL de déconnexion IDP** .
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique à AnswerHub** , copier la valeur de **l’Identificateur de Format du nom** et puis la coller dans la zone de texte **Format de nom d’identificateur IDP** .
    4.  Cliquez sur les **clés et les certificats**.

11. Sous l’onglet clés et les certificats, procédez comme suit :

    ![Clés et certificats] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Clés et certificats")

    1.  Créer un fichier **codé en base 64** de votre certificat téléchargé.  

        >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o)

    2.  Ouvrir votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte **IDP de clé publique (x 509 Format)** .
    3.  Cliquez sur **Enregistrer**.

12. Sous l’onglet **Configuration de IDP** , cliquez sur **Enregistrer**.

13. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

Afin de permettre aux utilisateurs d’AD Azure pour vous connecter à AnswerHub, il doivent être configurés dans AnswerHub.  
Dans le cas de AnswerHub, la mise en service est une tâche manuelle.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous au site de votre entreprise **AnswerHub** en tant qu’administrateur.

2.  Aller à **l’Administration**.

3.  Cliquez sur l’onglet **utilisateurs et groupes** .

4.  Dans le volet de navigation sur le côté gauche, dans la section **Gérer les utilisateurs** , cliquez sur **créer ou importer des utilisateurs**.

    ![Groupes & utilisateurs] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Groupes & utilisateurs")

5.  Tapez **l’adresse de messagerie**, un **nom d’utilisateur** et un **mot de passe** d’un compte Azure Active Directory valide que vous souhaitez mettre en service dans les zones de texte liées, puis cliquez sur **Enregistrer**.

>[AZURE.NOTE] Vous pouvez utiliser n’importe quel autres AnswerHub utilisateur compte outils de création ou API fournies par AnswerHub à disposition DAS des comptes d’utilisateurs.

##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Pour affecter des utilisateurs à AnswerHub, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page d’intégration **AnswerHub **application, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Oui")

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
