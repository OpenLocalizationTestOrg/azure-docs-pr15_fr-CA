<properties 
    pageTitle="Didacticiel : Intégration de Azure Active Directory avec le logiciel de rallye | Microsoft Azure" 
    description="Apprenez à utiliser des logiciels de fonctionner avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Didacticiel : Intégration de Azure Active Directory avec le logiciel de rallye
  
L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et logiciel de fonctionner.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un client de logiciel de fonctionner
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour fonctionner des logiciels
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scénario")
##<a name="enabling-the-application-integration-for-rally-software"></a>L’activation de l’intégration de l’application pour fonctionner des logiciels
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application pour fonctionner le logiciel.

###<a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour fonctionner le logiciel, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-rally-software-tutorial/IC749321.png "Ajouter application")

5.  Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application à partir de la galerie**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-rally-software-tutorial/IC749322.png "Ajouter une application à partir de gallerry")

6.  Dans la **zone Rechercher**, tapez **rallye**.

    ![Galerie des applications] (./media/active-directory-saas-rally-software-tutorial/IC769526.png "Galerie des applications")

7.  Dans le volet résultats, sélectionnez le **Logiciel de fonctionner**et puis cliquez sur **Terminer** pour ajouter l’application.

    ![Logiciel de fonctionner] (./media/active-directory-saas-rally-software-tutorial/IC769527.png "Logiciel de fonctionner")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique
  
L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier au logiciel de fonctionner avec son compte dans Azure annonce à l’aide de la fédération basée sur le protocole SAML.  
Dans le cadre de cette procédure, vous devez télécharger un certificat au logiciel de fonctionner.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans le portail Azure classique, sur la page intégration d’application **Logiciel de fonctionner **, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC749323.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous aux utilisateurs de se connecter à un congrès** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![Microsoft Azure AD Single Sign-On] (./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **URL de clients logiciels Rally** , tapez l’URL de votre en utilisant le modèle suivant «*https://\<nom du locataire\>. rally.com*», puis cliquez sur **suivant**.

    ![Configurer des URL de l’application] (./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configurer des URL de l’application")

4.  Dans la page **configuration de l’authentification unique au Congrès** , cliquez sur les métadonnées de téléchargement et puis de l’enregistrer sur votre ordinateur.

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC769530.png "De configurer l’authentification unique")

5.  Connectez-vous à vos clients de **Fonctionner le logiciel** .

6.  Dans la barre d’outils dans la partie supérieure, cliquez sur **le programme d’installation**et sélectionnez **abonnement**.

    ![Abonnement] (./media/active-directory-saas-rally-software-tutorial/IC769531.png "Abonnement")

7.  Cliquez sur le bouton **d’Action** dans la barre d’outils en haut à droite, puis sélectionnez **Modifier un abonnement**.

8.  Sur la page de la boîte de dialogue **abonnement** , effectuez les opérations suivantes, puis cliquez sur **Enregistrer & fermer**:

    ![Authentification] (./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentification")

    1.  Sélectionnez **authentification Rally ou de l’authentification unique** à partir de la liste déroulante de l’authentification
    2.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au logiciel de fonctionner** , copiez la valeur de **l’ID de fournisseur d’identité** et puis la coller dans la zone de texte **URL du fournisseur d’identité**
    3.  Dans le portail Azure classique, sur la page de la boîte de dialogue **configuration de l’authentification unique au logiciel de fonctionner** , copiez la valeur **d’URL de déconnexion à distance** .

9.  Sur le portail Azure classique, sélectionnez la confirmation de la configuration d’ouverture de session unique et puis cliquez sur **Terminer** pour fermer la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-rally-software-tutorial/IC769547.png "De configurer l’authentification unique")
##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur
  
DAS aux utilisateurs d’être en mesure de vous connecter, il doivent être configurés pour l’application de fonctionner des logiciels à l’aide de leurs noms d’utilisateur Azure Active Directory.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients de fonctionner le logiciel.

2.  Accédez à **le programme d’installation \> les utilisateurs**, puis cliquez sur **+ Ajouter un nouveau**.

    ![Utilisateurs] (./media/active-directory-saas-rally-software-tutorial/IC781039.png "Utilisateurs")

3.  Tapez le nom dans la zone de texte du nouvel utilisateur, puis cliquez sur **Ajouter, avec les détails**.

4.  Dans la section **Créer un utilisateur** , effectuez les opérations suivantes :

    ![Création d’utilisateur] (./media/active-directory-saas-rally-software-tutorial/IC781040.png "Création d’utilisateur")

    1.  Dans la zone de texte **Nom d’utilisateur** , tapez le nom de l’utilisateur AD Azure que vous souhaitez mettre en service.
    2.  Dans la zone de texte **Adresse de messagerie** , tapez l’adresse de messagerie de l’utilisateur AD Azure que vous souhaitez mettre en service.
    3.  Cliquez sur **Enregistrer & fermer**.

>[AZURE.NOTE]Vous pouvez utiliser n’importe quel autres Rally utilisateur compte création outils logiciels ou API fournie par le logiciel de fonctionner aux comptes d’utilisateurs de disposition DAS.

##<a name="assigning-users"></a>Affectation d’utilisateurs
  
Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>Pour affecter des utilisateurs au logiciel de fonctionner, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Logiciel de fonctionner** , cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-rally-software-tutorial/IC769548.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-rally-software-tutorial/IC767830.png "Oui")
  
Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).




