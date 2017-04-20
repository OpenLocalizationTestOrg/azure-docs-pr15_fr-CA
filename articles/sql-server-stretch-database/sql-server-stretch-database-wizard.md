<properties
    pageTitle="Mise en route en exécutant la base de données de l’activer pour étirer Assistant | Microsoft Azure"
    description="Découvrez comment configurer une base de données pour la base de données de l’échelle en exécutant la base de données de l’activer pour étirer Assistant."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>En exécutant la base de données de l’activer pour étirer Assistant Mise en route

Pour configurer une base de données pour la base de données de l’étirement, exécuter la base de données de l’activer pour étirer Assistant.  Cette rubrique décrit les informations que vous entrez et les choix que vous effectuez dans l’Assistant.

Pour en savoir plus sur la base de données de l’échelle, consultez [Extension de base de données](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Ultérieurement, si vous désactivez la base de données de l’échelle, n’oubliez pas que la désactivation d’étirement de base de données pour une table ou une base de données ne supprime pas l’objet distant. Si vous souhaitez supprimer de la table distante ou la base de données distante, vous devez déplacer à l’aide du portail de gestion Azure. Les objets distants continuent à supporter des coûts d’Azure jusqu'à ce que vous les supprimez manuellement. 

## <a name="launch-the-wizard"></a>Lancer l’Assistant

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données sur lequel vous souhaitez activer l’étirement.

2.  Droit\-cliquez sur et sélectionnez les **tâches**, puis **Étirer**et sélectionnez puis sélectionnez **Activer** pour lancer l’Assistant.

## <a name="Intro"></a>Introduction
Passez en revue l’objectif de l’Assistant et les conditions préalables.

Les conditions préalables indispensables sont les suivants :

-   Vous devez être un administrateur pour modifier les paramètres de la base de données.
-   Vous devez avoir un abonnement Microsoft Azure.
-   Votre SQL Server doit être en mesure de communiquer avec le serveur Azure.

![Page d’introduction de l’Assistant base de données d’étirement][StretchWizardImage1]

## <a name="Tables"></a>Sélectionner les tables
Sélectionnez les tables que vous souhaitez activer pour étirer.

Tables avec un grand nombre de lignes s’affichent en haut de la liste triée. Avant que l’Assistant affiche la liste des tables, il les analyse pour les types de données qui ne sont pas actuellement pris en charge par étirement de base de données.

![Sélectionnez la page de l’Assistant base de données de l’étirement de tables][StretchWizardImage2]

|Colonne|Description|
|----------|---------------|
|(sans titre)|La case à cocher de cette colonne pour activer la table sélectionnée pour étirer.|
|**Nom**|Spécifie le nom de la colonne dans la table.|
|(sans titre)|Un symbole de cette colonne peut représenter un avertissement qui ne se\'t vous empêche d’activer la table sélectionnée pour étirer. Il peut également représenter un problème de blocage qui vous empêche de l’activation de la table sélectionnée pour étirer \- , par exemple, étant donné que la table utilise un type de données non pris en charge. Placez le curseur sur le symbole pour afficher des informations supplémentaires dans une info-bulle. Pour plus d’informations, consultez [Limitations pour étirement de base de données](sql-server-stretch-database-limitations.md).|
|**Étiré**|Indique si la table est déjà activée pour étirer.|
|**Effectuer la migration**|Vous pouvez migrer une table entière (**Toute la Table**), ou vous pouvez spécifier un filtre sur une colonne existante dans la table. Si vous souhaitez utiliser une fonction de filtre différents pour sélectionner les lignes à migrer, exécutez l’instruction ALTER TABLE pour spécifier la fonction de filtre lorsque vous quittez l’Assistant. Pour plus d’informations sur la fonction de filtre, reportez-vous à la section [Sélectionner les lignes à migrer à l’aide d’une fonction de filtre](sql-server-stretch-database-predicate-function.md). Pour plus d’informations sur la façon d’appliquer la fonction, reportez-vous à la section [Stretch activer la base de données pour une table](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Lignes**|Spécifie le nombre de lignes dans la table.|
|**Taille (Ko)**|Spécifie la taille de la table dans la base de connaissances.|

## <a name="Filter"></a>Vous pouvez également fournir un filtre de ligne

Si vous souhaitez fournir une fonction de filtre pour sélectionner les lignes à migrer, effectuer les opérations suivantes dans la page **Sélectionner des tables** .

1.  Dans la liste **Sélectionnez les tables que vous souhaitez étirer** , sur **Tout le tableau** dans la ligne de la table. La boîte de dialogue **Sélectionner les lignes à étirer** s’ouvre.

    ![Définir une fonction de filtre][StretchWizardImage2a]

2.  Dans la boîte de dialogue **Sélectionner les lignes à étirer** , sélectionnez **Choisir les lignes**.

3.  Dans le **champ nom**, fournissez un nom pour la fonction de filtre.

4.  Pour la clause **Where** , choisir une colonne de la table, sélectionnez un opérateur et fournir une valeur.

5. Cliquez sur **Vérifier** pour tester la fonction. Si la fonction renvoie les résultats à partir de la table - autrement dit, s’il existe des lignes qui remplissent la condition - pour migrer le test signale une **Réussite**.

    >   [AZURE.NOTE] La zone de texte qui affiche la requête de filtre est en lecture seule. Vous ne pouvez pas modifier la requête dans la zone de texte.

6.  Cliquez sur terminé pour revenir à la page **Sélectionner les tables** .

La fonction de filtre est créée dans SQL Server que lorsque vous avez terminé l’Assistant. En attendant, vous pouvez revenir à la page **Sélectionner les tables** à modifier ou renommer la fonction de filtre.

![Sélectionnez les tables après la définition d’une fonction de filtre][StretchWizardImage2b]

Si vous souhaitez utiliser un autre type de fonction de filtre pour sélectionner les lignes à migrer, effectuez l’une des opérations suivantes.  

-   Quittez l’Assistant et exécutez l’instruction ALTER TABLE pour permettre l’étirement pour la table et spécifier une fonction de filtre. Pour plus d’informations, voir [Activation de la base de données de Stretch pour une table](sql-server-stretch-database-enable-table.md).  

-   Exécutez l’instruction ALTER TABLE pour spécifier une fonction de filtre lorsque vous quittez l’Assistant. Pour les étapes à suivre, voir [Ajouter une fonction de filtre après l’exécution de l’Assistant](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurer le déploiement d’Azure

1.  Connectez-vous à Microsoft Azure avec un compte Microsoft.

    ![Ouvrez une session pour Azure - Assistant de la base de données de l’échelle][StretchWizardImage3]

2.  Sélectionnez l’abonnement Azure existant à utiliser pour la base de données de l’échelle.

3.  Sélectionnez une région Azure.
    -   Si vous créez un nouveau serveur, le serveur est créé dans cette région.  
    -   Si vous avez des serveurs existants dans la région sélectionnée, l’Assistant répertorie les lorsque vous choisissez **les serveurs existants**.

    Pour réduire la latence, choisissez la région Azure dans lequel se trouve votre SQL Server. Pour plus d’informations sur les zones, reportez-vous à la section [Régions d’Azure](https://azure.microsoft.com/regions/).

4.  Spécifiez si vous souhaitez utiliser un serveur existant ou en créer un nouveau serveur Azure.

    Si Active Directory sur votre SQL Server est fédéré avec Azure Active Directory, vous pouvez éventuellement utiliser un compte de service fédérée pour SQL Server pour communiquer avec le serveur Azure. Pour plus d’informations sur la configuration requise pour cette option, reportez-vous à la section [Modifier base de données de définir les Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Créer un nouveau serveur**

        1.  Créer une connexion et un mot de passe pour l’administrateur du serveur.

        2.  Si vous le souhaitez, utiliser un compte de service fédérée pour SQL Server de communiquer avec le serveur Azure.

        ![Créer un nouveau serveur Azure - Assistant de la base de données d’étirement][StretchWizardImage4]

    -   **Serveur existant**

        1.  Sélectionnez le serveur Azure existant.

        2.  Sélectionnez la méthode d’authentification.

            -   Si vous sélectionnez **L’authentification SQL Server**, fournissez la connexion de l’administrateur et le mot de passe.

            -   Sélectionnez **Authentification intégrée à Active Directory** pour utiliser un compte de service fédérée pour SQL Server de communiquer avec le serveur Azure. Si le serveur sélectionné n’est pas intégré à Active Directory de Azure, cette option ne s’affiche pas.

        ![Sélectionnez un serveur Azure existant - Assistant de la base de données d’étirement][StretchWizardImage5]

## <a name="Credentials"></a>Sécuriser les informations d’identification
Vous devez disposer d’une clé principale de base de données pour sécuriser les informations d’identification à base de données de l’étirement utilise pour se connecter à la base de données distante.  

S’il existe déjà une clé principale de base de données, entrez le mot de passe pour celle-ci.  

![Sécuriser la page d’informations d’identification de l’Assistant base de données d’étirement][StretchWizardImage6b]

Si la base de données ne dispose pas d’une clé principale existante, entrez un mot de passe pour créer une clé principale de base de données.  

![Sécuriser la page d’informations d’identification de l’Assistant base de données d’étirement][StretchWizardImage6]

Pour plus d’informations sur la clé principale de base de données, consultez [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx). Pour plus d’informations sur les informations d’identification que l’Assistant crée, consultez [Création de base de données étendue d’informations d’identification (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Sélectionnez l’adresse IP
Utilisez la plage d’adresses IP sous-réseau (recommandé) ou l’adresse IP publique de votre SQL Server, pour créer une règle de pare-feu sur Azure qui vous permet de communiquer de SQL Server avec le serveur Azure.

L’adresse IP ou les adresses que vous fournissez sur cette page indiquent au serveur Azure pour autoriser des données entrantes, les requêtes et opérations de gestion initiées par SQL Server à traverser le pare-feu Azure. L’Assistant ne modifie en rien les paramètres du pare-feu sur le SQL Server.

![Sélectionnez la page d’adresse IP de l’Assistant base de données d’étirement][StretchWizardImage7]

## <a name="Summary"></a>Résumé
Vérifiez que vous avez entré les valeurs et les options que vous avez sélectionné dans l’Assistant et les coûts estimés pour Azure. Sélectionnez **Terminer** pour permettre l’étirement.

![Page de résumé de l’Assistant base de données d’étirement][StretchWizardImage8]

## <a name="Results"></a>Résultats
Examinez les résultats.

Pour surveiller l’état de migration de données, reportez-vous à la section [moniteur et résoudre les problèmes de migration de données (Database Stretch)](sql-server-stretch-database-monitor.md).

![Page de résultats de l’Assistant base de données d’étirement][StretchWizardImage9]

## <a name="KnownIssues"></a>Dépannage de l’Assistant
**Échec de l’Assistant base de données de l’échelle.**
Si la base de données de l’échelle n’est pas encore activé au niveau du serveur, et que vous exécutez l’Assistant sans le système des autorisations d’administrateur pour l’activer, l’Assistant échoue. Demandez à l’administrateur système pour activer l’extension de base de données sur l’instance du serveur local, puis exécutez à nouveau l’Assistant. Pour plus d’informations, consultez [requis : autorisation pour permettre l’étirement de base de données sur le serveur](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Étapes suivantes
Activer les tables supplémentaires pour étirement de base de données. Surveiller la migration des données et gérer l’étirement\-activé des bases de données et de tables.

-   [Activer la base de données Stretch pour une table](sql-server-stretch-database-enable-table.md) permettent de tables supplémentaires.

-   [Moniteur et résoudre les problèmes de migration de données](sql-server-stretch-database-monitor.md) pour connaître l’état de la migration des données.

-   [Suspendre et reprendre la base de données d’étirement](sql-server-stretch-database-pause.md)

-   [Gérer et dépanner l’étirement de base de données](sql-server-stretch-database-manage.md)

-   [Bases de données prenant en charge l’étirement de la sauvegarde](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Voir aussi

[Activer l’extension de base de données pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer l’étirement de base de données d’une table](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png
