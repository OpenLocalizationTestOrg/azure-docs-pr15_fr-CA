<properties
    pageTitle="Stockage cool Azure pour les objets BLOB | Microsoft Azure"
    description="Niveaux de stockage pour le stockage des objets Blob Azure offre un stockage économique pour les données d’objet basées sur des modèles d’accès. Le niveau de frais de stockage est optimisé pour les données utilisées moins fréquemment."
    services="storage"
    documentationCenter=""
    authors="michaelhauss"
    manager="vamshik"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="mihauss"/>


# <a name="azure-blob-storage-hot-and-cool-storage-tiers"></a>Stockage de Blob Azure : À chaud et refroidir des niveaux de stockage

## <a name="overview"></a>Vue d’ensemble

Stockage Azure propose maintenant deux niveaux de stockage pour le stockage des objets Blob (stockage de l’objet), afin que vous puissiez stocker vos données plus économique en fonction de la façon de l’utiliser. Le **niveau de stockage à chaud** d' Azure est optimisé pour stocker les données fréquemment sollicitées. Le **niveau de stockage pratique** de Azure est optimisé pour le stockage des données rarement consultées et de durée de vie longue. Les données dans la couche de stockage pratique peuvent tolérer une disponibilité légèrement inférieure, mais nécessite toujours une durabilité élevée similaires pour accéder à et les caractéristiques de débit en tant que données à chaud. Pour les données intéressantes, légèrement inférieur SLA de disponibilité et les coûts d’accès plus élevés sont compromis acceptables pour bien réduire les coûts de stockage.

Aujourd'hui, les données stockées dans le nuage croît à un rythme exponentiel. Pour gérer les coûts pour vos besoins de stockage en pleine expansion, il est utile d’organiser vos données en fonction d’attributs tels que la fréquence d’accès et de la période de rétention prévue. Les données stockées dans le cloud peuvent être différentes en fonction de comment est généré, traitée et accessible via sa durée de vie. Certaines données sont activement utilisées et modifiées tout au long de sa durée de vie. Certaines données très fréquemment sollicitées au début de sa durée de vie, avec accès suppression considérablement comme l’âge des données. Certaines données restent inactives dans le nuage et sont rarement, si jamais ouvert une fois stocké.

Chacun de ces scénarios d’accès de données décrites ci-dessus bénéficient d’un niveau différencié de stockage qui est optimisé pour un modèle d’accès particulier. Avec l’introduction de différents niveaux de stockage à chaud et sympas, Blob Azure stockage maintenant répond à ce besoin de niveaux de stockage différencié avec séparer les modèles de tarification.

## <a name="blob-storage-accounts"></a>Comptes de stockage BLOB

**Les comptes de stockage BLOB** sont stockage spécialisé des comptes pour le stockage des données non structurées en tant que BLOB (objets) dans le stockage Azure. Avec les comptes de stockage Blob, vous pouvez maintenant choisir entre niveaux de stockage de chaud et pratique pour stocker vos données moins fréquemment consultées sympas à un stockage de plus faible coût et stockent des données à chaud plus fréquemment à un moindre coût d’accès. Les comptes de stockage BLOB sont similaires à vos comptes existants de stockage à usage général et partagent tous les la grande durabilité, de disponibilité, l’évolutivité et des fonctionnalités de performances que vous utilisez aujourd'hui, y compris la cohérence d’API de 100 % pour les objets BLOB de bloc et ajouter des objets BLOB.

> [AZURE.NOTE] Les comptes de stockage BLOB prennent en charge de bloc uniquement et ajouter des objets BLOB et pas des blobs de page.

Les comptes de stockage BLOB exposent l’attribut de **Niveau d’accès** , qui permet de spécifier le niveau de stockage comme **chaude** ou **refroidir** en fonction des données stockées dans le compte. S’il existe une modification dans le modèle d’utilisation de vos données, vous pouvez également basculer entre ces différents niveaux de stockage à tout moment.

