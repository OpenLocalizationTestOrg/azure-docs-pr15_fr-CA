<properties
    pageTitle="Guide de sécurité de stockage Azure | Microsoft Azure"
    description="Détails de nombreuses méthodes de sécurisation du stockage Azure, y compris mais non limité à RBAC, cryptage du stockage, cryptage côté Client, 3.0 de SMB et le cryptage de disque Azure."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guide de sécurité de stockage Azure

##<a name="overview"></a>Vue d’ensemble

Le stockage Azure fournit un ensemble complet de fonctionnalités de sécurité qui ensemble permettent aux développeurs de créer des applications sécurisées. Le compte de stockage lui-même peut être sécurisé à l’aide du contrôle d’accès basée sur les rôles et Azure Active Directory. Données peuvent être sécurisées en transit entre une application et d’Azure à l’aide de [Cryptage côté Client](storage-client-side-encryption.md), HTTPS ou SMB 3.0. Données peuvent être définies pour être automatiquement cryptés lors de l’écriture dans le stockage Azure à l’aide du [Cryptage de Service de stockage (SSE)](storage-service-encryption.md). Disques du système d’exploitation et des données utilisés par les machines virtuelles peuvent être définies afin d’être crypté à l’aide du [Cryptage de disque Azure](../security/azure-security-disk-encryption.md). Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de [Signatures de l’accès partagé](storage-dotnet-shared-access-signature-part-1.md).

Cet article fournit une vue d’ensemble de chacune de ces fonctionnalités de sécurité qui peuvent être utilisées avec le stockage Azure. Des liens sont fournis à des articles qui vous donne les détails de chaque fonctionnalité, vous pouvez facilement effectuer des investigations sur chaque sujet.

Voici les rubriques qui seront traités dans cet article :

