<properties 
    pageTitle="Didacticiel : Intégration d’Azure Active Directory avec Concur | Microsoft Azure" 
    description="Apprenez à utiliser Concur avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Didacticiel : Intégration d’Azure Active Directory avec Concur  


L’objectif de ce didacticiel est d’afficher l’intégration d’Azure et Concur.  
Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure valide
-   Un locataire dans Concur

Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1.  L’activation de l’intégration de l’application pour Concur
2.  Configuration de l’authentification unique
3.  Configuration d’approvisionnement de l’utilisateur
4.  Affectation d’utilisateurs

![Scénario] (./media/active-directory-saas-concur-tutorial/IC769766.png "Scénario")

>[AZURE.NOTE] La configuration de votre abonnement Concur pour SSO fédéré via SAML est une tâche distincte, que vous devez contacter Concur à effectuer.

##<a name="enabling-the-application-integration-for-concur"></a>L’activation de l’intégration de l’application pour Concur

L’objectif de cette section doit décrire comment activer l’intégration de l’application pour Concur.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Pour activer l’intégration de l’application pour Concur, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour laquelle souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-concur-tutorial/IC700994.png "Applications")

4.  Pour ouvrir la **Bibliothèque de l’Application**, cliquez sur **Ajouter une application**et puis cliquez sur **Ajouter une application de mon entreprise à utiliser**.

    ![Ce que vous voulez faire ?] (./media/active-directory-saas-concur-tutorial/IC700995.png "Ce que vous voulez faire ?")

5.  Dans la **zone Rechercher**, tapez **Concur**.

    ![Galerie des applications] (./media/active-directory-saas-concur-tutorial/IC721727.png "Galerie des applications")

6.  Dans le volet de résultats, sélectionnez **Concur**, puis cliquez sur **Terminer** pour ajouter l’application.

    ![D’accord] (./media/active-directory-saas-concur-tutorial/IC721728.png "D’accord")
##<a name="configuring-single-sign-on"></a>Configuration de l’authentification unique

L’objectif de cette section doit décrire comment permettre aux utilisateurs de s’authentifier auprès de Concur avec leur compte Azure annonce à l’aide de la fédération basée sur le protocole SAML.

>[AZURE.NOTE] La configuration de votre abonnement Concur pour SSO fédéré via SAML est une tâche distincte, que vous devez contacter Concur à effectuer.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Pour configurer l’authentification unique, procédez comme suit :

1.  Dans Azure portal classique, sur la page intégration d’application **Concur **, cliquez **sur Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer Single Sign On** .

    ![De configurer l’authentification unique] (./media/active-directory-saas-concur-tutorial/IC769767.png "De configurer l’authentification unique")

2.  Dans la page **Comment voulez-vous que les utilisateurs à se connecter à Concur** , sélectionnez **Microsoft Azure AD Single Sign-On**, puis cliquez sur **suivant**.

    ![De configurer l’authentification unique] (./media/active-directory-saas-concur-tutorial/IC769768.png "De configurer l’authentification unique")