> [AZURE.NOTE] Modification de la couche de stockage peut entraîner des frais supplémentaires. Consultez la section [tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus de détails.

Scénarios d’utilisation exemple pour le niveau de stockage à chaud sont les suivantes :

- Données en cours d’utilisation ou doivent être accédé fréquemment (lecture à partir d’et écrite à).
- Données qui sont mis en place pour la migration de traitement et une éventuelle à la couche de stockage pratique.

Scénarios d’utilisation exemple pour le niveau de frais de stockage sont les suivantes :

- Sauvegarde, archivage et reprise après sinistre des groupes de données.
- Ancien contenu media pas consultées fréquemment plus mais est censée être disponible immédiatement lors de l’accès.
- Grands ensembles de données qui doivent être efficacement de coût stockée tandis que davantage de données sont rassemblé d’un traitement ultérieur. (*par exemple*, un stockage à long terme des données scientifiques, les données de télémétrie brut à partir d’une unité de production)
- Dans le cas de données d’origine (bruts) qui doivent être conservés, même après que qu’elle a été traitée dans une forme utilisable finale. (*par exemple*, les fichiers de support brut après le transcodage dans d’autres formats)
- Conformité et archivage qui doit être stocké pendant un certain temps et est difficilement accessibles. *(par exemple*, caméras de vidéosurveillance, ancien X-rayons/IRM pour les organismes de santé, les enregistrements audio et les transcriptions des appels des clients pour les services financiers)

Pour plus d’informations sur les comptes de stockage, reportez-vous à la section [comptes de stockage sur Azure](storage-create-storage-account.md) .

Pour les applications ne nécessitant que bloquent ou ajouter le stockage blob, nous recommandons d’utiliser des comptes de stockage Blob, pour tirer parti du modèle de tarification différencié du stockage hiérarchisé. Toutefois, nous comprenons que cela est possible, dans certaines circonstances où l’utilisation de comptes de stockage universelle serait le moyen, tel que :

- Vous devez utiliser des tableaux, des files d’attente ou fichiers et souhaitez que vos objets BLOB stockés dans le même compte de stockage. Notez qu’il n’y a aucun avantage technique pour stocker ces éléments dans le même compte que le comportant des clés partagées.
- Vous devez toujours utiliser le modèle de déploiement standard. Les comptes de stockage BLOB sont uniquement accessibles via le modèle de déploiement du Gestionnaire de ressources Azure.
- Vous devez utiliser des objets BLOB de page. Les comptes de stockage BLOB ne gèrent pas de BLOB de la page. Nous vous recommandons généralement à l’aide des objets BLOB de bloc à moins d’avoir un besoin spécifique pour les objets BLOB de page.
- Vous utilisez une version de l' [API REST de Services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) qui est antérieure à 14-02-2014 ou une bibliothèque cliente avec une version inférieure à 4.x et mise à niveau de votre application.

> [AZURE.NOTE] Les comptes de stockage BLOB sont actuellement pris en charge dans la majorité des régions Azure plus à suivre. Vous trouverez la liste à jour des régions disponibles sur la page [Services Azure par région](https://azure.microsoft.com/regions/#services) .

## <a name="comparison-between-the-storage-tiers"></a>Comparaison entre les différents niveaux de stockage

Le tableau suivant met en valeur la comparaison entre les deux niveaux de stockage :

<table border="1" cellspacing="0" cellpadding="0" style="border: 1px solid #000000;">
<col width="250">
<col width="250">
<col width="250">
<tbody>
<tr>
    <td><strong><center></center></strong></td>
    <td><strong><center>Niveau de stockage à chaud</center></strong></td>
    <td><strong><center>Niveau de frais de stockage</center></strong></td
</tr>
<tr>
    <td><strong><center>Disponibilité</center></strong></td>
    <td><center>99,9 %</center></td>
    <td><center>99 %</center></td>
</tr>
<tr>
    <td><strong><center>Disponibilité<br>(Lit de GRS-RA)</center></strong></td>
    <td><center>99,99 %</center></td>
    <td><center>99,9 %</center></td>
</tr>
<tr>
    <td><strong><center>Frais d’utilisation</center></strong></td>
    <td><center>Coûts de stockage élevés<br>Réduire les coûts de transaction et accès</center></td>
    <td><center>Réduction des coûts de stockage<br>Coûts plus élevés d’accès et de transaction</center></td>
</tr>
<tr>
    <td><strong><center>Taille de l’objet minimale<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Durée de stockage minimale<center></strong></td>
    <td colspan="2"><center>N/A</center></td>
</tr>
<tr>
    <td><strong><center>Temps de latence<br>(Temps au premier octet)<center></strong></td>
    <td colspan="2"><center>millisecondes</center></td>
</tr>
<tr>
    <td><strong><center>Cibles de l’évolutivité et les performances<center></strong></td>
    <td colspan="2"><center>Même en tant que comptes de stockage à usage général</center></td>
</tr>
</tbody>
</table>

> [AZURE.NOTE] Les comptes de stockage BLOB prend en charge les performances et les objectifs d’évolutivité en tant que comptes de stockage à usage général. Pour plus d’informations, consultez [cibles de performances et de l’évolutivité du stockage Azure](storage-scalability-targets.md) .

## <a name="pricing-and-billing"></a>Prix et facturation

Les comptes de stockage BLOB utilisent un nouveau modèle de tarification pour le stockage des objets blob en fonction du niveau de stockage. Lorsque vous utilisez un compte de stockage Blob, les considérations de facturation suivantes s’appliquent :

- **Les coûts de stockage**: en plus de la quantité de données stockées, le coût de stockage des données varie selon la couche de stockage. Le coût par gigaoctet est inférieur de la couche de stockage pratique que pour le niveau de stockage à chaud.
- **Les coûts d’accès aux données**: pour les données dans la couche de stockage sympas, vous sera facturée frais d’accès de données au gigaoctet de lectures et d’écritures.
- **Les coûts de transaction**: il existe des frais par transaction pour les deux niveaux. Toutefois, le coût par transaction pour la couche de stockage pratique est supérieur à celui de la couche de stockage à chaud.
- **Les coûts de transfert de données de réplication géographique**: Ceci s’applique uniquement aux comptes avec géo-réplication configurée, y compris de GRS et RA-GRS. Transfert des données géographique-réplication entraîne un coût au gigaoctet.
- **Les coûts de transfert de données sortantes**: transferts de données sortantes (données qui sont transférées à partir d’une région Azure) entraînent la facturation de l’utilisation de la bande passante sur une base par gigaoctet, cohérente avec les comptes de stockage à usage général.
- **Modification de la couche de stockage**: modification de la couche de stockage de refroidir à chaud entraînera une taxe égale à la lecture de toutes les données existant dans le compte de stockage pour chaque transition. D’autre part, remplaçant le niveau de stockage à chaud refroidir est exempt de coût.

> [AZURE.NOTE] Pour permettre aux utilisateurs de tester les nouveaux niveaux de stockage et de valider les fonctionnalités après lancement, les frais pour le stockage graduel de refroidir à chaud est annulé désactivé jusqu'à ce que les 2016 au 30 juin. À partir de 2016 de 1er juillet, les frais seront appliqueront à toutes les transitions de refroidir à chaud. Pour plus d’informations sur le modèle de tarification pour le stockage des objets Blob comptes Voir, page [Prix de stockage Azure](https://azure.microsoft.com/pricing/details/storage/) . Pour plus d’informations sur les données sortantes frais de transfert voir, page de [Détails de tarification de transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) .

## <a name="quick-start"></a>Démarrage rapide

Dans cette section, nous découvrirons les scénarios suivants en utilisant le portail Azure :

- Comment créer un compte de stockage Blob.
- Comment gérer un compte de stockage Blob.

### <a name="using-the-azure-portal"></a>L’utilisation du portail Azure

#### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Créer un compte de stockage Blob en utilisant le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de concentrateur, sélectionnez **Nouveau** > **données + stockage** > **compte de stockage**.

3. Entrez un nom pour votre compte de stockage.

    Ce nom doit être globalement unique ; Il est utilisé dans le cadre de l’URL utilisée pour accéder aux objets dans le compte de stockage.  

4. Sélectionnez le modèle de déploiement **Gestionnaire de ressources** .

    Stockage hiérarchisé peut uniquement être utilisé avec comptes de stockage du Gestionnaire de ressources ; C’est le modèle de déploiement recommandé pour les nouvelles ressources. Pour plus d’informations, consultez la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).  

5. Dans la liste déroulante Type de compte, sélectionnez le **Stockage Blob**.

    C’est lorsque vous sélectionnez le type de compte de stockage. Stockage hiérarchisé n’est pas disponible dans le stockage à usage général ; Il est uniquement disponible dans le compte de type stockage Blob.    

    Notez que lorsque vous sélectionnez cette option, le niveau de performances est paramétré sur Standard. Stockage hiérarchisé n’est pas disponible avec le niveau de performances Premium.

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS**ou **RA-GRS**. La valeur par défaut est **RA-GRS**.

    LRS = stockage redondant localement ; GRS = stockage redondant geo (2 zones) ; RA-GRS est un accès en lecture de stockage redondant geo (2 régions disposant d’un accès en lecture à la seconde).

    Pour plus d’informations sur les options de réplication de stockage Azure, consultez [réplication du stockage Azure](storage-redundancy.md).

7. Sélectionnez le niveau de stockage adapté à vos besoins : la valeur de la **couche d’accès aux** **sympa** ou **à chaud**. La valeur par défaut est **chaude**.

8. Sélectionnez l’abonnement dans lequel vous souhaitez créer le nouveau compte de stockage.

9. Spécifiez un groupe de ressources, ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

10. Sélectionnez la région de votre compte de stockage.

11. Cliquez sur **créer** pour créer le compte de stockage.

#### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Modifier le niveau de stockage d’un compte de stockage Blob en utilisant le portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Pour accéder à votre compte de stockage, sélectionnez toutes les ressources, puis sélectionnez votre compte de stockage.

3. De la lame de paramètres, cliquez sur **Configuration** pour afficher ou modifier la configuration du compte.

4. Sélectionnez le niveau de stockage adapté à vos besoins : la valeur de la **couche d’accès aux** **sympa** ou **à chaud**.

5. Cliquez sur Enregistrer en haut de la lame.

> [AZURE.NOTE] Modification de la couche de stockage peut entraîner des frais supplémentaires. Consultez la section [tarification et facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus de détails.

## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Évaluation et la migration vers les comptes de stockage Blob

L’objectif de cette section est d’aider les utilisateurs à effectuer une transition en douceur à l’aide de comptes de stockage Blob. Il existe deux scénarios d’utilisateur :

- Vous disposez d’un compte de stockage à usage général existant et que vous souhaitez évaluer une modification apportée à un compte de stockage Blob avec la couche de stockage adaptée.
- Vous avez décidé d’utiliser un compte de stockage Blob ou encore et à évaluer si vous devez utiliser le niveau de stockage à chaud ou frais.

Dans les deux cas, la première chose est d’estimer le coût de stockage et d’accès à vos données stockées dans un compte de stockage Blob et comparez les coûts en cours.

### <a name="evaluating-blob-storage-account-tiers"></a>Évaluation des niveaux de compte de stockage Blob

Afin d’estimer le coût de stockage et d’accès aux données stockées dans un compte de stockage Blob, vous devez évaluer votre modèle d’utilisation existant ou approximative de votre modèle d’utilisation attendu. En général, vous souhaiterez connaître :

- La consommation de votre stockage - la quantité de données est stocké et comment il évolue-t-elle sur une base mensuelle ?
- Votre modèle d’accès de stockage - la quantité de données sont lues et écrites sur le compte (y compris les nouvelles données) ? Le nombre de transactions est utilisé pour accéder aux données et les types de transactions sont elles ?

#### <a name="monitoring-existing-storage-accounts"></a>Surveillance des comptes de stockage existants

Pour surveiller vos comptes de stockage existants et rassembler ces données, vous pouvez faire utiliser d’Analytique de stockage Azure qui effectue la journalisation et fournit les données de métrique pour un compte de stockage.
Analytique de stockage peut stocker des métriques qui incluent des données statistiques et de la capacité de transaction globales sur les demandes au service de stockage Blob pour les comptes de stockage à usage général ainsi que les comptes de stockage Blob.
Ces données sont stockées dans des tables bien connus dans le même compte de stockage.

Pour plus d’informations, consultez [à propos des indicateurs Analytique du stockage](https://msdn.microsoft.com/library/azure/hh343258.aspx) et [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [AZURE.NOTE] Les comptes de stockage BLOB exposent le point de terminaison du service table uniquement pour stocker et accéder à des données métriques pour ce compte.

Pour surveiller la consommation de stockage pour le service de stockage de Blob, vous devez activer les mesures de capacité.
Avec cette option activée, les données de capacité sont enregistrées quotidiennement pour service d’objet Blob d’un compte de stockage et enregistrée sous la forme d’une entrée de table qui est écrites dans la table *$MetricsCapacityBlob* dans le même compte de stockage.

Pour analyser le modèle d’accès de données pour le service de stockage de Blob, vous devez activer les horaires de métriques de transactions au niveau de l’API.
Avec cette option activée, par API transactions sont agrégées de toutes les heures et enregistrées sous la forme d’une entrée de table qui est écrites dans la table *$MetricsHourPrimaryTransactionsBlob* dans le même compte de stockage. La table *$MetricsHourSecondaryTransactionsBlob* enregistre les transactions pour le point de terminaison secondaire en cas de comptes de stockage RA-GRS.

> [AZURE.NOTE] Dans le cas où vous disposez d’un compte de stockage à usage général dans lequel vous avez stocké des blobs de page et des disques des machines virtuelles à côté du bloc et ajoutez les données blob, ce processus d’estimation n’est pas applicable. C’est parce que vous n’aurez aucun moyen de capacité distinctive et de métriques de transactions en fonction du type d’objet blob pour seulement bloquent et ajouter des objets BLOB qui peut être migrées vers un compte de stockage Blob.

Pour obtenir une bonne approximation de la consommation des données vous et modèle d’accès, nous vous recommandons de sélectionner une période de rétention pour les métriques représentatif de votre utilisation régulière et extrapoler.
Une option consiste à conserver les données de la métrique de 7 jours et collecter les données de chaque semaine, pour l’analyse à la fin du mois.
Une autre option consiste à conserver les données de mesures pour les 30 derniers jours et de collecter et d’analyser les données à la fin de la période de 30 jours.

Pour plus d’informations sur l’activation, collecter et afficher les données de la métrique, veuillez consulter, [des indicateurs permettant le stockage Azure et l’affichage des données de métrique](storage-enable-and-view-metrics.md).

> [AZURE.NOTE] Stockage, d’accès et de téléchargement des données d’analytique sont également chargé comme données de l’utilisateur normal.

#### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilisation de mesures de l’utilisation d’estimer les coûts

##### <a name="storage-costs"></a>Coûts de stockage

La dernière entrée dans la capacité des mesures table *$MetricsCapacityBlob* avec les clés de ligne *'data'* indique la capacité de stockage consommée par les données de l’utilisateur.
La dernière entrée dans la capacité des mesures table *$MetricsCapacityBlob* avec les clés de ligne *« analytique »* indique la capacité de stockage consommée par les journaux analytique.

Cette capacité totale consommée par les données de l’utilisateur et l’analytique des journaux (si activée) utilisable pour estimer le coût de stockage de données dans le compte de stockage.
La même méthode peut également être utilisé pour estimer les coûts de stockage de bloc et ajouter des objets BLOB dans les comptes de stockage à usage général.

##### <a name="transaction-costs"></a>Coûts de transaction

La somme de *'TotalBillableRequests'*, pour toutes les entrées pour une API dans la table de métriques de transactions indique le nombre total de transactions pour cette API particulière. *par exemple*, le nombre total de transactions *'GetBlob'* dans une période donnée être calculé par la somme du total des demandes facturables pour toutes les entrées avec la clé de ligne *' utilisateur ; GetBlob'*.

Afin d’estimer les coûts de transaction pour les comptes de stockage Blob, vous devez ventiler les transactions en trois groupes dans la mesure où ils sont tarifés différemment.

- Écrire les transactions *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'*et *'CopyBlob'*.
- Supprimer les transactions de *'DeleteBlob'* et *'DeleteContainer'*.
- Toutes les autres transactions.

Pour estimer les coûts de transaction pour les comptes de stockage à usage général, vous devez regrouper toutes les transactions, quel que soit l’opération/API.

##### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Accès et géo-réplication de données transférer des coûts

Alors que l’analytique de stockage ne fournit pas la quantité de données lues et écrites dans un compte de stockage, il peut être estimé à peu près en consultant la table de métriques de transactions.
La somme de *'TotalIngress'* pour toutes les entrées pour une API dans la table de métriques de transactions indique la quantité totale de données d’entrée en octets de cette API particulière.
De même, la somme de *'TotalEgress'* indique le montant total des données de sortie, en octets.

Afin d’estimer les coûts d’accès aux données pour les comptes de stockage Blob, vous devez ventiler les transactions en deux groupes.

- La quantité de données récupérées à partir du compte de stockage peut être estimée en examinant la somme de *'TotalEgress'* pour principalement les opérations *'GetBlob'* et *'CopyBlob'* .
- La quantité de données écrites sur le compte de stockage peut être estimée en examinant la somme de *'TotalIngress'* pour principalement les *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* et *'AppendBlock'* opérations.

Le coût géo-réplication de transfert de données pour les comptes de stockage Blob peut également être calculé à l’aide de l’estimation de la quantité de données écrites dans le cas d’un compte de stockage GRS ou RA-GRS.

> [AZURE.NOTE] Pour obtenir un exemple plus détaillé sur le calcul des coûts pour l’utilisation de la couche de stockage à chaud ou refroidir, prenez un coup de œil à la FAQ intitulée *« Quels sont les niveaux d’accès à chaud et à froid et comment dois-je pour déterminer celle à use? »* dans la [Page de tarification de stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

### <a name="migrating-existing-data"></a>Migration des données existantes

Un compte de stockage Blob est spécialisé pour le stockage de bloc uniquement et ajouter des objets BLOB. Les comptes généraux de stockage existants, qui vous permettent de stocker des tables, files d’attente, fichiers et disques, ainsi que d’objets BLOB, ne peut pas être converties en comptes de stockage Blob. Pour utiliser les niveaux de stockage, vous devrez créer de nouveaux comptes de stockage Blob et migrez vos données existantes dans les nouveaux comptes créés.
Vous pouvez utiliser les méthodes suivantes pour migrer des données dans les comptes de stockage Blob à partir de périphériques de stockage de sur site, à partir des fournisseurs de stockage cloud de tiers ou de vos comptes de stockage à usage général existant dans Azure :

#### <a name="azcopy"></a>AzCopy

AzCopy est un utilitaire de ligne de commande Windows conçu pour hautes performances copie des données vers et depuis le stockage Azure. Vous pouvez utiliser AzCopy pour copier les données dans votre compte de stockage Blob à partir de vos comptes de stockage à usage général existant, ou pour charger les données à partir de vos périphériques de stockage sur site dans votre compte de stockage Blob.

Pour plus d’informations, consultez [transférer des données avec l’utilitaire de ligne de commande de AzCopy](storage-use-azcopy.md).

#### <a name="data-movement-library"></a>Bibliothèque de déplacement de données

Bibliothèque de mouvement de données stockage Azure pour .NET est basé sur l’infrastructure de mouvement de données de base correspondant à AzCopy. La bibliothèque est conçue pour les opérations de transfert de données hautes performances, fiable et simple semblable à AzCopy. Cela vous permet de vous permet de tirer pleinement parti des fonctionnalités fournies par AzCopy dans votre application en mode natif sans avoir à faire face à l’exécution et surveillance des instances externes de AzCopy.

Pour plus d’informations, consultez [Bibliothèque de mouvement de données de stockage Azure pour .net](https://github.com/Azure/azure-storage-net-data-movement)

#### <a name="rest-api-or-client-library"></a>API REST ou la bibliothèque cliente

Vous pouvez créer une application personnalisée pour migrer vos données dans un compte de stockage Blob à l’aide d’une des bibliothèques du client Azure ou les services de stockage Azure API REST. Stockage Azure fournit des bibliothèques de client riche pour plusieurs langues et plates-formes comme .NET, Java, C++, Node.JS, PHP, Ruby et Python. Les bibliothèques clientes proposent des fonctionnalités avancées telles que la logique des nouvelles tentatives, de consignation et de téléchargements parallèles. Vous pouvez également développer directement par rapport à l’API REST, qui peut être appelée par n’importe quel langage qui facilite les demandes HTTP/HTTPS.

Pour plus d’informations, consultez [Mise en route de stockage des objets Blob Azure](storage-dotnet-how-to-use-blobs.md).

> [AZURE.NOTE] BLOB crypté à l’aide du cryptage côté client stockage les métadonnées de chiffrement stockées dans l’objet blob. Il est absolument essentiel que n’importe quel mécanisme de copie doit assurer que les métadonnées de blob et en particulier les métadonnées liées au cryptage, est conservé. Si vous copiez les blobs sans ces métadonnées, le blob contenu sera pas récupérable à nouveau. Pour plus d’informations sur le chiffrement des métadonnées, consultez [chiffrement de côté de client de stockage Azure](storage-client-side-encryption.md).

## <a name="faqs"></a>Questions fréquentes

1. **Sont déjà des comptes de stockage toujours disponibles ?**

    Oui, les comptes de stockage existants sont toujours disponibles et ne sont pas modifiés dans le prix ou la fonctionnalité.  Ils n’ont pas la possibilité de choisir un niveau de stockage et n’aura pas les capacités de hiérarchisation à l’avenir.

2. **Quand et pourquoi dois-je démarrer à l’aide des comptes de stockage Blob ?**

    Les comptes de stockage BLOB sont spécialisées pour le stockage des objets BLOB et nous permettent de présenter les nouvelles fonctionnalités axées sur le blob. À l’avenir, comptes de stockage Blob sont la manière recommandée pour le stockage des objets BLOB, comme les fonctionnalités à venir, comme le stockage hiérarchique et hiérarchisation est présentée en fonction de ce type de compte. Toutefois, c’est à vous lorsque vous souhaitez effectuer la migration en fonction de vos besoins.

3. **Puis-je convertir mon compte de stockage existant à un compte de stockage Blob ?**

    N° Compte de stockage BLOB est un autre type de compte de stockage et vous devrez créer de nouveaux et de migrer vos données, comme expliqué ci-dessus.

4. **Est-il possible de stocker des objets dans les deux niveaux de stockage dans le même compte ?**

    L’attribut *« niveau d’accès »* , qui indique le niveau de stockage est définie à un niveau de compte et s’applique à tous les objets de ce compte. Vous ne pouvez pas définir l’attribut de niveau d’accès au niveau de l’objet.

5. **Est-il possible de modifier la couche de stockage de mon compte de stockage Blob ?**

    Oui. Vous ne pourrez pas modifier le niveau de stockage en définissant l’attribut *« niveau d’accès »* sur le compte de stockage. Modification de la couche de stockage s’applique à tous les objets stockés dans le compte. Modifier le niveau de stockage à partir de hot refroidir ne pas encourir les frais, lors de la modification de refroidir à chaud peut entraîner un coût Go pour lire toutes les données du compte.

6. **La fréquence puis pour modifier le niveau de stockage de mon compte de stockage Blob ?**

    Bien que nous n’appliquent pas une limitation sur la fréquence à laquelle le niveau de stockage peut être modifié, sachez que la modification de la couche de stockage de refroidir à chaud peut entraîner des frais importants. Il est déconseillé de modifier le niveau de stockage fréquemment.

7. **Les objets BLOB dans la couche de stockage cool se comporteront différemment de ceux de la couche de stockage à chaud ?**

    Objets BLOB dans la couche stockage à chaud ont le même temps de latence en tant que BLOB dans les comptes de stockage à usage général. Le niveau de frais de stockage des objets BLOB ont une latence de similaire (en millisecondes) sous la forme d’objets BLOB dans les comptes de stockage à usage général.

    Le niveau de frais de stockage des objets BLOB aura un niveau service disponibilité de légèrement inférieur (SLA) que les objets BLOB stockée dans le niveau de stockage à chaud. Pour plus d’informations, consultez [contrat SLA pour le stockage](https://azure.microsoft.com/support/legal/sla/storage).

8. **Puis-je stocker des objets BLOB de page et des disques des machines virtuelles dans des comptes de stockage Blob ?**

    Les comptes de stockage BLOB prennent en charge de bloc uniquement et ajouter des objets BLOB et pas des blobs de page. Les disques des machines virtuelles Azure bénéficient des blobs de page et ainsi les comptes de stockage Blob ne peut pas servir à stocker des disques de l’ordinateur virtuel. Toutefois, il est possible de stocker des sauvegardes des disques de l’ordinateur virtuel en tant que BLOB de bloc dans un compte de stockage Blob.

9. **Dois-je modifier mes applications existantes pour utiliser des comptes de stockage Blob ?**

    Les comptes de stockage BLOB sont des API de 100 % conformes avec les comptes de stockage à usage général pour bloc et ajouter des objets BLOB. Tant que votre application est à l’aide de bloquer des objets BLOB ou d’ajouter des objets BLOB et vous utilisez la version 2014-02-14 ou supérieur à l' [API REST de Services de stockage](https://msdn.microsoft.com/library/azure/dd894041.aspx) , puis votre application doit fonctionner directement. Si vous utilisez une version antérieure du protocole, vous devrez mettre à jour votre application pour utiliser la nouvelle version de manière à fonctionner en toute transparence avec les deux types de comptes de stockage. En général, nous avons toujours recommandé à l’aide de la version la plus récente, quel que soit le compte de stockage type que vous utilisez.

10. **Y aura-t-il une modification dans l’expérience de l’utilisateur ?**

    Comptes de stockage BLOB sont très similaires à un compte de stockage à usage général pour stocker des blocs et ajouter des objets BLOB et toutes les fonctionnalités clés de stockage Azure, y compris une durabilité élevée et de disponibilité, d’évolutivité, performances et sécurité. Autres que les fonctionnalités et les restrictions spécifiques à des comptes de stockage Blob et ses niveaux de stockage qui ont été appelées ci-dessus, tout le reste demeure inchangé.

## <a name="next-steps"></a>Étapes suivantes

### <a name="evaluate-blob-storage-accounts"></a>Évaluer les comptes de stockage Blob

[Vérification de la disponibilité des comptes de stockage Blob par région](https://azure.microsoft.com/regions/#services)

[Évaluer l’utilisation de vos comptes de stockage actuel en activant des métriques de stockage Azure](storage-enable-and-view-metrics.md)

[Vérifier le prix de stockage Blob par région](https://azure.microsoft.com/pricing/details/storage/)

[Vérification des transferts de données de tarification](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Commencer à utiliser les comptes de stockage Blob

[Mise en route de stockage des objets Blob Azure](storage-dotnet-how-to-use-blobs.md)

[Déplacement des données vers et depuis le stockage Azure](storage-moving-data.md)

[Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

[Parcourir et d’Explorer de vos comptes de stockage](http://storageexplorer.com/)
