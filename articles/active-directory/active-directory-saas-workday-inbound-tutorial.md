<properties 
    pageTitle="Didacticiel : Configuration de journée de travail pour la synchronisation entrante | Microsoft Azure" 
    description="Apprenez à utiliser la journée de travail en tant que source de données d’identité pour Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Didacticiel : Configuration de journée de travail de synchronisation entrante


L’objectif de ce didacticiel est de vous montrer les étapes que vous devez effectuer cette opération dans la journée de travail et AD Azure pour importer des personnes de la journée de travail vers Azure AD. 

Le scénario décrit dans ce didacticiel suppose que vous ayez les éléments suivants :

-   Un abonnement Azure AD Premium valide
-   Un locataire dans une journée de travail
  
Le scénario décrit dans ce didacticiel comprend les éléments suivants :

1. L’activation de l’intégration de l’application de la journée de travail 


2. Création d’un utilisateur de l’intégration système 


3. Création d’un groupe de sécurité 


4. Affectation de l’utilisateur système d’intégration au groupe de sécurité 


5. Configuration des options de groupe de sécurité 


6. Activation des modifications de stratégie de sécurité 


7. Configuration importation des utilisateurs dans Active Directory Azure 



##<a name="enabling-the-application-integration-for-workday"></a>L’activation de l’intégration de l’application de la journée de travail
  
L’objectif de cette section doit décrire comment activer l’intégration de l’application de la journée de travail.

### <a name="steps"></a>Étapes suivantes :

1.  Dans le portail Azure classique, dans le volet de navigation de gauche, cliquez sur **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3.  Pour ouvrir la vue des applications, dans la vue du répertoire, cliquez sur **Applications** dans le menu supérieur.

    ![Applications] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applications")

4.  Cliquez sur **Ajouter** au bas de la page.

    ![Ajouter application] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Ajouter application")

  
