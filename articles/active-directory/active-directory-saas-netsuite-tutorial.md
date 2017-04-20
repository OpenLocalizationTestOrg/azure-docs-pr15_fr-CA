<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec NetSuite | Microsoft Azure"
    description="Apprenez à utiliser NetSuite avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Didacticiel : Comment intégrer Azure Active Directory NetSuite

Ce didacticiel va vous montrer comment connecter votre environnement NetSuite à Azure Active Directory (AD Azure). Vous apprendrez comment configurer l’authentification unique pour NetSuite, comment faire pour activer la mise en service automatique d’utilisateur et comment affecter des utilisateurs à accéder à NetSuite. 

##<a name="prerequisites"></a>Conditions préalables

1. Pour accéder à Active Directory de Azure via [Azure portal classique](https://manage.windowsazure.com), vous devez avoir un abonnement Azure valide.

2. Vous devez disposer de l’accès administrateur à un abonnement de [NetSuite](http://www.netsuite.com/portal/home.shtml) . Vous pouvez utiliser un compte d’essai gratuit pour chaque service.

##<a name="step-1-add-netsuite-to-your-directory"></a>Étape 1 : Ajoutez NetSuite à votre répertoire

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Sélectionnez Active Directory à partir du volet de navigation de gauche.][0]

2. Dans la liste **répertoire** , sélectionnez le répertoire que vous souhaitez ajouter NetSuite à.

3. Cliquez sur **Applications** dans le menu supérieur.

    ![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la **zone Rechercher**, tapez **NetSuite**. Sélectionnez **NetSuite** dans les résultats, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Ajouter NetSuite.][4]

7. Vous devez maintenant voir la page de démarrage rapide pour NetSuite :

    ![Page de démarrage rapide du NetSuite dans Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Étape 2 : Activer l’ouverture de session unique

1. Dans AD Azure, sur la page de démarrage rapide pour NetSuite, cliquez sur le bouton **de configurer l’authentification unique** .

    ![Le seul signe sur bouton Configurer][6]

2. Une boîte de dialogue s’ouvre, vous verrez un écran vous demandant « Comment souhaitez-vous aux utilisateurs de se connecter à NetSuite ? » Sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Sélectionnez Azure AD unique Single Sign-On][7]

    > [AZURE.NOTE] Pour en savoir plus sur les différentes seule session options, [Cliquez ici](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Dans la page **Configurer les paramètres d’application** , pour le champ **URL de réponse** , tapez l’URL de votre client NetSuite à l’aide d’un des formats suivants :
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Tapez l’URL de votre client][8]

4. Dans la page **configuration de l’authentification unique à NetSuite** , cliquez sur **télécharger les métadonnées**et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Télécharger les métadonnées.][9]

5. Ouvrir un nouvel onglet dans votre navigateur et connectez-vous à votre site d’entreprise Netsuite en tant qu’administrateur.

6. Dans la barre d’outils en haut de la page, cliquez sur **le programme d’installation**, puis cliquez sur **Assistant Gestion d’installation**.

    ![Accédez à l’Assistant Gestion d’installation][10]

7. Dans la liste des **Tâches de configuration** , sélectionnez **l’intégration**.

    ![Accédez à l’intégration][11]

8. Dans la section **Gérer l’authentification** , cliquez sur **SAML Single Sign-on**.

    ![Atteindre SAML Single Sign-on][12]

9. Sur la page **Installation de SAML** , effectuez les opérations suivantes :

    - Dans Azure Active Directory, copiez la valeur de **L’URL de connexion à distance** et collez-le dans le champ de la **Page de connexion de fournisseur de l’identité** de NetSuite.

        ![La page Installation de SAML.][13]

    - Dans NetSuite, sélectionnez comme **Méthode principale de l’authentification**.

    - Pour le champ intitulé **SAMLV2 identité fournisseur de métadonnées**, sélectionnez **Charger un fichier de métadonnées IDP**. Cliquez ensuite sur **Parcourir** pour charger le fichier de métadonnées que vous avez téléchargé à l’étape #4.

        ![Télécharger les métadonnées][16]

    - Cliquez sur **Envoyer**.

9. Dans AD Azure, sélectionnez la case à cocher confirmation de configuration d’ouverture de session unique pour activer le certificat que vous avez téléchargé pour NetSuite. Puis cliquez sur **suivant**.

    ![Activez la case à cocher confirmation][14]

10. Sur la dernière page de la boîte de dialogue, tapez une adresse de messagerie si vous souhaitez recevoir des notifications par courrier électronique pour les erreurs et les avertissements liés à la maintenance de cette configuration de l’authentification unique. 

    ![Tapez votre adresse de courriel.][15]

11. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Ensuite, cliquez sur **attributs** en haut de la page.

    ![Cliquez sur attributs.][17]

12. Cliquez sur **Ajouter l’attribut de l’utilisateur**.

    ![Cliquez sur Ajouter un attribut de l’utilisateur.][18]

