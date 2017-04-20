<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory ServiceNow et ServiceNow Express | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et de ServiceNow et de ServiceNow Express."
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
    ms.date="10/27/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-servicenow-and-servicenow-express"></a>Didacticiel : Intégration d’Azure Active Directory ServiceNow et ServiceNow Express.


Dans ce didacticiel, vous allez apprendre à intégrer des ServiceNow et ServiceNow Express avec Azure Active Directory (AD Azure).

Intégration de ServiceNow et ServiceNow Express avec AD Azure vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à ServiceNow et ServiceNow Express
- Vous pouvez activer vos utilisateurs pour automatiquement obtenir signé sur ServiceNow et ServiceNow Express (Single Sign-On) avec leur compte Azure AD
- Vous pouvez gérer vos comptes dans un emplacement central : le portail classique Azure

Si vous souhaitez connaître plus de détails sur l’intégration de l’application SaaS avec AD Azure, consultez [accès aux applications et single sign-on avec Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration de publicités Azure avec ServiceNow et ServiceNow Express, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Pour les clients de ServiceNow, version de Calgary, une instance ou une ServiceNow ou supérieur
- Pour ServiceNow Express, une instance de ServiceNow Express, version de Helsinki ou supérieur
- ServiceNow fermier doit avoir le [Plusieurs fournisseur unique signe de plug-in](http://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0) est activé. Pour ce faire en envoyant une demande de service à <https://hi.service-now.com/> 


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous ne recommandons pas l’utilisation d’un environnement de production.


Pour tester les étapes de ce didacticiel, vous devez suivre ces recommandations :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas un environnement d’essai Azure AD, vous pouvez obtenir un mois d’évaluation [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez Azure AD de l’authentification unique dans un environnement de test. Le scénario décrit dans ce didacticiel se compose de deux blocs de construction principaux :

1. Ajout de ServiceNow à partir de la galerie
2. Configuration et test AD Azure authentification unique pour ServiceNow ou ServiceNow Express


## <a name="adding-servicenow-from-the-gallery"></a>Ajout de ServiceNow à partir de la galerie
Pour configurer l’intégration de ServiceNow ou de ServiceNow Express dans AD Azure, vous devez ajouter ServiceNow à partir de la galerie à la liste des applications gérées de SaaS. 

**Pour ajouter des ServiceNow à partir de la galerie, procédez comme suit :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**. 

    ![Active Directory][1]

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Applications][4]

6. Dans la zone Rechercher, tapez **ServiceNow**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_01.png)

7. Dans le volet de résultats, sélectionnez **ServiceNow**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test AD Azure authentification unique
Dans cette section, vous configurez et test de l’authentification unique de l’annonce Azure avec ServiceNow ou ServiceNow Express basé sur un utilisateur de test appelé « Britta Simon ».

Pour de l’authentification unique fonctionner, AD Azure doit connaître l’utilisateur équivalent dans ServiceNow à un utilisateur dans AD Azure. En d’autres termes, une relation de liaison entre un utilisateur AD Azure et l’utilisateur connexe dans ServiceNow doit être établi.
Ce lien est établi en affectant la valeur du **nom d’utilisateur** dans AD Azure comme valeur du **nom d’utilisateur** dans ServiceNow. Pour configurer et tester AD Azure single sign-on avec ServiceNow, vous devez effectuer les blocs de construction suivantes :

1. **[Configuration d’AD Azure Single Sign-On pour les ServiceNow](#configuring-azure-ad-single-sign-on-for-servicenow)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
2. **[Configuration d’AD Azure Single Sign-On pour les ServiceNow Express](#configuring-azure-ad-single-sign-on-for-servicenow-express)** - pour autoriser les utilisateurs à utiliser cette fonctionnalité.
3. **[Création d’une annonce Azure tester l’utilisateur](#creating-an-azure-ad-test-user)** - tester AD Azure single sign-on avec Britta Simon.
4. **[Création d’un ServiceNow de test utilisateur](#creating-a-servicenow-test-user)** - d’avoir un équivalent de Britta Simon dans ServiceNow qui est lié à la représentation sous forme de publicité Azure de sa.
5. **[Tester l’utilisateur affectant la publicité Azure](#assigning-the-azure-ad-test-user)** - pour activer Britta Simon utilisation de l’authentification unique de l’annonce Azure.
6. **[Test de l’authentification unique](#testing-single-sign-on)** - pour vérifier si la configuration fonctionne.

> [AZURE.NOTE] Si vous voulez configurer ServiceNow omettez l’étape 2. De même, si vous voulez configurer ServiceNow Express omettez l’étape 1.

### <a name="configuring-azure-ad-single-sign-on-for-servicenow"></a>Configuration Azure AD unique Single Sign-On pour les ServiceNow

1.  Dans le portail classique AD Azure, sur la page d’intégration **ServiceNow** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ServiceNow** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer des URL de l’application")

    une barre d’outils. dans la zone de texte **ServiceNow signe d’URL** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur votre application de ServiceNow selon le modèle : `https://<instance-name>.service-now.com`.

    b. Dans la zone **identificateur** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur votre application de ServiceNow selon le modèle : `https://<instance-name>.service-now.com`.

    c. Cliquez sur **suivant**

4.  Pour configurer automatiquement ServiceNow pour l’authentification basée sur les SAML AD Azure, entrez votre nom de l’instance ServiceNow, le nom d’utilisateur admin et le mot de passe administrateur dans le formulaire de **configuration automatique de l’authentification unique** , cliquez sur *configurer*. Notez que le nom d’utilisateur admin fourni doit avoir le rôle **security_admin** dans ServiceNow pour que cela fonctionne. Dans le cas contraire, pour configurer manuellement les ServiceNow pour utiliser des annonces Azure comme un fournisseur d’identité SAML, cliquez sur **configurer l’application pour l’ouverture de session unique**, puis cliquez sur **suivant** et suivez les étapes ci-dessous.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer des URL de l’application")



5.  Dans la page **configuration de l’authentification unique à ServiceNow** , cliquez sur **Télécharger le certificat**, enregistrez le fichier de certificat local sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "De configurer l’authentification unique")

1. Ouverture de session sur votre application de ServiceNow en tant qu’administrateur.
2. Activer le plug-in _d’intégration - plusieurs fournisseur unique ouverture de session d’installation_ en suivant les étapes suivantes :

    une barre d’outils. Dans le volet de navigation sur le côté gauche, accédez à la section de la **Définition de système** et puis cliquez sur **plug-ins**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_03.png "Plug-in d’activation")
    
    b. Recherche _d’intégration - plusieurs fournisseur unique ouverture de session d’installation_.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_04.png "Plug-in d’activation")

    c. Sélectionnez le plug-in. Côtés droit, cliquez sur et sélectionnez **Activer/mise à niveau**.
    
    d. Cliquez sur le bouton **Activer** .

2. Dans le volet de navigation sur le côté gauche, cliquez sur **Propriétés**.  

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_06.png "Configurer des URL de l’application")


3. Dans la boîte de dialogue de **Plusieurs propriétés du fournisseur d’authentification unique** , procédez comme suit :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694981.png "Configurer des URL de l’application")

    une barre d’outils. **Activer plusieurs fournisseur SSO**, sélectionnez **Oui**.

    b. **Activer le fournisseur multiple, intégration de l’authentification unique a obtenu l’enregistrement de débogage**, sélectionnez **Oui**.

    c. Dans la zone de texte **du champ de l’utilisateur qui table...** , tapez **nom_utilisateur**.

    d. Cliquez sur **Enregistrer**.



1. Dans le volet de navigation sur le côté gauche, cliquez sur **x509 de certificats**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_05.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Certificats X.509** , cliquez sur **Nouveau**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694974.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Certificats X.509** , effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "De configurer l’authentification unique")

    une barre d’outils. Cliquez sur **Nouveau**.

    b. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **TestSAML2.0**).

    c. Sélectionnez **actif**.

    d. Sous **Format**, sélectionnez **PEM**.

    e. En tant que **Type**, sélectionnez **Faire confiance à un certificat magasin**.
    
    f. Ouvrez votre certificat codée en Base64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **Certificat PEM** .

    g. Cliquez sur **mise à jour**.


