<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Qlik Sense Enterprise | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qlik Sense Enterprise."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Didacticiel : Intégration d’Azure Active Directory avec Qlik Sense Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Qlik Sense Enterprise avec Azure Active Directory (AD Azure).

Intégration Qlik Sense Enterprise avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à l’entreprise de sens Qlik
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur Qlik Sense Enterprise (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec Qlik Sense Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Qlik Sense Enterprise sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de Qlik Sense Enterprise à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Ajout de Qlik Sense Enterprise à partir de la galerie
Pour configurer l’intégration des Qlik Sense Enterprise dans AD Azure, vous devez ajouter Qlik Sense Enterprise à partir de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter des Qlik Sense Enterprise à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Qlik Sense Enterprise**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Dans le volet de résultats, sélectionnez **Qlik Sense Enterprise**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure Qlik Sense Enterprise, basé sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans Qlik Sense Enterprise à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Qlik Sense Enterprise doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans Qlik Sense Enterprise.

Pour configurer et tester AD Azure single sign-on avec Qlik Sense Enterprise, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’une entreprise de sens Qlik tester l’utilisateur](#creating-a-qliksense-enterprise-test-user)** - d’avoir un équivalent de Britta Simon dans Qlik Sense Enterprise qui est lié à la représentation sous forme de publicité Azure de lui.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application d’entreprise de sens de Qlik.


**Pour configurer AD Azure SSO avec Qlik Sense Enterprise, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page de **Qlik Sense Enterprise** application integration, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Qlik Sense Enterprise** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session pour votre application d’entreprise de détection de Qlik selon le modèle suivant : **https://\<Qlik sens entièrement qualifié complet nom de l’hôte\>: 443 / < préfixe de Proxy virtuel\>/samlauthn/**.
    
    > [AZURE.NOTE] Notez la barre oblique à la fin de cet URI.  Il est requis.

    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique à Qlik Sense Enterprise** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    une barre d’outils. Cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier sur votre ordinateur.  Soyez prêt à modifier ce fichier de métadonnées avant de le télécharger vers le serveur de détection des Qlik.

    b. Cliquez sur **suivant**.

5. Préparer le fichier XML des métadonnées de fédération afin que vous puissiez la télécharger et qui au serveur de détection des Qlik.

    > [AZURE.NOTE] Avant de télécharger les métadonnées IdP sur le serveur de détection des Qlik, le fichier doit être modifié pour supprimer les informations pour assurer le bon fonctionnement entre Azure AD et le serveur de détection des Qlik.

    ![QlikSense][qs24]

    une barre d’outils. Ouvrez le fichier FederationMetaData.xml téléchargé à partir d’Azure dans un éditeur de texte.

    b. Recherchez la valeur **RoleDescriptor**.  Il y a quatre entrées (deux paires de balises d’élément).

    c. Supprimer entre les balises RoleDescriptor et toutes les informations à partir du fichier.

    d. Enregistrez le fichier et conserver à proximité pour l’utiliser plus loin dans ce document.

6. Accédez à la Console de gestion Qlik sens Qlik (QMC) en tant qu’utilisateur qui peut créer des configurations de proxy virtuel.

7. Dans le QMC, cliquez sur l’élément de menu Proxy virtuel.

    ![QlikSense][qs6] 

8. En bas de l’écran, cliquez sur le bouton Créer un.

    ![QlikSense][qs7]

9. L’écran Modifier le proxy virtuelle s’affiche.  Sur le côté droit de l’écran est un menu pour afficher les options de configuration de.

    ![QlikSense][qs9]

10. Avec l’option de menu d’Identification vérifiée, entrez les informations d’identification pour la configuration des proxy virtuel Azure.

    ![QlikSense][qs8]  
    
    une barre d’outils. Le champ Description est un nom convivial pour la configuration de proxy virtuel.  Pour obtenir une description, entrez une valeur.
    
    b. Le champ préfixe identifie le point de terminaison proxy virtuel pour la connexion à sens Qlik avec Azure AD Single Sign-On.  Entrez un nom de préfixe unique pour ce proxy virtuel.

    c. Délai d’inactivité de session (minutes) est le délai d’expiration pour les connexions via ce proxy virtuel.

    d. Le nom d’en-tête de cookie de Session est le nom du cookie stocker l’identificateur de session pour la session de Qlik sens que reçoit d’un utilisateur après une authentification réussie.  Ce nom doit être unique.

11. Cliquez sur l’option de menu de l’authentification pour le rendre visible.  L’écran d’authentification s’affiche.

    ![QlikSense][qs10]

    une barre d’outils. La liste déroulante **mode d’accès anonyme** détermine si les utilisateurs anonymes peuvent accéder Qlik sens via le proxy virtuel.  L’option par défaut n’est aucun utilisateur anonyme.

    b. La **méthode d’authentification** de la liste déroulante détermine que le schéma d’authentification du proxy virtuel utilisera.  Dans la liste déroulante, sélectionnez SAML.  Plusieurs options s’affichent en conséquence.

    c. Dans le **champ de l’URI SAML hôte**, entrer que leur nom d’hôte sera saisir pour accéder à Qlik logique par le biais de ce proxy virtuel SAML.  Le nom d’hôte est l’uri du serveur de détection des Qlik.

    d. L' **ID d’entité SAML**, saisissez la même valeur qu’entrée dans le champ URI de hôte SAML.

    e. Les **métadonnées de SAML IdP** est le fichier modifié précédemment dans la section **Modifier les métadonnées de fédération à partir de la Configuration d’Active Directory Azure** .  **Avant de télécharger les métadonnées IdP, le fichier doit être modifié** pour supprimer les informations pour assurer le bon fonctionnement entre Azure AD et le serveur de détection des Qlik.  **Veuillez vous reporter aux instructions ci-dessus si le fichier doit être modifié.**  Si le fichier a été modifié, cliquez sur le bouton Parcourir et sélectionnez le fichier de métadonnées modifiées à télécharger dans la configuration du proxy virtuel.

    f. Entrez le nom de l’attribut ou la référence de schéma pour l’attribut SAML qui représente **nom d’utilisateur** AD Azure envoie au serveur de détection des Qlik.  Informations de référence de schéma ne sont disponibles dans la configuration de la publication écrans application Azure.  Pour utiliser l’attribut nom, **Entrez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Entrez la valeur du **répertoire de l’utilisateur** qui seront reliés aux utilisateurs lorsqu’ils s’authentifient au serveur de détection des Qlik via AD Azure.  Valeurs codées en dur doivent être entourés de **crochets []**.  Pour utiliser un attribut envoyé dans l’assertion SAML de publicité d’Azure, entrez le nom de l’attribut dans cette zone de texte **sans** des crochets.

    h. L' **algorithme de signature SAML** définit la signature pour la configuration de proxy virtuel le certificat du service fournisseur (dans ce cas, serveur de détection de Qlik).  Si le serveur de détection des Qlik utilise un certificat généré à l’aide de Microsoft Enhanced RSA et le fournisseur de services cryptographiques AES, modifier l’algorithme de signature SAML **SHA -**256.

    i. La section de mappage d’attribut SAML permet des attributs supplémentaires comme les groupes à envoyer au sens de la Qlik à utiliser dans les règles de sécurité.

12. Cliquez sur l’option de menu pour le rendre visible d’équilibrage de la charge.  L’écran de l’équilibrage de charge s’affiche.

    ![QlikSense][qs11]

13. Cliquez sur Ajouter nouveau serveur nœud, nœud de moteur select ou Qlik sens de nœuds envoie des sessions pour des fins d’équilibrage de charge et cliquez sur le bouton Ajouter.

    ![QlikSense][qs12]

14. Cliquez sur l’option de menu avancée pour la rendre visible. L’écran Avancé s’affiche.

    ![QlikSense][qs13]

    une barre d’outils. La liste d’autorisation hôte identifie les noms d’hôtes qui sont acceptés lors de la connexion au serveur de détection des Qlik.  **Entrez le nom d’hôte, spécifient les utilisateurs lors de la connexion au serveur de détection des Qlik.** Le nom d’hôte est la même valeur que l’uri d’hôte SAML sans le https://.

15. Cliquez sur le bouton Appliquer.

    ![QlikSense][qs14]

16. Cliquez sur OK pour accepter le message d’avertissement indiquant que les proxys liés au proxy virtuel seront redémarrés.

    ![QlikSense][qs15]

17. Sur le côté droit de l’écran, le menu d’éléments associés s’affiche.  Cliquez sur l’option de menu de proxys.

    ![QlikSense][qs16]

18. L’écran serveur proxy s’affiche.  Cliquez sur le bouton de lien du bas pour lier un proxy sur le serveur virtuel.

    ![QlikSense][qs17]

19. Sélectionnez le nœud du proxy qui prennent en charge cette connexion proxy virtuel et cliquez sur le bouton de lien.  Après la liaison, le proxy apparaît sous proxy associée.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Après environ 5 à 10 secondes, le message d’actualisation de QMC s’affiche.  Cliquez sur le bouton Actualiser les QMC.

    ![QlikSense][qs20]

21. Lorsque le QMC est actualisée, cliquez sur l’élément de menu de proxys virtuelle. La nouvelle entrée de proxy virtuel SAML est répertoriée dans le tableau à l’écran.  Clic sur l’entrée de proxy virtuel.

    ![QlikSense][qs51]

22. En bas de l’écran, Active le bouton métadonnées de télécharger le Service Pack.  Cliquez sur le bouton de métadonnées de SP de téléchargement pour enregistrer les métadonnées dans un fichier.

    ![QlikSense][qs52]

23. Ouvrez le fichier de métadonnées de Service Pack.  Observez l’entrée **entityID** et l’entrée **AssertionConsumerService** .  Ces valeurs sont équivalentes pour l' **identificateur** et l' **URL pour l’ouverture de session** dans la configuration de l’application Azure AD. Si elles ne correspondent pas vous devez les remplacer dans l’Assistant de configuration Azure AD App.

    ![QlikSense][qs53]

24. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

25. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
 
    ![Azure AD unique Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Création d’un utilisateur de test Qlik Sense Enterprise

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Qlik Sense Enterprise. Travaillez avec l’équipe de support Qlik Sense Enterprise pour ajouter les utilisateurs de la plate-forme Qlik Sense Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès à Qlik Sense Enterprise.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à Qlik Sense Enterprise, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Qlik Sense Enterprise**.

    ![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


## <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque Qlik Sense Enterprise dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application d’entreprise de sens de Qlik.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png