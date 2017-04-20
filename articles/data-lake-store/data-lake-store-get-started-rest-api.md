<properties 
   pageTitle="Mise en route de magasin de LAC de données à l’aide des API REST | Microsoft Azure" 
   description="WebHDFS autres API permet d’effectuer des opérations sur le magasin de données lac" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Mise en route de magasin de LAC de données Azure à l’aide des API de repos

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Dans cet article, vous apprendrez comment utiliser WebHDFS reste les API et données lac magasin reste à effectuer la gestion des comptes ainsi que les opérations de système de fichiers sur le lac Azure Data Store. Magasin de LAC de données Azure expose sa propre API reste pour les opérations de gestion de compte. Toutefois, parce que le magasin lac de données est compatible avec l’écosystème très et Hadoop, il prend en charge à l’aide des API de reste de WebHDFS pour les opérations de système de fichiers.

>[AZURE.NOTE] Pour plus d’informations sur l’API REST prise en charge pour le magasin de données lac, voir [Référence de l’API Azure données lac magasin reste](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Conditions préalables

- **Abonnement d’un Azure**. Consultez [Azure d’obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. L’application Azure annonce vous permet d’authentifier l’application magasin de LAC des données avec Azure AD. Il existe différentes approches pour authentifier avec AD Azure, qui sont **l’authentification de l’utilisateur final** ou **service-service**. Pour plus d’informations sur l’authentification et les instructions, voir [authentifier avec le magasin de données LAC avec Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [coin](http://curl.haxx.se/). Cet article utilise le roulage pour montrer comment effectuer des appels d’API REST par rapport à un compte de banque de données lac.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifient à l’aide d’Azure Active Directory ?

Vous pouvez utiliser deux approches pour s’authentifier à l’aide d’Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Authentification de l’utilisateur final (interactive)

Dans ce scénario, l’application invite l’utilisateur à se connecter et toutes les opérations sont effectuées dans le contexte de l’utilisateur. Pour l’authentification interactive, effectuez les opérations suivantes.

1. Par l’intermédiaire de votre application, rediriger l’utilisateur vers l’URL suivante :

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<URI de redirection > doit être codée pour une utilisation dans une URL. Ainsi, pour https://localhost, utilisez `https%3A%2F%2Flocalhost`)

    Pour ce didacticiel, vous pouvez remplacer les valeurs d’espace réservé dans l’URL ci-dessus et collez-le dans la barre d’adresses d’un navigateur web. Vous allez être redirigé pour s’authentifier à l’aide de votre nom de connexion Azure. Une fois que vous vous connectez avec succès, la réponse est affichée dans la barre d’adresse du navigateur. La réponse sera au format suivant :
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capturer le code d’autorisation de la réponse. Pour ce didacticiel, vous pouvez copier le code d’autorisation dans la barre d’adresse du navigateur web et passez dans la demande POST sur le point de terminaison de jeton, comme indiqué ci-dessous :

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Dans ce cas, le \<redirection-URI > ne doivent pas être codé.

3. La réponse est un objet JSON qui contient un jeton d’accès (par exemple, `"access_token": "<ACCESS_TOKEN>"`) et un jeton d’actualisation (par exemple, `"refresh_token": "<REFRESH_TOKEN>"`). Votre application utilise le jeton d’accès lors de l’accès le lac Azure Data Store et le jeton d’actualisation pour obtenir un autre jeton d’accès lors de l’expiration d’un jeton d’accès.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Lorsque le jeton d’accès arrive à expiration, vous pouvez demander un nouveau jeton d’accès en utilisant le jeton d’actualisation, comme indiqué ci-dessous :

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Pour plus d’informations sur l’authentification de l’utilisateur interactif, consultez [code d’autorisation accorder des flux](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Authentification de service-service (non interactif)

Dans ce scénario, le l’application fournit ses propres informations d’identification pour effectuer les opérations. Pour ce faire, vous devez émettre une demande POST, similaire à celui illustré ci-dessous. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Le résultat de cette requête inclut un jeton d’autorisation (indiqué par `access-token` dans la sortie ci-dessous) qui vous passe par la suite à vos appels d’API REST. Enregistrer ce jeton d’authentification dans un fichier texte ; vous aurez besoin plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la **non interactif** approche. Pour plus d’informations sur la non interactif (appels de service-service), reportez-vous à la section [Service aux appels de service à l’aide des informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Créer un compte de banque de données lac

Cette opération est basée sur l’API REST appel défini [ici](https://msdn.microsoft.com/library/mt694078.aspx).

Utilisez la commande cURL suivant. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment. La charge utile de demande de cette commande est contenue dans le fichier **input.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json de ressembler à ceci :

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Créer des dossiers dans un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilisez la commande cURL suivant. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment. Cette commande crée un répertoire appelé **mytempdir** sous le dossier racine de votre compte de banque de données lac.

Vous devez voir une réponse comme suit si l’opération se termine avec succès :

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Liste des dossiers dans un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilisez la commande cURL suivant. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment.

Vous devez voir une réponse comme suit si l’opération se termine avec succès :

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Charger les données dans un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Téléchargement des données à l’aide de l’API REST de WebHDFS d’est un processus en deux étapes, comme expliqué ci-dessous.

1. Envoyer une demande HTTP PUT sans envoyer les données du fichier à télécharger. Dans la commande suivante, remplacez ** \<yourstorename >** avec le nom de votre banque de données lac.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    La sortie de cette commande être contient une URL de redirection temporaire, comme celui illustré ci-dessous.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Vous devez désormais envoyer une autre demande HTTP PUT par rapport à l’URL indiquée pour la propriété de **l’emplacement** dans la réponse. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    Le résultat sera semblable au suivant :

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Lecture de données à partir d’un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Lecture de données à partir d’un magasin de LAC données compte est un processus en deux étapes.

* D’abord vous envoyez une demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Cette opération retourne un emplacement pour envoyer la demande GET suivante à.
* Puis envoyer une demande GET sur le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Le contenu du fichier s’affiche.

Toutefois, comme il n’existe aucune différence entre les paramètres d’entrée entre la première et la deuxième étape, vous pouvez utiliser la `-L` pour soumettre la demande de premier paramètre. `-L`option essentiellement regroupe deux demandes en une seule et rendra la courbure doit refaire la demande sur le nouvel emplacement. Enfin, la sortie de tous les appels de demande s’affiche, comme illustré ci-dessous. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Vous devriez voir une sortie semblable à la suivante :

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Renommer un fichier dans un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Pour renommer un fichier, utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Vous devriez voir une sortie semblable à la suivante :

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Supprimer un fichier à partir d’un compte de banque de données lac

Cette opération est basée sur l’API REST de WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Pour supprimer un fichier, utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Vous devez voir une sortie comme suit :

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Supprimer un compte de banque de données lac

Cette opération est basée sur l’API REST appel défini [ici](https://msdn.microsoft.com/library/mt694075.aspx).

Utilisez la commande cURL suivante pour supprimer un compte de banque de données lac. Remplacer ** \<yourstorename >** avec le nom de votre banque de données lac.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Vous devez voir une sortie comme suit :

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Voir aussi

- [Ouvrir des applications de données Source compatibles avec magasin de LAC de données Azure](data-lake-store-compatible-oss-other-applications.md)
 
