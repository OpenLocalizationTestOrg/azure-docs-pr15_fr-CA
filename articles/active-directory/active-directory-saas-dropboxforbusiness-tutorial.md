<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec échange pour entreprise | Microsoft Azure" 
    description="Apprenez à utiliser la boîte de dépôt pour entreprises avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Didacticiel : Intégration d’Azure Active Directory avec boîte de dépôt pour les entreprises
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et d’échange pour les entreprises.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client test dans le dossier d’échange pour les entreprises
  
À la fin de ce didacticiel, les utilisateurs AD Azure affectées au dossier d’échange pour l’entreprise sera en mesure d’ouverture de session unique dans l’application à votre dossier d’échange pour les entreprises de la société site (service fournisseur initiée signe), ou à l’aide de l' [Introduction dans le panneau d’accès](active-directory-saas-access-panel-introduction.md).
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration des applications d’échange pour les entreprises
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scénario")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>L’activation de l’intégration des applications d’échange pour les entreprises
  
L’objectif de cette section doit décrire comment activer l’intégration des applications d’échange pour les entreprises.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Pour activer l’intégration des applications d’échange pour les entreprises, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **boîte de dépôt pour les entreprises**.

    ![Galerie des applications] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez le **dossier d’échange pour les entreprises**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Boîte de dépôt pour les entreprises] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Boîte de dépôt pour les entreprises")

##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès d’échange pour les entreprises avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

Dans le cadre de cette procédure, vous devez télécharger un certificat codé en base 64 de votre dossier d’échange pour les clients de l’entreprise. Si vous n’êtes pas familiarisé avec cette procédure, reportez-vous à la section [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **boîte de dépôt pour les entreprises** , cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à la boîte de dépôt pour les entreprises** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , effectuez les opérations suivantes :

    une barre d’outils. Ouverture de session sur votre boîte de dépôt pour les clients de l’entreprise. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "De configurer l’authentification unique")

    b. Dans le volet de navigation sur le côté gauche, cliquez sur **Console Administrateur**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "De configurer l’authentification unique")

    c. Sur la **Console d’administration**, cliquez sur **authentification** dans le volet de navigation de gauche. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "De configurer l’authentification unique")

    d. Dans la section **de l’authentification unique** , sélectionnez **Activer l’authentification unique**, puis cliquez sur **plus** pour développer cette section.  

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "De configurer l’authentification unique")

    e. Copiez l’URL en regard **d’utilisateurs peuvent se connecter en entrant leur adresse e-mail ou ils peuvent atteindre directement**. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "De configurer l’authentification unique")

    f. Sur le portail Azure classique, dans la zone URL de **connexion boîte de dépôt pour les entreprises** , collez l’URL. 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "De configurer l’authentification unique")  



4. Dans la page **configuration de l’authentification unique au dossier d’échange pour l’entreprise** , cliquez sur **Télécharger le certificat**et puis enregistrez le fichier de certificat sur votre ordinateur.  

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "De configurer l’authentification unique")


5. Dans votre boîte de dépôt pour les clients d’entreprise, dans la section **de l’authentification unique** de la page **d’authentification** , effectuez les opérations suivantes : 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "De configurer l’authentification unique")

    une barre d’outils. Cliquez sur **obligatoire**.

    b. Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au dossier d’échange pour les entreprises** , copier la valeur de **l’URL de la page connexion** et puis la coller dans la zone de texte **URL de connexion** .


    c. Créer un fichier **codé en Base 64** de votre certificat téléchargé. 

    > [AZURE.TIP] Pour plus d’informations, consultez [comment convertir un certificat binaire dans un fichier texte](http://youtu.be/PlgrzUZ-Y1o).


    d. Cliquez sur le bouton **« Choisir un certificat »** et accédez à votre **fichier de certificat de codé en base 64**.


    e. Cliquez sur **« Enregistrer »** pour terminer la configuration de votre dossier d’échange pour les clients de l’entreprise.


6. Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** . 

    ![De configurer l’authentification unique] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "De configurer l’authentification unique")



##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
L’objectif de cette section doit décrire comment activer l’approvisionnement de l’utilisateur des comptes d’utilisateurs Active Directory pour le dossier d’échange pour les entreprises.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1. Dans le portail Azure classique, sur la page intégration d’application **boîte de dépôt pour les entreprises** , cliquez sur **configurer approvisionnement de l’utilisateur** pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** .

2. Activer utilisateur approvisionnement à échange de page de l’entreprise, en cliquant sur Activer approvisionnement de l’utilisateur pour ouvrir la connexion au dossier d’échange pour créer un lien avec la boîte de dialogue annonce Azure.  

    ![Mise en service de l’utilisateur] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Mise en service de l’utilisateur")

3. Dans la boîte de dialogue **se connecter à un dossier d’échange pour créer un lien avec l’annonce Azure** , vous connecter à votre dossier d’échange pour les clients de l’entreprise. 

    ![Mise en service de l’utilisateur] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Mise en service de l’utilisateur")



4. Cliquez sur **Autoriser** pour accorder Azure AD pour l’accès au dossier d’échange. 

    ![Mise en service de l’utilisateur] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Mise en service de l’utilisateur")



5. Pour terminer la configuration, cliquez sur le bouton **Terminer** .  

    ![Mise en service de l’utilisateur] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Mise en service de l’utilisateur")




##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Pour affecter des utilisateurs à un dossier d’échange pour les entreprises, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **boîte de dépôt pour les entreprises **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Oui")
  


Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé pour la boîte de dépôt pour les entreprises.

Dans un premier temps de vérification, vous pouvez vérifier le statut de l’approvisionnement, en cliquant sur **tableau de bord** dans la page d’intégration application **échange pour entreprise** sur Azure Portal classique.

![Affecter des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Affecter des utilisateurs")


Un cycle de configuration d’utilisateur terminé est indiqué par un statut liés.

![Affecter des utilisateurs] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Affecter des utilisateurs")


Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès.
Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)