3.  Dans la page **Configurer l’URL App** , dans la zone de texte **Se concerter URL de connexion** , tapez vos clients concur connexion URL et puis cliquez sur **suivant**: 

    ![Configurer connexion URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurer connexion URL")

4.  Dans la page **configuration de l’authentification unique à Concur** , effectuez les opérations suivantes.

    ![Configurer connexion URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurer connexion URL")

    1.  Cliquez sur Télécharger les métadonnées et le fichier de données de sécurité puis sur votre ordinateur.
    2.  Contactez l’équipe de support Concur pour configurer l’authentification unique pour vos clients.
    3.  Sélectionnez la confirmation de la configuration d’ouverture de session unique, puis cliquez sur **terminé** pour fermer la boîte de dialogue **Configuration de session unique** .  

    >[AZURE.NOTE] La configuration de votre abonnement Concur pour SSO fédéré via SAML est une tâche distincte, que vous devez contacter Concur à effectuer.

##<a name="configuring-user-provisioning"></a>Configuration d’approvisionnement de l’utilisateur

L’objectif de cette section doit décrire comment activer la mise en service de comptes d’utilisateurs Active Directory Concur.

Pour activer les applications du Service des dépenses, il doit être la configuration correcte et l’utilisation d’un profil d’administrateur de Service Web. Ne pas ajouter simplement le rôle Admin de WS à votre profil d’administrateur existant que vous utilisez pour les fonctions administratives de T & E.

Consultants d’accord ou l’administrateur de client doit créer un profil administrateur du Service Web distinct, et l’administrateur de Client doit utiliser ce profil pour les fonctions d’administrateur de Services Web (par exemple, permettant des applications). Ces profils doivent être maintenus séparés à partir de quotidienne T & E profil admin l’administrateur de client (du profil admin T & E ne doit pas avoir le rôle WSAdmin).

Lorsque vous créez le profil à utiliser pour l’activation de l’application, entrez le nom de l’administrateur de client dans les champs du profil utilisateur. Propriété seront attribués au profil. Une fois le profil créé, le client doit se connecter avec ce profil, cliquez sur le bouton «*Activer*» pour une application partenaire dans le menu de Services Web.

Pour les raisons suivantes, cette action ne doit pas être effectuée avec le profil qu’ils utilisent pour l’administration de T & E normale.

1.  Le client doit être celui qui clique sur «*Oui*» sur la fenêtre de la boîte de dialogue qui s’affiche après l’activation d’une application. Cliquez sur confirme que le client est prêt pour l’application du partenaire d’accéder à leurs données, afin que vous ou le partenaire ne cliquez sur ce bouton Oui.
2.  Si un administrateur a activé une application client à l’aide de l’administrateur de T & E profil quitte la société (entraînant le profil qui est désactivé), les applications activées à l’aide de que profil ne fonctionnera pas tant que l’application est activée avec un autre profil WS Admin actif. C’est pourquoi vous êtes supposé pour créer des profils distincts WS Admin.
3.  Si un administrateur quitte la société, le nom associé au profil WS Admin peut être modifié à l’administrateur de remplacement si vous le souhaitez sans impact sur que l’application activée car ce profil n’a pas besoin de désactiver

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Pour configurer la mise en service de l’utilisateur, effectuez les opérations suivantes :

1.  Connectez-vous à vos clients **Concur** .

2.  Dans le menu **Administration** , sélectionnez **Services Web**.

    ![Concur fermier] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur fermier")

3.  Sur le côté gauche, dans le volet de **Services Web** , sélectionnez **Activer l’Application du partenaire**.

    ![Activer l’Application partenaire] (./media/active-directory-saas-concur-tutorial/IC721730.png "Activer l’Application partenaire")

4.  Dans la liste **Activer l’Application** , sélectionnez **Azure Active Directory**, puis cliquez sur **Activer**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Cliquez sur **Oui** pour fermer la boîte de dialogue **Confirmer l’Action** .

    ![Confirmer l’Action] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confirmer l’Action")

6.  Dans le portail Azure classique, sélectionnez **Concur** à partir de la liste des applications pour ouvrir la page de la boîte de dialogue **Concur** .

7.  Pour ouvrir la boîte de dialogue **Configurer la mise en service des utilisateurs** , cliquez sur **configurer approvisionnement de l’utilisateur**.

8.  Entrez le nom d’utilisateur et le mot de passe de votre administrateur Concur, puis cliquez sur **suivant**.

9.  Pour terminer la configuration, sur la page de **Confirmation** , cliquez sur le bouton **Terminer** .

Vous pouvez maintenant créer un compte test, attendez 10 minutes et vérifiez que le compte a été synchronisé à Concur.
##<a name="assigning-users"></a>Affectation d’utilisateurs

Pour tester votre configuration, vous devez accorder les utilisateurs AD Azure que vous souhaitez autoriser à l’aide de l’accès de l’application lui en leur affectant.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Pour affecter des utilisateurs à Concur, effectuez les opérations suivantes :

1.  Dans le portail Azure classique, créez un compte de test.

2.  Sur la page intégration d’application **Concur **, cliquez sur **affecter des utilisateurs**.

    ![Affecter des utilisateurs] (./media/active-directory-saas-concur-tutorial/IC769771.png "Affecter des utilisateurs")

3.  Sélectionnez votre utilisateur de test et cliquez sur **attribuer**, puis cliquez sur **Oui** pour confirmer votre affectation.

    ![Oui] (./media/active-directory-saas-concur-tutorial/IC767830.png "Oui")

Vous devez maintenant attendre 10 minutes et vérifiez que le compte a été synchronisé à Concur.

Si vous souhaitez tester vos paramètres d’ouverture de session unique, ouvrez le panneau d’accès. Pour plus d’informations sur le panneau d’accès, consultez [Introduction au panneau d’accès](active-directory-saas-access-panel-introduction.md).
