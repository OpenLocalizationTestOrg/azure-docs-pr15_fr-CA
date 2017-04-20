<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory Lifesize cloud | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de nuage de Lifesize."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Didacticiel : Intégration d’Azure Active Directory Lifesize cloud

Dans ce didacticiel, vous allez apprendre à intégrer Lifesize Cloud Azure Active Directory (AD Azure).

Intégration Lifesize nuage avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure annonce ayant accès au Cloud de Lifesize
- Vous pouvez autoriser les utilisateurs à automatiquement obtenir signé sur nuage Lifesize (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure cloud de Lifesize, vous devez les éléments suivants :

- Un abonnement Azure AD
- Une connexion unique Lifesize Cloud sur abonnement activé


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de nuage de Lifesize à partir de la galerie
2. Configuration et test AD Azure authentification unique


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Ajout de nuage de Lifesize à partir de la galerie
Pour configurer l’intégration de Lifesize Cloud dans AD Azure, vous devez ajouter Lifesize nuage à partir de la bibliothèque à la liste des applications gérées de SaaS.

**Pour ajouter le nuage de Lifesize à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory][1]
2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **Lifesize Cloud**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. Dans le volet résultats, sélectionnez le **Nuage de Lifesize**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et test AD Azure SSO avec Lifesize nuage basé sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur homologue dans le nuage de Lifesize à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans le nuage de Lifesize doit être établi.

Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur de l' **utilisateur** dans le nuage de Lifesize.

Pour configurer et tester AD Azure single sign-on avec Lifesize Cloud, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration AD Azure Single Sign-On](#configuring-azure-ad-single-sign-on)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
3. **[Création d’un nuage Lifesize tester l’utilisateur](#creating-a-lifesize-cloud-test-user)** - d’avoir un équivalent de Britta Simon dans le nuage Lifesize qui est lié à la représentation sous forme de publicité Azure de lui.
4. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
5. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique de l’annonce Azure

Dans cette section, vous activez Azure AD de l’authentification unique dans le portail classique et configurez l’authentification unique dans votre application en nuage de Lifesize.


**Pour configurer AD Azure SSO avec Lifesize Cloud, effectuez les opérations suivantes :**

1. Dans le portail classique, sur la page intégration d’application **Cloud de Lifesize** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configuration de l’authentification unique** .
     
    ![Configurer l’authentification unique][6] 

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Lifesize Cloud** , sélectionnez **Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. Sur la page de la boîte de dialogue **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    une barre d’outils. Dans la zone de texte **URL de connexion** , tapez l’URL utilisée par vos utilisateurs à l’ouverture de session sur votre application en nuage de Lifesize selon le modèle suivant : **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Cliquez sur **suivant**
 
4. Dans la page **configuration de l’authentification unique au nuage de Lifesize** , effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    une barre d’outils. Cliquez sur **Télécharger le certificat**et puis enregistrez le fichier sur votre ordinateur.

    b. Cliquez sur **suivant**.


5. Pour obtenir l’authentification unique configurée pour votre application, la connexion d’accès à l’application de nuage de Lifesize avec des privilèges d’administrateur.

6. Dans le coin supérieur droit, cliquez sur votre nom et puis cliquez sur les **Paramètres avancés**

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. Dans la paramètres avancés maintenant, cliquez sur le lien **Configuration de l’authentification unique** . Ceci ouvrira la page de Configuration de l’authentification unique de l’instance.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Maintenant, configurez les valeurs suivantes dans l’interface utilisateur de configuration de l’authentification unique.    

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copier la valeur de l’URL de l’émetteur à partir d’AD Azure et coller le texte sélectionné dans le textbox de **l’Émetteur identité du fournisseur** .

    • Copier la valeur de l’URL de connexion à distance à partir d’AD Azure et coller le texte sélectionné dans la zone de texte **URL de connexion** .

    • Ouvrez le certificat téléchargé dans le bloc-notes et copiez le contenu du certificat, à l’exclusion de lignes commencer le certificat et le certificat de fin, le coller dans la zone de texte du **Certificat X.509** .

    • Dans le mappage d’attribut de SAML pour la zone de texte **prénom** permet d’entrer la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • Dans le mappage d’attribut de SAML pour la zone de texte **Nom** Entrez la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • Dans le mappage d’attribut de SAML pour la zone de texte **courrier électronique** permet d’entrer la valeur en tant que **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Pour vérifier la configuration, vous pouvez cliquer sur le bouton **Test** .

    > [AZURE.NOTE] Pour le test réussi, vous devez exécuter l’Assistant configuration dans Active Directory Azure et offrent également un accès à des utilisateurs ou des groupes qui peuvent effectuer le test.
    
10. Activer l’authentification en vérifiant sur le bouton **d’Activation de l’authentification unique** .

11. Cliquez maintenant sur le bouton de **mise à jour** afin que tous les paramètres sont enregistrés. Cela génère la valeur RelayState. Copiez la valeur de RelayState qui est générée dans la zone de texte. Nous aurons besoin de cette valeur dans les étapes suivantes.

12. Dans le portail classique, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**.
    
    ![Azure AD unique Single Sign-On][10]

13. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.  
 
    ![Azure AD unique Single Sign-On][11]

14. Maintenant la connexion dans le portail de gestion Azure **https://portal.azure.com** en utilisant les informations d’identification de l’administrateur

15. Cliquez sur le lien **Plus de Services** dans le volet de navigation gauche
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Recherche d’Azure Active Directory et cliquez sur le lien **Azure Active Directory**
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Vous trouverez toutes vos applications SaaS sous le bouton **d’Applications d’entreprise** .

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Cliquez maintenant sur le lien de **Toutes les Applications** de la lame suivante
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Recherchez l’application Lifesize pour lequel vous souhaitez le RelayState l’installation. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Cliquez maintenant sur **l’ouverture de session unique** la liaison de la lame

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. La case à cocher **Afficher les paramètres d’URL avancés** s’affiche. Cliquez sur la case à cocher.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Maintenant configurer le RelayState de l’application, vous voyez dans la page de configuration de l’authentification unique application Lifesize. 

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Enregistrer les paramètres.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.


![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :  ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes : ![création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Création d’un utilisateur de test de nuage de Lifesize

Dans cette section, vous créez un utilisateur appelé Britta Simon dans le nuage de Lifesize. Nuage de Lifesize ne prend pas en charge la mise en service automatique de l’utilisateur. Après une authentification réussie à Azure AD, l’utilisateur doit être configuré automatiquement dans l’application. 


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO par lui accorder son accès à nuage de Lifesize.

![Affecter des utilisateurs][200] 

**Pour affecter des Britta Simon Lifesize cloud, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Lifesize Cloud**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203]

4. Dans la liste utilisateurs, sélectionnez **Brian Simon**.

5. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous testez votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque Lifesize Cloud dans le panneau d’accès, vous devez obtenir automatiquement signé sur votre application en nuage de Lifesize.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
