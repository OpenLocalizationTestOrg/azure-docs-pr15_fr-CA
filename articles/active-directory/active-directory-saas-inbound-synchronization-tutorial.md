<properties 
    pageTitle="Didacticiel : Configuration de journée de travail pour la synchronisation entrante | Microsoft Azure" 
    description="Découvrez comment utiliser entrée de synchronisation avec Azure Active Directory pour activer l’ouverture de session unique, la mise en service automatique et bien plus encore !" 
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
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Didacticiel : Configuration de journée de travail de synchronisation entrante
>[AZURE.NOTE]Azure Premium de Active Directory (AD) est disponible pour les clients en Chine à l’aide de l’instance dans le monde d’Azure AD.    
Azure AD Premium n’est pas pris en charge dans le service Microsoft Azure exploité par 21Vianet en Chine.    

L’objectif de ce didacticiel est de vous montrer les étapes que vous devez effectuer cette opération dans la journée de travail et Microsoft Azure AD pour importer des personnes à partir de la journée de travail à Microsoft Azure AD.    
 Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :  

-   Un abonnement Azure valide  
-   Un locataire dans une journée de travail  

Le scénario décrit dans ce didacticiel comprend les éléments suivants :  

1.  L’activation de l’intégration de l’application de la journée de travail  
2.  Création d’un utilisateur de l’intégration système  
3.  Création d’un groupe de sécurité  
4.  Affectation de l’utilisateur système d’intégration au groupe de sécurité  
5.  Configuration des options de groupe de sécurité  
6.  Activation des modifications de stratégie de sécurité  
7.  Configuration de l’importation dans Microsoft Azure AD utilisateur  

##<a name="enabling-the-application-integration-for-workday"></a>L’activation de l’intégration de l’application de la journée de travail

L’objectif de cette section doit décrire comment activer l’intégration de l’application de la force de vente.    

###<a name="to-enable-the-application-integration-for-workday-perform-the-following-steps"></a>Pour activer l’intégration de l’application de la journée de travail, effectuez les opérations suivantes :

1.  Dans le portail de gestion Azure, dans le volet de navigation de gauche, cliquez sur **Active Directory**.    

    ![Active Directory] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700993.png "Active Directory")  

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.    

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.    

    ![Applications] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700994.png "Applications")  

4.  Pour ouvrir la **Bibliothèque de l’Application**, cliquez sur **Ajouter une application**et puis cliquez sur **Ajouter une application de mon entreprise à utiliser**.    

    ![Ce que vous voulez faire ?] (./media/active-directory-saas-inbound-synchronization-tutorial/IC700995.png "Ce que vous voulez faire ?")  

5.  Dans la **zone Rechercher**, tapez la **journée de travail**.    

    ![Journée de travail] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701021.png "Journée de travail")  

6.  Dans le volet de résultats, sélectionnez la **journée de travail**, puis cliquez sur **Terminer** pour ajouter l’application.    

    ![Journée de travail] (./media/active-directory-saas-inbound-synchronization-tutorial/IC701022.png "Journée de travail")  

##<a name="creating-an-integration-system-user"></a>Création d’un utilisateur de l’intégration système

1.  Dans l' **Atelier de la journée de travail**, entrez **créer l’utilisateur** dans la zone de recherche et puis cliquez sur le lien, **Créez les utilisateur d’intégration système**.     

    ![Création d’utilisateur] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750979.png "Création d’utilisateur")  

2.  Exécuter la tâche de créer un utilisateur intégration système en fournissant un nom d’utilisateur et le mot de passe pour un nouvel utilisateur de système d’intégration.  Laissez nécessitent nouveau mot de passe à l’option de connexion suivante est désactivé, dans la mesure où cet utilisateur va se connecter par programme.    
    Laissez les Minutes de délai d’expiration de Session avec sa valeur par défaut de 0, ce qui empêchera les sessions de l’utilisateur expirent prématurément.    

    ![Créer l’utilisateur de l’intégration système] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750980.png "Créer l’utilisateur de l’intégration système")  

##<a name="creating-a-security-group"></a>Création d’un groupe de sécurité

Pour le scénario décrit dans ce didacticiel, vous devez créer un groupe de sécurité sans contrainte d’intégration système et lui affecter l’utilisateur.    

1.  Entrez créer un groupe de sécurité dans la zone Rechercher, puis cliquez sur le lien, créez un groupe de sécurité.     

    ![Groupe de CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750981.png "Groupe de CreateSecurity")  

2.  Exécuter la tâche créer un groupe de sécurité.  Sélectionnez le groupe de sécurité intégration système — pas limités dans la liste déroulante Type de groupe de sécurité avec clients, pour créer un groupe de sécurité à laquelle les membres seront explicitement ajoutés.     

    ![Groupe de CreateSecurity] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750982.png "Groupe de CreateSecurity")  

##<a name="assigning-the-integration-system-user-to-the-security-group"></a>Affectation de l’utilisateur système d’intégration au groupe de sécurité

1.  Entrez modifier le groupe de sécurité dans la zone Rechercher, puis cliquez sur le lien **Modifier le groupe de sécurité**.     

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750983.png "Modifier le groupe de sécurité")  

2.  Recherchez et sélectionnez le groupe de sécurité d’intégration par nom    

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750984.png "Modifier le groupe de sécurité")  

