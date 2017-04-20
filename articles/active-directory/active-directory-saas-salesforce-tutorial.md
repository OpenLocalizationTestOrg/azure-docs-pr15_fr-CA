<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec une force de vente | Microsoft Azure"
    description="Apprenez à utiliser la force de vente avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !"
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

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Didacticiel : Comment intégrer la force de vente avec Azure Active Directory

Ce didacticiel va vous montrer comment connecter l’environnement de la force de vente à Azure Active Directory. Vous apprendrez comment configurer l’authentification unique pour la force de vente, comment faire pour activer la mise en service automatique d’utilisateur et comment affecter des utilisateurs à accéder à la force de vente.

##<a name="prerequisites"></a>Conditions préalables

1. Pour accéder à Active Directory de Azure via [Azure portal classique](https://manage.windowsazure.com), vous devez avoir un abonnement Azure valide.

2. Vous devez disposer d’un client valide dans [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Si vous utilisez un compte **d’essai** Salesforce.com, puis vous sera impossible de configurer la mise en service automatique d’utilisateur. Comptes d’évaluation n’ont pas de l’accès aux API nécessaires activé jusqu'à ce qu’ils sont achetés.
> 
> Vous pouvez contourner cette limitation en utilisant un [compte de développeur gratuit](https://developer.salesforce.com/signup) pour compléter ce didacticiel.

Si vous utilisez un environnement Sandbox de la force de vente, veuillez consulter le [didacticiel d’intégration Sandbox de la force de vente](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Didacticiels vidéo

Vous pouvez suivre ce didacticiel à l’aide de vidéos ci-dessous.

**Didacticiel vidéo première partie : comment faire pour activer l’ouverture de session unique**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Didacticiel vidéo deuxième partie : Comment automatiser l’approvisionnement de l’utilisateur**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Étape 1 : Ajouter la force de vente à votre répertoire

1. Dans [Azure portal classique](https://manage.windowsazure.com), dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Sélectionnez Active Directory à partir du volet de navigation de gauche.][0]

2. Dans la liste **répertoire** , sélectionnez le répertoire que vous souhaitez ajouter la force de vente à.

3. Cliquez sur **Applications** dans le menu supérieur.

    ![Cliquez sur Applications.][1]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Cliquez sur Ajouter pour ajouter une nouvelle application.][2]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Cliquez sur Ajouter une application à partir de la galerie.][3]

6. Dans la **zone Rechercher**, tapez la **force de vente**. Sélectionnez la **force de vente** dans les résultats, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Ajouter la force de vente.][4]

7. Vous devez maintenant voir la page de démarrage rapide de la force de vente :

    ![Page de démarrage rapide de force de vente dans Azure AD][5]

##<a name="step-2-enable-single-sign-on"></a>Étape 2 : Activer l’ouverture de session unique

1. Avant de pouvoir configurer l’ouverture de session unique, vous devez configurer et déployer un domaine personnalisé pour votre environnement de la force de vente. Pour obtenir des instructions sur la façon de procéder, consultez [La valeur d’un nom de domaine](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Dans Quick Start page de force de vente AD Azure, cliquez sur le bouton **de configurer l’authentification unique** .

    ![Le seul signe sur bouton Configurer][6]

3. Une boîte de dialogue s’ouvre, vous verrez un écran vous demandant « Comment souhaitez-vous aux utilisateurs de se connecter à la force de vente ? » Sélectionnez **Azure AD de l’authentification unique**, puis cliquez sur **suivant**.

    ![Sélectionnez Azure AD unique Single Sign-On][7]

    > [AZURE.NOTE] Pour en savoir plus sur les différentes seule session options, [Cliquez ici](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Dans la page **Configurer les paramètres d’application** , renseignez l' **URL de connexion** en tapant l’URL de domaine votre force de vente en utilisant le format suivant :
 - Compte d’entreprise :`https://<domain>.my.salesforce.com`
 - Compte de développeur :`https://<domain>-dev-ed.my.salesforce.com` 

    ![Type dans votre URL d’ouverture de session][8]

5. Dans la page **configuration de l’authentification unique à la force de vente** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Télécharger le certificat][9]

6. Ouvrir un nouvel onglet dans votre navigateur, puis connectez-vous à votre compte d’administrateur de force de vente.

7. Dans le volet de navigation de **administrateur** , cliquez sur les **Contrôles de sécurité** pour développer la section. Cliquez sur **Paramètres d’authentification unique**.

    ![Cliquez sur paramètres d’authentification unique dans les contrôles de sécurité][10]

8. Dans la page **Paramètres d’authentification unique** , cliquez sur le bouton **Modifier** .

    ![Cliquez sur le bouton Modifier][11]

    > [AZURE.NOTE] Si vous ne parvenez pas à activer l’ouverture de session unique des paramètres de votre compte de la force de vente, vous devrez peut-être contacter le support technique de force de vente pour que la fonctionnalité est activée pour vous.

9. Sélectionnez **SAML activée**, puis cliquez sur **Enregistrer**.

    ![Sélectionnez SAML activé][12]

10. Pour configurer votre SAML unique paramètres d’authentification, cliquez sur **Nouveau**.

    ![Sélectionnez SAML activé][13]

11. Sur la page **SAML simple Sign-On paramètre modifier** , définissez les configurations suivantes :

    ![Capture d’écran des configurations que vous devez prendre][14]

 - Pour le champ **nom** , tapez un nom convivial pour cette configuration. Fournir une valeur pour le **nom** remplir automatiquement la zone de texte **Nom de l’API** .

 - Dans AD Azure, copier la valeur de **l’URL de l’émetteur** et collez-la dans le champ de **l’émetteur** de la force de vente.

 - Dans la **zone de texte Id de l’entité**, tapez votre nom de domaine de force de vente en utilisant le modèle suivant :
     - Compte d’entreprise :`https://<domain>.my.salesforce.com`
     - Compte de développeur :`https://<domain>-dev-ed.my.salesforce.com`

 - Cliquez sur **Parcourir** ou **Choisir un fichier** pour ouvrir la boîte de dialogue **Choisir un fichier à télécharger** , sélectionnez le certificat de la force de vente, puis cliquez sur **Ouvrir** pour charger le certificat.

 - Sélectionnez **Type d’identité SAML** **Assertion contient le nom d’utilisateur de l’utilisateur salesforce.com**.

 - **Emplacement d’identité SAML**, sélectionnez **identité dans l’élément NameIdentifier de l’instruction de l’objet**

 - Dans AD Azure, copiez la valeur de **L’URL de connexion à distance** et puis la coller dans le champ **URL connexion du fournisseur d’identité** dans la force de vente.

 - Pour le **Service fournisseur initiée par demande de liaison**, sélectionnez la **Redirection HTTP**.

 - Enfin, cliquez sur **Enregistrer** pour appliquer vos SAML unique paramètres d’authentification.

12. Dans le volet de navigation de gauche dans la force de vente, cliquez sur **Gestion de domaine** pour développer la section, puis cliquez sur **Mon domaine**.

    ![Cliquez sur mon domaine][15]

13. Faites défiler jusqu'à la section de **Configuration de l’authentification** , puis cliquez sur le bouton **Modifier** .

    ![Cliquez sur le bouton Modifier][16]

14. Dans la section **Service d’authentification** , sélectionnez le nom convivial de votre configuration de SAML SSO, puis cliquez sur **Enregistrer**.

    ![Sélectionnez la configuration de l’authentification unique][17]

    > [AZURE.NOTE] Si plus d’un service d’authentification est activée, puis lorsque les utilisateurs tentent de se lancer à l’ouverture de session unique à votre environnement de la force de vente, ils seront invités à sélectionner le service d’authentification à vous connecter avec. Si vous ne souhaitez pas que cela se produise, vous devez **laisser tous les autres services d’authentification non contrôlés**.

15. Dans AD Azure, sélectionnez la case à cocher confirmation de configuration d’ouverture de session unique pour activer le certificat que vous avez téléchargé à la force de vente. Puis cliquez sur **suivant**.

    ![Activez la case à cocher confirmation][18]

16. Sur la dernière page de la boîte de dialogue, tapez une adresse de messagerie si vous souhaitez recevoir des notifications par courrier électronique pour les erreurs et les avertissements liés à la maintenance de cette configuration de l’authentification unique. 

    ![Tapez votre adresse de courriel.][19]

17. Cliquez sur **Terminer** pour fermer la boîte de dialogue. Pour tester votre configuration, reportez-vous à la section ci-dessous intitulée [Affecter des utilisateurs à la force de vente](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Étape 3 : Activer l’attribution automatisée

1. Dans la page de démarrage rapide de publicité Azure pour la force de vente, cliquez sur le bouton **configurer approvisionnement de l’utilisateur** .

    ![Cliquez sur le bouton Configurer la mise en service des utilisateurs][20]

2. Dans la boîte de dialogue **configurer approvisionnement de l’utilisateur** , tapez votre nom d’utilisateur admin de force de vente et d’un mot de passe.

    ![Tapez votre nom d’utilisateur admin et le mot de passe][21]

    > [AZURE.NOTE] Si vous configurez un environnement de production, la meilleure solution consiste à créer un nouveau compte d’administration de la force de vente spécifiquement pour cette étape. Ce compte doit disposer du profil **Administrateur système** attribué dans la force de vente.

3. Pour obtenir le jeton de sécurité de votre force de vente, ouvrez un nouvel onglet et la connexion sur le même compte d’administration de force de vente. Dans le coin supérieur droit de la page, cliquez sur votre nom, puis cliquez sur **Mes paramètres**.

    ![Cliquez sur votre nom, puis cliquez sur Mes paramètres][22]

4. Dans le volet de navigation de gauche, cliquez sur **personnel** pour développer la section et puis cliquez sur **Réinitialiser mon jeton de sécurité**.

    ![Cliquez sur votre nom, puis cliquez sur Mes paramètres][23]

5. Sur la page **Réinitialiser mon jeton de sécurité** , cliquez sur le bouton **Réinitialiser le jeton de sécurité** .

    ![Lisez les avertissements.][24]

6. Vérifier la boîte de réception électronique associé au compte admin. Rechercher un message électronique à partir de Salesforce.com qui contient le nouveau jeton de sécurité.

7. Copiez le jeton, accédez à votre fenêtre Azure AD et collez-le dans le champ de **Jeton de sécurité utilisateur** . Puis cliquez sur **suivant**.

    ![Coller dans le jeton de sécurité][25]

8. Sur la page de confirmation, vous pouvez choisir de recevoir des notifications par courrier électronique pour lors de la mise en service des défaillances se produisent. Cliquez sur **Terminer** pour fermer la boîte de dialogue.

    ![Tapez votre adresse de courriel pour recevoir des notifications][26]

##<a name="step-4-assign-users-to-salesforce"></a>Étape 4 : Assigner des utilisateurs à la force de vente

1. Pour tester votre configuration, commencez par créer un nouveau compte de test dans le répertoire.

2. Sur la page de démarrage rapide de force de vente, cliquez sur le bouton **Affecter des utilisateurs** .

    ![Cliquez sur affecter des utilisateurs][27]

3. Sélectionnez votre utilisateur de test, puis cliquez sur le bouton **affecter** au bas de l’écran :

 - Si vous n’avez pas activer mise en service de l’utilisateur automatisées, vous verrez le message suivant pour confirmer :

        ![Confirm the assignment.][28]

 - Si vous avez activé l’approvisionnement automatisé de l’utilisateur, vous verrez une invite de commandes pour définir le type de profil de la force de vente que l’utilisateur doit avoir. Les utilisateurs qui vient d’être mis en service doivent apparaître dans votre environnement de la force de vente après quelques minutes.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Si vous sont la mise en service dans un environnement de **développeur** de force de vente, vous aurez un nombre très limité de licences disponibles pour chaque profil. Par conséquent, il est préférable à disposition des utilisateurs pour le profil **d’Utilisateur de libre Chatter** a 4,999 licences disponibles.

4. Pour tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès à [https://myapps.microsoft.com](https://myapps.microsoft.com/), puis ouvrir une session sur le compte de test, cliquez sur la **force de vente**.

##<a name="related-articles"></a>Articles connexes

- [Index des articles pour la gestion d’Application dans Azure Active Directory](active-directory-apps-index.md)
- [Liste des didacticiels sur la façon d’intégrer les applications SaaS](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
