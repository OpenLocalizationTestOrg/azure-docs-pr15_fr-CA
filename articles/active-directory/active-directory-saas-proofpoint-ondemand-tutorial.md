<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Proofpoint à la demande | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Proofpoint à la demande."
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
    ms.date="10/05/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Didacticiel : Intégration d’Azure Active Directory avec Proofpoint à la demande

Dans ce didacticiel, vous allez apprendre à intégrer Proofpoint à la demande avec Azure Active Directory (AD Azure).

Intégrant Proofpoint à la demande AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Proofpoint à la demande.
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé pour Proofpoint à la demande (l’ouverture de session unique ou SSO) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central, le portail classique Azure.

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [qu’est l’accès aux applications et à l’ouverture de session unique avec Active Directory de Azure ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec Proofpoint à la demande, vous devez les éléments suivants :

- Un abonnement Azure AD
- Un Proofpoint sur demande unique session d’abonnement


Pour tester les étapes de ce didacticiel, suivez ces recommandations :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez [obtenir une version d’évaluation d’un mois](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajouter Proofpoint à la demande de la galerie.
2. Configuration et test de l’authentification unique de l’annonce Azure.


## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Ajouter Proofpoint à la demande de la galerie
Pour configurer l’intégration de Proofpoint à la demande en publicité Azure, vous devez ajouter Proofpoint à la demande à partir de la bibliothèque à la liste des applications gérées de SaaS.

1. Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Icône de Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **APPLICATIONS** dans le menu du haut.

    ![Élément de menu des APPLICATIONS][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Bouton Ajouter][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Choix de l’ajout d’une application à partir de la galerie][4]

6. Dans la zone Rechercher, tapez **Proofpoint à la demande**.

    ![Lorsque vous tapez « Proofpoint » à la demande](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)

7. Dans le volet résultats, sélectionnez **Proofpoint à la demande**et puis cliquez sur **Terminer** pour ajouter l’application.



##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique de l’annonce Azure
Dans cette section, vous configurez et testez AD Azure single sign-on avec Proofpoint à la demande d’un utilisateur de test nommé Britta Simon.

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans Proofpoint à la demande à un utilisateur dans AD Azure. En d’autres termes, vous devez établir une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Proofpoint à la demande.

Vous établissez cette relation de lien en affectant la valeur de **nom d’utilisateur** dans AD Azure comme valeur **nom d’utilisateur** dans Proofpoint à la demande.

Pour configurer et tester AD Azure single sign-on avec Proofpoint à la demande, procédez comme suit :

1. [Configurer Active Directory Azure SSO](#configuring-azure-ad-single-sign-on), pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. [Créer un utilisateur de test AD Azure](#creating-an-azure-ad-test-user), pour tester AD Azure single sign-on avec Britta Simon.
3. [Créer un Proofpoint sur utilisateur de test à la demande](#creating-a-proofpoint-ondemand-test-user), d’avoir un équivalent de Britta Simon dans Proofpoint à la demande qui est liée à la représentation sous forme de publicité Azure de lui.
4. [Attribuer à l’utilisateur de test AD Azure](#assigning-the-azure-ad-test-user), permettent de Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. [Test de session unique](#testing-single-sign-on), afin de vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre Proofpoint sur l’application de la demande.

1. Dans le portail classique, sur la page intégration d’application **Proofpoint à la demande** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![« Configuration de l’authentification unique » de bouton][6]

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Proofpoint à la demande** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Case d’option « Microsoft Azure AD Single Sign-On »](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)

3. Dans la page **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![« Configurer les paramètres de l’application » une page avec des zones](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)

    une barre d’outils. Dans la zone **URL de connexion à** , tapez l’URL où les utilisateurs se connecter à votre Proofpoint sur l’application de la demande. Utilisez le modèle suivant : **https://\<nom d’hôte\>.pphosted.com/ppssamlsp_hostname**

    b. Dans la zone **identificateur** , tapez l’URL en utilisant le modèle suivant : **https://\<nom d’hôte / >.pphosted.com/ppssamlsp**

    c. Dans la zone **URL de réponse** , tapez l’URL en utilisant le modèle suivant : **https://\<nom d’hôte / >.pphosted.com:portnumber/v1/samlauth/samlconsumer**

    d. Cliquez sur **suivant**.

4. Dans la page **configuration de l’authentification unique à Proofpoint à la demande** , procédez comme suit :

    ![« Configurer l’ouverture de session unique à Proofpoint à la demande » de page avec des boutons de « Télécharger le certificat »](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.

5. Pour obtenir l’authentification unique configurée pour votre application, contactez le Proofpoint sur l’équipe de support à la demande et par les éléments suivants :

    • Le certificat téléchargé

    • L’ID d’entité

    • L’URL SAML SSO

6. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Case à cocher qui confirme que vous avez configuré l’ouverture de session unique][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Page de confirmation][11]


### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur de test nommé Britta Simon dans le portail classique.


![L’utilisateur test dans la liste des utilisateurs][20]

1. Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Icône de Active Directory](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **utilisateurs**.

    ![Élément de menu d’utilisateurs](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils du bas, cliquez sur **Ajouter un utilisateur**.

    ![Bouton Ajouter un utilisateur](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)

5. Sur la page de **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![page de « Faites-nous part cet utilisateur » avec zones renseigné](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)

    une barre d’outils. Dans la zone **TYPE d’utilisateur** , sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la zone **Nom d’utilisateur** , tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page **profil utilisateur** , effectuez les opérations suivantes : ![la page « profil utilisateur » avec des zones](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)

    une barre d’outils. Dans la zone **prénom** , tapez **Brian**.  

    b. Dans la zone **Nom** , tapez **Simon**.

    c. Dans la zone **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Bouton de création d’un mot de passe temporaire](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)

8. Sur la page **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![« Obtenir le mot de passe temporaire » la page avec les informations de mot de passe](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)

    une barre d’outils. Notez la valeur dans la zone **Nouveau mot de passe** .

    b. Cliquez sur **terminé**.   



### <a name="create-a-proofpoint-on-demand-test-user"></a>Créer un Proofpoint sur utilisateur de test à la demande

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Proofpoint à la demande. Travaillez avec Proofpoint sur l’équipe de prise en charge de la demande pour ajouter des utilisateurs dans le Proofpoint sur la plate-forme de la demande.


### <a name="assign-the-azure-ad-test-user"></a>Attribuer à l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO par lui accorder son accès à Proofpoint à la demande.

![Informations utilisateur, affichage de l’accès par le biais de la méthode directe][200]

1. Dans le portail classique, dans la vue du répertoire, cliquez sur **APPLICATIONS** dans le menu du haut pour ouvrir la vue des applications.

    ![Élément de menu des APPLICATIONS][201]

2. Dans la liste des applications, sélectionnez **Proofpoint à la demande**.

    ![Liste des applications avec Proofpoint sur demande sélectionnée](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Élément de menu d’utilisateurs][203]

4. Dans la liste des utilisateurs, sélectionnez **Brian Simon**.

5. Sur la barre d’outils du bas, cliquez sur **attribuer**.

    ![Attribuer bouton][205]


### <a name="test-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque **Proofpoint à la demande** sur le panneau d’accès, vous devez être automatiquement connecté à votre Proofpoint sur l’application de la demande.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png
