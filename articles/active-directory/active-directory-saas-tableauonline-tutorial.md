<properties
    pageTitle="Didacticiel : Azure Active Directory intégrant Tableau en ligne | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau en ligne."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Didacticiel : Azure Active Directory intégrant Tableau en ligne

Dans ce didacticiel, vous allez apprendre à intégrer le Tableau en ligne avec Azure Active Directory (AD Azure).

Intégration de Tableau en ligne avec Active Directory Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à un Tableau en ligne
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur Tableau en ligne (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec Tableau en ligne, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique **En ligne de Tableau** sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout en ligne de Tableau à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-tableau-online-from-the-gallery"></a>Ajout en ligne de Tableau à partir de la galerie
Pour configurer l’intégration de Tableau en ligne dans AD Azure, vous devez ajouter à partir de la galerie en ligne de Tableau à la liste des applications gérées de SaaS.

**Pour ajouter le Tableau en ligne à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez la **Ligne de Tableau**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)

7. Dans le volet de résultats, sélectionnez le **Tableau en ligne**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez de l’authentification unique de l’annonce Azure en ligne Tableau basé sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans le Tableau en ligne à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le Tableau en ligne doit être établi.
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de l' **utilisateur** dans le Tableau en ligne.

Pour configurer et tester AD Azure single sign-on avec Tableau en ligne, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un Tableau en ligne test utilisateur](#creating-a-Tableau-Online-test-user)** - avoir de Britta Simon contrepartie dans la ligne de Tableau qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application en ligne de Tableau.

**Pour configurer AD Azure SSO avec Tableau en ligne, effectuez les opérations suivantes :**

1. Dans le menu du haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique][6]
2. Dans le portail classique, sur la page d’intégration application **En ligne de Tableau** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7] 

3. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la ligne du Tableau** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)

4. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)


    une barre d’outils. Dans la zone de texte URL de connexion, tapez une URL en utilisant le modèle suivant :`https://sso.online.tableau.com`

    c. Cliquez sur **suivant**.

5. Dans la page **configuration de l’authentification unique au Tableau en ligne** , cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)

6. Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]
8. Dans une fenêtre différente, ouverture de session sur votre application en ligne de Tableau. Pointez sur **paramètres** , puis **l’authentification**

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)

9. Sous la section des **Types d’authentification** . Cochez la case **Single sign-on avec SAML** pour activer SAML.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

10. Faites défiler la liste jusqu'à la section **Importer le fichier de métadonnées dans le Tableau en ligne** .  Cliquez sur Parcourir et importer le fichier de métadonnées que vous avez téléchargé à partir d’AD Azure. Ensuite, cliquez sur **Appliquer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

11. Dans la section **des assertions de correspondance** , insérez le nom d’assertion de fournisseur d’identité correspondant pour l’adresse de courriel, prénom et nom. Pour obtenir ces informations depuis Active Directory Azure :

    une barre d’outils. Revenir à AD Azure. Dans le portail Azure classique, sur la page d’intégration application **En ligne de Tableau** , dans le menu de la partie supérieure, cliquez sur **attributs**. Copier le nom pour les valeurs : userprincipalname, givenname et surname.
     
    ![Azure AD unique Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)

    b. Basculez vers l’application en ligne de Tableau, puis définir la section **Tableau des attributs en ligne** comme suit :
    
    -  E-mail : **courrier** ou **userprincipalname**
    -  Prénom : **givenname**
    -  Nom : **nom de famille**

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-tableau-online-test-user"></a>Création d’un utilisateur de test en ligne de Tableau

Dans cette section, vous créez un utilisateur appelé Britta Simon dans le Tableau en ligne.

1. Sur **Ligne du Tableau**, cliquez sur **paramètres** , puis sur section **d’authentification** . Faites défiler jusqu'à la section **Sélectionner des utilisateurs** . Cliquez sur **Ajouter des utilisateurs** , puis **Entrez les adresses électroniques**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Sélectionnez **Ajouter des utilisateurs pour sign-on (SSO) d’authentification unique**. Dans la zone de texte **Permet d’entrer les adresses de messagerie** , ajouterbritta.simon@contoso.com

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)

3.  Cliquez sur **créer**.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès au Tableau en ligne.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon au Tableau en ligne, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

3. Dans la liste des applications, sélectionnez la **Ligne du Tableau**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 

4. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

5. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

6. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque de Tableau en ligne dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application en ligne de Tableau.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png
