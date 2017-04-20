<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Evidence.com | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Evidence.com."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/23/2016"
    ms.author="asmalser"/>


# <a name="tutorial-azure-active-directory-integration-with-evidencecom"></a>Didacticiel : Intégration d’Azure Active Directory avec Evidence.com

L’objectif de ce didacticiel est d’afficher la configuration de l’authentification unique entre Azure Active Directory (DAS) et Evidence.com. Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :
    
* Un abonnement valide de Microsoft Azure
* Un abonnement de Evidence.com avec authentification unique activée (e-mail earlyaccess@evidence.com si basée sur SAML single sign-on n’est pas activé)

À la fin de ce didacticiel, le DAS auquel vous avez affecté des Evidence.com accès pourront à l’ouverture de session unique dans l’application à l’aide du panneau d’accès DAS.

## <a name="add-evidencecom-to-your-directory"></a>Ajoutez Evidence.com à votre répertoire

Cette section explique comment ajouter des Evidence.com sous la forme d’une application intégrée dans Azure Active Directory.

**Pour activer l’intégration de l’application pour preuve :**

1.  Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

4.  Pour ouvrir la bibliothèque de l’Application, cliquez sur **Ajouter**, puis cliquez sur **Ajouter une application à partir de la galerie**.

5.  Dans la zone Rechercher, tapez **Evidence.com**.

6.  Dans le volet de résultats, sélectionnez **Evidence.com**, puis cliquez sur **Terminer** pour ajouter l’application.


## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

Cette section explique comment permettre aux utilisateurs de s’authentifier à Evidence.com avec leur compte Azure Active Directory, à l’aide de fédération basée sur le protocole SAML.

**Pour configurer l’authentification unique, procédez comme suit :**

1.  Après l’ajout de Evidence.com dans Azure portal classique, cliquez sur **Configuration de l’authentification unique**. 
 
2.  Sur l’écran suivant, sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

3.  Dans l’écran configurer l’application URL, entrez l’URL où les utilisateurs signera dans l’URL de votre client Evidence.com (exemple : https://yourtenant.evidence.com, puis cliquez sur **suivant**. 

4.  Cliquez sur le lien **Télécharger le certificat** et enregistrez-le sur votre disque local. Ce certificat et les URL de métadonnées (ID de l’entité, l’authentification unique URL de connexion et signe les URL) serviront pour configurer l’authentification unique sur le site de Evidence.com. 

5.  Dans une fenêtre de navigateur web distincte, connectez-vous à votre Evidence.com client en tant qu’administrateur et accédez à l’onglet **Admin**
      
6.  Cliquez sur **l’agence de session unique**
 
7.  Sélectionnez **SAML selon l’authentification unique**
 
8.  Copiez l' **URL de l’émetteur**, les valeurs de **Session unique** et **Déconnexion unique** indiqués dans Azure portal classique et dans les champs correspondants dans Evidence.com.

9.  Ouvrir le certificat téléchargé à l’étape 4 à l’aide d’un éditeur de texte comme Notepad.exe, puis copiez et collez le contenu dans la zone **Certificat de sécurité** . 

10. Enregistrer la configuration dans Evidence.com.
 
11. Dans le portail Azure classique, vérifiez à **confirmer que vous avez configurée à l’ouverture de session unique tel que décrit ci-dessus**. Activez cette option activera le certificat actuel commencer à utiliser pour cette case à cocher de l’application.
 
12. Sur la page de confirmation d’ouverture de session unique, cliquez sur **Terminer**.  


## <a name="creating-an-evidencecom-test-user"></a>Création d’un utilisateur de test Evidence.com

Pour les utilisateurs AD Azure être en mesure de se connecter, ils doivent être configurés pour l’accès à l’intérieur de l’application Evidence.com. Cette section décrit comment créer des comptes d’utilisateur AD Azure dans Evidence.com

**Pour configurer un compte d’utilisateur dans Evidence.com :**

1.  Dans une fenêtre de navigateur web, ouvrez une session dans le site de votre entreprise Evidence.com en tant qu’administrateur.

2.  Accédez à l’onglet **Admin** .

3.  Cliquez sur **Ajouter un utilisateur**.

4.  Cliquez sur le bouton **Ajouter** .

5.  L' **Adresse E-mail** de l’utilisateur ajouté doit correspondre le nom d’utilisateur des utilisateurs dans Active Directory Azure qui vous souhaitez accorder l’accès. Si le nom d’utilisateur et votre adresse électronique n’est pas la même valeur dans votre organisation, vous pouvez utiliser la **Evidence.com > attributs > Single Sign-On** section du portail Azure classique pour modifier la nameidenitifer envoyé à Evidence.com à l’adresse de messagerie.


## <a name="assigning-users-to-evidencecom"></a>Affectation des utilisateurs à Evidence.com

Pour mis en service DAS pour permettre aux utilisateurs de voir Evidence.com sur son panneau d’accès, il doivent être attribués accès au sein du portail classique Azure.

**Pour affecter des utilisateurs à Evidence.com :**

1.  Sur la page de démarrage rapide pour les Evidence.com dans Azure portal classique, cliquez sur **affecter des utilisateurs à Evidence.com**.
 
2.  Dans le menu **Afficher** , sélectionnez si vous souhaitez affecter un utilisateur ou un groupe à Evidence.com, puis cliquez sur le bouton de coche.
 
3.  Dans la liste **utilisateurs** , sélectionnez les utilisateurs à grouper dans laquelle vous voulez affecter des Evidence.com.
 
4.  Dans le pied de page, cliquez sur le bouton **affecter** .

