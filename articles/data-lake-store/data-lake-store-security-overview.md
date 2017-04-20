<properties
   pageTitle="Vue d’ensemble de la sécurité dans le magasin de données lac | Microsoft Azure"
   description="Comprendre comment le lac Azure Data Store est une banque de données plus sécurisée"
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
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Sécurité dans le magasin de LAC de données Azure

De nombreuses entreprises tirent parti de l’analytique des données volumineuses de perspectives métier afin de les aider à prendre des décisions intelligentes. Une organisation peut disposer d’un environnement réglementé et complexe, avec un nombre croissant d’utilisateurs divers. Il est essentiel pour une entreprise afin de vous assurer que les données critiques de l’entreprise sont stockées en toute sécurité, avec le niveau approprié d’accès accordé à des utilisateurs individuels. Magasin de LAC de données Azure est conçu pour aider à répondre à ces exigences de sécurité. Dans cet article, obtenir des informations sur les fonctionnalités de sécurité lac de magasin de données, y compris :

* Authentification
* Autorisation
* Isolation du réseau
* Protection des données
* L’audit

## <a name="authentication-and-identity-management"></a>Gestion d’identité et d’authentification

L’authentification est le processus par lequel l’identité d’un utilisateur est vérifiée lorsque l’utilisateur interagit avec un magasin de données lac ou tout service qui se connecte au magasin de données lac. Pour l’authentification et de gestion des identités, banque de données lac utilise [Azure Active Directory](../active-directory/active-directory-whatis.md), une identité complète et la solution de cloud de gestion des accès qui simplifie la gestion des utilisateurs et des groupes.

Chaque abonnement Azure peut être associé à une instance d’Azure Active Directory. Uniquement les utilisateurs et les identités de service qui sont définies dans votre service Active Directory de Azure accéder à votre compte de banque de données lac, via le portail Azure, les outils de ligne de commande, ou via des applications client, votre organisation crée à l’aide du SDK de banque Azure Data lac. Principaux avantages de l’utilisation d’Azure Active Directory sous la forme d’un mécanisme de contrôle d’accès centralisé sont :

* Simplifier la gestion du cycle de vie des identités. L’identité d’un utilisateur ou d’un service (une identité principal de service) peut être créée rapidement et rapidement révoquée par simplement la suppression ou la désactivation du compte dans le répertoire.

* Authentification à plusieurs facteurs. [Une authentification multifactorielle](../multi-factor-authentication/multi-factor-authentication.md) fournit une couche supplémentaire de sécurité pour les connexions utilisateur et des transactions.

* Authentification à partir de n’importe quel client via un protocole standard ouvert, par exemple OAuth ou OpenID.

* Fédération avec les fournisseurs d’identité nuage et les services de répertoire d’entreprise.

## <a name="authorization-and-access-control"></a>Autorisation et contrôle d’accès

Après que Azure Active Directory authentifie un utilisateur afin que l’utilisateur peut accéder le lac Azure Data Store, contrôles d’autorisation d’accès des autorisations de magasin de données lac. Magasin de données lac sépare d’autorisation pour les activités liées aux données et de compte de la manière suivante :

* [Contrôle d’accès basé sur les rôles](../active-directory/role-based-access-control-what-is.md) (RBAC) fourni par Azure pour la gestion des comptes
* ACL POSIX pour accéder aux données de la banque


### <a name="rbac-for-account-management"></a>RBAC Gestion de compte

Par défaut, quatre rôles de base sont définies pour la banque de données lac. Les rôles permettent de différentes opérations sur un compte de banque de données lac via le portail Azure, applets de commande PowerShell et autres API. Les rôles de propriétaire et les collaborateurs peuvent effectuer diverses fonctions d’administration sur le compte. Vous pouvez affecter le rôle de lecteur aux utilisateurs uniquement interagissent avec les données.