-   [Sécurité du plan d’administration](#management-plane-security) – sécurisation de votre compte de stockage

    Le plan de gestion comprend les ressources utilisées pour gérer votre compte de stockage. Dans cette section, nous allons parler du modèle de déploiement d’Azure le Gestionnaire de ressources et comment utiliser le contrôle d’accès basée sur les rôles (RBAC) pour contrôler l’accès à vos comptes de stockage. Nous parlerons également la gestion de vos clés de compte de stockage et comment les régénérer.

-   [Données de plan de sécurité](#data-plane-security) – sécurisation de l’accès à vos données

    Dans cette section, nous allons étudier l’accès aux objets de données réelles dans votre compte de stockage, par exemple les blobs, les fichiers, les files d’attente et les tables, à l’aide de Signatures de l’accès partagé et stockées les stratégies d’accès. Nous allons aborder les associations de sécurité de niveau de service et les associations de sécurité au niveau des comptes. Nous verrons également comment limiter l’accès à une adresse IP spécifique (ou plage d’adresses IP), le protocole utilisé pour HTTPS de limiter et comment révoquer une Signature accès partagé sans attendre qu’il expire.

-   [Cryptage en Transit](#encryption-in-transit)

    Cette section explique comment sécuriser des données lors de son transfert dans ou en dehors du stockage Azure. Nous nous pencherons sur l’utilisation recommandée de HTTPS et le chiffrement utilisé par les PME/PMI 3.0 Azure partages de fichiers. Nous allons également au cryptage côté Client, ce qui vous permet de chiffrer les données avant leur transfert dans le stockage dans une application cliente et pour décrypter les données après transfert de stockage.

-   [Cryptage au repos](#encryption-at-rest)

    Nous parlerons de stockage Service de cryptage (SSE) et comment vous pouvez l’activer pour un compte de stockage, entraînant votre bloc BLOB, BLOB de la page et ajouter des objets BLOB sont automatiquement cryptés lors de l’écriture dans le stockage Azure. Nous examinerons également comment vous pouvez utiliser le cryptage de disque Azure et découvrez les différences de base et les cas de cryptage de disque et de SSE par rapport au cryptage côté Client. Nous examinerons brièvement conformité FIPS pour les ordinateurs du gouvernement américain.

-   À l’aide de [Stockage Analytique](#storage-analytics) pour auditer l’accès de stockage Azure

    Cette section explique comment rechercher des informations dans les journaux d’analytique de stockage pour une demande. Nous examinons analytique de stockage réel des données de journal et voir comment faire pour déterminer si une demande est effectuée avec la clé de compte de stockage, avec une signature d’accès partagé, ou de façon anonyme et si elle a réussi ou a échoué.

-   [Activation des Clients basés sur navigateur utilisant les CORS](#Cross-Origin-Resource-Sharing-CORS)

    Cette section explique comment autoriser la ressource d’origine croisée partage (CORS). Nous allons parler des accès inter-domaines et comment le gérer avec les capacités CORS intégrées dans le stockage Azure.

##<a name="management-plane-security"></a>Sécurité du plan d’administration

Le plan de gestion se compose des opérations qui affectent le compte de stockage lui-même. Par exemple, vous pouvez créer ou supprimer un compte de stockage obtenir une liste des comptes de stockage dans un abonnement, récupérer les clés de compte de stockage ou régénérer les clés de compte de stockage.

Lorsque vous créez un nouveau compte de stockage, vous sélectionnez un modèle de déploiement standard ou le Gestionnaire de ressources. Le modèle classique de création de ressources dans Azure autorise uniquement l’accès de type tout ou rien à l’abonnement et à son tour, le compte de stockage.

Ce guide se concentre sur le modèle de gestionnaire de ressources qui est le moyen recommandé pour créer des comptes de stockage. Avec les comptes de stockage de gestionnaire de ressources, plutôt que donnant accès à tout l’abonnement, vous pouvez contrôler l’accès à un niveau plus limité sur le plan de gestion à l’aide du contrôle d’accès basée sur les rôles (RBAC).

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Comment faire pour sécuriser votre compte de stockage avec contrôle d’accès basée sur les rôles (RBAC)

Parlons à présent ce qu’est le RBAC, et comment vous pouvez l’utiliser. Chaque abonnement Azure a un Azure d’Active Directory. Applications à partir de ce répertoire, les groupes et les utilisateurs peuvent accéder pour gérer les ressources de l’abonnement Azure qui utilisent le modèle de déploiement du Gestionnaire de ressources. Cela est appelé contrôle d’accès basée sur les rôles (RBAC). Pour gérer cet accès, vous pouvez utiliser le [portail Azure](https://portal.azure.com/), les [Outils de l’interface CLI d’Azure](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou les [API reste fournisseur des ressources de stockage Azure](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Avec le modèle de gestionnaire de ressources, vous placez le compte de stockage dans un accès de groupe et le contrôle des ressources pour le plan de gestion de ce compte de stockage spécifique à l’aide d’Azure Active Directory. Par exemple, vous pouvez accorder à certains utilisateurs la possibilité d’accéder aux clés de la compte de stockage, tandis que les autres utilisateurs peuvent afficher des informations sur le compte de stockage, mais il est impossible d’accéder aux clés de compte de stockage.

####<a name="granting-access"></a>L’autorisation d’accès

L’accès en attribuant le rôle RBAC approprié pour les utilisateurs, les groupes et les applications, à la portée de droite. Pour accorder l’accès à tout l’abonnement, vous affectez un rôle au niveau de l’abonnement. Vous pouvez accorder l’accès à toutes les ressources dans un groupe de ressources par l’octroi d’autorisations au groupe de ressources. Vous pouvez également attribuer des rôles spécifiques à des ressources spécifiques, tels que les comptes de stockage.

Voici les principaux points que vous devez savoir sur l’utilisation de RBAC pour accéder aux opérations de gestion d’un compte de stockage Azure :

-   Lorsque vous affectez des accès, vous affectez en fait un rôle pour le compte que vous souhaitez avoir accès. Vous pouvez contrôler l’accès aux opérations permet de gérer ce compte de stockage, mais pas pour les objets de données dans le compte. Par exemple, vous pouvez accorder des autorisations pour récupérer les propriétés du compte de stockage (par exemple, la redondance), mais pas à un conteneur ou des données dans un conteneur de stockage des objets Blob.

-   Pour une personne soit autorisé à accéder aux objets de données dans le compte de stockage, vous pouvez les autoriser à lire les clés de compte de stockage, et cet utilisateur puis ces clés permettent d’accéder aux objets BLOB, les files d’attente, les tables et les fichiers.

-   Rôles peuvent être affectés à un compte d’utilisateur spécifique, un groupe d’utilisateurs, ou à une application spécifique.

-   Chaque rôle possède une liste d’Actions et pas les Actions. Par exemple, le rôle de collaborateur de l’ordinateur virtuel a une Action de « listKeys » qui permet des clés du compte de stockage pour la lecture. Le collaborateur a « Pas d’Actions », tels que la mise à jour de l’accès pour les utilisateurs dans Active Directory.

-   Inclure les rôles pour le stockage (mais ne sont pas limitées à) suivantes :

    -   Propriétaire : ils peuvent gérer tout, y compris les accès.

    -   Collaborateur – ils peuvent tout faire le propriétaire peut sauf attribuer l’accès. Un utilisateur disposant de ce rôle peut afficher et régénérer les clés de compte de stockage. Avec les touches de compte de stockage, ils peuvent accéder les objets de données.

    -   Lecteur – ils peuvent afficher des informations sur le compte de stockage, à l’exception des secrets. Par exemple, si vous assignez un rôle avec des autorisations de lecture sur le compte de stockage à une personne, ils peuvent afficher les propriétés du compte de stockage, mais ils ne peuvent pas apporter des modifications aux propriétés ou afficher les clés de compte de stockage.

    -   Collaborateur du compte de stockage – ils peuvent gérer le compte de stockage – ils peuvent lire les groupes de ressources de l’abonnement et les ressources et créer et gérer les déploiements de groupe de ressources d’abonnement. Ils peuvent également accéder les clés de compte de stockage, qui à son tour signifie qu’ils peuvent accéder au plan des données.

    -   Accès administrateur d’utilisateurs : ils peuvent gérer l’accès utilisateur pour le compte de stockage. Par exemple, ils peuvent accorder l’accès en lecture à un utilisateur spécifique.

    -   Collaborateurs de la Machine virtuelle – ils peuvent gérer des ordinateurs virtuels, mais pas le compte de stockage auxquels ils sont connectés. Ce rôle peut répertorient les clés de compte de stockage, ce qui signifie que l’utilisateur auquel vous attribuez ce rôle peut mettre à jour le plan de données.

        Dans l’ordre pour un utilisateur de créer un ordinateur virtuel, ils doivent être en mesure de créer le fichier de disque dur virtuel correspondant dans un compte de stockage. Pour ce faire, ils doivent être en mesure de récupérer la clé de compte de stockage et de passer à l’API de création de la machine virtuelle. Par conséquent, ils doivent avoir cette autorisation pour elles peuvent répertorient les touches de compte de stockage.

- La capacité à définir des rôles personnalisés est une fonctionnalité qui vous permet de composer une série d’actions dans une liste d’actions disponibles qui peuvent être effectuées sur les ressources d’Azure.

- L’utilisateur doit configurer dans Azure Active Directory avant de leur affecter un rôle.

- Vous pouvez créer un rapport qui a accordé/révoqué le type d’accès dans lequel et sur quelle étendue à l’aide de PowerShell ou l’interface CLI d’Azure.

####<a name="resources"></a>Ressources

-   [Contrôle d’accès basé sur rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Cet article explique le contrôle d’accès basé sur le rôle de Azure Active Directory et comment il fonctionne.

-   [RBAC : Les rôles intégrée](../active-directory/role-based-access-built-in-roles.md)

    Cet article décrit en détail tous les rôles prédéfinis disponibles dans RBAC.

-   [Présentation du déploiement du Gestionnaire de ressources et déploiement classique](../resource-manager-deployment-model.md)

    Cet article explique le déploiement du Gestionnaire de ressources et les modèles de déploiement classiques et explique les avantages liés à l’aide des groupes de ressources et le Gestionnaire de ressources

-   [Azure Compute, du réseau et les fournisseurs de stockage dans le Gestionnaire de ressources Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    Cet article explique le fonctionnement d’Azure Compute, réseau, les fournisseurs de stockage sous le modèle de gestionnaire de ressources.

-   [Gestion du contrôle d’accès basé sur les rôles avec l’API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Cet article explique comment utiliser l’API REST pour gérer RBAC.

-   [Référence de l’API de stockage Azure ressource fournisseur reste](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Il s’agit de la référence de l’API que vous pouvez utiliser pour gérer votre compte de stockage par programme.

-   [Guide du développeur pour l’authentification avec l’API du Gestionnaire de ressources Azure](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    Cet article explique comment faire pour authentifier à l’aide de l’API du Gestionnaire de ressources.

-   [Contrôle d’accès par rôle pour Microsoft Azure à partir de Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Voici un lien vers une vidéo sur Channel 9 de la conférence de la Ignite MS 2015. Dans cette session, ils parlent accéder aux fonctions de gestion et création de rapports dans Azure et d’Explorer les meilleures pratiques concernant la sécurisation de l’accès aux abonnements Azure à l’aide d’Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Gestion de vos clés de compte de stockage

Clés de compte de stockage sont des chaînes de 512 bits créés par Azure qui, avec le nom de compte de stockage, peut être utilisé pour accéder aux objets de données stockés dans le compte de stockage, par exemple, BLOB, les entités au sein d’une table, des messages de la file d’attente et des fichiers sur un partage de fichiers d’Azure. Contrôle d’accès pour le stockage compte clés contrôle l’accès au plan de données pour ce compte de stockage.

Chaque compte de stockage possède deux clés, appelées « Clé 1 » et « 2 » dans le [portail Azure](http://portal.azure.com/) et clés dans les applets de commande PowerShell. Ceux-ci peuvent être régénérées manuellement à l’aide de plusieurs méthodes, y compris, mais sans limitation à l’aide du [portail Azure](https://portal.azure.com/), PowerShell, la CLI d’Azure, ou par programme à l’aide de la bibliothèque cliente du stockage .NET ou l’API REST de Azure Storage Services.

Il existe plusieurs raisons pour régénérer les clés de votre compte de stockage.

-   Vous pouvez les régénérer sur une base régulière, pour des raisons de sécurité.

-   Vous souhaitez régénérer les clés de votre compte de stockage si quelqu'un géré à pirater une application et récupérer la clé qui a été codée en dur ou enregistrés dans un fichier de configuration, en leur donnant un accès complet à votre compte de stockage.

-   Un autre cas de régénération de la clé est si votre équipe utilise une application Explorateur de stockage qui conserve la clé de compte de stockage, et un des membres de l’équipe quitte. L’application continue de fonctionner, en leur donnant accès à votre compte de stockage une fois qu’ils ne disparaissent. C’est en fait la raison principale qu'ils créé des Signatures de l’accès partagé au niveau des comptes – vous pouvez utiliser un SAS au niveau des comptes au lieu de stocker des touches d’accès rapide dans un fichier de configuration.

####<a name="key-regeneration-plan"></a>Plan de la régénération de la clé

Vous ne souhaitez pas simplement régénérer la clé que vous utilisez sans une planification. Si vous procédez ainsi, vous a coupé tous les accès à ce compte de stockage, qui peut entraîner l’interruption de service importante. C’est pourquoi il y a deux clés. Vous devez regénérer une seule touche à la fois.

Avant la régénération des clés de votre, assurez-vous que vous disposez d’une liste de toutes les applications qui dépendent du compte de stockage, ainsi que d’autres services que vous utilisez dans Azure. Par exemple, si vous utilisez Azure Media Services dépendent de votre compte de stockage, vous devez resynchroniser les touches d’accès avec votre service de support après la régénération de la clé. Si vous utilisez des applications comme un Explorateur de stockage, vous devrez fournir les nouvelles clés pour ces applications. Notez que si vous avez des ordinateurs virtuels dont les fichiers VHD sont stockés dans le compte de stockage, ils ne subiraient en régénérant les clés de compte de stockage.

Vous pouvez régénérer vos clés dans le portail Azure. Une fois les clés sont régénérées ils peuvent prendre jusqu'à 10 minutes pour être synchronisé entre les Services de stockage.

Lorsque vous êtes prêt, voici le processus général détaillant comment vous devez changer votre clé. Dans ce cas, l’hypothèse est que vous utilisez actuellement 1 de la clé et que vous vous apprêtez à tous pour utiliser à la place de touche 2.

1.  Régénérer la touche 2 pour vous assurer qu’elle est sûre. Pour cela, dans le portail Azure.

2.  Dans toutes les applications où se trouve la clé de stockage, modifier la clé de stockage pour utiliser la nouvelle valeur de clé 2. Testez et publiez l’application.

3.  Après tout, les applications et les services sont des et avec succès, régénérez clé 1. Cela garantit que toute personne à qui vous avez donné pas expressément la nouvelle clé n’auront plus accès au compte de stockage.

Si vous utilisez actuellement la clé 2, vous pouvez utiliser le même processus, mais inversez les noms de clé.

Vous pouvez migrer sur deux jours, modification de chaque application pour utiliser la nouvelle clé et sa publication. Après chacun d'entre eux le sont, vous puis revenez en arrière et régénérer l’ancienne clé de manière à ce qu’il ne fonctionne plus.

Une autre option consiste à placer la clé de compte de stockage dans un [Coffre-fort de clé Azure](https://azure.microsoft.com/services/key-vault/) comme un secret et que vos applications de récupérer la clé à partir de là. Puis lorsque vous régénérez la clé et le coffre-fort de clé Azure de mise à jour, les applications ne devront pas être redéployé, car ils reprendra la nouvelle clé à partir du coffre-fort de clé Azure automatiquement. Notez que vous pouvez avoir l’application de lire la clé à chaque fois que vous en avez besoin, ou que vous pouvez mettre en cache en mémoire et en cas d’échec lors de l’utilisation, récupérer de nouveau la clé dans le coffre-fort de clé Azure.

À l’aide de la chambre forte de clé Azure ajoute également un autre niveau de sécurité pour les clés de stockage. Si vous utilisez cette méthode, vous n’aurez jamais stockage clé codées dans un fichier de configuration, ce qui supprime cette avenue d’une personne l’accès aux clés sans l’autorisation spécifique.

Un autre avantage de l’utilisation d’Azure clé coffre-fort est que vous pouvez également contrôler l’accès à vos clés à l’aide d’Azure Active Directory. Cela signifie que vous pouvez accorder l’accès à quelques applications qui ont besoin de récupérer les clés à partir de la chambre forte de clé Azure et de savoir que les autres applications ne seront pas en mesure d’accéder aux clés sans leur accorder l’autorisation spécifiquement.

Remarque : il est recommandé d’utiliser uniquement une des clés dans l’ensemble de vos applications en même temps. Si vous utilisez la touche 1 à certains endroits et touche 2 dans d’autres, vous ne pourrez pas faire pivoter vos clés sans une application perdent l’accès.

####<a name="resources"></a>Ressources

-   [À propos des comptes de stockage Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    Cet article donne une vue d’ensemble des comptes de stockage et présente l’affichage, la copie et la régénération des clés d’accès de stockage.

-   [Référence de l’API de stockage Azure ressource fournisseur reste](https://msdn.microsoft.com/library/mt163683.aspx)

    Cet article contient des liens vers des articles spécifiques sur les clés de compte de stockage de récupération et la régénérer les clés de compte de stockage pour un compte Azure à l’aide de l’API REST. Remarque : Il s’agit des comptes de stockage de gestionnaire de ressources.

-   [Opérations sur les comptes de stockage](https://msdn.microsoft.com/library/ee460790.aspx)

    Cet article dans le Gestionnaire de Service de stockage reste API Reference contient des liens vers des articles spécifiques sur la récupération et de régénérer les clés de compte de stockage à l’aide de l’API REST. Remarque : Il s’agit pour les comptes de stockage classique.

-   [Dire adieu pour la gestion de clés, de gérer l’accès aux données de stockage Azure à l’aide d’Active Directory Azure](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    Cet article explique comment utiliser Active Directory pour contrôler l’accès à vos clés de stockage Azure dans Azure clé coffre-fort. Il montre également comment utiliser un projet d’Automation d’Azure pour régénérer les clés de toutes les heures.

##<a name="data-plane-security"></a>Sécurité de plan de données

Plan de la sécurité des données fait référence aux méthodes utilisées pour sécuriser les objets de données stockés dans le stockage Azure – les objets BLOB, les files d’attente, les tables et les fichiers. Nous avons vu les méthodes pour crypter les données et la sécurité pendant le transit des données, mais comment procéder à l’autorisation d’accès aux objets ?

Il existe deux méthodes pour contrôler l’accès aux objets de données eux-mêmes. Le premier consiste à contrôler l’accès aux clés de compte de stockage, et la seconde utilise des Signatures de l’accès partagé à accorder l’accès aux objets de données spécifique pour une durée spécifique.

Une exception à noter est que vous pouvez autoriser l’accès public à votre BLOB en définissant le niveau d’accès pour le conteneur qui contient les objets BLOB en conséquence. Si vous définissez l’accès pour un conteneur d’objet Blob ou un conteneur, il permettra de public un accès en lecture pour les objets BLOB dans ce conteneur. Cela signifie que toute personne ayant une URL pointant vers un objet blob dans ce conteneur peut l’ouvrir dans un navigateur sans les clés de compte de stockage ou à l’aide d’une Signature d’accès partagé.

###<a name="storage-account-keys"></a>Clés de compte de stockage

Clés de compte de stockage sont des chaînes de 512 bits créés par Azure qui, avec le nom de compte de stockage, peut être utilisé pour accéder aux objets de données stockés dans le compte de stockage.

Par exemple, vous pouvez lire des objets BLOB écrire dans les files d’attente, créer des tables et modifier les fichiers. La plupart de ces actions peuvent être effectuées via le portail d’Azure, ou à l’aide d’une des nombreuses applications Explorateur de stockage. Vous pouvez également écrire du code pour utiliser l’API REST ou l’une des bibliothèques du Client de stockage pour effectuer ces opérations.

Comme expliqué dans la section sur la [Sécurité du plan d’administration](#management-plane-security), accès aux clés de stockage pour un compte de stockage classique peuvent être accordé en donnant un accès complet à l’abonnement Azure. Accès aux clés de stockage pour un compte de stockage en utilisant le modèle de gestionnaire de ressources Azure peut être contrôlé via le contrôle d’accès basée sur les rôles (RBAC).

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Comment déléguer l’accès aux objets dans votre compte à l’aide de Signatures de l’accès partagé et les stratégies d’accès stockées

Une Signature accès partagé est une chaîne contenant un jeton de sécurité qui peut être associé à un URI qui vous permet de déléguer l’accès à des objets de stockage et de spécifier des contraintes telles que les autorisations et la plage de date/heure d’accès.

Vous pouvez accorder l’accès à un BLOB, des conteneurs, des messages de file d’attente, des fichiers et des tables. Avec des tables, vous pouvez réellement accorder l’autorisation d’accéder à une plage d’entités dans la table en spécifiant les plages partition et ligne auquel vous souhaitez que l’utilisateur ait accès. Par exemple, si vous avez des données stockées avec une clé de partition d’état géographique, vous pouvez accorder à une personne l’accès aux données uniquement pour la Californie.

Dans un autre exemple, éventuellement donner un jeton SAS qui lui permet d’écrire des entrées dans une file d’attente à une application web et donner un travailleur application de rôle un jeton SAS pour obtenir des messages de la file d’attente et de les traiter. Ou bien, vous pouvez donner à un client un jeton SAS, ils peuvent permet de télécharger des images vers un conteneur dans le stockage Blob et donner une autorisation d’application web pour lire ces images. Dans les deux cas, il existe une séparation des préoccupations, chaque application peut être attribuée simplement l’accès dont ils ont besoin pour effectuer leur tâche. Ceci est possible grâce à l’utilisation des Signatures de l’accès partagé.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Pourquoi vous souhaitez utiliser des Signatures de l’accès partagé

Pourquoi devrais-je utiliser un SAS au lieu de simplement donner votre clé de compte de stockage, qui est beaucoup plus facile ? Communiquez votre clé de compte de stockage est à un partage les clés du Royaume de votre stockage. Il accorde l’accès complet. Une personne peut utiliser vos clés et télécharger leur bibliothèque de musique à votre compte de stockage. Ils pourraient également remplacer vos fichiers infectés par des virus de versions ou voler vos données. Vous fournissent un accès illimité à votre compte de stockage est un élément qui ne doit pas être prise à la légère.

Signatures d’accès partagé, vous pouvez attribuer un client uniquement les autorisations requises pour une durée limitée. Par exemple, si un utilisateur transfère un blob à votre compte, vous pouvez accorder les accès en écriture pour juste assez de temps charger le blob (selon la taille du blob, bien entendu). Et si vous changez d’avis, vous pouvez révoquer cet accès.

En outre, vous pouvez spécifier que les demandes effectuées à l’aide d’un SAS sont limités à une certaine adresse IP ou plage d’adresses IP externe vers Azure. Vous pouvez également exiger que les demandes sont effectuées à l’aide d’un protocole spécifique (HTTPS ou HTTP/HTTPS). Cela signifie que si vous souhaitez uniquement autoriser le trafic HTTPS, vous pouvez définir uniquement le protocole requis pour HTTPS et HTTP trafic sera bloqué.

####<a name="definition-of-a-shared-access-signature"></a>Définition d’une Signature d’accès partagé

Une Signature accès partagé est un ensemble de paramètres de requête ajoutée à l’URL pointant vers la ressource

qui fournit des informations sur l’accès autorisé et la durée pour laquelle l’accès est autorisé. Voici un exemple ; Cet URI fournit un accès en lecture à un objet blob pendant cinq minutes. Remarque que SAS des paramètres de requête doit être codé URL, par exemple de 3 % pour les deux-points ( :) ou % 20 pour un espace.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Comment la Signature d’accès partagé est authentifiée par le Service de stockage Azure

Lorsque le service de stockage reçoit la requête, il utilise les paramètres de la requête d’entrée et crée une signature en utilisant la même méthode que le programme appelant. Il compare ensuite les deux signatures. Si elles s’accordent, le service de stockage peut vérifier la version du service de stockage afin de vous assurer qu’il est valide, vérifiez que la date et l’heure sont dans la fenêtre spécifiée, assurez-vous que l’accès demandé correspond à la demande, etc..

Par exemple, avec notre URL ci-dessus, si l’URL a été pointant vers un fichier au lieu d’un objet blob, cette demande échoue parce qu’elle spécifie que la Signature de l’accès partagé est pour un objet blob. Si la commande reste appelée a été mise à jour d’un objet blob, il échoue parce que la Signature d’accès partagé Spécifie que l’accès en lecture seule est autorisé.

####<a name="types-of-shared-access-signatures"></a>Types de Signatures d’accès partagé

-   Un SAS de niveau de service peut servir à accéder aux ressources spécifiques d’un compte de stockage. Certains exemples sont récupération d’une liste d’objets BLOB dans un conteneur, le téléchargement d’un blob, une entité dans une table de mise à jour, ajout de messages à une file d’attente ou téléchargez un fichier vers un partage de fichiers.

-   Un SAS au niveau des comptes peut servir pour quoi que ce soit un SAS de niveau de service peut être utilisé pour accéder à. En outre, elle peut donner des options pour les ressources qui ne sont pas autorisés avec un SAS de niveau de service, telles que la possibilité de créer des conteneurs, des tables, des files d’attente et des partages de fichiers. Vous pouvez également spécifier à la fois les accès à plusieurs services. Par exemple, vous pouvez accorder à une personne l’accès à des objets BLOB et de fichiers dans votre compte de stockage.

####<a name="creating-an-sas-uri"></a>Création d’un URI SAS

1.  Vous pouvez créer un URI ad hoc à la demande, tous les paramètres de requête définir chaque fois.

    Il s’agit réellement flexible, mais si vous avez un ensemble logique de paramètres sont identiques chaque fois, à l’aide d’une stratégie d’accès stockée est préférable.

2.  Vous pouvez créer une stratégie d’accès stockées pour un tout conteneur, un partage de fichiers, une table ou une file d’attente. Puis vous pouvez utiliser ceci comme base pour les URI de SAS que vous créez. Des autorisations basées sur les stratégies d’accès stockés peuvent être facilement révoquées. Vous pouvez avoir jusqu'à 5 stratégies définies sur chaque conteneur, une file d’attente, un tableau ou un partage de fichiers.

    Par exemple, si vous souhaitez que plusieurs personnes de lire les objets BLOB dans un conteneur spécifique, vous pouvez créer une stratégie d’accès stockées qui indique « accorder l’accès en lecture » et tous les autres paramètres qui seront les mêmes chaque fois. Vous pouvez ensuite créer un URI SAS en utilisant les paramètres de la stratégie d’accès stockées et en spécifiant la date et l’heure d’expiration. L’avantage de ceci est que vous n’êtes pas obligé de spécifier tous les paramètres de requête à chaque fois.

####<a name="revocation"></a>Révocation

Supposons que votre SAS a été compromis, ou que vous souhaitez modifier en raison de la sécurité de l’entreprise ou les réglementations en vigueur. Comment révoquer l’accès à une ressource à l’aide de ce SAS ? Il dépend de comment vous avez créé l’URI SAS.

Si vous utilisez l’URI ad hoc, vous disposez de trois options. Vous pouvez émettre des jetons de SAS avec des stratégies d’expiration court et attendez simplement que le point d’expirer, les associations de sécurité. Vous pouvez renommer ou supprimer la ressource (en supposant que le jeton a été portée à un seul objet). Vous pouvez modifier les touches de compte de stockage. Cette dernière option peut avoir un impact important, en fonction du nombre de services est à l’aide de ce compte de stockage et probablement n’est pas quelque chose que vous voulez faire sans une planification.

Si vous utilisez un SAS dérivé d’une stratégie d’accès stockées, vous pouvez supprimer l’accès en révoquant la stratégie d’accès stockées, vous pouvez simplement modifier afin qu’il a déjà expiré, ou vous pouvez le supprimer complètement. Cela prend effet immédiatement et invalide toutes associations de sécurité créées à l’aide de cette stratégie d’accès stockées. Mise à jour ou de suppression de la stratégie d’accès stockées peuvent personnes impact ce conteneur spécifique, le partage de fichiers, de l’accès à la table ou la file d’attente via SAS, mais si les clients sont écrits afin de leur demandent une nouvelles associations de sécurité lorsque le deviendrait non valide, cette solution fonctionnera correctement.

Parce qu’à l’aide d’un SAS dérivé d’une stratégie d’accès stockées vous donne la possibilité de révoquer cette SAS immédiatement, il est recommandé de toujours utiliser des stratégies d’accès stockées lorsque cela est possible.

####<a name="resources"></a>Ressources

Pour plus d’informations sur l’utilisation des Signatures de l’accès partagé et stockées les stratégies d’accès, avec des exemples, consultez les articles suivants :

-   Ce sont les articles de référence.

    -   [Service SAS](https://msdn.microsoft.com/library/dn140256.aspx)

        Cet article fournit des exemples d’utilisation d’un SAS de niveau de service avec les objets BLOB, messages de la file d’attente, la table et fichiers.

    -   [Construction d’un service SAS](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Création d’un compte SAS](https://msdn.microsoft.com/library/mt584140.aspx)

-   Il s’agit des didacticiels pour créer des Signatures de l’accès partagé et stockées les stratégies d’accès à l’aide de la bibliothèque cliente .NET.

    -   [À l’aide de Signatures d’accès partagé (SAS)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Partagé des Signatures d’accès, partie 2 : Créer et utiliser un SAS avec le Service d’objet Blob](storage-dotnet-shared-access-signature-part-2.md)

        Cet article contient une explication du modèle SAS, exemples de Signatures d’accès partagé, et utilisent des recommandations sur les meilleures pratiques d’associations de sécurité. Vous trouverez également la révocation de l’autorisation accordée.

-   Limitation de l’accès par l’adresse IP (IP ACL)

    -   [Ce qui est un point de terminaison de liste de contrôle d’accès (ACL) ?](../virtual-network/virtual-networks-acl.md)

    -   [Construction d’un Service SAS](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Il s’agit de l’article de référence pour les associations de sécurité de niveau de service ; Il inclut un exemple de ACLing de IP.

    -   [Création d’un compte SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Il s’agit de l’article de référence pour les associations de sécurité au niveau des comptes ; Il inclut un exemple de ACLing de IP.

-   Authentification

    -    [Authentification pour les Services de stockage Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Partagé des Signatures d’accès didacticiels de mise en route

    -   [Didacticiel de mise en route de SAS](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Cryptage en Transit

###<a name="transport-level-encryption--using-https"></a>Cryptage au niveau transport – à l’aide de HTTPS

Un autre que vous devez prendre afin de garantir la sécurité de vos données de stockage Azure consiste à crypter les données entre le client et le stockage Azure. La première recommandation est de toujours utiliser le protocole [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , ce qui garantit une communication sécurisée via Internet.

Vous devez toujours utiliser HTTPS lors de l’appel de l’API reste ou d’accéder à des objets dans le stockage. En outre, les **Signatures de l’accès partagé**, qui peut être utilisé pour déléguer l’accès aux objets de stockage Azure, incluent une option pour spécifier que seul le protocole HTTPS peut être utilisé lors de l’utilisation partagée des Signatures accès, garantissant que quelqu'un envoie des liens avec des jetons SAS utilise le protocole approprié.

####<a name="resources"></a>Ressources

-   [Activer HTTPS pour une application dans Azure Application Service](../app-service-web/web-sites-configure-ssl-certificate.md)

    Cet article vous explique comment activer HTTPS pour une application Web de Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Avec le chiffrement pendant le transit des partages de fichiers Azure

Stockage de fichier Azure prend en charge l’HTTPS lors de l’utilisation de l’API REST, mais sont plus couramment utilisé comme un partage de fichiers SMB est attaché à une machine virtuelle. SMB 2.1 ne prend pas charge le cryptage, les connexions sont autorisées uniquement dans la même région dans Azure. Toutefois, SMB 3.0 prend en charge le cryptage et peut être utilisé avec Windows Server 2012 R2, Windows 8, Windows 8.1 et Windows 10, permettant de cross-région accéder et même access sur le bureau.

Notez que si les partages de fichiers Azure peut être utilisés avec Unix, le client SMB de Linux ne pas prend en charge le cryptage, afin seulement l’accès est autorisé au sein d’une région d’Azure. Prise en charge du cryptage pour Linux est sur la feuille de route des développeurs Linux responsables de la fonctionnalité SMB. Lorsqu’ils ajoutent le cryptage, vous devez la même possibilité pour accéder à un partage de fichier Azure sous Linux, comme vous le faites pour Windows.

####<a name="resources"></a>Ressources

-   [Comment faire pour utiliser le stockage Azure avec Linux](storage-how-to-use-files-linux.md)

    Cet article explique comment télécharger des fichiers et de montage d’un partage de fichier Azure sur un système Linux.

-   [Mise en route de stockage Azure sous Windows](storage-dotnet-how-to-use-files.md)

    Cet article donne une vue d’ensemble des partages de fichiers d’Azure et comment les monter et les utiliser à l’aide de PowerShell et .NET.

-   [Stockage Azure à l’intérieur](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    Cet article annonce la disponibilité générale de stockage Azure et fournit des informations techniques sur le chiffrement SMB 3.0.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>À l’aide du cryptage côté Client pour sécuriser les données que vous envoyez au stockage

Une autre option qui vous permet de garantir que vos données sont sécurisées, tout en cours de transfert entre une application cliente et le stockage est le cryptage côté Client. Les données sont cryptées avant d’être transférées dans le stockage Azure. Lors de l’extraction de données de stockage Azure, les données sont décryptées après réception du côté client. Même si les données sont cryptées à traverser le câble, nous vous conseillons également HTTPS, car celui-ci contient des contrôles d’intégrité de données intégrées qui aide à réduire les erreurs de réseau qui affectent l’intégrité des données.

Cryptage côté client est également une méthode de chiffrement de vos données au repos, comme les données sont stockées dans sa forme cryptée. Nous aborderons cela plus en détail dans la section sur le [cryptage au repos](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Cryptage au repos

Il existe trois fonctionnalités Azure qui assurent le cryptage au repos. Chiffrement de disque Azure est utilisé pour crypter les disques du système d’exploitation et des données dans le Machines virtuelles IaaS. Les deux autres – cryptage côté Client et SSE – sont tous deux utilisés pour crypter les données dans le stockage Azure. Nous allons étudier chacun de ces éléments, puis effectuer une comparaison et voir lorsque chacun d’eux peut être utilisé.

Vous pouvez utiliser le cryptage côté Client pour crypter les données en transit (qui est également stocké sous forme cryptée dans le stockage), vous pouvez simplement utiliser le protocole HTTPS au cours du transfert et disposer d’un moyen pour les données à crypter automatiquement lorsqu’il est stocké. Il existe deux façons de procéder--cryptage de disque Azure et SSE. Une est utilisée pour crypter des données sur les disques du système d’exploitation et les données utilisées par les ordinateurs virtuels directement, et l’autre est utilisé pour crypter les données écrites dans le stockage Blob Azure.

###<a name="storage-service-encryption-sse"></a>Cryptage (SSE) du Service de stockage

SSE vous permet de demander que le service de stockage crypter automatiquement les données lors de l’écriture sur le stockage Azure. Lorsque vous lisez les données de stockage Azure, il sera décrypté par le service de stockage avant d’être retourné. Cela vous permet de sécuriser vos données sans avoir à modifier le code ou ajouter du code à toutes les applications.

Il s’agit d’un paramètre qui s’applique au compte de stockage entier. Vous pouvez activer et désactiver cette fonction en modifiant la valeur du paramètre. Pour ce faire, vous pouvez utiliser le portail Azure, PowerShell, la CLI d’Azure, l’API REST de stockage ressource fournisseur ou la bibliothèque cliente du stockage .NET. Par défaut, SSE est désactivée.

À ce stade, les clés utilisées pour le cryptage sont gérés par Microsoft. Nous génère les clés à l’origine et de gérer le stockage sécurisé des clés, ainsi que la rotation régulière telle que définie par la politique interne de Microsoft. À l’avenir, vous obtenir la capacité de gérer vos propres clés de cryptage et fournir un chemin de migration à partir des clés de gérés par Microsoft aux clés de gérés par le client.

Cette fonctionnalité est disponible pour les comptes Standard et Premium stockage créés en utilisant le modèle de déploiement du Gestionnaire de ressources. SSE s’applique uniquement pour bloquer des objets BLOB, le BLOB de la page et ajouter des objets BLOB. Les autres types de données, y compris les tables, les files d’attente et les fichiers ne seront pas chiffrées.

Données sont cryptées uniquement si SSE est activée et les données sont écrites dans le stockage Blob. Activation ou désactivation de SSE n’affecte pas les données existantes. En d’autres termes, lorsque vous activez le cryptage, il ne sera pas revenir en arrière et chiffrer les données qui existe déjà ; ni déchiffrera les données qui existe déjà lorsque vous désactivez SSE.

Si vous souhaitez utiliser cette fonctionnalité avec un compte de stockage classique, vous pouvez créer un nouveau compte de stockage du Gestionnaire de ressources et utiliser AzCopy pour copier les données vers le nouveau compte. 

###<a name="client-side-encryption"></a>Cryptage côté client

Nous avons mentionné le cryptage côté client lorsque vous abordez le cryptage des données en transit. Cette fonctionnalité vous permet par programmation crypter vos données dans une application cliente avant de l’envoyer sur le câble à écrire dans le stockage Azure et par programme de déchiffrer vos données après l’avoir récupérée à partir du stockage Azure.

Ceci ne permet pas le cryptage en transit, mais il fournit également la fonctionnalité de cryptage au repos. Notez que bien que les données sont cryptées en transit, nous recommandons l’utilisation de HTTPS pour tirer parti des contrôles l’intégrité des données intégré qui aide à réduire les erreurs de réseau qui affectent l’intégrité des données.

Un exemple d’où vous pouvez utiliser Ceci est si vous avez une application web qui stocke des objets BLOB et récupère les objets BLOB, et que vous souhaitez que l’application et les données pour être aussi sécurisé que possible. Dans ce cas, vous utilisez le cryptage côté client. Le trafic entre le client et le Service d’objet Blob Azure contient la ressource chiffrée et personne ne peut interpréter les données en transit et il reconstituer dans votre BLOB privé.

Cryptage côté client est intégré dans le Java et les bibliothèques de client de stockage .NET, qui à leur tour utilisent les API de coffre-fort de clé Azure, rendant très facile à implémenter. Le processus de cryptage et de décryptage des données utilise la technique de l’enveloppe et stocke des métadonnées utilisées par le cryptage dans chaque objet de stockage. Par exemple, pour les objets BLOB, il le stocke dans les métadonnées de l’objet blob, tandis que pour les files d’attente, il ajoute à chaque message de la file d’attente.

Pour le cryptage proprement dit, vous pouvez générer et gérer vos propres clés de cryptage. Vous pouvez également utiliser des clés générées par la bibliothèque cliente du stockage Azure, ou vous pouvez avoir le coffre-fort de clé Azure à générer les clés. Vous pouvez stocker vos clés de chiffrement dans votre espace de stockage de clés sur site, ou vous pouvez les stocker dans un coffre-fort de clé Azure. Azure clé Vault vous permet d’accorder l’accès pour les secrets dans Azure, clé de chambre forte à des utilisateurs spécifiques à l’aide d’Azure Active Directory. Cela signifie que non seulement qui peut lire le coffre-fort de clé Azure et récupérer les clés que vous utilisez pour le chiffrement côté client.

####<a name="resources"></a>Ressources

-   [Crypter et décrypter le BLOB dans le stockage Azure de Microsoft à l’aide de la chambre forte de clé Azure](storage-encrypt-decrypt-blobs-key-vault.md)

    Cet article explique comment utiliser le cryptage de côté client avec Azure clé de stockage en chambre forte, y compris comment créer le KEK et le stocker dans le coffre-fort à l’aide de PowerShell.

-   [Cryptage côté client et Azure coffre-fort de clé pour le stockage de Microsoft Azure](storage-client-side-encryption.md)

    Cet article donne une explication du cryptage côté client et fournit des exemples d’utilisation de la bibliothèque de client de stockage pour crypter et décrypter des ressources à partir des quatre services de stockage. Il parle également Azure clé coffre-fort.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>En utilisant le cryptage de disque Azure pour crypter les disques utilisés par vos machines virtuelles

Chiffrement de disque Azure est une nouvelle fonctionnalité qui est actuellement en mode Aperçu. Cette fonctionnalité vous permet de crypter les disques du système d’exploitation et les disques de données utilisés par un ordinateur virtuel de IaaS. Pour Windows, les lecteurs sont cryptés à l’aide de la technologie de cryptage BitLocker standard. Pour Linux, les disques sont chiffrés à l’aide de la technologie DM-Crypt. Il est intégré avec coffre-fort de clé Azure pour vous permettent de contrôler et de gérer les clés de cryptage de disque.

La solution de cryptage de disque Azure prend en charge les scénarios de cryptage trois clients suivants :

-   Activer le cryptage sur les nouveaux ordinateurs virtuels de IaaS créé à partir de client crypté les fichiers VHD et les clés de chiffrement fournie par le client, qui sont stockés dans un coffre-fort de clé Azure.

-   Activer le cryptage sur les nouveaux IaaS ordinateurs virtuels créés à partir de l’Azure Marketplace.

-   Activer le cryptage sur les machines virtuelles IaaS existantes déjà en cours d’exécution dans Azure.

>[AZURE.NOTE] Pour ordinateurs virtuels Linux en déjà en cours d’exécution dans Azure, ou nouveaux Linux ordinateurs virtuels créés à partir des images sur le marché d’Azure, le cryptage du disque du système d’exploitation n’est pas actuellement pris en charge. Le cryptage du Volume du système d’exploitation pour les machines virtuelles de Linux est pris en charge uniquement pour les ordinateurs virtuels qui ont été cryptés sur site et téléchargement dans Azure. Cette restriction ne s’applique qu’au disque du système d’exploitation ; chiffrement de volumes de données pour une VM Linux est pris en charge.

La solution prend en charge les éléments suivants pour les machines virtuelles de IaaS pré-version publique lorsqu’il est activé dans Microsoft Azure :

-   Intégration avec Azure coffre-fort de clé

-   [A, D et G série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/) standard

-   Activer le cryptage sur VMs IaaS créés à l’aide du modèle de [Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md)

-   Public Azure toutes les [régions](https://azure.microsoft.com/regions/)

Cette fonctionnalité garantit que toutes les données sur les disques de votre ordinateur virtuel est crypté au repos dans le stockage Azure.

####<a name="resources"></a>Ressources

-   [Cryptage disque Azure pour Windows et les ordinateurs virtuels de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    Cet article décrit la version de cryptage de disque Azure et fournit un lien pour télécharger le livre blanc.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparaison de cryptage de disque Azure, SSE et cryptage côté Client

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS VMs et leurs fichiers de disque dur virtuel

Disques utilisés par les ordinateurs virtuels de IaaS, nous recommandons à l’aide du cryptage de disque Azure. Vous pouvez activer SSE pour crypter les fichiers VHD qui sont utilisés pour sauvegarder les disques dans le stockage Azure, mais elle ne crypte les données nouvellement écrites. Cela signifie que si vous créez une machine virtuelle et que vous activez SSE sur le compte de stockage qui contient le fichier de disque dur virtuel, seules les modifications seront cryptées, pas le fichier de disque dur virtuel d’origine.

Si vous créez un ordinateur virtuel à l’aide d’une image depuis le site Marketplace Azure, Azure effectue une [copie superficielle](https://en.wikipedia.org/wiki/Object_copying) de l’image à votre compte de stockage dans le stockage Azure et même si vous avez activé de SSE n’est pas chiffré. Une fois qu’il crée la machine virtuelle et commence la mise à jour de l’image, SSE démarre le chiffrement des données. Pour cette raison, il est préférable d’utiliser le cryptage de disque Azure sur les ordinateurs virtuels créés à partir des images sur le marché d’Azure si vous souhaitez les entièrement chiffré.

Si vous mettez un ordinateur virtuel déjà chiffré dans Azure en local, vous ne pourrez pas télécharger les clés de chiffrement dans Azure clé coffre-fort et continuer à utiliser le cryptage pour cet ordinateur virtuel que vous utilisiez sur site. Chiffrement de disque Azure est activé pour traiter ce scénario.

Si vous avez non cryptées de disque dur virtuel en local, vous pouvez la télécharger dans la galerie sous la forme d’une image personnalisée et mettre en service un ordinateur virtuel à partir de celui-ci. Si cela vous à utiliser les modèles du Gestionnaire de ressources, vous pouvez demander à activer le chiffrement de disque Azure lorsqu’il démarre la machine virtuelle.

Lorsque vous ajoutez un disque de données et montez sur la machine virtuelle, vous pouvez activer le chiffrement de disque Azure sur ce disque de données. Il crypte tout d’abord ce disque de données localement et la couche de gestion de service sera effectuez une écriture différée sur le stockage pour le stockage de contenu est chiffré.

####<a name="client-side-encryption"></a>Cryptage côté client####

Cryptage côté client est la méthode la plus sûre de cryptage de vos données, car il crypte avant de transit et crypte les données au repos. Toutefois, elle exige que vous ajoutez du code à vos applications à l’aide de stockage, vous ne souhaiterez pas faire. Dans ce cas, vous pouvez utiliser HTTPs pour vos données en transit et SSE pour crypter les données au repos.

Avec le chiffrement côté client, vous pouvez crypter les entités de table, les messages de la file d’attente et BLOB. Avec SSE, vous ne pouvez crypter que des objets BLOB. Si vous avez besoin des données de tables et de la file d’attente afin d’être crypté, vous devez utiliser le cryptage côté client.

Cryptage côté client est entièrement géré par l’application. Ceci est l’approche la plus sure, mais est nécessaire effectuer des modifications par programme dans votre application et mettre en place des processus de gestion des clés. Vous utilisez cela lorsque vous souhaitez la sécurité supplémentaire pendant le transit, et que vous souhaitez que vos données stockées cryptées.

Cryptage côté client est une charge plus sur le client, et vous devez en tenir compte dans vos plans de l’évolutivité, en particulier si vous sont de chiffrement et de transfert d’un grand nombre de données.

####<a name="storage-service-encryption-sse"></a>Cryptage (SSE) du Service de stockage

SSE est géré par le stockage Azure. L’utilisation de SSE ne prévoit pas la sécurité des données en transit, mais elle crypte les données comme il est écrit dans le stockage Azure. Il n’existe aucun impact sur les performances lors de l’utilisation de cette fonctionnalité.

Vous pouvez uniquement crypter des blobs de bloc, ajouter des objets BLOB et page BLOB à l’aide de SSE. Si vous devez chiffrer les données de la table ou les données de file d’attente, vous devez envisager d’utiliser le cryptage côté client.

Si vous avez une archive ou une bibliothèque de fichiers de disque dur virtuel que vous utilisez comme base pour la création de nouvelles machines virtuelles, vous pouvez créer un nouveau compte de stockage, activer SSE et ensuite de télécharger les fichiers de disque dur virtuel à ce compte. Les fichiers de disque dur virtuel d’être cryptés par le stockage Azure.

Si vous disposez de cryptage de disque Azure activée pour les disques dans une machine virtuelle et de SSE activé pour le compte de stockage contenant les fichiers du disque dur virtuel, il fonctionne correctement ; il entraînera les données nouvellement écrites cryptées à deux reprises.

##<a name="storage-analytics"></a>Analytique de stockage

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>À l’aide de stockage Analytique pour contrôler le type d’autorisation

Pour chaque compte de stockage, vous pouvez activer Analytique de stockage Azure effectuer la journalisation et de stocker les données de la métrique. Il s’agit d’un excellent outil à utiliser lorsque vous souhaitez vérifier les mesures des performances d’un compte de stockage, ou résoudre un compte de stockage car vous avez des problèmes de performances.

Un autre élément de données que vous pouvez voir dans les journaux d’analytique de stockage est la méthode d’authentification utilisée par un utilisateur lors de l’accès au stockage. Par exemple, le stockage Blob, vous pouvez voir si elles utilisé une Signature accès partagés ou les clés de compte de stockage, ou si le blob d’accéder n’est pas public.

Cela peut être très utile si vous sont étroitement en empêchant l’accès au stockage. Par exemple, dans le stockage Blob vous tous les conteneurs en privé et mettre en œuvre l’utilisation d’un service d’associations de sécurité dans l’ensemble de vos applications. Vous pouvez vérifier les journaux régulièrement pour voir si vos objets BLOB est accessibles à l’aide des touches de compte de stockage, qui peuvent indiquer une violation de la sécurité, ou si les objets BLOB sont publics, mais ils ne doivent pas être.

####<a name="what-do-the-logs-look-like"></a>Les journaux aspect ?

Une fois que vous activez les indicateurs de compte de stockage et journalisation via le portail d’Azure, données d’analytique commence à s’accumulent rapidement. La journalisation et les mesures de chaque service est séparé ; la journalisation est uniquement écrit en activité dans ce compte de stockage, alors que la métrique est enregistrée toutes les minutes, toutes les heures ou tous les jours, en fonction de sa configuration.

Les journaux sont stockés dans le BLOB de bloc dans un conteneur nommé $logs dans le compte de stockage. Ce conteneur est créé automatiquement lorsque le stockage Analytique est activé. Une fois ce conteneur est créé, Impossible de le supprimer, même si vous pouvez supprimer son contenu.

Sous le conteneur $logs, il existe un dossier pour chaque service, et il y a les sous-dossiers pour l’année/mois/jour/heure. Sous heure, les journaux sont simplement numérotés. Il s’agit de l’aspect de la structure de répertoire :

![Affichage des fichiers journaux](./media/storage-security-guide/image1.png)

Chaque demande de stockage Azure est enregistrée. Voici un aperçu d’un fichier journal, en indiquant les premiers champs peu.

![Instantané d’un fichier journal](./media/storage-security-guide/image2.png)

Vous pouvez voir que vous pouvez utiliser les journaux pour effectuer le suivi de tout type d’appels à un compte de stockage.

####<a name="what-are-all-of-those-fields-for"></a>Quels sont ces champs pour tous ?

Il existe un article répertorié dans les ressources ci-dessous qui fournit la liste des champs dans les journaux et leur utilisation pour de nombreuses. Voici la liste des champs dans l’ordre :

![Instantané des champs dans un fichier journal](./media/storage-security-guide/image3.png)

Nous sommes intéressés dans les entrées de GetBlob, et comment ils sont authentifiés, donc recherchez les entrées avec le type d’opération « Get-Blob » et de vérifier le statut de demande<sup>(colonne 4)</sup> et le type d’autorisation<sup>(colonne 8)</sup> .

Par exemple, dans les premières lignes de la liste ci-dessus, l’état de la demande est « Succès » et le type d’autorisation est « authentifié ». Cela signifie que la demande a été validée à l’aide de la clé de compte de stockage.

####<a name="how-are-my-blobs-being-authenticated"></a>Comment mes objets BLOB est en cours d’authentification ?

Nous avons trois cas qui nous intéresse.

1.  Le blob est public, et il est accessible à l’aide d’une URL sans Signature accès partagé. Dans ce cas, le statut de la demande est « AnonymousSuccess » et le type d’autorisation est « anonyme ».

    1.0 ; 2015-11-17T02:01:29.0488963Z ; GetBlob ; **AnonymousSuccess**; 200 ; 124 37 ; **anonyme**; mystorage...

2.  Le blob est privé et a été utilisé avec une Signature d’accès partagé. Dans ce cas, le statut de la demande est « SASSuccess » et le type d’autorisation est « sas ».

    1.0 ; 2015-11-16T18:30:05.6556115Z ; GetBlob ; **SASSuccess**; 200 ; 416 ; 64 ; **sas**; mystorage...

3.  Le blob est privé et la clé de stockage a été utilisée pour y accéder. Dans ce cas, le statut de la demande est «**Réussite**», et le type d’autorisation est «**authentifié**».

    1.0 ; 2015-11-16T18:32:24.3174537Z ; GetBlob ; **Succès**206 ; 59 ; 22 ; **authentifié**; mystorage...

Vous pouvez utiliser l’Analyseur de Message de Microsoft pour afficher et analyser ces fichiers journaux. Il inclut des fonctions de recherche et de filtre. Par exemple, vous pouvez souhaiter rechercher des instances de GetBlob pour voir si l’utilisation est à vos attentes, c'est-à-dire pour vous assurer que quelqu'un pas accède à votre compte de stockage inapproprié.

####<a name="resources"></a>Ressources

-   [Analytique de stockage](storage-analytics.md)

    Cet article est une présentation analytique de stockage et comment les activer.

-   [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    Cet article illustre le Format de stockage du journal Analytique et décrit en détail les champs disponibles, y compris le type d’authentification, qui indique le type d’authentification utilisé pour la demande.

-   [Surveiller un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)

    Cet article explique comment configurer des mesures de surveillance et enregistrement d’un compte de stockage.

-   [Résolution des problèmes de bout en bout à l’aide des indicateurs du stockage Azure et journalisation, AzCopy et Analyseur de Message](storage-e2e-troubleshooting.md)

    Cet article parle de dépannage à l’aide de l’Analytique de stockage et montre comment utiliser l’Analyseur de Message de Microsoft.

-   [Guide de fonctionnement Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)

    Cet article est la référence de l’Analyseur de Message Microsoft et inclut des liens vers un didacticiel, démarrage rapide et résumé des fonctionnalités.

##<a name="cross-origin-resource-sharing-cors"></a>Ressource d’origine croisée partage (CORS)

###<a name="cross-domain-access-of-resources"></a>Accès aux autres domaines de ressources

Lorsqu’un navigateur web en cours d’exécution dans un domaine effectue une requête HTTP pour une ressource à partir d’un autre domaine, il s’agit d’une demande HTTP d’origine croisée. Par exemple, une page HTML provenant de contoso.com effectue une demande pour un format jpeg hébergé sur fabrikam.blob.core.windows.net. Pour des raisons de sécurité, les navigateurs limitent les requêtes HTTP entre origine initiées à partir de scripts, tels que JavaScript. Cela signifie que, lorsque du code JavaScript sur une page web contoso.com demande que jpeg sur fabrikam.blob.core.windows.net, le navigateur n’autorise pas la demande.

Ce que cela a-t-il avec stockage Azure Ainsi, si vous stockez des éléments statiques tels que les fichiers de données JSON ou XML dans le stockage Blob à l’aide d’un compte de stockage appelée Fabrikam, le domaine pour les actifs sera fabrikam.blob.core.windows.net, et l’application web de contoso.com ne sera pas en mesure d’y accéder à l’aide de JavaScript dans la mesure où les domaines sont différents. Cela est également vrai si vous essayez d’appeler un des Services Azure Storage – tels que le stockage de Table – qui retournent des données JSON d’être traitées par le client JavaScript.

####<a name="possible-solutions"></a>Solutions possibles

Une pour résoudre ce problème consiste à affecter un domaine personnalisé comme « storage.contoso.com » à fabrikam.blob.core.windows.net. Le problème est que vous ne pouvez affecter que ce domaine personnalisé pour le compte de stockage. Que se passe-t-il si les ressources sont stockées dans plusieurs comptes de stockage ?

Une autre façon de résoudre ce problème est d’agir en tant que proxy pour les appels de stockage de l’application web. Cela signifie que si vous téléchargez un fichier dans le stockage Blob, l’application web serait soit écrire localement et puis copiez-le dans le stockage Blob ou il aurait tout lire dans la mémoire et puis les écrire dans le stockage Blob. Alternativement, vous pouvez écrire une application web dédiée (par exemple, une API Web) qui télécharge les fichiers localement et les écrit dans le stockage Blob. Quoi qu’il en soit, vous devez tenir compte de cette fonction lors de l’identification de l’évolutivité des besoins.

####<a name="how-can-cors-help"></a>Comment peut-il CORS ?

Stockage Azure permet d’activer le partage des ressources entre origine CORS –. Pour chaque compte de stockage, vous pouvez spécifier les domaines qui peuvent accéder aux ressources de ce compte de stockage. Par exemple, dans notre cas ci-dessus, nous pouvons activer CORS sur le compte de stockage fabrikam.blob.core.windows.net et le configurer pour autoriser l’accès pour contoso.com. Puis le contoso.com d’application web peut accéder directement aux ressources dans fabrikam.blob.core.windows.net.

Une chose à noter est que CORS autorise l’accès, mais il ne fournit pas d’authentification, ce qui est requise pour tous les non-accès public des ressources de stockage. Cela signifie que vous ne pouvez accéder BLOB que si elles sont publiques ou vous incluez une Signature à accès partagé est ce qui vous donne l’autorisation appropriée. Les fichiers, les files d’attente et les tables n’ont aucun accès public et nécessitent un SAS.

Par défaut, les CORS est désactivé sur tous les services. Vous pouvez activer les CORS à l’aide de l’API REST ou la bibliothèque client de stockage à appeler une des méthodes pour définir les stratégies de service. Si vous le faites, vous incluez une règle CORS, qui est au format XML. Voici un exemple d’une règle CORS qui a été définie pour un compte de stockage à l’aide de l’opération de définition des propriétés de Service pour le Service d’objet Blob. Vous pouvez effectuer cette opération à l’aide de la bibliothèque client de stockage ou d’autres API pour stockage Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Voici la signification de chaque ligne :

-   **AllowedOrigins** Cette option indique à quels domaines non correspondante peuvent demander et recevoir des données à partir du service de stockage. Cela signifie que les forêts contoso.com et fabrikam.com peuvent demander des données depuis le stockage Blob pour un compte de stockage spécifiques. Vous pouvez également le définir sur un caractère générique (\*) pour permettre à tous les domaines d’accéder aux demandes.

-   **AllowedMethods** Il s’agit de la liste des méthodes (verbes de requête HTTP) qui peut être utilisé lors de la création de la demande. Dans cet exemple, seules les PUT et GET sont autorisés. Vous pouvez le définir sur un caractère générique (\*) pour permettre à toutes les méthodes à utiliser.

-   **AllowedHeaders** Voici les en-têtes de demande, le domaine d’origine peut spécifier lors de la création de la demande. Dans cet exemple, tous les en-têtes de métadonnées commençant par x-ms-meta-données, x-ms-meta-cible et x-ms-meta-abc sont autorisés. Le caractère générique (\*) indique que tout début d’en-tête avec le préfixe spécifié est autorisé.

-   **ExposedHeaders** Ceci indique que les en-têtes de réponse doivent être exposés par le navigateur à l’émetteur de la demande. Dans cet exemple, un en-tête commençant par « x-ms - meta- » seront exposées.

-   **MaxAgeInSeconds** Il s’agit de la quantité maximale de temps qu’un navigateur met en cache la requête OPTIONS de contrôle en amont. (Pour plus d’informations sur la demande de contrôle en amont, consultez le premier article ci-dessous).

####<a name="resources"></a>Ressources

Pour plus d’informations sur les CORS et comment l’activer, consultez ces ressources.

-   [Prise en charge (CORS) pour les Services de stockage Azure sur Azure.com le partage des ressources entre-origine](storage-cors-support.md)

    Cet article fournit une vue d’ensemble de CORS et sur la façon de définir les règles pour les services de stockage différents.

-   [Prise en charge (CORS) pour les Services de stockage Azure sur MSDN le partage des ressources entre-origine](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Il s’agit de la documentation de référence pour la prise en charge pour les Services de stockage Azure CORS. Il comporte des liens vers des articles à appliquer à chaque service de stockage et montre un exemple et explique chaque élément dans le fichier CORS.

-   [Stockage de Microsoft Azure : Présentation CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Il s’agit d’un lien vers l’article de blog initial annonce CORS et montrant comment l’utiliser.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Forum aux questions sur la sécurité du stockage Azure

1.  **Comment puis-je vérifier l’intégrité des blobs que je suis transfert dans ou en dehors du stockage Azure si je ne parviens pas à utiliser le protocole HTTPS ?**

    Si pour une raison quelconque vous devez utiliser HTTP au lieu de HTTPS et que vous travaillez avec des objets BLOB de bloc, vous pouvez utiliser la vérification MD5 pour vérifier l’intégrité des blobs en cours de transfert. Cela aidera avec protection contre les erreurs de la couche réseau et du transport, mais pas nécessairement avec les attaques par intermédiaires.

    Si vous pouvez utiliser le protocole HTTPS, qui fournit une sécurité de niveau transport, puis en utilisant la vérification MD5 est redondant et inutiles.
    
    Pour plus d’informations, consultez la [Vue d’ensemble de MD5 de Blob Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **Qu’en est-il de la compatibilité FIPS pour le gouvernement des États-Unis ?**

    Les États-Unis FIPS Federal Information Processing Standard () définit les algorithmes cryptographiques approuvés pour une utilisation par les systèmes d’ordinateur gouvernement fédéral américain pour la protection des données sensibles. L’activation de FIPS mode sur un serveur Windows ou le bureau indique le système d’exploitation que les algorithmes de chiffrement validés FIPS seulement doivent être utilisés. Si une application utilise des algorithmes non conformes, interrompt les applications. Les versions avec.NET Framework 4.5.2 ou une version ultérieure, l’application bascule automatiquement pour utiliser des algorithmes compatibles FIPS lorsque l’ordinateur est en mode FIPS, les algorithmes de chiffrement.

    Microsoft laisse chaque client à décider d’activer le mode FIPS. Nous pensons qu’il n’y a aucune raison valable pour les clients qui ne sont pas soumis à des réglementations gouvernementales pour activer le mode FIPS par défaut.

    **Ressources**

-   [Pourquoi nous sommes recommande pas le « Mode FIPS » plus](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    Cet article de blog donne une vue d’ensemble de FIPS et explique pourquoi ils ne pas activer le mode FIPS par défaut.

-   [FIPS 140 Validation](https://technet.microsoft.com/library/cc750357.aspx)

    Cet article fournit des informations sur comment les produits Microsoft et des modules de cryptage conformes à la norme FIPS pour le gouvernement fédéral des États-Unis.

-   [« Cryptographie système : utiliser FIPS pour le cryptage, le hachage et la signature des algorithmes compatibles » effets de paramètres de sécurité dans Windows XP et les versions ultérieures de Windows](https://support.microsoft.com/kb/811833)

    Cet article traite de l’utilisation du mode FIPS dans les anciens ordinateurs Windows.
