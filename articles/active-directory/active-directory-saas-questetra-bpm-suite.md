<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une Suite BPM de Questetra | Microsoft Aure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de la Suite BPM de Questetra."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Didacticiel : Intégration d’Azure Active Directory avec une Suite BPM de Questetra

L’objectif de ce didacticiel est de vous montrer comment intégrer la Suite BPM de Questetra avec Azure Active Directory (AD Azure).  
Intégrant Suite BPM de Questetra AD Azure vous offre les avantages suivants : 

- Vous pouvez contrôler dans Azure AD qui a accès à la Suite de gestion des processus métiers de Questetra 
- Vous pouvez permettre à vos utilisateurs pour automatiquement obtenir signé à la Suite de gestion des processus métiers de Questetra (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables 

Pour configurer l’intégration de publicités Azure avec Suite BPM de Questetra, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Description du scénario
L’objectif de ce didacticiel est de permettre de tester Azure AD de session unique dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose de trois blocs de construction principaux :

1. Ajout de Suite BPM de Questetra à partir de la galerie 
2. Configuration et test AD Azure authentification unique


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Ajout de Suite BPM de Questetra à partir de la galerie
Pour configurer l’intégration de la Suite BPM de Questetra dans AD Azure, vous devez ajouter Questetra BPM Suite à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter la Suite BPM de Questetra à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Suite BPM de Questetra**.

    ![Applications][5]

7. Dans le volet résultats, sélectionnez la **Suite BPM de Questetra**et puis cliquez sur **Terminer** pour ajouter l’application.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
L’objectif de cette section est de vous montrer comment configurer et tester Azure AD-session unique avec une Suite BPM de Questetra basée sur un utilisateur de test appelé « Brian Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit savoir quel est l’utilisateur équivalent dans Questetra BPM Suite à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans la Suite BPM de Questetra doit être établi.  
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de suite BPM de Questetra **nom d’utilisateur** .
 
Pour configurer et tester AD Azure single sign-on avec Suite BPM de Questetra, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’une Suite BPM de Questetra test utilisateur](#creating-a-questetra-bpm-suite-test-user)** - d’avoir un équivalent de Britta Simon dans Suite BPM Questetra qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration d’Azure AD unique Single Sign-On

L’objectif de cette section est pour activer Azure AD de l’authentification unique dans le portail classique Azure et configurer l’authentification unique dans votre application Suite BPM de Questetra.

**Pour configurer AD Azure SSO avec Questetra BPM Suite, effectuez les opérations suivantes :**

1. Dans le portail Azure classique, sur la page intégration d’application **Suite BPM de Questetra** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .

    ![Configurer l’authentification unique][8]

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Questetra BPM Suite** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Azure AD unique Single Sign-On][9]


3. Dans une fenêtre de navigateur web différents, vous connecter à votre site d’entreprise **Suite BPM de Questetra** en tant qu’administrateur.

4. Dans le menu du haut, cliquez sur **Paramètres système**. 

    ![Azure AD unique Single Sign-On][10]

5. Pour ouvrir la page **SingleSignOnSAML** , cliquez sur **L’authentification unique (SAML)**. 

    ![Azure AD unique Single Sign-On][11]


6. Dans le portail classique Azure, sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes : 

    ![Configurer les paramètres de l’application][13]
 
    une barre d’outils. Sur votre site d’entreprise **Questetra BPM Suite** , dans la section informations de SP, copiez l' **URL de l’ACS**et puis la coller dans la zone de texte **URL de connexion** .

    b. Votre site d’entreprise **Questetra BPM Suite** , dans la section informations de processeur de stockage, copiez l' **ID de l’entité**et puis la coller dans la zone de texte **URL de l’émetteur** .

    c. Sur votre site d’entreprise **Questetra BPM Suite** , dans la section informations de SP, copiez l' **URL de l’ACS**et puis la coller dans la zone de texte **URL de la réponse** .

    d. Cliquez sur **suivant**.

 