5. Dans la zone Rechercher, tapez **journée de travail**.

    ![Ajouter une application à partir de gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Ajouter une application à partir de gallerry")

6. Dans le volet de résultats, sélectionnez la journée de travail, puis cliquez sur Terminer pour ajouter l’application.

    ![Galerie des applications] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Galerie des applications")





## <a name="creating-an-integration-system-user"></a>Création d’un utilisateur de l’intégration système

### <a name="steps"></a>Étapes suivantes :


1. Dans l' **Atelier de la journée de travail**, tapez créer un utilisateur dans la zone Rechercher, puis cliquez sur **Créer un utilisateur intégration système**. 

    ![Créer un utilisateur] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Créer un utilisateur")



2. Exécuter la tâche de **Créer un utilisateur intégration système** en fournissant un nom d’utilisateur et le mot de passe pour un nouvel utilisateur de système d’intégration.  Laissez nécessitent nouveau mot de passe à l’option de connexion suivante est désactivé, dans la mesure où cet utilisateur va se connecter par programme. Laissez les Minutes de délai d’expiration de Session avec sa valeur par défaut de 0, ce qui empêchera les sessions de l’utilisateur expirent prématurément. 

    ![Créer l’utilisateur de l’intégration système] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Créer l’utilisateur de l’intégration système")
 




## <a name="creating-a-security-group"></a>Création d’un groupe de sécurité

Pour le scénario décrit dans ce didacticiel, vous devez créer un groupe de sécurité sans contrainte d’intégration système et lui affecter l’utilisateur.

### <a name="steps"></a>Étapes suivantes :

1. Entrez créer un groupe de sécurité dans la zone Rechercher, puis cliquez sur **Créer un groupe de sécurité**. 

    ![Groupe de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Groupe de CreateSecurity")
 

2. Exécuter la tâche créer un groupe de sécurité.  Sélectionnez le groupe de sécurité intégration système — pas limités dans la liste déroulante Type de groupe de sécurité avec clients, pour créer un groupe de sécurité à laquelle les membres seront explicitement ajoutés. 

    ![Groupe de CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Groupe de CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>Affectation de l’utilisateur système d’intégration au groupe de sécurité

### <a name="steps"></a>Étapes suivantes :


1. Entrez modifier le groupe de sécurité dans la zone Rechercher, puis cliquez sur **Modifier le groupe de sécurité**. 

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifier le groupe de sécurité")
 
 

2. Recherchez et sélectionnez le groupe de sécurité d’intégration par nom. 

    ![Modifier le groupe de sécurité] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifier le groupe de sécurité")

 

3. Ajouter le nouvel utilisateur de système d’intégration dans le nouveau groupe de sécurité. 

    ![Groupe de sécurité du système] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Groupe de sécurité du système")  




## <a name="configuring-security-group-options"></a>Configuration des options de groupe de sécurité

Dans cette étape, vous accordez pour les nouvelles autorisations de groupe de sécurité pour les opérations **Get** et **Put** sur les objets déposés par les stratégies de sécurité de domaine suivants :

- Mise en service de compte externe

- Données de travail : Rapports de travail Public

- Travail des données : Tous les postes

- Traitement des données : En cours dotation en personnel des informations

- Données de travail : Votre profession sur un profil de travailleur

 
### <a name="steps"></a>Étapes suivantes :

1. Entrez les stratégies de sécurité de domaine dans la zone de recherche et puis cliquez sur le lien, les stratégies de sécurité de domaine pour le domaine fonctionnel.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Stratégies de sécurité de domaine")  
 

2. Pour un système de recherche et sélectionnez la zone fonctionnelle du **système** .  Cliquez sur **OK**.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Stratégies de sécurité de domaine")  


3. Dans la liste des stratégies de sécurité pour la zone fonctionnelle du système, développez Administration de la sécurité, puis sélectionnez la stratégie de sécurité de domaine, le service de compte externe.  

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Stratégies de sécurité de domaine")  


4. Cliquez sur **Modifier les autorisations**, et ensuite, sur la page de la boîte de dialogue **Modifier les autorisations**, ajouter le nouveau groupe de sécurité à la liste des groupes de sécurité disposant d’autorisations d’intégration **Get** et **Put** . 

    ![Autorisation de modification] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Autorisation de modification")  

 

5. Répétez l’étape 1, ci-dessus, pour revenir à l’écran pour sélectionner les domaines fonctionnels, et cette fois, la recherche de dotation en personnel, sélectionnez la zone fonctionnelle du recrutement et cliquez sur **OK**.

    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Stratégies de sécurité de domaine")  
 

6. Dans la liste des stratégies de sécurité pour la zone fonctionnelle du recrutement, développer des données d’un collaborateur : personnel et répétez l’étape 4 ci-dessus pour chacun de ces autres stratégies de sécurité :

     - Données de travail : Rapports de travail Public

     - Travail des données : Tous les postes

     - Traitement des données : En cours dotation en personnel des informations

     - Données de travail : Votre profession sur un profil de travailleur


    ![Stratégies de sécurité de domaine] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Stratégies de sécurité de domaine")  







## <a name="activating-security-policy-changes"></a>Activation des modifications de stratégie de sécurité

### <a name="steps"></a>Étapes suivantes :

1. Entrez activer dans la zone Rechercher, puis cliquez sur le lien Activer de modifications de stratégie de sécurité en attente. 

    ![Activer] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Activer") 
 

2. Commencer la tâche Activer les modifications en attente sécurité stratégie en entrant un commentaire à des fins d’audit, puis cliquez sur **OK**. 

    ![Activation en attente] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Activation en attente")   
 

3. Terminer la tâche dans l’écran suivant en cochant la case intitulée confirmer, puis cliquez sur **OK**. 

    ![Activation en attente] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Activation en attente")  





## <a name="configuring-user-import-in-azure-ad"></a>Configuration importation des utilisateurs dans Active Directory Azure

L’objectif de cette section doit décrire comment configurer AD Azure pour importer des utilisateurs à partir de la journée de travail.


### <a name="steps"></a>Étapes suivantes :


1. Sur la page intégration d’application **journée de travail** , cliquez sur **Importer de l’utilisateur de configurer** pour ouvrir la boîte de dialogue **Configurer la mise en service** .


2. Dans la page **paramètres et les informations d’identification de l’administrateur** , effectuez les opérations suivantes, puis cliquez sur **suivant**: 

    ![Les paramètres et les informations d’identification de l’administrateur] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Les paramètres et les informations d’identification de l’administrateur")  
 
    une barre d’outils. Dans la zone Nom de jour de travail admin utilisateur, tapez le nom de l’utilisateur que vous avez créé dans la création d’une section utilisateur du système intégration.

    b. Dans la zone de mot de passe admin de journée de travail, tapez le mot de passe de l’utilisateur que vous avez créé dans la création une section utilisateur du système intégration.

    c. Dans la zone URL du locataire journée de travail, tapez l’URL ou les clients de votre journée de travail.


3. Sur la page de **Test de connexion** , cliquez sur **Démarrer le test** pour vérifier la connectivité, puis cliquez sur **suivant**. 

    ![Tester la connexion] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Tester la connexion")  
 

4. Sur la page d’options de **déploiement** , cliquez sur **suivant**. 

    ![Options de mise en service] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Options de mise en service")


5. Dans la boîte de dialogue **Démarrer la mise en service** , cliquez sur **terminé**. 

    ![Démarrer la mise en service] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Démarrer la mise en service")
 

Vous pouvez maintenant passer à la section **utilisateurs** et vérifier si l’utilisateur de votre journée de travail a été importée.



## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste des didacticiels sur la façon d’intégrer les applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Quel est l’accès de l’application et de l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
