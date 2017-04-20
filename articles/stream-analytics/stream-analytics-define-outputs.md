<properties
    pageTitle="Flux des sorties d’Analytique : Options de stockage, analyse | Microsoft Azure"
    description="Obtenir des informations sur le ciblage des options de sorties de données Analytique de flux de données, y compris la BI de puissance pour les résultats de l’analyse."
    keywords="transformation de données, les résultats de l’analyse, les options de stockage de données"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
    documentationCenter="" 
    authors="jeffstokes72"
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Flux des sorties d’Analytique : Options de stockage, analyse

Lors de la création d’une tâche de flux de données Analytique, envisagez comment les données résultantes seront consommées. Comment vous allez afficher les résultats de la tâche de flux de données Analytique et dans lequel vous stockerez il ?

Afin de permettre une variété de modèles d’application, Azure flux Analytique a différentes options de stockage de sortie et d’affichage des résultats de l’analyse. Cela permet de facilement afficher la sortie de la tâche et vous offre une grande souplesse dans la consommation et le stockage de la sortie de la tâche pour l’entreposage de données et d’autres opérations. Toute sortie configuré dans la tâche doit exister avant le démarrage de la tâche et transmission des événements commencent. Par exemple, si vous utilisez le stockage Blob sous la forme d’une sortie, la tâche ne crée pas un compte de stockage automatiquement. Il doit être créé par l’utilisateur avant le démarrage de la tâche d’ASA.

## <a name="azure-data-lake-store"></a>Magasin de LAC de données Azure