7. Dans la page **configuration de l’authentification unique à Questetra BPM Suite** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configurer l’authentification unique][14]


8. Sur votre site de la société **Questetra BPM Suite** , effectuez les opérations suivantes : 

    ![Configurer l’authentification unique][15]

    une barre d’outils. Sélectionnez **Activer l’ouverture de session unique**.
     
    b. Sur le portail Azure classique, copiez la valeur de **l’URL de l’émetteur** et puis la coller dans la zone de texte **ID de l’entité** .

    c. Sur le portail Azure classique, copier la valeur de **l’URL de Service unique ouverture de session** et puis la coller dans la zone de texte **URL de la page connexion** .

    d. Sur le portail Azure classique, copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **URL de la page de déconnexion** .

    e. Dans la zone de texte **format de NameID** , tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : emailAddress**.


    f. Créer un fichier encodé en base 64 de votre certificat téléchargé. 

    >[AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

    g. Ouvrez votre certificat codé en base 64 dans le bloc-notes et copiez le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **certificat de Validation** . 

    h. Cliquez sur **Enregistrer**.


9. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![Nouveautés d’Azure AD Connect][17]


10. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  

    ![Nouveautés d’Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
L’objectif de cette section est de créer un utilisateur test dans Azure portal classique appelé Britta Simon.

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Créer une annonce Azure test utilisateur][100] 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Créer une annonce Azure test utilisateur][101] 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**. 

    ![Créer une annonce Azure test utilisateur][102] 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :

    ![Créer une annonce Azure test utilisateur][103]
 
    une barre d’outils. En tant **Type d’utilisateur**, sélectionnez **nouvel utilisateur de votre organisation**.
  
    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur Suivant.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : 

    ![Créer une annonce Azure test utilisateur][104] 
  
    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**. 
 
    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Créer une annonce Azure test utilisateur][105]  

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Créer une annonce Azure test utilisateur][106]   
  
    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.
  
    b. Cliquez sur **terminé**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Création d’un utilisateur de test Suite BPM de Questetra

L’objectif de cette section est de créer un utilisateur appelé Britta Simon Questetra BPM suite.

**Pour créer un utilisateur appelé Britta Simon Questetra BPM suite, effectuez les opérations suivantes :**

1.  Ouverture de session sur le site de votre entreprise Suite BPM de Questetra en tant qu’administrateur.
2.  Accédez à **paramètres système > liste > nouvel utilisateur**. 
3.  Dans la boîte de dialogue Nouvel utilisateur, effectuez les opérations suivantes : 

    ![Créer l’utilisateur test][300] 

    une barre d’outils. Dans la zone de texte **nom** , tapez le nom d’utilisateur de Brian dans Azure AD.

    b. Dans la zone de texte **message** , tapez les nom d’utilisateur de Brian dans Azure AD.

    c. Dans la zone de texte **mot de passe** , tapez un mot de passe.

4.  Cliquez sur **Ajouter un nouvel utilisateur**.



### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

L’objectif de cette section est l’activation Britta Simon à utiliser Azure de l’authentification unique par l’octroi d’accès à la Suite de gestion des processus métiers de Questetra.

![Nouveautés d’Azure AD Connect][200]

**Pour affecter des Britta Simon Questetra BPM suite, effectuez les opérations suivantes :**

1. Sur le portail Azure classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Nouveautés d’Azure AD Connect][201]

2. Dans la liste des applications, sélectionnez **Suite BPM de Questetra**.

    ![Nouveautés d’Azure AD Connect][205]

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Nouveautés d’Azure AD Connect][202]

1. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

    ![Nouveautés d’Azure AD Connect][203]

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Nouveautés d’Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.  
Lorsque vous cliquez sur la mosaïque Suite BPM de Questetra dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application Suite BPM de Questetra.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 