<properties
   pageTitle="Clé des Notes de publication de coffre-fort .NET 2.x API | Microsoft Azure"
   description="Les développeurs .NET utilise cette API en code pour Azure clé coffre-fort"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Chambre forte Azure clé de .NET 2.0 - Guide de Migration et les Notes de publication

Les remarques et les conseils suivants sont pour les développeurs qui travaillent avec Azure clé coffre-fort .NET / bibliothèque C#. Dans la modification de la 1.0 version à la version 2.0, un certain nombre de mises à jour ont été apportée seront nécessitent le travail de migration de votre code afin de tirer parti des améliorations fonctionnelles et de fonctionnalité des ajouts de prise en charge des certificats de clé coffre-fort.

Prise en charge des certificats de clé Vault offre pour la gestion de vos x509 les certificats et les comportements suivants :  

-   Autorise le propriétaire d’un certificat créer un certificat via un processus de création de clé coffre-fort ou l’importation d’un certificat existant. Cela inclut à la fois les auto-signés et autorité de certificat généré des certificats.

- Permet à un propriétaire de certificat de clé coffre-fort mettre en œuvre un stockage sécurisé et gestion de X509 certificats sans interaction avec la clé privée.  

-   Autorise le propriétaire d’un certificat créer une stratégie qui dirige le stockage en chambre forte de la clé pour gérer le cycle de vie d’un certificat.  

-   Permet des détenteurs de certificats afin de fournir des informations de contact pour la notification des événements de cycle de vie d’expiration et de renouvellement de certificat.  

-   Le renouvellement automatique avec émetteurs sélectionnés - clé coffre-fort partenaire X509 prend en charge les fournisseurs de certificats / Autorités de certification.
    - Remarque - fournisseurs/autorités Non associés sont également autorisées mais, ne prendra pas en charge la fonction de renouvellement automatique.


## <a name="net-support"></a>Prise en charge .NET
- **.NET 4.0** n’est pas pris en charge par la version 2.0 du .NET Framework en chambre forte de la clé Azure / bibliothèque C#
- **Cœur de .NET** est pris en charge par la version 2.0 du .NET Framework en chambre forte de la clé Azure / bibliothèque C#

## <a name="namespaces"></a>Espaces de noms
- L’espace de noms pour les **modèles** passe de **Microsoft.Azure.KeyVault** à **Microsoft.Azure.KeyVault.Models**.
- L’espace de noms **Microsoft.Azure.KeyVault.Internal** est supprimée.
- L’espace de noms de dépendances Azure SDK sont modifiés à partir de **Hyak.Common** et de **Hyak.Common.Internals** à **Microsoft.Rest** et **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Type de change
- *Code secret* modifié en *SecretBundle*
- *Dictionnaire* modifié à *IDictionary*
- *Liste des<T>, string []* en *IList<T> *
- *NextList* modifié en *NextPageLink*


## <a name="return-types"></a>Types de retour
- Retourneront **KeyList** et **SecretList** *IPage<T> * au lieu de *ListKeysResponseMessage*
- Le généré **BackupKeyAsync** retourne *BackupKeyResult* , qui contient la *valeur* (blob de sauvegarde). Avant que la méthode a été encapsulée et retourner uniquement la valeur.

## <a name="exceptions"></a>Exceptions
- *KeyVaultClientException* est modifié en *KeyVaultErrorException*
- L’erreur de service est modifié à partir de l’exception de *. Erreur de* à l’exception de *. Body.Error.Message*.
- Permet de supprimer les infos supplémentaires à partir du message d’erreur **[JsonExtensionData]**.

## <a name="constructors"></a>Constructeurs
- Au lieu d’accepter un *HttpClient* sous la forme d’un argument de constructeur, le constructeur accepte uniquement *[] de DelegatingHandler*ou de *HttpClientHandler* .



## <a name="downloaded-packages"></a>Packages téléchargés  
Lorsqu’un client traite une dépendance de clé de coffre-fort suivantes ont été téléchargées
#### <a name="previous-package-list"></a>Liste de package précédente
- version de package id="Hyak.Common » = « 1.0.2 » targetFramework = « net45 »
- version de package id="Microsoft.Azure.Common » = « 2.0.4 » targetFramework = « net45 »
- version de package id="Microsoft.Azure.Common.Dependencies » = « 1.0.0 » targetFramework = « net45 »
- version de package id="Microsoft.Azure.KeyVault » = « 1.0.0 » targetFramework = « net45 »
- version de package id="Microsoft.Bcl » = « 1.1.9 » targetFramework = « net45 »
- version de package id="Microsoft.Bcl.Async » = « 1.0.168 » targetFramework = « net45 »
- version de package id="Microsoft.Bcl.Build » = « 1.0.14 » targetFramework = « net45 »
- version de package id="Microsoft.Net.Http » = « 2.2.22 » targetFramework = « net45 »

#### <a name="current-package-list"></a>Liste de package en cours
- version de package id="Microsoft.Azure.KeyVault » = « 2.0.0-preview » targetFramework = « net45 »
- version de package id="Microsoft.Rest.ClientRuntime » = « 2.2.0 » targetFramework = « net45 »
- version de package id="Microsoft.Rest.ClientRuntime.Azure » = « 3.2.0 » targetFramework = « net45 »


## <a name="class-changes"></a>Modifications de classe

- Classe de **UnixEpoch** a été supprimé.
- Classe de **Base64UrlConverter** est renommé en **Base64UrlJsonConverter**

## <a name="other-changes"></a>Autres modifications

- Prise en charge de la configuration de stratégie de nouvelle tentative d’opération KV sur les défaillances temporaires a été ajouté à cette version de l’API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>NuGet de Microsoft.Azure.Management.KeyVault
- Pour les opérations qui a retourné un *coffre-fort*, le type de retour est une classe contenant une propriété de la chambre forte. Le type de retour est à présent *en chambre forte*.
- *PermissionsToKeys* et *PermissionsToSecrets* sont à présent *Permissions.Keys* et *Permissions.Secrets*
- Certaines modifications des types de retour s’appliquent au contrôle plan ainsi.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>NuGet de Microsoft.Azure.KeyVault.Extensions
- Le package est divisé pour **Microsoft.Azure.KeyVault.Extensions** et **Microsoft.Azure.KeyVault.Cryptography** pour les opérations de cryptographie.
