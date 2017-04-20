<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Cisco étincelle | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et d’allumage de Cisco."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Didacticiel : Intégration d’Azure Active Directory avec Cisco ETINCELLE

Dans ce didacticiel, vous allez apprendre à intégrer Cisco étincelle Azure Active Directory (AD Azure).

Intégrant Cisco étincelle AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à étincelle de Cisco
- Vous pouvez permettre à vos utilisateurs pour automatiquement obtenir signé à étincelle Cisco (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec allumage de Cisco, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique **Étincelle de Cisco** sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout d’allumage de Cisco à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-cisco-spark-from-the-gallery"></a>Ajout d’allumage de Cisco à partir de la galerie
Pour configurer l’intégration de Cisco étincelle dans AD Azure, vous devez ajouter l’allumage de Cisco à partir de la galerie à la liste des applications gérées de SaaS.

**Pour ajouter l’allumage de Cisco à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Allumage de Cisco**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. Dans le volet de résultats, sélectionnez **l’Allumage de Cisco**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et testez AD Azure SSO avec allumage Cisco basées sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans Cisco étincelle à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Cisco Spark doit être établi.
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans l’allumage de Cisco. Pour configurer et tester Azure AD single sign-on avec allumage de Cisco, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Tester l’utilisateur créant une étincelle Cisco](#creating-a-cisco-spark-test-user)** - avoir un équivalent de Britta Simon dans Cisco étincelle qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application d’allumage de Cisco.

Application d’allumage Cisco attend les assertions SAML contenant des attributs spécifiques. Configurez les attributs suivants pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **« Atrributes »** de l’application. La capture d’écran suivante montre un exemple de cela.

![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Pour configurer AD Azure SSO avec Cisco étincelle, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **Cisco étincelle** , dans le menu de la partie supérieure, cliquez sur **attributs**.
     
    ![Configurer l’authentification unique][5]


2. Dans la boîte de dialogue **attributs de jeton SAML** , effectuez les opérations suivantes :

    une barre d’outils. Cliquez sur **Ajouter attribut utilisateur** pour ouvrir la boîte de dialogue **Ajouter des Attribure utilisateur** .

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Dans la zone de texte **Nom de l’attribut** , tapez **uid**.
    
    c. Dans la liste **Valeur de l’attribut** , sélectionnez **user.userprincipal**.
    
    d. Cliquez sur **terminé**. Ensuite, **Appliquer les modifications** au bas de la page.

3. Dans le menu du haut, cliquez sur **Démarrage rapide**.

    ![Configurer l’authentification unique][6]

4. Dans le portail classique, sur la page intégration d’application **Cisco étincelle** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7] 

5. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à l’allumage de Cisco** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    une barre d’outils. Dans la zone de texte URL de connexion, tapez une URL en utilisant le modèle suivant : `https://web.ciscospark.com/#/signin`.

    b. Cliquez sur **suivant**.


7. Dans la page **configuration de l’authentification unique à l’allumage de Cisco** , cliquez sur **télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Connectez-vous à la [Gestion de Collaboration Cloud Cisco](https://admin.ciscospark.com/) avec vos informations d’identification d’administrateur intégral.
9. Sélectionnez les **paramètres** et dans la section **authentification** , cliquez sur **Modifier**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Sélectionnez **intégrer un fournisseur d’identité de 3 rd-party. (Avancé)** et accédez à l’écran suivant.
11. Cliquez sur **Télécharger un fichier de métadonnées** , puis enregistrez le fichier sur votre ordinateur.
12. Dans la page **Importer les métadonnées Idp** , faites glisser et déposez le fichier de métadonnées Azure annonce sur la page ou utiliser l’option du fichier de navigateur pour localiser et télécharger le fichier de métadonnées AD Azure. Ensuite, sélectionnez **Exiger le certificat signé par une autorité de certification dans les métadonnées (plus sécurisée)** et cliquez sur **suivant**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Sélectionnez **Tester la connexion SSO**et lorsqu’un nouvel onglet de navigateur s’ouvre, s’authentifier avec AD Azure en vous connectant.
14. Revenir à l’onglet Explorateur de **Gestion de Collaboration de nuage de Cisco** . Si le test a réussi, sélectionnez **ce test a réussi. Activer l’ouverture de session unique option** et cliquez sur **suivant**.

7. Sur le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

8. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
    
    ![Azure AD unique Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-a-cisco-spark-test-user"></a>Création d’un utilisateur de test d’allumage de Cisco

Dans cette section, vous créez un utilisateur appelé Britta Simon dans allumage de Cisco. Dans cette section, vous créez un utilisateur appelé Britta Simon dans allumage de Cisco.

1. Allez à la [Gestion de Collaboration Cloud Cisco](https://admin.ciscospark.com/) avec vos informations d’identification administrateur intégral.
2. Cliquez sur **utilisateurs** , puis sur **Gérer les utilisateurs**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Dans la fenêtre **Gérer les utilisateurs** , sélectionner **manuellement ajouter ou modifier des utilisateurs** et cliquez sur **suivant**.
4. Sélectionnez **les noms et adresse E-mail**. Ensuite, remplissez la zone de texte que vous suivez :

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**

    b. Dans la zone de texte **Nom** , tapez **Simon**

    c. Dans la zone de texte **adresse de messagerie** , tapez**britta.simon@contoso.com**

5. Cliquez sur le signe plus pour ajouter Britta Simon. Ensuite, cliquez sur **suivant**.
6. Dans la fenêtre **Ajouter des Services pour les utilisateurs** , cliquez sur **Enregistrer** , puis sur **Terminer**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en autorisant son accès à étincelle de Cisco.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon à étincelle de Cisco, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Allumage de Cisco**.

    ![Configurer l’authentification unique](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque d’allumage de Cisco dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application d’allumage de Cisco.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
