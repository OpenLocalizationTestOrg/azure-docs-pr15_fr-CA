<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec le logiciel de l’halogène"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et le logiciel d’halogène."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Didacticiel : Intégration d’Azure Active Directory avec le logiciel de l’halogène

L’objectif de ce didacticiel est de vous montrer comment intégrer le logiciel d’halogène avec Azure Active Directory (AD Azure).

Intégration du logiciel de l’halogène avec AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure annonce ayant accès au logiciel d’halogène 
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur logiciel halogènes (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec le logiciel de l’halogène, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une logiciel d’halogène connexion unique abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout d’un logiciel d’halogènes de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-halogen-software-from-the-gallery"></a>Ajout d’un logiciel d’halogènes de la galerie
Pour configurer l’intégration des logiciels d’halogène dans AD Azure, vous devez ajouter halogène logiciel à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter des logiciels d’halogène à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page. 

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **logiciel d’halogènes**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez **Halogène logiciel**et puis cliquez sur **Terminer** pour ajouter l’application.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester Azure AD-session unique avec le logiciel halogène basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, Azure AD doit savoir quel est l’utilisateur équivalent logiciel d’halogène pour un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le logiciel de l’halogène doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans le logiciel de l’halogène.
 
Pour configurer et tester Azure AD de l’authentification unique avec le logiciel de l’halogène, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure unique Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un logiciel d’halogène tester l’utilisateur](#creating-a-halogen-software-test-user)** - d’avoir un équivalent de Britta Simon logiciel halogène qui est lié à la représentation sous forme de publicité Azure de lui.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application logicielle d’halogènes.


**Pour configurer AD Azure session unique avec le logiciel de l’halogène, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **Logicielle d’halogène** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][8]

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter au logiciel d’halogène** , sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][9]

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :  ![configurer les paramètres d’application][10]
 
     une barre d’outils. dans la zone de texte **URL de connexion** , tapez l’URL de votre utilisé par vos utilisateurs à se connecter à votre application d’halogène logiciel selon le modèle suivant : *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Cliquez sur **suivant**.
 
4. Dans la page **configuration de l’authentification unique au logiciel d’halogène** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de métadonnées localement sur votre ordinateur.
    
    ![Nouveautés d’Azure AD Connect][11]

5. Dans une fenêtre différente, ouverture de session sur votre application **Halogène logiciel** en tant qu’administrateur.

6. Cliquez sur l’onglet **Options** . 

    ![Nouveautés d’Azure AD Connect][12]


7. Dans le volet de navigation de gauche, cliquez sur **Configuration de SAML**. 

    ![Nouveautés d’Azure AD Connect][13]

8. Dans la page **Configuration de SAML** , effectuez les opérations suivantes :  ![Nouveautés Azure Connect de publicité][14]

    une barre d’outils. En tant qu' **Identificateur Unique**, sélectionnez **NameID**.

    b. En tant **Cartes Unique identificateur à**, sélectionnez le **nom d’utilisateur**.

    c. Pour charger votre fichier de métadonnées téléchargé, cliquez sur **Parcourir** pour sélectionner le fichier, puis sur **Télécharger le fichier**.

    d. Pour tester la configuration, cliquez sur **Exécuter le Test**. 

    > [AZURE.NOTE] Vous devez attendre pour que le message «*le SAML test est terminé. Veuillez fermer cette fenêtre*». Ensuite, fermez la fenêtre du navigateur ouvert. La case à cocher **Activer le SAML** n’est activé que si le test a été effectué.

    e. Sélectionnez **Activer SAML**.
    
    f. Cliquez sur **Enregistrer les modifications**. 


9. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** . 

    ![Nouveautés d’Azure AD Connect][15]

10. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Nouveautés d’Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Nouveautés d’Azure AD Connect][100] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Nouveautés d’Azure AD Connect][101] 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Nouveautés d’Azure AD Connect][102] 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Nouveautés d’Azure AD Connect][103] 
 
    une barre d’outils. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur Suivant.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Nouveautés d’Azure AD Connect][104] 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans le **Nom** txtbox, type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Nouveautés d’Azure AD Connect][105]  

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Nouveautés d’Azure AD Connect][106]   

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Création d’un utilisateur de test de logiciel d’halogène

L’objectif de cette section est pour créer un utilisateur appelé Britta Simon halogène logiciel.

**Pour créer un utilisateur appelé Britta Simon dans le logiciel de l’halogène, effectuez les opérations suivantes :**

1. Ouvrez une session sur votre application **Halogène logiciel** en tant qu’administrateur.

2. Cliquez sur l’onglet **Centre utilisateur** , puis cliquez sur **Créer un utilisateur**.

    ![Nouveautés d’Azure AD Connect][300]  

3. Sur la page de la boîte de dialogue **Nouvel utilisateur** , effectuez les opérations suivantes :

    ![Nouveautés d’Azure AD Connect][301]

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**. 
  
    b. Dans la zone de texte **Nom** , tapez **Simon**.
  
    c. Dans la zone de texte **nom d’utilisateur** , tapez **les nom d’utilisateur d’Emmanuel Brita dans Azure portal classique**.
  
    d. Dans la zone de texte **mot de passe** , tapez un mot de passe pour Brian.
  
    e. Cliquez sur **Enregistrer**.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès au logiciel d’halogène.

![Nouveautés d’Azure AD Connect][200]

**Pour affecter des Britta Simon à halogène logiciel, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Nouveautés d’Azure AD Connect][201]

2. Dans la liste des applications, sélectionnez **Halogène logiciel**.

    ![Nouveautés d’Azure AD Connect][202]

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Nouveautés d’Azure AD Connect][203]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

    ![Nouveautés d’Azure AD Connect][204]

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Nouveautés d’Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque halogène logiciels dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application logicielle d’halogènes.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png