![Rôles RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Rôles RBAC")

Notez que, bien que les rôles sont affectés pour la gestion des comptes, certains rôles affectent l’accès aux données. Vous devez utiliser les ACL pour contrôler l’accès aux opérations qu’un utilisateur peut effectuer sur le système de fichiers. Le tableau suivant présente un résumé des droits de gestion et de données des droits d’accès pour les rôles par défaut.

| Rôles                    | Droits de gestion               | Droits d’accès aux données | Explication |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Aucun rôle attribué         | Aucun                            | Régi par ACL    | L’utilisateur ne peut pas utiliser les applets de commande PowerShell de Azure Azure portal pour rechercher la banque de données lac. L’utilisateur peut utiliser les outils de ligne de commande uniquement.
| Propriétaire  | Tous les  | Tous les  | Le rôle de propriétaire est un super utilisateur. Ce rôle permet de gérer tous les éléments et dispose d’un accès complet aux données.
| Lecteur   | En lecture seule  | Régi par ACL    | Le rôle de lecteur peut voir tout ce qu’en matière de gestion de compte, telles que les utilisateur est affecté à un rôle. Le rôle de lecteur ne peut pas apporter des modifications.   |
| Collaborateur              | Tous, sauf à ajouter et supprimer des rôles | Régi par ACL    | Le rôle de collaborateur peut gérer certains aspects d’un compte, comme les déploiements et la création et la gestion des alertes. Le rôle de collaborateur ne peut pas ajouter ou supprimer des rôles.
| Administrateur d’accès utilisateur | Ajouter et supprimer des rôles            | Régi par ACL    | Le rôle d’administrateur d’accès utilisateur peut gérer l’accès des utilisateurs aux comptes. |

Pour obtenir des instructions, reportez-vous à la section [affecter des utilisateurs ou des groupes de sécurité pour les comptes de la banque de données lac](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Utilisation des ACL pour les opérations sur les systèmes de fichiers

Magasin de données lac est un système de fichiers hiérarchique comme Hadoop Distributed fichier système très et il prend en charge [Les ACL POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Il contrôle en lecture (r), écriture (w) et exécution (x) des autorisations pour le rôle de propriétaire, le groupe propriétaires et pour les autres utilisateurs et des groupes de ressources. Dans le lac banque publique aperçu des données (la version actuelle), les ACL est activés sur des fichiers individuels dans le dossier racine et sur les sous-dossiers. Les ACL que vous appliquez au dossier racine s’appliquent également à tous les dossiers et fichiers enfants.

Nous vous recommandons de définir des ACL pour plusieurs utilisateurs à l’aide de [groupes de sécurité](../active-directory/active-directory-accessmanagement-manage-groups.md). Ajouter des utilisateurs à un groupe de sécurité et ensuite affecter les ACL pour un fichier ou un dossier à ce groupe de sécurité. Cela est utile lorsque vous souhaitez fournir un accès personnalisé, car vous n’êtes pas limité à l’ajout d’un maximum de neuf entrées d’accès personnalisé. Pour plus d’informations sur la façon de mieux sécuriser les données stockées dans le magasin de données lac en utilisant des groupes de sécurité Active Directory de Azure, consultez [affecter des utilisateurs ou le groupe de sécurité comme les ACL pour le système de fichiers de banque de LAC de données Azure](data-lake-store-secure-data.md#filepermissions).

![Accès à la liste standard et personnalisé] (./media/data-lake-store-security-overview/adl.acl.2.png "Accès à la liste standard et personnalisé")

## <a name="network-isolation"></a>Isolation du réseau

Utiliser le magasin de LAC données pour contrôler l’accès à votre magasin de données au niveau du réseau. Vous pouvez établir des pare-feux et définir une plage d’adresses IP pour les clients de confiance. Une plage d’adresses IP, seuls les clients qui ont une adresse IP dans la plage définie peuvent se connecter au magasin de données lac.

![Paramètres de pare-feu et accès IP] (./media/data-lake-store-security-overview/firewall-ip-access.png "Paramètres de pare-feu et l’adresse IP")

## <a name="data-protection"></a>Protection des données

Les organisations veulent que garantir la sécurité tout au long de son cycle de vie de leurs données critiques. Pour les données en transit, banque de données lac utilise le protocole de sécurité TLS (Transport Layer) standard de l’industrie pour sécuriser les données qui se déplacement entre un client et un magasin de données lac.

Protection des données pour les données au repos sera disponible dans les versions futures.

## <a name="auditing-and-diagnostic-logs"></a>Journaux d’audit et de Diagnostics

Vous pouvez utiliser les journaux d’audit ou de diagnostics, selon si vous recherchez des journaux pour les activités liées à la gestion ou des activités liées aux données.

*  Activités de gestion utilisent l’API du Gestionnaire de ressources Azure et utilisées dans le portail Azure via les journaux d’audit.
*  Les activités liées aux données utilisent les API de reste de WebHDFS et utilisées dans le portail Azure via les journaux de diagnostic.

### <a name="auditing-logs"></a>Journaux d’audit

Pour respecter les réglementations, une organisation peut nécessiter des pistes d’audit suffisante si elle a besoin d’approfondir les incidents spécifiques. Magasin de données lac a intégré de surveillance et d’audit et enregistre toutes les activités de gestion de compte.

Compte gestion des journaux d’audit, afficher et sélectionner les colonnes que vous souhaitez ouvrir une session. Vous pouvez également exporter des journaux d’audit pour le stockage Azure.

![Journaux d’audit] (./media/data-lake-store-security-overview/audit-logs.png "Journaux d’audit")

### <a name="diagnostic-logs"></a>Journaux de diagnostic

Vous pouvez définir des pistes d’audit de l’accès aux données dans le portail Azure (dans les paramètres de Diagnostic) et créer un compte de stockage Azure Blob où les journaux sont stockés.

![Journaux de diagnostic] (./media/data-lake-store-security-overview/diagnostic-logs.png "Journaux de diagnostic")

Après avoir configuré les paramètres de diagnostic, vous pouvez afficher les journaux dans l’onglet **Journaux de Diagnostic** .

Pour plus d’informations sur l’utilisation des journaux de diagnostic avec magasin de LAC de données Azure, consultez les [journaux de diagnostic d’accès pour le magasin de données lac](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Résumé

Les clients d’entreprise exigent une plate-forme de nuage d’analytique de données sécurisée et facile à utiliser. Magasin de LAC de données Azure est conçu pour aider les adresses à que ces exigences grâce à la gestion d’identité et d’authentification via l’intégration Azure Active Directory, l’autorisation basée sur les ACL, l’isolation du réseau, le cryptage des données en transit et placez (venir) et l’audit.

Si vous souhaitez voir les nouvelles fonctionnalités de magasin de données lac, nous envoyer vos commentaires sur le [forum d’UserVoice de banque de données lac](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)
- [Mise en route de la banque de données lac](data-lake-store-get-started-portal.md)
- [Sécuriser les données dans le magasin de données lac](data-lake-store-secure-data.md)
