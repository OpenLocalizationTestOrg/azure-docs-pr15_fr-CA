## <a name="azure-storage-linked-service"></a>Stockage Azure lié Service

Le **stockage Azure lié service** vous permet de lier un compte de stockage Azure à une usine de données Azure à l’aide de la **clé de compte**. Ainsi, la fabrique de données disposant d’un accès global au stockage Azure. Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de stockage Azure lié.

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| type de | La propriété type doit être définie : **AzureStorage** | Oui |
| connectionString | Indiquez les informations nécessaires pour vous connecter à un stockage Azure pour la propriété connectionString. | Oui |

Consultez l’article suivant pour obtenir la procédure pour affichage/copier la clé de compte pour un stockage Azure : [Affichage, la copie et stockage de régénérer les touches d’accès](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Exemple :**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Sas de stockage Azure lié Service  
Une signature d’accès partagé (SAS) fournit le délégué l’accès à des ressources dans votre compte de stockage. Cela signifie que vous pouvez accorder à qu'un client des autorisations limitées d’objets dans votre compte de stockage pour une période donnée de temps et avec un jeu d’autorisations spécifié sans avoir à partager les touches d’accès de votre compte. Les associations de sécurité sont un URI qui englobe dans ses paramètres de requête toutes les informations nécessaires à un accès authentifié à une ressource de stockage. Pour accéder à des ressources de stockage avec les associations de sécurité, le client doit uniquement pour les associations de sécurité passez à la méthode ou le constructeur approprié. Pour obtenir des informations détaillées sur les associations de sécurité, reportez-vous à la section [des Signatures accès partagé : compréhension du modèle de SAS](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
Le service SAS de stockage Azure lié vous permet de lier un compte de stockage Azure à une usine de données Azure à l’aide d’une Signature de l’accès partagé (SAS). Ainsi, la fabrique de données disposant d’un accès restreint/temps/spécifique à toutes les ressources (blob/conteneur) dans le stockage. Le tableau suivant fournit la description pour les éléments JSON spécifiques au service de SAS de stockage Azure lié. 

| Propriété | Description | Obligatoire |
| :-------- | :----------- | :-------- |
| type de | La propriété type doit être définie : **AzureStorageSas**  | Oui |
| sasUri | Spécifier des URI de Signature de l’accès partagé aux ressources de stockage Azure comme blob, conteneur ou un tableau. Consultez les notes ci-dessous pour plus de détails. | Oui | 


**Exemple :**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Lors de la création d’un **URI de SAS**, en tenant compte des éléments suivants :  

- Usine de données Azure prend en charge uniquement **Service SAS**, pas les associations de sécurité compte. Pour plus d’informations sur ces deux types, consultez [Types de Signatures de l’accès partagé](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- Les **autorisations** en lecture/écriture appropriées doivent être définies sur des objets en fonction de l’utilisation du service lié (lecture, écriture, en lecture-écriture) dans votre usine de données.
- **Délai d’expiration** doit être définie de manière appropriée. Assurez-vous que l’accès aux objets de stockage Azure n’expire pas pendant la période active du pipeline.
- URI doit être créé au niveau du droit conteneur/blob ou Table selon le besoin. Uri SAS d’un blob Azure permet le service Factory de données accéder à ce blob particulier. Un Uri SAS à un conteneur blob Azure permet au service de données usine parcourir les objets BLOB dans ce conteneur. Si vous avez besoin de fournir un accès plus/moins objets ultérieurement, ou mettre à jour de l’URI SAS, pensez à mettre à jour le service lié avec le nouvel URI.   