1. Dans le volet de navigation sur le côté gauche, cliquez sur **Fournisseurs d’identité**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_07.png "De configurer l’authentification unique")

1. Dans la boîte de dialogue **Fournisseurs d’identité** , cliquez sur **Nouveau**:

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694977.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue **Fournisseurs d’identité** , cliquez sur **SAML2 clic1 ?**:

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694978.png "De configurer l’authentification unique")


1. Dans la boîte de dialogue Propriétés de clic1 SAML2, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694982.png "De configurer l’authentification unique")


    une barre d’outils. dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **SAML 2.0**).

    b. Dans la zone de texte **Champ utilisateur** , type **courrier électronique** ou **user_id**, selon lequel le champ est utilisé pour identifier les utilisateurs dans votre déploiement de ServiceNow. 
    
    > [AZURE.NOTE] Vous pouvez configue Azure AD pour l’émission de l’ID d’utilisateur AD Azure (nom utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la **ServiceNow > attributs > Single Sign-On** section du portail classique Azure et le mappage du champ souhaité pour l’attribut **nameidentifier** . La valeur stockée pour l’attribut sélectionné dans Azure annonce (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ de saisie (par exemple, user_id)

    c. Dans le portail classique AD Azure, copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte **URL du fournisseur d’identité** .

    d. Dans le portail classique AD Azure, copiez la valeur de **l’URL de la demande d’authentification** et puis la coller dans la zone de texte **du fournisseur d’identité AuthnRequest** .

    e. Dans le portail classique AD Azure, copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **du fournisseur d’identité SingleLogoutRequest** .

    f. Dans la zone de texte **Page d’accueil de ServiceNow** , tapez l’URL de votre page d’accueil de ServiceNow instance.

    > [AZURE.NOTE] La page d’accueil de ServiceNow instance est une concaténation de votre **URL de clients de ServieNow** et de **/navpage.do** (par exemple :`https://fabrikam.service-now.com/navpage.do`).
 

    g. Dans la **ID d’entité / émetteur** zone de texte, tapez l’URL de votre client de ServiceNow.

    h. Dans la zone de texte **URL de public** , tapez l’URL de votre client de ServiceNow. 

    i. Dans la zone **Protocole de liaison pour SingleLogoutRequest du IDP** , entrez **urn : oasis : noms : tc : SAML:2.0:bindings:HTTP-rediriger**.

    j. Dans la zone de texte NameID stratégie, tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : non**.

    k. Désactivez l’option **créer un AuthnContextClass**.

    l. Dans la **Méthode de AuthnContextClassRef**, tapez `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`. Cela n’est nécessaire que si vous êtes la seule organisation du nuage. Si vous utilisez sur site ADFS ou AMF pour l’authentification, vous ne devez pas configurer cette valeur. 

    m. Dans la zone de **L’inclinaison de l’horloge** , tapez **60**.

    n. Comme un **Seul signe de Script**, sélectionnez **MultiSSO_SAML2_Update1**.

    o. En tant que **x509 certificat de**, sélectionnez le certificat que vous avez créé à l’étape précédente.

    p. Cliquez sur **Envoyer**. 



6. Sur le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "De configurer l’authentification unique")

7. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.
 
    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "De configurer l’authentification unique")

