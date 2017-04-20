<properties
   pageTitle="Comparaison de magasin de LAC Azure données avec Azure stockage Blob | Microsoft Azure"
   description="Comparaison de magasin de LAC Azure données avec Azure stockage Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Comparaison de magasin de LAC de données Azure et Blob Azure Storage

Le tableau de cet article résume les différences entre le lac Azure Data Store et de stockage des objets Blob Azure le long de certains aspects essentiels importante du traitement des données. Stockage de Blob Azure est un magasin d’objets extensible est conçu pour une grande variété de scénarios de stockage. Magasin de LAC de données Azure est un référentiel d’une évolutivité qui est optimisé pour les charges de travail données analytique.

|    | Magasin de LAC de données Azure  | Stockage des objets Blob Azure  |
|----|-----------------------|--------------------|
| Objectif  | Stockage optimisé pour les charges de travail données analytique                                                                          | Objet d’usage général stocker pour une large gamme de scénarios de stockage                                                                                |
| Cas d’usage                                   | Lot, interactif, en flux continu analytique et les données d’apprentissage machine tels que fichiers journaux, les données IoT, cliquez sur le flux de données, groupes de données volumineux | N’importe quel type de données texte ou binaire, par exemple, application sauvegarder fin, des données de sauvegarde, des supports de stockage de DONNEES en continu et en général |
| Concepts clés                                | Compte de banque de données lac contient des dossiers, qui contient à son tour des données stockées sous forme de fichiers | Compte de stockage a des conteneurs, qui possède à son tour les données sous la forme d’objets BLOB |
| Structure de | Système de fichiers hiérarchique                                                                                                    | Magasin d’objets avec l’espace de noms plat  |
| API   | API REST via HTTPS | API REST via HTTP/HTTPS                                                                                                                            |
| API côté serveur                             | [API REST de WebHDFS compatibles](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Stockage de Blob Azure API REST](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| Client système de fichiers Hadoop                   | Oui                                                                                                                         | Oui                                                                                                                                                 |
| Opérations sur les données - authentification            | En fonction des [identités d’Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | Basé sur des secrets partagés - [Compte les touches d’accès](../storage/storage-create-storage-account.md#manage-your-storage-account) et les [Clés de Signature de l’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Opérations sur les données - protocole d’authentification     | 2.0 de OAuth. Les appels doivent contenir un JWT valide (jeton Web JSON) émis par Azure Active Directory                     | Hash-based Message Authentication Code (HMAC). Les appels doivent contenir un hachage SHA-256 codé en Base64 sur une partie de la requête HTTP. |
| Opérations sur les données - autorisation               | Listes de contrôle d’accès POSIX (ACL).  ACL basées sur Azure Active Directory identités peut être définies au niveau fichier et dossier. | Pour les autorisations au niveau des comptes – utiliser les [Touches d’accès compte](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Pour le compte, conteneur ou autorisation d’objet blob - utiliser les [Clés de Signature de l’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Opérations sur les données - audit                    | Disponible. Voir [ici](data-lake-store-diagnostic-logs.md) pour plus d’informations.                                                                                                                   | Disponible                                                                                                                                           |
| Cryptage des données au repos                     | Transparente, côté serveur (disponible prochainement)<ul><li>Avec touches de service géré</li><li>Avec des clés de client géré dans Azure KeyVault</li></ul>| <ul><li>Transparent, SSI</li> <ul><li>Avec touches de service géré</li><li>Avec touches de gérés par le client dans les KeyVault d’Azure (prochainement)</li></ul><li>Cryptage côté client</li></ul> |
| Opérations de gestion (par exemple, créer un compte) | [Contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-what-is.md) (RBAC) fourni par Azure pour la gestion des comptes                                                                       | [Contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-what-is.md) (RBAC) fourni par Azure pour la gestion des comptes                                                                                               |
| Kits de développement logiciel développeur                              | .NET, Java, Node.js                                                                                                         | .NET, Java, Python, Node.js, C++, Ruby                                                                                                              |
| Performances de charge de travail Analytique              | Optimisation des performances pour les charges de travail parallèles analytique. Un débit élevé et Ops ES/s.                                           | Pas optimisé pour les charges de travail analytique                                                                                                               |
| Limites de taille                                 | Aucune limite de taille de compte, les tailles de fichier ou nombre de fichiers                                                                   | Limites spécifiques documenté [ici](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Redondance géographique                              | Localement redondants (plusieurs copies des données dans une seule région Azure)                                                             | Localement redondants (LRS), global redondants (GRS), accès en lecture globalement redondants (RA-GRS). Voir [ici](../storage/storage-redundancy.md) pour plus d’informations |
| État du service                               | Version d’évaluation                                                                                                              | Disponible                                                                                                                                 |
| Disponibilité régionale  | Consultez [ici](https://azure.microsoft.com/regions/#services)| Consultez [ici](https://azure.microsoft.com/regions/#services) |
| Prix                                       |     Reportez-vous à la section [tarification](https://azure.microsoft.com/pricing/details/data-lake-store/)| Reportez-vous à la section [tarification](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)
- [Mise en route de la banque de données lac](data-lake-store-get-started-portal.md)



