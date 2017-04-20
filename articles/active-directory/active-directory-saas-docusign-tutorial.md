<properties
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec DocuSign | Microsoft Azure"
    description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et DocuSign."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Didacticiel : Intégration d’Azure Active Directory avec DocuSign

L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et DocuSign.
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

- Un abonnement Azure valide
- Un locataire dans DocuSign



Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1. [L’activation de l’intégration de l’application pour DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configuration de l’authentification unique](#configuring-single-sign-on) 


3. [Configuration de la mise en service de compte](#configuring-account-provisioning) 


4. [Affectation d’utilisateurs](#assigning-users) 

    ![Configuration de l’authentification unique][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>L’activation de l’intégration de l’application pour DocuSign

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour DocuSign, effectuez les opérations suivantes :

1. Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Configuration de l’authentification unique][1]

2. Dans la liste répertoire, sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Configuration de l’authentification unique][2]

4. Cliquez sur **Ajouter** au bas de la page.

    ![Applications][3]

5. Sur la page que voulez-vous faire de boîte de dialogue, cliquez sur **Ajouter une application à partir de la galerie**.

    ![Configuration de l’authentification unique][4]


6. Dans la zone Rechercher, tapez **DocuSign**.

    ![Configuration de l’authentification unique][5]

7. Dans le volet de résultats, sélectionnez **DocuSign**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![Configuration de l’authentification unique][6]


## <a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de DocuSign avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1. Dans Azure portal classique, sur la page de **l’intégration de l’application DocuSign** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue Configurer Single Sign On.

    ![Configuration de l’authentification unique][7]

2. Dans la page **Comment voulez-vous que les utilisateurs à se connecter à DocuSign** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur Suivant.

    ![Configuration de l’authentification unique][8]

3. Dans la page **Configurer les paramètres d’application** , effectuez les opérations suivantes :

    ![Configuration de l’authentification unique][61]

    une barre d’outils. Dans la zone de texte **URL de session** , tapez `https://account.docusign.com/*`.  

    b. Dans la zone **identificateur** , tapez `https://account.docusign.com/*`.  
   
    c. Cliquez sur **suivant**. 


    > [AZURE.TIP] Les valeurs d’identificateur et de l’URL de connexion sont uniquement des espaces réservés. Instructions expliquant comment extraire les valeurs réelles de votre environnement est couvertes plus loin dans cette rubrique.
 

4. Dans la page **configuration de l’authentification unique à DocuSign** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat local sur votre ordinateur.

    ![Configuration de l’authentification unique][10]


5. Dans une fenêtre de navigateur web différent, ouvrez une session dans votre **portail d’administration de DocuSign** en tant qu’administrateur.


6. Dans le menu de navigation sur la gauche, cliquez sur **les domaines**.

    ![Configuration de l’authentification unique][51]

7. Dans le volet droit, cliquez sur **Domaine de revendication**.

    ![Configuration de l’authentification unique][52]

8. Dans la boîte de dialogue **demande un domaine** dans la zone de texte **Nom de domaine** , tapez le domaine de votre société, puis cliquez sur **demande**. Assurez-vous que vous vérifiez le domaine et le statut est actif.

    ![Configuration de l’authentification unique][53]

9. Dans le menu sur le côté gauche, cliquez sur **Fournisseurs d’identité**  

    ![Configuration de l’authentification unique][54]

10. Dans le volet droit, cliquez sur **Ajouter fournisseur d’identité**. 
    
    ![Configuration de l’authentification unique][55]

11. Dans la page **Paramètres de fournisseur d’identité** , effectuez les opérations suivantes :

    ![Configuration de l’authentification unique][56]


    une barre d’outils. Dans la zone de texte **nom** , tapez un nom unique pour votre configuration. Veuillez ne pas utiliser d’espaces.

    b. Dans le portail classique Azure, copiez l’URL de l’émetteur et puis la coller dans la zone de texte **d’Émetteur de fournisseur d’identité** .

    c. Dans le portail Azure classique, copiez l' **URL de connexion d’accès à distance**et puis la coller dans la zone de texte **URL connexion du fournisseur d’identité** .

    d. Dans le portail classique Azure, copiez l' **URL de déconnexion à distance**et puis la coller dans la zone de texte **URL déconnexion du fournisseur d’identité** .

    e. Sélectionnez la **Demande d’authentification de connexion**.

    f. En tant que **demande d’envoyer un seul par**, sélectionnez **Valider**.

    g. **Envoyer la demande de déconnexion par**, sélectionnez **Valider**. 


12. Dans la section **Mappage d’attribut personnalisé** , cliquez sur le champ que vous souhaitez mapper avec demande de publicité Azure. Dans cet exemple, la demande **emailaddress** est mappée avec la valeur de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. C’est le nom de la demande par défaut à partir d’AD Azure pour la revendication de courrier électronique. 

    > [AZURE.NOTE] **Identificateur de l’utilisateur** approprié permet de mapper l’utilisateur à partir d’AD Azure au mappage d’utilisateur Docusign. Sélectionnez le champ approprié et entrez la valeur appropriée en fonction des paramètres de votre organisation.

    ![Configuration de l’authentification unique][57]

13. Dans la section **Certificat d’identité du fournisseur** , cliquez sur **Ajouter un certificat**et ensuite télécharger le certificat que vous avez téléchargé à partir du portail classique de publicité Azure.   

    ![Configuration de l’authentification unique][58]

14. Cliquez sur **Enregistrer**.

15. Dans la section **Fournisseurs d’identité** , cliquez sur **Actions**, puis cliquez sur les **points de terminaison**.   

    ![Configuration de l’authentification unique][59]



10. Sur le portail Azure classique, revenez à la page **Configurer les paramètres d’application** . 

16. Sur le **portail d’administration de DocuSign**, dans la section **Points de terminaison de vue SAML 2.0** effectuez, les opérations suivantes :

    ![Configuration de l’authentification unique][60]

    une barre d’outils. Copiez l' **URL du fournisseur de Service de l’émetteur**et puis la coller dans la zone de texte **identificateur** sur Azure portal classique.

    b. Copiez l' **URL de connexion du fournisseur de Service**et ensuite coller dans la zone de texte **URL de connexion** sur le portail classique Azure.

    c.  Cliquez sur **Fermer**  


10. Sur le portail Azure classique, cliquez sur **suivant**. 


15. Sur le portail Azure classique, sélectionnez la **confirmation de la configuration d’ouverture de session unique**, puis cliquez sur **suivant**.

    ![Applications][14]

10. Sur la page de **confirmation d’ouverture de session unique** , cliquez sur **Terminer**.

    ![Applications][15]
 

## <a name="configuring-account-provisioning"></a>Configuration de la mise en service de compte

L’objectif de cette section doit décrire comment activer l’approvisionnement de l’utilisateur des comptes d’utilisateurs Active Directory DocuSign.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1. Dans **Azure portal classique**, sur la page de **l’intégration de l’application DocuSign** , cliquez sur **configurer la mise en service de compte** pour ouvrir la boîte de dialogue Configurer la mise en service des utilisateurs.

    ![Configuration de la mise en service de compte][30]

2. Sur la page de **paramètres et les informations d’identification d’administration** , fournir les informations d’identification d’un compte de DocuSign avec des droits suffisants pour permettre la mise en service automatique de l’utilisateur et puis cliquez sur **suivant**. 

    ![Configuration de la mise en service de compte][31]

3. Dans la boîte de dialogue **Test de connexion** , cliquez sur **Démarrer le test**et après un test réussi, cliquez sur **suivant**.

    ![Configuration de la mise en service de compte][32]

3. Sur la page de **Confirmation** , cliquez sur **Terminer**.

    ![Configuration de la mise en service de compte][33]
 

## <a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Pour affecter des utilisateurs à DocuSign, effectuez les opérations suivantes :

1. Dans **Azure portal classique**, créez un compte de test.

2. Sur la page de **l’intégration de l’application DocuSign** , cliquez sur **affecter des utilisateurs**.

    ![Affectation d’utilisateurs][40]
 

3. Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Affectation d’utilisateurs][41]


Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé à DocuSign.

Dans un premier temps de vérification, vous pouvez vérifier le statut de l’approvisionnement, en cliquant sur le tableau de bord dans la D sur la page d’intégration DocuSign application dans Azure portal classique.

![Affectation d’utilisateurs][42]

Un cycle de configuration d’utilisateur terminé est indiqué par un statut liés :

![Affectation d’utilisateurs][43]


Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès.

Pour plus d’informations sur le panneau d’accès, consultez Introduction au panneau d’accès.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png