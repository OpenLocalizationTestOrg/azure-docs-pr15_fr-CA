<properties 
   pageTitle="Gestionnaire de base de données SQL Azure via le portail Azure | Microsoft Azure" 
   description="Le Gestionnaire de base de données SQL Azure dans Azure portal vous permet de consulter et de mettre en œuvre des recommandations pour les bases de données SQL existantes qui peuvent améliorer les performances de la requête en cours." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Gestionnaire de base de données SQL via le portail Azure

> [AZURE.SELECTOR]
- [Présentation de l’Assistant base de données SQL](sql-database-advisor.md)
- [Portail](sql-database-advisor-portal.md)

Le Gestionnaire de base de données SQL Azure dans Azure portal vous permet de consulter et de mettre en œuvre des recommandations pour les bases de données SQL existantes qui peuvent améliorer les performances de la requête en cours.

## <a name="viewing-recommendations"></a>Affichage des recommandations

La page de recommandations est où vous permet d’afficher les recommandations en fonction de leur impact potentiel pour améliorer les performances. Vous pouvez également afficher le statut des opérations historiques. Sélectionnez une recommandation ou un état pour afficher plus de détails.

Pour afficher et appliquer les recommandations, vous devez les autorisations de [contrôle d’accès par rôle](../active-directory/role-based-access-control-configure.md) dans Azure. **Lecteur**, **Collaborateur de base de données SQL** autorisations est requises pour les recommandations relatives aux vues et le **propriétaire**, les autorisations de **Collaborateur de base de données SQL** sont nécessaires pour exécuter toutes les actions ; créer ou supprimer des index et annuler la création de l’index.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **plus de services** > de**bases de données SQL**, puis sélectionnez votre base de données.
5. Cliquez sur **recommandation de performances** pour afficher des recommandations disponibles pour la base de données sélectionnée.

> [AZURE.NOTE] Pour obtenir des recommandations une base de données doit être sur un jour de l’utilisation et il doit être une activité. Il doit également être cohérente d’une activité. Le Gestionnaire de base de données SQL peut optimiser plus facilement pour les modèles de requête cohérente, qu’il peut pour les pics de taches aléatoires de l’activité. Si les recommandations ne sont pas disponibles, la page de **recommandation de performances** doit fournir un message expliquant pourquoi.

![Recommandations](./media/sql-database-advisor-portal/recommendations.png)

Voici un exemple de recommandation de « Résoudre le problème de schéma » dans le portail Azure.