### <a name="configuring-azure-ad-single-sign-on-for-servicenow-express"></a>Configuration Azure AD unique Single Sign-On pour les ServiceNow Express

1.  Dans le portail classique AD Azure, sur la page d’intégration **ServiceNow** application, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à ServiceNow** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749324.png "De configurer l’authentification unique")

3.  Dans la page **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC769497.png "Configurer des URL de l’application")

    une barre d’outils. dans la zone de texte **ServiceNow signe d’URL** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur votre application de ServiceNow selon le modèle : `https://<instance-name>.service-now.com`.

    b. Dans la zone de texte **URL de l’émetteur** , tapez l’URL de votre utilisé par vos utilisateurs à l’ouverture de session sur votre application de ServiceNow selon le modèle de `https://<instance-name>.service-now.com`.

    c. Cliquez sur **suivant**

4.  Cliquez sur **configurer l’application pour l’ouverture de session unique**, puis cliquez sur **suivant** et effectuez les étapes suivantes.

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/IC7694971.png "Configurer des URL de l’application")

5.  Dans la page **configuration de l’authentification unique à ServiceNow** , cliquez sur **Télécharger le certificat**, enregistrer le fichier de certificat local sur votre ordinateur, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC749325.png "De configurer l’authentification unique")

6. Ouverture de session sur votre application Express de ServiceNow en tant qu’administrateur.

