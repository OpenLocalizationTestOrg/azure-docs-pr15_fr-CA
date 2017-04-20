## <a name="repeatability-during-copy"></a>Répétabilité pendant la copie

Lors de la copie de données vers Azure SQL/SQL Server à partir d’autres données stocke un doit esprit répétabilité afin d’éviter des résultats inattendus. 

Lors de la copie des données à base de données/SQL Server de SQL Azure, activité de copie sera par défaut le jeu de données à la table de récepteur d’ajout par défaut. Par exemple, lors de la copie des données à partir d’une source de fichier CSV (données de valeurs séparées par des virgules) contenant les deux enregistrements à base de données/SQL Server de SQL Azure, c’est ce que la table ressemble à :
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Supposons qu’a détecté des erreurs dans le fichier source et de la mise à jour de la quantité de Tube de vers le bas à partir de 2 à 4 dans le fichier source. Si vous exécutez de nouveau la tranche de données pour cette période, vous trouverez deux nouveaux enregistrements ajoutés à la base de données/SQL Server de SQL Azure. La ci-dessous suppose que les colonnes de la table ont la contrainte de clé primaire.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Pour éviter ce problème, vous devez spécifier la sémantique UPSERT grâce à une de le ci-dessous 2 mécanismes énoncées ci-dessous.

> [AZURE.NOTE] Une section peut être ré-exécutée automatiquement dans Azure Data Factory conformément à la stratégie de nouvelle tentative spécifiée.

### <a name="mechanism-1"></a>Mécanisme de 1

Vous pouvez utiliser la propriété **sqlWriterCleanupScript** pour effectuer tout d’abord les action de nettoyage lorsqu’une tranche est exécutée. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

Le script de nettoyage sera exécuté à la première pendant la copie pour une tranche donnée qui supprime les données de la Table SQL correspondant à cette tranche. L’activité sera ensuite insérer les données dans la Table SQL. 

Si la section est maintenant exécuter une nouvelle fois, puis vous trouverez que la quantité est mis à jour comme vous le souhaitez.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Supposons que l’enregistrement de la rondelle plate est supprimé de la csv d’origine. Exécuter de nouveau la tranche produirait le résultat suivant : 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Rien de nouveau devait être effectué. L’activité de copie a exécuté le script de nettoyage pour supprimer les données correspondantes de cette tranche. Puis il lire les entrées de la csv (qui puis contenues uniquement 1 enregistrement) et de l’insérer dans la Table. 

### <a name="mechanism-2"></a>Mécanisme de 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName n’est pas pris en charge pour l’entrepôt de données de SQL Azure en ce moment. 

Un autre mécanisme pour atteindre la répétabilité est en ayant une colonne dédiée (**sliceIdentifierColumnName**) dans la Table de la cible. Cette colonne peut être utilisée, par Azure Data Factory, pour s’assurer que la source et la destination restent synchronisés. Cette approche fonctionne lorsqu’il y a une grande souplesse dans la modification ou la définition du schéma de Table de SQL de destination. 

Cette colonne peut être utilisée par les Azure Data Factory à des fins de répétabilité et dans le processus Azure Data Factory n’effectuera les modifications de schéma pour la Table. Moyen d’utiliser cette approche :

1.  Définir une colonne de type binary (32) dans la Table de SQL de destination. Il ne convient d’aucuns contraintes sur cette colonne. Nommons-le cette colonne en tant que 'ColumnForADFuseOnly' pour cet exemple.
2.  Utiliser dans l’activité de copie comme suit :

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure Data Factory remplira cette colonne en fonction de ses besoins pour s’assurer que la source et la destination restent synchronisés. Les valeurs de cette colonne ne doivent pas être utilisés en dehors de ce contexte par l’utilisateur. 

Similaire au mécanisme de 1, activité de copie sera automatiquement tout d’abord nettoyer les données pour la section donnée à partir de la destination SQL Table et puis exécutez l’activité copie normalement pour insérer des données à partir de la source vers la destination de cette tranche. 
