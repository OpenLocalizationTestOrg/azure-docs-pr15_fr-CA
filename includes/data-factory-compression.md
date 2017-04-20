### <a name="compression-support"></a>Prise en charge de la compression  
Traitement des gros volumes de données peut provoquer des goulots d’étranglement d’e/s et le réseau. Par conséquent, les données compressées dans les magasins peuvent non seulement accélérer le transfert des données sur le réseau et économiser l’espace disque, mais mettent également des améliorations significatives des performances dans le traitement des données volumineuses. Actuellement, la compression est pris en charge pour les banques de données de fichiers comme les Blob Azure ou système de fichiers local.  

> [AZURE.NOTE] Paramètres de compression ne sont pas pris en charge pour les données dans le **AvroFormat**, **OrcFormat**ou **ParquetFormat**. 

Pour spécifier la compression pour un groupe de données, utilisez la propriété **compression** dans le groupe de données JSON, comme dans l’exemple suivant :   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  
 
La section **compression** possède deux propriétés :  
  
- **Type :** le codec de compression **GZIP**, **Deflate** ou **BZIP2**.  
- **Niveau :** le taux de compression, ce qui peut être **optimale** ou **plus rapide**. 
    - **Plus rapide :** L’opération de compression doit se terminer aussi rapidement que possible, même si le fichier résultant n’est pas compressé de manière optimale. 
    - **Optimal**: l’opération de compression doit être optimal compressée, même si l’opération prend plus de temps pour terminer. 
    
    Pour plus d’informations, consultez la rubrique de [Niveau de Compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Supposons que l’exemple ci-dessus de dataset est utilisé comme le résultat d’une activité de copie, l’activité de copie compresse les données de sortie avec les codec GZIP à l’aide de rapport optimal et puis écrire les données compressées dans un fichier nommé pagecounts.csv.gz dans le stockage Blob Azure.   

Lorsque vous spécifiez la propriété compression dans un groupe de données d’entrée JSON, le pipeline peut lire les données compressées à partir de la source et lorsque vous spécifiez la propriété dans un dataset de sortie JSON, l’activité de copie peut écrire des données compressées à la destination. Voici quelques exemples de scénarios : 

- Données GZIP en lecture compressés à partir d’un blob Azure, décompresser et écrire des données de résultat à une base de données SQL d’Azure. Vous définissez le groupe de données d’entrée Azure Blob avec la compression propriété JSON dans ce cas. 
- Lire des données à partir d’un fichier de texte brut à partir du système de fichiers local, à l’aide du format GZip compresser et écrire les données compressées dans un blob Azure. Vous définissez un groupe de données Blob Azure sortie avec la propriété JSON de compression dans ce cas.  
- Lecture de données compressé avec GZIP à partir d’un blob Azure, décompresser, compresser à l’aide de BZIP2 et écrire les données de résultat pour un blob Azure. Vous définissez le groupe de données Azure Blob d’entrée avec le type de compression GZIP et le dataset de sortie avec le type de compression dans ce cas la valeur BZIP2.   