![Résoudre les problème de schéma](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Recommandations sont triées par leur impact potentiel sur les performances dans les quatre catégories suivantes :

| Impact | Description |
| :--- | :--- |
| Élevé | L’impact des performances plus important doivent fournir des recommandations d’impact élevé. |
| Support | Impact moyen recommandations devraient améliorer les performances, mais pas de manière significative. |
| Faible | Recommandations de faible impact doivent fournir des performances supérieures à celles sans, mais des améliorations peuvent ne pas être significatives. 


### <a name="removing-recommendations-from-the-list"></a>Suppression des recommandations de la liste

Si votre liste de recommandations contient des éléments que vous souhaitez supprimer de la liste, vous pouvez ignorer la recommandation :

1. Sélectionnez une recommandation dans la liste des **recommandations**.
2. Sur la lame de **Détails** , cliquez sur **Ignorer** .


Si vous le souhaitez, vous pouvez ajouter des éléments supprimés à la liste de **recommandations** :

1. La lame de **recommandations** en cliquant sur **vue est ignoré**.
1. Sélectionnez un élément ignoré dans la liste pour afficher ses détails.
1. Éventuellement, cliquez sur **Annuler supprimer** pour ajouter l’index à la liste principale des **recommandations**.



## <a name="applying-recommendations"></a>Application des recommandations

Gestionnaire de base de données SQL vous donne un contrôle total sur la manière dont les recommandations sont activées à l’aide d’une des trois options suivantes : 

- Appliquer les recommandations un à la fois.
- Le Gestionnaire d’accès appliquer automatiquement les recommandations (qui s’applique actuellement aux recommandations d’index uniquement).
- Pour implémenter une recommandation manuellement, exécutez le script T-SQL recommandé par rapport à votre base de données.

Sélectionnez toute recommandation pour afficher ses détails et puis cliquez sur **Afficher le script** pour passer en revue les détails exacts de la création de la recommandation.

La base de données reste en ligne pendant que le Gestionnaire d’accès s’applique à la recommandation--à l’aide du Gestionnaire de base de données SQL jamais utilise une base de données hors connexion.

### <a name="apply-an-individual-recommendation"></a>Appliquer une recommandation individuelle

Vous pouvez vérifier et accepter des recommandations, un à la fois.

1. Sur la lame de **recommandations** , cliquez sur une recommandation.
2. Sur la lame de **Détails** , cliquez sur **Appliquer**.

    ![Appliquer la recommandation](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Activer la gestion automatique des index

Vous pouvez définir le Gestionnaire de base de données SQL pour mettre en œuvre automatiquement les recommandations. Comme les recommandations deviennent disponibles qu’ils sont automatiquement appliquées. Comme avec toutes les opérations d’index gérées par le service si l’impact sur les performances est négatif la recommandation est annulée.

1. Sur la lame de **recommandations** , cliquez sur **automatisation**:

    ![Paramètres du Gestionnaire d’accès](./media/sql-database-advisor-portal/settings.png)

2. Le conseiller la valeur automatiquement les index à **créer** ou **Supprimer** :

    ![Index recommandés](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Exécuter manuellement le script T-SQL recommandé

Sélectionnez toute recommandation et puis cliquez sur **Afficher le script**. Exécutez ce script sur la base de données pour appliquer manuellement la recommandation.

*Les index sont exécutées manuellement ne sont pas surveillés et validés pour l’impact sur les performances par le service* afin qu’il est suggéré que vous surveillez ces index après sa création afin de vérifier qu’ils offrent des gains de performances et ajustement ou les supprimer si nécessaire. Pour plus d’informations sur la création d’index, voir [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>L’annulation de recommandations

Les recommandations qui sont dans un état **en attente**, de **vérification**ou de **succès** peuvent être annulées. Recommandations dont l’état **d’exécution** ne peut pas être annulées.

1. Sélectionnez une recommandation dans la zone de **Paramétrage de l’historique** pour ouvrir la lame de **Détails de recommandations** .
2. Cliquez sur **Annuler** pour abandonner le processus de l’application de la recommandation.



## <a name="monitoring-operations"></a>Surveillance des opérations

Application d’une recommandation ne peut pas arriver instantanément. Le portail fournit des détails sur l’état des opérations de recommandation. Un index pouvant être dans les états possibles sont les suivantes :

| État | Description |
| :--- | :--- |
| En attente | Appliquer la recommandation de commande a été reçue et est planifié pour l’exécution. |
| En cours d’exécution | La recommandation est appliquée. |
| Opération réussie | Recommandation a été appliquée avec succès. |
| Erreur | Une erreur s’est produite au cours du processus de l’application de la recommandation. Cela peut être un problème transitoire, voire un schéma modifier à la table et le script n’est plus valide. |
| Retour | La recommandation a été appliquée, mais il a été jugée non performantes et est en cours de rétablissement automatique. |
| Revenu | La recommandation a été rétablie. |

Cliquez sur une recommandation de processus à partir de la liste pour voir plus de détails :

![Index recommandés](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Rétablissement d’une recommandation

Si vous avez utilisé le Gestionnaire d’accès à appliquer la recommandation (ce qui signifie que vous ne s’est pas exécuté manuellement le script T-SQL) il sera automatiquement rétablie il s’il trouve l’impact sur les performances est négative. Si pour une raison quelconque vous souhaitez tout simplement restaurer une recommandation, vous pouvez effectuer les opérations suivantes :


1. Dans la zone **historique de réglage** , sélectionnez une recommandation appliquée avec succès.
2. Cliquez sur **Rétablir** sur la lame de **Détails de la recommandation** .

![Index recommandés](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Analyse d’impact sur les performances de recommandations d’index

Une fois que les recommandations sont implémentées avec succès (actuellement, les opérations d’index et paramétrer des recommandations de requêtes uniquement) vous pouvez cliquer sur **Aperçu de la requête** sur la lame de détails de recommandation d’ouvrir des [Perspectives sur les performances de requête](sql-database-query-performance.md) et de voir l’impact sur les performances de vos requêtes top.

![Impact sur les performances de moniteur](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Résumé

Gestionnaire de base de données SQL fournit des recommandations pour améliorer les performances de base de données SQL. En fournissant les scripts T-SQL, ainsi que l’individu et entièrement automatique (actuellement index uniquement), le conseiller fournit assistance utile dans l’optimisation de votre base de données et finalement les performances de requête.



## <a name="next-steps"></a>Étapes suivantes

Surveiller vos recommandations et continuer à les appliquer pour affiner les performances. Les charges de travail de base de données sont dynamiques et modifier en continu. Gestionnaire d’accès de la base de données SQL va continuer à surveiller et à fournir des recommandations qui peuvent potentiellement améliorer les performances de votre base de données. 

 - Pour une vue d’ensemble du Gestionnaire de base de données SQL, reportez-vous à la section [Gestionnaire de base de données SQL](sql-database-advisor.md) .
 - Reportez-vous à la section [Perspectives sur les performances de requête](sql-database-query-performance.md) pour en savoir plus sur l’affichage de l’impact sur les performances de vos requêtes top.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Magasin de requête](https://msdn.microsoft.com/library/dn817826.aspx)
- [CRÉATION D’INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
- [Contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-configure.md)