Analytique de flux prend en charge [Le lac Azure Data Store](https://azure.microsoft.com/services/data-lake-store/). Ce système de stockage vous permet de vous permet de stocker des données de n’importe quelle vitesse de taille, type et ingestion pour analytique exploratoires et opérationnels. À ce stade, création et configuration de magasin de données lac sorties est pris en charge uniquement dans le portail classique d’Azure. En outre, flux Analytique doit être autorisé pour accéder au magasin de LAC de données. Pour plus d’informations sur l’autorisation et l’ouverture de l’aperçu de magasin de données Lake (si nécessaire) sont décrits dans l' [article de sortie de données lac](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autoriser un magasin lac de données Azure

Lorsque le stockage de données lac est sélectionné sous la forme d’une sortie dans le portail de gestion d’Azure, le système vous demandera d’autoriser une connexion à un magasin de LAC de données existant.  

![Autoriser le magasin de données lac](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Renseignez ensuite les propriétés de la sortie du magasin de données lac comme indiqué ci-dessous :

![Autoriser le magasin de données lac](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Le tableau ci-dessous répertorie les noms de propriétés et de leur description nécessaires à la création d’une sortie de magasin de données lac.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger la sortie de requête vers le magasin de données lac dans les requêtes.</td>
</tr>
<tr>
<td>Nom de compte</td>
<td>Le nom du compte de stockage de données lac où vous envoyez votre sortie. S’affiche avec une liste de comptes de banque de données lac auquel l’utilisateur connecté au portail a accès à liste déroulante.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe modèle [<I>facultatif</I>]</td>
<td>Le chemin d’accès du fichier utilisé pour écrire les fichiers dans le compte de banque de données lac spécifié. <BR>{date}, {time}<BR>Exemple 1 : folder1/logs / {date} / {time}<BR>Exemple 2 : folder1/logs / {date}</td>
</tr>
<tr>
<td>Format de date [<I>facultatif</I>]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans laquelle les fichiers sont organisés. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [<I>facultatif</I>]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès de préfixe, spécifiez le format dans lequel les fichiers sont organisés. Actuellement, la seule valeur prise en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation d’événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le seul format de codage pris en charge pour l’instant.</td>
</tr>
<tr>
<td>Séparateur</td>
<td>Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation de données CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. Ligne séparée Spécifie que la sortie sera formatée en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau spécifie que la sortie sera formatée comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renouvellement de l’autorisation de magasin de données lac

Vous devrez ré-authentifier votre compte de banque de données lac si son mot de passe a été modifié depuis la création ou de dernière authentification de votre travail.

![Autoriser le magasin de données lac](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Base de données SQL

[Base de données de SQL Azure](https://azure.microsoft.com/services/sql-database/) peut servir d’une sortie pour les données relationnelles dans la nature ou pour les applications qui dépendent du contenu hébergé dans une base de données relationnelle. Les tâches de flux de données Analytique écrit dans une table existante dans une base de données de SQL Azure.  Notez que le schéma de table doit correspondre exactement les champs et leurs types de sortie à partir de votre projet. Un [Entrepôt de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) peut également être spécifié sous la forme d’une sortie via la base de données de SQL option de sortie également (il s’agit d’une fonctionnalité d’aperçu). Le tableau ci-dessous répertorie les noms de propriété et leur description pour la création d’une sortie de base de données SQL.

| Nom de la propriété | Description |
|---------------|-------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger la sortie de requête vers cette base de données dans les requêtes. |
| Base de données | Le nom de la base de données où vous envoyez votre sortie |
| Nom du serveur | Le nom du serveur de base de données SQL |
| Nom d’utilisateur | Le nom d’utilisateur qui a l’accès en écriture à la base de données |
| Mot de passe | Le mot de passe pour vous connecter à la base de données |
| Table | Le nom de la table dans laquelle la sortie sera écrite. Le nom de table respecte la casse et le schéma de cette table doit correspondre exactement au nombre de champs et leurs types générés par la sortie de votre projet. |

> [AZURE.NOTE] L’offre de base de données de SQL Azure est actuellement pris en charge pour une sortie de tâche dans le flux de données Analytique. Toutefois, un ordinateur virtuel de Azure exécutant SQL Server avec une base de données attachée n’est pas pris en charge. Cela peut être modifiée dans les futures versions.

## <a name="blob-storage"></a>Stockage des objets BLOB

Stockage des objets BLOB offre une solution économique et évolutive pour stocker de grandes quantités de données non structurées dans le nuage.  Pour une introduction sur le stockage des objets Blob Azure et de son utilisation, consultez la documentation sur [l’utilisation des objets BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

Le tableau ci-dessous répertorie les noms de propriété et leur description pour la création d’une sortie blob.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à ce stockage blob dans les requêtes.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où vous envoyez votre sortie.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>La clé secrète avec le compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique des objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un objet blob dans le service d’objet Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe modèle [facultatif]</td>
<td>Le chemin d’accès du fichier utilisé pour écrire le BLOB dans le conteneur spécifié.<BR>Dans le chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances de 2 variables suivantes pour spécifier la fréquence à laquelle les objets BLOB est écrits :<BR>{date}, {time}<BR>Exemple 1 : cluster1/logs / {date} / {time}<BR>Exemple 2 : cluster1/logs / {date}</td>
</tr>
<tr>
<td>Format de date [facultatif]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans laquelle les fichiers sont organisés. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [facultatif]</td>
<td>Si le jeton de l’heure est utilisé dans le chemin d’accès de préfixe, spécifiez le format dans lequel les fichiers sont organisés. Actuellement, la seule valeur prise en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation d’événement</td>
<td>Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le seul format de codage pris en charge pour l’instant.</td>
</tr>
<tr>
<td>Séparateur</td>
<td>Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation de données CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Format</td>
<td>Applicable uniquement pour la sérialisation JSON. Ligne séparée Spécifie que la sortie sera formatée en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau spécifie que la sortie sera formatée comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Concentrateur d’événements

[Concentrateurs d’événement](https://azure.microsoft.com/services/event-hubs/) est hautement évolutive de publication-abonnement ingestor de l’événement. Il peut collecter des millions d’événements par seconde.  Une utilisation d’un concentrateur d’événements en tant que sortie est lors de la sortie d’une tâche de flux de données Analytique sera l’entrée d’un autre travail en continu.

Il existe quelques paramètres qui sont nécessaires pour configurer des flux de données d’événement concentrateur sous la forme d’une sortie.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à ce concentrateur d’événements dans les requêtes. |
| Namespace de Bus de service | Bus de Service d’un espace de noms est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événements, vous a également créé un espace de noms du Bus des services |
| Concentrateur d’événements | Le nom de votre sortie de concentrateur d’événements |
| Nom de concentrateur des événements à l’aide de la stratégie | La stratégie d’accès partagé, qui peut être créée sous l’onglet configurer de concentrateur d’événement. Chaque stratégie d’accès partagé aura un nom, les autorisations que vous définissez, et accéder aux clés |
| Clé de concentrateur des événements à l’aide de la stratégie | La clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms du Bus des services |
| Colonne de clé de partition [facultatif] | Cette colonne contienne la clé de partition pour la sortie du concentrateur de l’événement. |
| Format de sérialisation d’événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | CSV et JSON, UTF-8 est le seul format de codage pris en charge pour l’instant |
| Séparateur | Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation des données au format CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |
| Format | Applicable uniquement pour le type JSON. Ligne séparée Spécifie que la sortie sera formatée en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau spécifie que la sortie sera formatée comme un tableau d’objets JSON. |

## <a name="power-bi"></a>Alimentation BI

[Alimentation BI](https://powerbi.microsoft.com/) peut servir d’une sortie pour une tâche de flux de données Analytique de prévoir une expérience riche de visualisation des résultats de l’analyse. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, génération de rapports et métrique par rapport.

### <a name="authorize-a-power-bi-account"></a>Autoriser un compte BI de puissance

1.  Lorsque l’alimentation BI est sélectionnée sous la forme d’une sortie dans le portail de gestion d’Azure, un message vous demandera d’autoriser un utilisateur de BI d’alimentation existant ou pour créer un nouveau compte d’alimentation BI.  

    ![Autoriser l’utilisateur BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Créer un nouveau compte si vous ne pas encore avoir un, puis cliquez sur Autoriser.  Un écran semblable à la suivante s’affiche.  

    ![Compte Azure alimentation BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  Dans cette étape, fournir le compte de l’école ou de travail pour autoriser la sortie d’alimentation BI. Si vous n'êtes pas déjà inscrit pour le décisionnel de l’alimentation, cliquez sur connexion maintenant. Le compte de l’école ou de travail que vous utilisez pour alimentation BI peut être différent du compte d’abonnement Azure qui vous êtes actuellement connecté à l’aide de.

### <a name="configure-the-power-bi-output-properties"></a>Configurer les propriétés de sortie d’alimentation BI

Une fois le compte de la puissance BI authentifié, vous pouvez configurer les propriétés de votre sortie de puissance BI. Le tableau ci-dessous est la liste des noms de propriétés et leur description pour configurer votre sortie de puissance BI.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial utilisé dans les requêtes pour diriger la sortie de requête à ce PowerBI. |
| Espace de travail de groupe | Pour activer le partage de données avec d’autres utilisateurs de puissance BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte courant BI ou cliquez sur « Mon espace de travail » si vous ne souhaitez pas écrire à un groupe.  Mise à jour d’un groupe existant nécessite le renouvellement de l’authentification BI d’alimentation. | 
| Nom du groupe de données | Fournissez un nom de groupe de données il est souhaitée pour la sortie de puissance BI à utiliser |
| Nom de la table | Fournir un nom de table dans le dataset de la sortie de puissance BI. Actuellement, sortie de puissance BI de tâches de flux de données Analytique peut avoir uniquement une table dans un groupe de données |

Pour une vue d’ensemble de la configuration d’une sortie de puissance BI et le tableau de bord, consultez l’article de [Azure flux Analytique & BI d’alimentation](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Ne créez pas explicitement le groupe de données et la table dans le tableau de bord d’alimentation. Le groupe de données et la table seront automatiquement remplies lorsque la tâche est démarrée et sortie de pompage de démarrage de la tâche en puissance BI. Notez toutefois que si la requête de travail ne génère aucun résultat, le groupe de données et la table ne seront pas créés. Sachez également que si l’alimentation BI avait déjà un groupe de données et la table avec le même nom que celui fourni dans cette tâche de flux de données Analytique, les données existantes seront remplacées.

### <a name="renew-power-bi-authorization"></a>Renouvellement de l’autorisation de puissance BI

Vous devrez ré-authentifier votre compte courant BI si son mot de passe a été modifié depuis la création ou de dernière authentification de votre travail. Si plusieurs facteurs d’authentification (AMF) est configurée sur votre client Azure Active Directory (DAS), vous devez également renouveler l’autorisation d’alimentation BI toutes les 2 semaines. Un symptôme de ce problème est aucune sortie des tâches et une « authentification utilisateur erreur » dans les journaux de l’opération :

  ![Erreur de jeton d’actualisation d’alimentation BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Pour résoudre ce problème, arrêtez votre travail en cours d’exécution et accédez à votre sortie de puissance BI.  Cliquez sur le lien « Renouvellement d’autorisation » et redémarrez votre travail à partir de la dernière fois arrêté afin d’éviter la perte de données.

  ![Alimentation BI renouvellement d’autorisation](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Stockage de table

[Stockage Azure Table](../storage/storage-introduction.md) offre un stockage hautement disponible, hautement évolutif, afin qu’une application peut automatiquement mettre à l’échelle pour répondre à la demande de l’utilisateur. Stockage de table est un stockage de clé/attribut NoSQL de Microsoft qui peut exploiter pour les données structurées avec moins de contraintes sur le schéma. Stockage par Table Azure peut servir à stocker des données pour la persistance et de récupération efficace.

Le tableau ci-dessous répertorie les noms de propriété et leur description pour créer une table de sortie.

| Nom de la propriété | Description |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial utilisé dans les requêtes pour diriger la sortie de la requête pour le stockage de cette table. |
| Compte de stockage | Le nom du compte de stockage où vous envoyez votre sortie. |
| Clé de compte de stockage | La touche d’accès rapide avec le compte de stockage. |
| Nom de la table | Le nom de la table. La table sera créée si elle n’existe pas. |
| Clé de partition | Le nom de la colonne de sortie contenant la clé de partition. Clé de partition est un identificateur unique pour la partition d’une table donnée qui constitue la première partie de la clé primaire de l’entité. Il s’agit d’une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Clé de ligne | Le nom de la colonne de sortie contenant la clé de la ligne. La clé de ligne est un identificateur unique pour une entité au sein d’une partition donnée. Il constitue la deuxième partie de la clé primaire de l’entité. La clé de ligne est une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Taille de lot | Le nombre d’enregistrements pour une opération par lots. Généralement, la valeur par défaut est suffisant pour la plupart des tâches, consultez les [spécifications de la Table de traitement par lots](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) pour plus d’informations sur la modification de ce paramètre. |

## <a name="service-bus-queues"></a>Files d’attente de Bus de service

[Files d’attente de Bus de service](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une première dans, remise des messages à un ou plusieurs consommateurs concurrents premier sorti (FIFO). En règle générale, les messages doivent être reçus et traités par les destinataires dans l’ordre temporel dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par le consommateur d’un seul message.

Le tableau ci-dessous répertorie les noms de propriété et leur description pour la création d’une sortie de la file d’attente.

| Nom de la propriété | Description |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger la sortie de requête vers cette file d’attente de Bus de Service dans les requêtes. |
| Namespace de Bus de service | Bus de Service d’un espace de noms est un conteneur pour un ensemble d’entités de messagerie. |
| Nom de la file d’attente | Le nom de la file d’attente de Bus de Service. |
| Nom de stratégie de file d’attente | Lorsque vous créez une file d’attente, vous pouvez également créer des stratégies d’accès partagé dans l’onglet Configuration de la file d’attente. Chaque stratégie d’accès partagé va avoir un nom, les autorisations que vous définissez, et accéder aux clés. |
| Clé de stratégie de file d’attente | La clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms du Bus des services |
| Format de sérialisation d’événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | CSV et JSON, UTF-8 est le seul format de codage pris en charge pour l’instant |
| Séparateur | Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation des données au format CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |
| Format | Applicable uniquement pour le type JSON. Ligne séparée Spécifie que la sortie sera formatée en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau spécifie que la sortie sera formatée comme un tableau d’objets JSON. |

## <a name="service-bus-topics"></a>Rubriques de Bus de service

Tandis que les files d’attente de Bus de Service fournissent une méthode de communication d’un à un à partir de l’expéditeur au récepteur, [Rubriques de Bus de Service](https://msdn.microsoft.com/library/azure/hh367516.aspx) fournissent un formulaire un-à-plusieurs de la communication.

Le tableau ci-dessous répertorie les noms de propriété et leur description pour créer une table de sortie.

| Nom de la propriété | Description |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à cette rubrique de Bus de Service dans les requêtes. |
| Namespace de Bus de service | Bus de Service d’un espace de noms est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événements, vous a également créé un espace de noms du Bus des services |
| Nom de la rubrique | Rubriques sont la messagerie entités, similaires aux files d’attente et les concentrateurs de l’événement. Ils sont conçus pour collecter les flux d’événements à partir d’un certain nombre de différents périphériques et services. Lorsqu’une rubrique est créée, il reçoit également un nom spécifique. Les messages envoyés à une rubrique ne seront pas disponibles, sauf si un abonnement est créé, ainsi assurez-vous il y a un ou plusieurs abonnements sous la rubrique |
| Nom de la rubrique stratégie | Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé dans l’onglet Configuration de la rubrique. Chaque stratégie d’accès partagé aura un nom, les autorisations que vous définissez, et accéder aux clés |
| Clé de stratégie de rubrique | La clé d’accès partagé utilisée pour authentifier l’accès à l’espace de noms du Bus des services |
| Format de sérialisation d’événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le seul format de codage pris en charge pour l’instant |
| Séparateur | Applicable uniquement pour la sérialisation de CSV. Analytique de flux prend en charge un certain nombre de délimiteurs communs pour la sérialisation des données au format CSV. Les valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |

## <a name="documentdb"></a>DocumentDB

[DocumentDB d’Azure](https://azure.microsoft.com/services/documentdb/) est un entièrement gérés NoSQL document de base de données service qui offre de requêtes et de transactions par rapport à développement rapide, des performances prévisibles et fiables et exempt de schéma de données.

Le tableau ci-dessous répertorie les noms de propriété et leur description pour la création d’une sortie DocumentDB.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Nom de compte</td>
<td>Le nom du compte DocumentDB.  Cela peut également être le point de terminaison pour le compte.</td>
</tr>
<tr>
<td>Clé de compte</td>
<td>La clé d’accès partagé pour le compte DocumentDB.</td>
</tr>
<tr>
<td>Base de données</td>
<td>Le nom de la base de données DocumentDB.</td>
</tr>
<tr>
<td>Modèle de nom de collection</td>
<td>Le modèle de nom de collection pour les collections à utiliser. Le format du nom de collection peut être construit en utilisant le jeton facultatif {partition}, où les partitions de démarrer à partir de 0.<BR>Par exemple Les types suivants sont des entrées valides :<BR>MyCollection {partition}<BR>MyCollection<BR>Notez que les collections doivent exister avant que la tâche de flux de données Analytique est démarrée et qu’il ne sera pas créée automatiquement.</td>
</tr>
<tr>
<td>Clé de partition</td>
<td>Le nom du champ dans les événements de sortie permet de spécifier la clé de partitionnement de sortie provenant de plusieurs collections.</td>
</tr>
<tr>
<td>ID de document</td>
<td>Selon le nom du champ dans les événements de sortie permet de spécifier la clé primaire, laquelle insérer ou mettre à jour des opérations.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir de l’aide, essayez notre [forum d’Analytique de flux de données Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez été introduit pour l’Analytique des flux de données, un service managé pour la diffusion analytique des données à partir de l’Internet des objets. Pour en savoir plus sur ce service, voir :

- [Mise en route à l’aide d’Analytique de flux de données Azure](stream-analytics-get-started.md)
- [Mettre à l’échelle les travaux Azure Analytique de flux](stream-analytics-scale-jobs.md)
- [Référence de langage de requête Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence des API de flux Azure Analytique gestion reste](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