7. Dans le volet de navigation sur le côté gauche, cliquez sur **Single Sign-On**.  

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/ic7694980ex.png "Configurer des URL de l’application")


8. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur l’icône de configuration dans le coin supérieur droit et définissez les propriétés suivantes :

    ![Configurer des URL de l’application] (./media/active-directory-saas-servicenow-tutorial/ic7694981ex.png "Configurer des URL de l’application")

    une barre d’outils. Activer/désactiver **Activer plusieurs fournisseur SSO** à droite.

    b. Activer/désactiver **Activer l’enregistrement pour l’intégration de l’authentification unique fournisseur multiples de débogage** vers la droite.

    c. Dans la zone de texte **du champ de l’utilisateur qui table...** , tapez **nom_utilisateur**.


9. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur **Ajouter un nouveau certificat**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694973ex.png "De configurer l’authentification unique")



10. Dans la boîte de dialogue **Certificats X.509** , effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694975.png "De configurer l’authentification unique")

    une barre d’outils. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **TestSAML2.0**).

    b. Sélectionnez **actif**.

    c. Sous **Format**, sélectionnez **PEM**.

    d. En tant que **Type**, sélectionnez **Faire confiance à un certificat magasin**.

    e. Créer un fichier encodé en base 64 de votre certificat téléchargé.
   
    > [AZURE.NOTE] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).
    
    f. Ouvrez votre certificat codée en Base64 dans le bloc-notes et copie le contenu de ce dernier dans votre Presse-papiers puis la coller dans la zone de texte de **Certificat PEM** .

    g. Cliquez sur **mise à jour**.


11. Dans la boîte de dialogue **De l’authentification unique** , cliquez sur **Ajouter un nouveau IdP**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694976ex.png "De configurer l’authentification unique")


12. Dans la boîte de dialogue **Ajouter un nouveau fournisseur identité** , sous **Configurer fournisseur d’identité**, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694982ex.png "De configurer l’authentification unique")


    une barre d’outils. Dans la zone de texte **nom** , tapez un nom pour votre configuration (par exemple : **SAML 2.0**).

    b. Dans le portail classique AD Azure, copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte **URL du fournisseur d’identité** .

    c. Dans le portail classique AD Azure, copiez la valeur de **l’URL de la demande d’authentification** et puis la coller dans la zone de texte **du fournisseur d’identité AuthnRequest** .

    d. Dans le portail classique AD Azure, copiez la valeur de **l’URL de Service unique Sign-Out** et puis la coller dans la zone de texte **du fournisseur d’identité SingleLogoutRequest** .

    e. **Certificat d’identité du fournisseur**, sélectionnez le certificat que vous avez créé à l’étape précédente.