3.  Ajouter le nouvel utilisateur de système d’intégration dans le nouveau groupe de sécurité.       

    ![Groupe de sécurité du système] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750985.png "Groupe de sécurité du système")  

##<a name="configuring-security-group-options"></a>Configuration des options de groupe de sécurité

Dans cette étape, vous accordez pour les nouvelles autorisations de groupe de sécurité pour les opérations Get et Put sur les objets déposés par les stratégies de sécurité de domaine suivants :  

-   Mise en service de compte externe  
-   Données de travail : Rapports de travail Public  
-   Travail des données : Tous les postes  
-   Traitement des données : En cours dotation en personnel des informations  
-   Données de travail : Votre profession sur un profil de travailleur  

&nbsp;  

1.  Entrez les stratégies de sécurité de domaine dans la zone de recherche et puis cliquez sur le lien, les stratégies de sécurité de domaine pour le domaine fonctionnel.     

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750986.png "Stratégies de sécurité de domaine")  

2.  Pour un système de recherche et sélectionnez la zone fonctionnelle du système.  Cliquez sur le bouton étiqueté, OK.     

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750987.png "Stratégies de sécurité de domaine")  

3.  Dans la liste des stratégies de sécurité pour la zone fonctionnelle du système, développez Administration de la sécurité, puis sélectionnez la stratégie de sécurité de domaine, le service de compte externe.     

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750988.png "Stratégies de sécurité de domaine")  

4.  Cliquez sur le bouton Modifier les autorisations, et ensuite, sur l’écran Modifier les autorisations, ajouter le nouveau groupe de sécurité à la liste des groupes de sécurité disposant d’autorisations d’intégration Get et Put.     

    ![Autorisation de modification] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750989.png "Autorisation de modification")  

5.  Répétez l’étape 1, ci-dessus, pour revenir à l’écran pour sélectionner les domaines fonctionnels, et cette fois, la recherche de dotation en personnel, sélectionnez la zone fonctionnelle du recrutement et cliquez sur le bouton étiqueté, OK.    

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750990.png "Stratégies de sécurité de domaine")  

6.  Dans la liste des stratégies de sécurité pour la zone fonctionnelle du recrutement, développer des données d’un collaborateur : personnel et répétez l’étape 4 ci-dessus pour chacun de ces autres stratégies de sécurité :    

    -   Données de travail : Rapports de travail Public  
    -   Travail des données : Tous les postes  
    -   Traitement des données : En cours dotation en personnel des informations  
    -   Données de travail : Votre profession sur un profil de travailleur    

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750991.png "Stratégies de sécurité de domaine")  

##<a name="activating-security-policy-changes"></a>Activation des modifications de stratégie de sécurité

1.  Entrez activer dans la zone Rechercher, puis cliquez sur le lien Activer de modifications de stratégie de sécurité en attente.    

    ![Activer] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750992.png "Activer")  

2.   Commencer la tâche de modifications de stratégie de sécurité en attente activer par saisie d’un commentaire à des fins d’audit, puis en cliquant sur le bouton étiqueté, OK.      

    ![Activation en attente] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750993.png "Activation en attente")  

3.  Terminer la tâche dans l’écran suivant en cochant la case intitulée confirmation et en cliquant sur le bouton étiqueté, OK.     

    ![Activation en attente] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750994.png "Activation en attente")  

##<a name="configuring-user-import-in-microsoft-azure-ad"></a>Configuration de l’importation dans Microsoft Azure AD utilisateur

L’objectif de cette section doit décrire comment configurer Microsoft Azure AD pour importer des utilisateurs à partir de la journée de travail.    

###<a name="to-configure-user-import-in-microsoft-azure-ad-perform-the-following-steps"></a>Pour configurer l’importation de l’utilisateur dans Microsoft Active Directory Azure, effectuez les opérations suivantes :

1.  Sur la page intégration d’application **journée de travail** , cliquez sur **Importer de l’utilisateur de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service** .    

2.  Dans la page **paramètres et les informations d’identification de l’administrateur** , effectuez les opérations suivantes, puis cliquez sur suivant :    

    ![Les paramètres et les informations d’identification de l’administrateur] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750995.png "Les paramètres et les informations d’identification de l’administrateur")    

    1.  Dans la zone de texte **nom d’utilisateur admin journée de travail** , tapez le nom de l’utilisateur que vous avez créé dans la section [Création d’un utilisateur de l’intégration système](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    2.  Dans la zone de texte **mot de passe administrateur journée de travail** , tapez le mot de passe de l’utilisateur que vous avez créé dans la section [Création d’un utilisateur de l’intégration système](https://msdn.microsoft.com/library/azure/Dn762434.aspx#BKMK_CreateUser) .    
    3.  Dans la zone de texte **URL du locataire journée de travail** , tapez l’URL ou les clients de votre journée de travail.    

3.  Sur la page de **Test de connexion** , cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **suivant**.    

    ![Tester la connexion] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750996.png "Tester la connexion")  

4.  Dans la page **options de provisionnement** , cliquez sur **suivant**.    

    ![Options de mise en service] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750997.png "Options de mise en service")  

5.  Dans la boîte de dialogue **Démarrer la mise en service** , cliquez sur **terminé**.    

    ![Démarrer la mise en service] (./media/active-directory-saas-inbound-synchronization-tutorial/IC750998.png "Démarrer la mise en service")  

Vous pouvez maintenant passer à la section **utilisateurs** et vérifier si l’utilisateur de votre journée de travail a été importée.    