13. Pour le champ de **Nom d’attribut** , tapez dans `account`. Pour le champ de **Valeur d’attribut** , tapez votre ID de compte NetSuite. Vous trouverez ci-dessous des instructions sur comment trouver votre numéro de compte :

    ![Ajouter votre ID de compte NetSuite.][19]

    - Dans NetSuite, cliquez sur **le programme d’installation** à partir du menu de navigation supérieur.
    - Puis cliquez sur la section de **Tâches de configuration** du menu de navigation de gauche, sélectionnez la section **intégration** et cliquez sur **Préférences de Services Web**.
    - Copier votre ID de compte de NetSuite et collez-le dans le champ **Valeur de l’attribut** dans Active Directory Azure.

        ![Obtenir votre ID de compte][20]

14. Dans AD Azure, cliquez sur **Terminer** pour terminer l’ajout de l’attribut SAML. Dans le menu en bas puis cliquez sur **Appliquer les modifications** .

    ![Enregistrez vos modifications.][21]

15. Avant que les utilisateurs peuvent effectuer une ouverture de session unique dans les NetSuite, il doivent être préalablement assignés les autorisations appropriées dans NetSuite. Suivez les instructions ci-dessous pour affecter ces autorisations.

    - Dans le menu de navigation supérieure, cliquez sur **le programme d’installation**, puis cliquez sur **Assistant Gestion d’installation**.

        ![Accédez à l’Assistant Gestion d’installation][10]

    - Dans le menu de navigation de gauche, sélectionnez **Utilisateurs et rôles**, puis cliquez sur **Gérer les rôles**.

        ![Accès à la gestion des rôles][22]

    - Cliquez sur **nouveau rôle**.

    - Tapez un **nom** pour le nouveau rôle et cochez la case **Unique de session uniquement** .

        ![Nom du nouveau rôle.][23]

    - Cliquez sur **Enregistrer**.

    - Dans le menu du haut, cliquez sur **autorisations**. Puis, cliquez sur **le programme d’installation**.

        ![Accédez aux autorisations][24]

    - Sélectionnez **définir les SAM de l’authentification unique**, puis cliquez sur **Ajouter**.

    - Cliquez sur **Enregistrer**.

    - Dans le menu de navigation supérieure, cliquez sur **le programme d’installation**, puis cliquez sur **Assistant Gestion d’installation**.

        ![Accédez à l’Assistant Gestion d’installation][10]

    - Dans le menu de navigation de gauche, sélectionnez **Utilisateurs et rôles**, puis cliquez sur **Gérer les utilisateurs**.

        ![Accès à la gestion des utilisateurs][25]

    - Sélectionnez un utilisateur de test. Puis cliquez sur **Modifier**.

        ![Accès à la gestion des utilisateurs][26]

    - Dans la boîte de dialogue rôles, sélectionnez le rôle que vous avez créé et cliquez sur **Ajouter**.

        ![Accès à la gestion des utilisateurs][27]

    - Cliquez sur **Enregistrer**.

19. Pour tester votre configuration, reportez-vous à la section ci-dessous intitulée [Affecter des utilisateurs à NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Étape 3 : Activer l’attribution automatisée

> [AZURE.NOTE] Par défaut, vos utilisateurs mis en service seront ajoutés à la filiale de la racine de votre environnement de NetSuite.

1. Dans Azure Active Directory, sur la page de démarrage rapide pour NetSuite, cliquez sur la **Configuration des utilisateurs de configurer**.

    ![Configurer la mise en service de l’utilisateur][28]

2. Dans la boîte de dialogue qui s’ouvre, tapez vos informations d’identification d’administration pour NetSuite, puis cliquez sur **suivant**.

    ![Tapez vos informations d’identification de NetSuite admin.][29]

3. Sur la page de confirmation, tapez dans votre adresse de courriel pour recevoir des notifications de mise en service des échecs.

    ![Confirmer.][30]

4. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

##<a name="step-4-assign-users-to-netsuite"></a>Étape 4 : Assigner des utilisateurs à NetSuite

1. Pour tester votre configuration, commencer la création d’un nouveau compte de test dans le répertoire.

2. Sur la page de démarrage rapide de NetSuite, cliquez sur le bouton **Affecter des utilisateurs** .

    ![Cliquez sur affecter des utilisateurs][31]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **affecter** au bas de l’écran :

 - Si vous n’avez pas activer mise en service de l’utilisateur automatisées, vous verrez le message suivant pour confirmer :

        ![Confirm the assignment.][32]

 - Si vous avez activé l’approvisionnement automatisé de l’utilisateur, vous verrez une invite de commandes pour définir le type de rôle de l’utilisateur doit avoir dans NetSuite. Les utilisateurs qui vient d’être mis en service doivent apparaître dans votre environnement de NetSuite après quelques minutes.

4. Pour tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès à [https://myapps.microsoft.com](https://myapps.microsoft.com/), ouvrir une session sur le compte de test et cliquez sur **NetSuite**.

##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