13. Cliquez sur **Paramètres avancés**et sous les **Autres propriétés de fournisseur d’identité**, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694983ex.png "De configurer l’authentification unique")

    une barre d’outils. Dans la zone **Protocole de liaison pour SingleLogoutRequest du IDP** , entrez **urn : oasis : noms : tc : SAML:2.0:bindings:HTTP-rediriger**.

    b. Dans la zone de texte **NameID stratégie** , tapez **urn : oasis : noms : tc : SAML:1.1:nameid-format : non**.    

    c. Dans la **Méthode de AuthnContextClassRef**, tapez **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**.
    
    d. Désactivez l’option **créer un AuthnContextClass**.

14. Sous **Propriétés de fournisseur de Service supplémentaires**, effectuez les opérations suivantes :

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/ic7694984ex.png "De configurer l’authentification unique")

    une barre d’outils. Dans la zone de texte **Page d’accueil de ServiceNow** , tapez l’URL de votre page d’accueil de ServiceNow instance.

    > [AZURE.NOTE] La page d’accueil de ServiceNow instance est une concaténation de votre **URL de clients de ServieNow** et de **/navpage.do** (par exemple : `https://fabrikam.service-now.com/navpage.do`).

    b. Dans la **ID d’entité / émetteur** zone de texte, tapez l’URL de votre client de ServiceNow.

    c. Dans la zone de texte **URI d’Audience** , tapez l’URL de votre client de ServiceNow. 

    d. Dans la zone de **L’inclinaison de l’horloge** , tapez **60**.

    e. Dans la zone de texte **Champ utilisateur** , type **courrier électronique** ou **user_id**, selon lequel le champ est utilisé pour identifier les utilisateurs dans votre déploiement de ServiceNow.
    
    > [AZURE.NOTE] Vous pouvez configue Azure AD pour l’émission de l’ID d’utilisateur AD Azure (nom utilisateur principal) ou l’adresse e-mail comme identificateur unique dans le jeton SAML en accédant à la **ServiceNow > attributs > Single Sign-On** section du portail classique Azure et le mappage du champ souhaité pour l’attribut **nameidentifier** . La valeur stockée pour l’attribut sélectionné dans Azure annonce (par exemple, nom d’utilisateur principal) doit correspondre à la valeur stockée dans ServiceNow pour le champ de saisie (par exemple, user_id)

    f. Cliquez sur **Enregistrer**. 


15. Sur le portail classique AD Azure, sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **suivant**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694990.png "De configurer l’authentification unique")

16. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.
 
    ![De configurer l’authentification unique] (./media/active-directory-saas-servicenow-tutorial/IC7694991.png "De configurer l’authentification unique")

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
L’objectif de cette section doit décrire comment activer l’approvisionnement de l’utilisateur des comptes d’utilisateurs Active Directory ServiceNow.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1. Dans le portail classique de gestion d’Azure, sur la page d’intégration **ServiceNow** application, cliquez sur **configurer approvisionnement de l’utilisateur**. 

    ![Mise en service de l’utilisateur] (./media/active-directory-saas-servicenow-tutorial/IC769498.png "Mise en service de l’utilisateur")


2. Dans la page **entrer vos informations d’identification de ServiceNow pour activer la mise en service automatique de l’utilisateur** , indiquez les paramètres de configuration suivants : configurer la mise en service des utilisateurs 

     une barre d’outils. Dans la zone de texte **Nom de l’Instance ServiceNow** , tapez le nom d’occurrence ServiceNow.

     b. Dans la zone de texte **Nom d’utilisateur Admin ServiceNow** , tapez le nom du compte d’administrateur ServiceNow.

     c. Dans la zone de texte **Mot de passe ServiceNow administrateur** , tapez le mot de passe pour ce compte.

     d. Cliquez sur **Valider** pour vérifier votre configuration.

     e. Cliquez sur le bouton **suivant** pour ouvrir la page des **prochaines étapes** .

     f. Si vous souhaitez configurer tous les utilisateurs de cette application, sélectionnez «**provisionnement automatique de tous les comptes d’utilisateurs dans l’annuaire pour cette application**». 

    ![Étapes suivantes] (./media/active-directory-saas-servicenow-tutorial/IC698804.png "Étapes suivantes")

     g. Dans la page **étapes** , cliquez sur **Terminer** pour enregistrer votre configuration.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test AD Azure
