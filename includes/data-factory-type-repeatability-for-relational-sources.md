## <a name="repeatability-during-copy"></a>Répétabilité pendant la copie

Lors de la copie des données depuis et vers des magasins relationnels, vous devez conserver la répétabilité à l’esprit pour éviter des résultats inattendus. 

Une section peut être réexécutée automatiquement dans Azure Data Factory en fonction de la stratégie de nouvelles tentatives spécifiée. Nous vous conseillons de définir une stratégie de nouvelle tentative pour se prémunir contre les pannes transitoires. Par conséquent, la répétabilité est un aspect important à prendre en compte pendant le déplacement de données. 

**En tant que source :**

> [AZURE.NOTE] Les exemples suivants concernent les SQL Azure, mais sont applicables à n’importe quel magasin de données qui prend en charge les groupes de données rectangulaire. Il se peut que vous deviez ajuster le **type** de source et de la propriété de **requête** (par exemple : requête au lieu de sqlReaderQuery) pour les données de magasin.   

En règle générale, lors de la lecture à partir de magasins relationnels, vous pouvez lire uniquement les données correspondant à cette tranche. En utilisant les variables WindowStart et WindowEnd disponibles dans Azure Data Factory est un moyen de le faire. Informations sur les variables et les fonctions dans Azure Data Factory ici dans l’article [de planification et de l’exécution](../articles/data-factory/data-factory-scheduling-and-execution.md) . Exemple : 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Cette requête lit des données à partir de 'MyTable' qui tombe dans la plage de durée de tranche. Relancement de cette tranche qui garantisse également toujours ce problème. 

Dans les autres cas, vous souhaiterez peut-être lire l’intégralité du tableau (supposons que pour une seule fois déplacer uniquement) et peuvent définir le sqlReaderQuery comme suit :

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    
