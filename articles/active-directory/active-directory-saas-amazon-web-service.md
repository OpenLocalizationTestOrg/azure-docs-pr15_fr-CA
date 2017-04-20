<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Services (AWS) | Microsoft Azure"
    description="Découvrez comment utiliser Amazon Web Services (AWS) avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Didacticiel : Intégration d’Azure Active Directory avec Amazon Web Services (AWS)

L’objectif de ce didacticiel est de vous montrer comment intégrer Amazon Web Services (AWS) avec Azure Active Directory (AD Azure).  
Intégration Amazon Web Services (AWS) avec AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à Amazon Web Services (AWS) 
- Vous pouvez permettre à vos utilisateurs pour automatiquement obtenir signé à Amazon Web Services (AWS) (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec Amazon Web Services (AWS), vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Amazon Web Services (AWS) sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de trois blocs de construction principaux :

1. Ajout d’Amazon Web Service (AWS) à partir de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Ajout d’Amazon Web Service (AWS) à partir de la galerie
Pour configurer l’intégration de Amazon Web Services (AWS) dans Active Directory Azure, vous devez ajouter Amazon Web Services (AWS) à partir de la galerie à la liste des applications gérées de SaaS.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Pour ajouter des Amazon Web Services (AWS) à partir de la galerie, procédez comme suit :

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur. 
   
    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page. 
   
    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**. 
   
    ![Applications][4]

6. Dans la zone Rechercher, tapez **Amazon Web Services (AWS)**.
   
    ![Applications][5]

7. Dans le volet de résultats, sélectionnez **Amazon Web Services (AWS)**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Applications][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester AD Azure SSO avec Amazon Web Services (AWS) basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans Amazon Web Services (AWS) à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans Amazon Web Services (AWS) doit être établie.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de données, **nom d’utilisateur** dans Amazon Web Services (AWS).
 
Pour configurer et tester Azure AD de l’authentification unique avec Amazon Web Services (AWS), vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure unique Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un Service Web de Amazon (AWS) test utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un équivalent de Britta Simon dans Amazon Web Services (AWS) qui est liée à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application d’Amazon Web Service (AWS).  
Votre application Amazon Web Services (AWS) attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter les mappages d’attributs personnalisés à votre configuration **d’attributs de jeton saml** . La capture d’écran suivante montre un exemple de cela.


![Configurer l’authentification unique][27]

**Pour configurer AD Azure SSO avec Amazon Web Services (AWS), effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **Amazon Web Services (AWS)** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][7]

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Amazon Web Services (AWS)** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique][8]

3. Dans la page de la boîte de dialogue **Configurer les paramètres d’application** , cliquez sur Suivant. 

    ![Configurer les paramètres de l’application][9]
 
4. Dans la page **configuration de l’authentification unique à Amazon Web Services (AWS)** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.

    ![Configurer l’authentification unique][10]

5. Dans une fenêtre différente, ouverture de session sur le site de votre entreprise d’Amazon Web Service (AWS) en tant qu’administrateur.

6. Cliquez sur **Accueil de la Console**.

    ![Configurer l’authentification unique][11]

7. Cliquez sur **Gestion des accès et des identités**. 

    ![Configurer l’authentification unique][12]

8. Cliquez sur **Fournisseurs d’identité**, puis cliquez sur **Créer un fournisseur**. 

    ![Configurer l’authentification unique][13]

9. Sur la page de la boîte de dialogue **Configurer un fournisseur** , procédez comme suit : 

    ![Configurer l’authentification unique][14]

     une barre d’outils. En tant que **Type de fournisseur**, sélectionnez **SAML**.

     b. Dans la zone de texte **Nom du fournisseur** , tapez un nom de fournisseur (par exemple : *bois non traités*).

     c. Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Fichier**.

     d. Cliquez sur **étape suivante**.


10. Sur la page de la boîte de dialogue **Vérifier les informations de fournisseur** , cliquez sur **créer**. 

    ![Configurer l’authentification unique][15]

11. Cliquez sur **rôles**, puis cliquez sur **Créer un nouveau rôle**. 

    ![Configurer l’authentification unique][16]

12. Dans la boîte de dialogue **Définir un nom de rôle** , procédez comme suit : 

    ![Configurer l’authentification unique][17]

    une barre d’outils. Dans la zone de texte **Nom du rôle** , tapez un nom de rôle (par exemple : *TestUser*).

    b. Cliquez sur **étape suivante**.

13. Dans la boîte de dialogue **Sélectionner le Type de rôle** , procédez comme suit : 

    ![Configurer l’authentification unique][18]

    une barre d’outils. Sélectionnez un **rôle pour l’accès de fournisseur d’identité**.

    b. Dans la section **autorisations Web Single Sign-On (WebSSO) un accès aux fournisseurs SAML** , cliquez sur **Sélectionner**.


