<properties 
    pageTitle="Résoudre les problèmes d’Azure Data Factory" 
    description="Découvrez comment résoudre les problèmes liés à l’aide de la fabrique de données Azure." 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>Résolution des problèmes de Data Factory
Cet article fournit des conseils de dépannage pour les problèmes lors de l’utilisation d’Azure Data Factory. Cet article ne répertorie pas tous les problèmes possibles lors de l’utilisation du service, mais il couvre certains problèmes et les conseils de dépannage générales.   

## <a name="troubleshooting-tips"></a>Conseils de dépannage

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>Erreur : L’abonnement n’est pas enregistré pour utiliser l’espace de noms 'Microsoft.DataFactory'
Si vous recevez cette erreur, le fournisseur de ressources Azure Data Factory n’a pas été enregistré sur votre ordinateur. Effectuez le des opérations suivantes : 

1. Lancer PowerShell Azure. 
2. Connectez-vous à votre compte Azure à l’aide de la commande suivante.
        Connexion-AzureRmAccount 
3. Exécutez la commande suivante pour enregistrer le fournisseur Azure Data Factory.
        Registre-AzureRmResourceProvider - ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>Problème : Les erreurs non autorisés lors de l’exécution d’une applet de commande Data Factory
Vous utilisez probablement pas l’abonnement ou le compte Azure droite avec PowerShell Azure. Utiliser les applets de commande suivantes pour sélectionner le compte Azure droit et abonnement à utiliser avec PowerShell Azure. 

1. Connexion-AzureRmAccount - utilisation de l’ID utilisateur et le mot de passe
2. Get-AzureRmSubscription - permet d’afficher tous les abonnements pour le compte. 
3. Sélectionnez-AzureRmSubscription &lt;nom d’abonnement&gt; -sélectionnez l’abonnement. Utiliser le même que celui que vous utilisez pour créer une fabrique de données sur le portail Azure.

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>Problème : Ne pas lancer le programme d’installation Express du passerelle de gestion des données à partir du portail Azure
Le programme d’installation Express de la passerelle de gestion des données nécessite Internet Explorer ou un navigateur compatible Microsoft ClickOnce. Si le programme d’installation Express ne parvient pas à démarrer, effectuez l’une des opérations suivantes : 

- Utilisez Internet Explorer ou un navigateur compatible Microsoft ClickOnce.

    Si vous utilisez Chrome, accédez à la [Boutique web de Chrome](https://chrome.google.com/webstore/), rechercher avec « ClickOnce » mot-clé, choisissez l’une des extensions de ClickOnce et l’installer. 
    
    Procédez de même pour Firefox (complément installation). Cliquez sur le bouton de Menu Ouvrir dans la barre d’outils (trois lignes horizontales dans le coin supérieur droit), cliquez sur modules complémentaires, recherche par mot clé de « ClickOnce », choisissez l’une des extensions de ClickOnce et installez-le. 

- Cliquez sur le lien **Configuration manuelle** figurant sur la même lame dans le portail. Cette approche vous permet de télécharger le fichier d’installation et de l’exécuter manuellement. Une fois que l’installation est terminée, vous voyez la boîte de dialogue de Configuration de gestion des données à l’aide de la passerelle. Copiez la **clé** à partir de l’écran du portail et l’utiliser dans le Gestionnaire de configuration pour enregistrer manuellement la passerelle avec le service.  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>Problème : Échec de connexion au local SQL Server 
Lancer le **Gestionnaire de Configuration de passerelle de gestion des données** sur l’ordinateur passerelle et utilisez l’onglet **dépannage** pour tester la connexion à SQL Server à partir de l’ordinateur passerelle. Voir [passerelle de résoudre les problèmes](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle des problèmes.   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>Problème : Les sections d’entrée sont en état d’attente encore être

Les tranches peuvent être **en attente** pour diverses raisons. Une des raisons courantes est que la propriété **externe** n’est pas définie sur **true**. N’importe quel groupe de données qui est généré à l’extérieur de la portée de la fabrique de données Azure doit être marquée avec la propriété **externe** . Cette propriété indique que les données sont externes et pas sauvegardés par les pipelines dans l’usine de données. Les tranches de données sont marqués comme **prêt** , une fois que les données sont disponibles dans le magasin respectif. 

Voir l’exemple suivant pour l’utilisation de la propriété **externe** . Vous pouvez éventuellement spécifier **externalData*** lorsque vous définissez externe sur true.

Consultez l’article de [groupes de données](data-factory-create-datasets.md) pour plus d’informations sur cette propriété.
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

Pour résoudre cette erreur, ajoutez la propriété **externe** et la section facultative **externalData** à la définition de JSON de la table d’entrée et recréer la table. 

### <a name="problem-hybrid-copy-operation-fails"></a>Problème : Hybride copie échoue
Reportez-vous à la section [résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir la procédure pour résoudre les problèmes avec la copie vers/à partir d’une banque de données locale à l’aide de la passerelle de gestion des données. 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>Problème : HDInsight de la demande Échec de mise en service
Lorsque vous utilisez un service lié du type HDInsightOnDemand, vous devez spécifier un linkedServiceName qui pointe vers un stockage de Blob Azure. Service de fabrique de données utilise ce système de stockage pour stocker les journaux et les fichiers de prise en charge de votre cluster d’HDInsight à la demande.  Parfois la mise en service d’un cluster d’HDInsight à la demande échoue avec l’erreur suivante :

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Cette erreur indique généralement que l’emplacement du compte de stockage spécifié dans le linkedServiceName n’est pas dans le même emplacement de centre de données où la mise en service de HDInsight qui se passe. Exemple : Si votre fabrique de données est en ouest des États-Unis et le stockage Azure dans les États-Unis, le provisionnement à la demande échoue dans l’ouest des États-Unis.

En outre, est une deuxième additionalLinkedServiceNames de propriété JSON où les comptes de stockage supplémentaires peuvent être spécifiés dans la demande HDInsight. Ces comptes de stockage supplémentaires doivent être dans le même emplacement que le cluster HDInsight ou qu’il échoue avec l’erreur même.

### <a name="problem-custom-net-activity-fails"></a>Problème : Personnalisé .NET activité échoue
Pour obtenir la procédure détaillée, voir [Déboguer un pipeline avec une activité personnalisée](data-factory-use-custom-activities.md#debug-the-pipeline) . 

## <a name="use-azure-portal-to-troubleshoot"></a>Azure portal permet de résoudre les problèmes 

### <a name="using-portal-blades"></a>À l’aide de lames de portail
Consultez le [pipeline du moniteur](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline) pour obtenir la procédure. 

### <a name="using-monitor-and-manage-app"></a>À l’aide du moniteur et de gérer des applications
Consultez [moniteur et de gérer les pipelines de fabrique de données à l’aide de surveiller et gérer l’application](data-factory-monitor-manage-app.md) pour plus de détails. 

## <a name="use-azure-powershell-to-troubleshoot"></a>Utilisez Azure PowerShell pour résoudre les problèmes

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>Utilisez PowerShell d’Azure pour résoudre une erreur  
Pour plus d’informations, reportez-vous à la section [Moniteur Data Factory les pipelines à l’aide de PowerShell d’Azure](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline) . 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 