Dans cette section, vous créez un utilisateur test dans le portail classique appelé Britta Simon.

![Créer utilisateur d’AD Azure][20]

**Pour créer un utilisateur test dans Azure AD, effectuez les opérations suivantes :**

1. Dans **Azure portal classique**, dans le volet de navigation de gauche, cliquez sur **Active Directory**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_09.png) 

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu de la partie supérieure, cliquez sur **utilisateurs**.
    
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_03.png) 

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur** , dans la barre d’outils dans la partie inférieure, cliquez sur **Ajouter un utilisateur**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_04.png) 

5. Sur la page de dialogue **nous dire sur cet utilisateur** , effectuez les opérations suivantes :
 
    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_05.png) 

    une barre d’outils. En tant que Type d’utilisateur, sélectionnez nouvel utilisateur de votre organisation.

    b. Dans la **zone de texte**nom d’utilisateur, tapez **BrittaSimon**.

    c. Cliquez sur **suivant**.

6.  Sur la page de la boîte de dialogue **Profil d’utilisateur** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_06.png) 

    une barre d’outils. Dans la zone de texte **nom** , tapez **Brian**.  

    b. Dans la zone de texte **Nom** , type, **Simon**.

    c. Dans la zone de texte **Nom complet** , tapez **Brian Simon**.

    d. Dans la liste **rôle** , sélectionnez **utilisateur**.

    e. Cliquez sur **suivant**.

7. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , cliquez sur **créer**.

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_07.png) 

8. Sur la page de boîte de dialogue **obtenir le mot de passe temporaire** , effectuez les opérations suivantes :

    ![Création d’un utilisateur de test AD Azure](./media/active-directory-saas-servicenow-tutorial/create_aaduser_08.png) 

    une barre d’outils. Notez que la valeur de **Nouveau mot de passe**.

    b. Cliquez sur **terminé**.   


### <a name="creating-a-servicenow-test-user"></a>Création d’un utilisateur de test ServiceNow

Dans cette section, vous créez un utilisateur appelé Britta Simon dans ServiceNow. Dans cette section, vous créez un utilisateur appelé Britta Simon dans ServiceNow. Si vous ne connaissez pas l’ajout d’un utilisateur dans votre compte de ServiceNow ou de ServiceNow Express, contactez l’équipe de support technique de ServiceNow.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test AD Azure

Dans cette section, vous activez Britta Simon à utiliser Azure SSO en accordant l’accès à ServiceNow.

![Affecter des utilisateurs][200] 

**Pour faire Britta Simon ServiceNow, effectuez les opérations suivantes :**

1. Sur le portail classique, pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **ServiceNow**.

    ![Configurer l’authentification unique](./media/active-directory-saas-servicenow-tutorial/tutorial_servicenow_10.png) 

1. Dans le menu du haut, cliquez sur **utilisateurs**.

    ![Affecter des utilisateurs][203] 

1. Dans la liste de tous les utilisateurs, sélectionnez **Brian Simon**.

2. Dans la barre d’outils dans la partie inférieure, cliquez sur **attribuer**.

    ![Affecter des utilisateurs][205]


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester votre annonce Azure unique configuration de l’authentification à l’aide du panneau d’accès.

Lorsque vous cliquez sur la mosaïque ServiceNow dans le panneau d’accès, vous devez obtenir automatiquement signé-on à votre application ServiceNow.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-servicenow-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-servicenow-tutorial/tutorial_general_205.png