14. Dans la boîte de dialogue **Établir d’approbation** , effectuez les opérations suivantes :  

    ![Configurer l’authentification unique][19]
     
     une barre d’outils. En tant que fournisseur SAML, sélectionnez le fournisseur SAML que vous avez créé previousley (par exemple : *bois non traités*) 

     b. Cliquez sur **étape suivante**.


15. Dans la boîte de dialogue **Vérifier la confiance de rôle** , cliquez sur **Étape suivante**. 

    ![Configurer l’authentification unique][32]


16. Dans la boîte de dialogue **Attacher une stratégie** , cliquez sur **Étape suivante**.  

    ![Configurer l’authentification unique][33]


17. Dans la boîte de dialogue **révision** , effectuez les opérations suivantes :   

    ![Configurer l’authentification unique][34]

     une barre d’outils. Copiez la valeur de **Rôle Infos** .

     b. Copiez la valeur d’infos des **Entités de confiance** .

     c. Cliquez sur **créer un rôle**. 

18. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.

    ![Nouveautés d’Azure AD Connect][20]

19. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer** pour fermer la boîte de dialogue **de configurer l’authentification unique** .

    ![Nouveautés d’Azure AD Connect][22]


20. Dans le menu du haut, cliquez sur **attributs** pour ouvrir la boîte de dialogue **Attributs de jeton SAML** . 

    ![Configurer l’authentification unique][21]

21. Cliquez sur **Ajouter un attribut de l’utilisateur**. 

    ![Configurer l’authentification unique][23]

22. Dans la boîte de dialogue Ajouter un attribut utilisateur, effectuez les opérations suivantes. 

    ![Configurer l’authentification unique][24] 

    une barre d’outils. Dans la zone de texte **Nom de l’attribut** , tapez **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Dans la zone **Valeur de l’attribut** , entrez **[valeur rôle Infos], [valeur approuvé les infos entité]**.

    >[AZURE.TIP] Ce sont les valeurs que vous avez copié à partir de la boîte de dialogue révision lorsque vous avez créé votre rôle. 

    c. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur** .

23. Cliquez sur **Ajouter un attribut de l’utilisateur**. 

    ![Configurer l’authentification unique][23]


24. Dans la boîte de dialogue Ajouter un attribut utilisateur, effectuez les opérations suivantes. 

    ![Configurer l’authentification unique][25]


     une barre d’outils. Dans la zone de texte **Nom de l’attribut** , tapez **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Dans la zone **Valeur d’attribut** , tapez ou sélectionnez **user.userprincipalname** à partir de la zone de liste déroulante.
     
    ![Configurer l’authentification unique][35]
    

     c. Cliquez sur **Terminer** pour fermer la boîte de dialogue **Ajouter un attribut utilisateur** .


25. Cliquez sur **appliquer les modifications**. 

    ![Configurer l’authentification unique][26]





### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure

L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.
  2. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.
  3. Cliquez sur Suivant.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans le **Nom** txtbox, type, **Simon**.
  
    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.
  
    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.
  
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Création d’un utilisateur de test Amazon Web Services (AWS)

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Pour créer un utilisateur appelé Britta Simon dans Amazon Web Services (AWS), effectuez les opérations suivantes :

1. Ouvrez une session en tant qu’administrateur le site de votre entreprise **d’Amazon Web Service (AWS)** .

2. Cliquez sur l’icône **d’Accueil de la Console** . 

    ![Configurer l’authentification unique][11]

3. Cliquez sur identité et accéder à la gestion. 

    ![Configurer l’authentification unique][28]

4. Dans le tableau de bord, cliquez sur utilisateurs, puis cliquez sur Create New Users. 

    ![Configurer l’authentification unique][29]

5. Dans la boîte de dialogue Créer un utilisateur, effectuez les opérations suivantes : 

    ![Configurer l’authentification unique][30]

     une barre d’outils. Dans les zones de texte **Entrez les noms d’utilisateur** , tapez le nom d’utilisateur de Brita Simon (userprincipalname) dans Active Directory Azure.

     b. Cliquez sur **créer**.




### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure SSO en autorisant son accès à Amazon Web Services (AWS).

![Affecter des utilisateurs][31]

**Pour faire Britta Simon CloudPassage, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][26]

2. Dans la liste des applications, sélectionnez **Amazon Web Services (AWS)**.

    ![Affecter des utilisateurs][27]

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][25]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][29]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque Amazon Web Services (AWS) dans le panneau d’accès, vous devez obtenir automatiquement signé-sur à votre application d’Amazon Web Service (AWS).


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















