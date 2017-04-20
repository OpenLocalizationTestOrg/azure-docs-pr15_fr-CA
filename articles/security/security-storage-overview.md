<properties
   pageTitle="Vue d’ensemble de la sécurité stockage Azure | Microsoft Azure"
   description=" Le stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur la durabilité, de disponibilité et d’évolutivité pour répondre aux besoins de leurs clients. Cet article fournit une vue d’ensemble des principales fonctionnalités de sécurité Azure qui peuvent être utilisées avec le stockage Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Vue d’ensemble de la sécurité stockage Azure

Le stockage Azure est la solution de stockage cloud pour les applications modernes qui s’appuient sur la durabilité, de disponibilité et d’évolutivité pour répondre aux besoins de leurs clients. Le stockage Azure fournit un ensemble complet de fonctionnalités de sécurité :

- Le compte de stockage peut être sécurisé à l’aide du contrôle d’accès basée sur les rôles et Azure Active Directory.
- Données peuvent être sécurisées en transit entre une application et d’Azure à l’aide de cryptage côté Client, HTTPS ou SMB 3.0.
- Données peuvent être définies pour être automatiquement cryptés lors de l’écriture dans le stockage Azure à l’aide du cryptage de Service de stockage.
- Disques du système d’exploitation et des données utilisés par les machines virtuelles peuvent être définies afin d’être crypté à l’aide du cryptage de disque Azure.
- Accès délégué aux objets de données dans le stockage Azure peuvent être accordées à l’aide de Signatures de l’accès partagé.
- La méthode d’authentification utilisée par un utilisateur lors de l’accès au stockage peut être suivie à l’aide d’analytique de stockage.

Pour une analyse plus détaillée au niveau de la sécurité dans le stockage Azure, consultez le [guide de sécurité de stockage Azure](../storage/storage-security-guide.md). Ce guide propose une immersion totale dans les fonctionnalités de sécurité du stockage Azure comme clés de compte de stockage, cryptage des données en transit, les autres et analytique de stockage.

Cet article fournit une vue d’ensemble des fonctionnalités de sécurité Azure qui peut être utilisé avec le stockage Azure. Des liens sont fournis aux articles qui donnent des détails de chaque fonctionnalité, vous en apprendrez davantage.

Voici les principales fonctionnalités qui seront traités dans cet article :

- Contrôle d’accès basé sur les rôles
- Accès délégué aux objets de stockage
- Cryptage en transit
- Cryptage au reste de stockage de chiffrement de Service
- Chiffrement de disque Azure
- Chambre forte de clé Azure

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur les rôles (RBAC)

Vous pouvez protéger votre compte de stockage avec contrôle d’accès basée sur les rôles (RBAC). Restriction de l’accès basé sur les principes de sécurité [doivent savoir](https://en.wikipedia.org/wiki/Need_to_know) et de [moindre privilège](https://en.wikipedia.org/wiki/Principle_of_least_privilege) est impératif pour les entreprises qui veulent appliquer des stratégies de sécurité pour accéder aux données. Ces droits d’accès sont accordées en assignant des rôles RBAC appropriés à des groupes et des applications au niveau d’une portée. Vous pouvez utiliser [les rôles RBAC intégrés](../active-directory/role-based-access-built-in-roles.md), tels que les collaborateurs du compte de stockage, pour affecter des privilèges aux utilisateurs.

Pour en savoir plus :

- [Contrôle d’accès basé sur rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Accès délégué aux objets de stockage

Une signature d’accès partagé (SAS) fournit le délégué l’accès à des ressources dans votre compte de stockage. Les associations de sécurité signifie que vous pouvez accorder à qu'un client des autorisations limitées d’objets dans votre compte de stockage pour une période donnée de temps et avec un jeu d’autorisations spécifié. Vous pouvez accorder ces autorisations limitées sans avoir à partager les touches d’accès de votre compte. Les associations de sécurité sont un URI qui englobe, dans ses paramètres de requête, toutes les informations nécessaires pour l’accès authentifié pour une ressource de stockage. Pour accéder à des ressources de stockage avec les associations de sécurité, le client doit uniquement pour les associations de sécurité passez à la méthode ou le constructeur approprié.

Pour en savoir plus :

- [Compréhension du modèle SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Créer et utiliser un SAS avec stockage Blob](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Cryptage en transit
Cryptage en transit est un mécanisme de protection des données lorsqu’elles sont transmises sur des réseaux. Avec le stockage Azure, vous pouvez sécuriser à l’aide de données :

- [Le cryptage au niveau transport](../storage/storage-security-guide.md#encryption-in-transit), tel que HTTPS lorsque vous transférez des données dans ou en dehors du stockage Azure.
- [Cryptage en ligne](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), telles que le chiffrement de SMB 3.0 Azure partages de fichiers.
- [Cryptage côté client](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), pour chiffrer les données avant leur transfert dans le stockage et pour décrypter les données après transfert de stockage.

Pour en savoir plus sur le cryptage côté client :

- [Cryptage côté client pour le stockage de Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Sécurité du nuage contrôle série : le cryptage des données en Transit](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Cryptage au repos

Pour de nombreuses organisations, [cryptage des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire vers la souveraineté des données, de conformité et de confidentialité des données. Il existe trois fonctionnalités Azure qui assurent le cryptage des données « au repos » :

- [Cryptage des services de stockage](../storage/storage-security-guide.md#encryption-at-rest) vous permet de demander que le service de stockage crypter automatiquement les données lors de l’écriture sur le stockage Azure.
- [Cryptage côté client](../storage/storage-security-guide.md#client-side-encryption) fournit également la fonctionnalité de cryptage au repos.
- [Cryptage du disque dur Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) vous permet de chiffrer les disques du système d’exploitation et les disques de données utilisées par un ordinateur virtuel de IaaS.

Pour en savoir plus sur le cryptage des services de stockage :

- [Cryptage des Service stockage Azure](https://azure.microsoft.com/services/storage/) est disponible pour [Le stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Pour plus d’informations sur d’autres types de stockage Azure, consultez [fichiers](https://azure.microsoft.com/services/storage/files/), [disque (stockage Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [Table](https://azure.microsoft.com/services/storage/tables/)et [file d’attente](https://azure.microsoft.com/services/storage/queues/).
- [Cryptage des Service stockage Azure pour données au repos](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Chiffrement de disque Azure

Cryptage de disque Azure pour les machines virtuelles (VM) vous aide à exigences de conformité et de sécurité de l’organisation adresse en cryptant vos disques VM (y compris les disques d’amorçage et de données) avec des clés et des stratégies que vous contrôlez dans [Azure clé coffre-fort](https://azure.microsoft.com/services/key-vault/).

Chiffrement de disque pour les ordinateurs virtuels fonctionne pour les systèmes d’exploitation Linux et Windows. Elle utilise également la clé de coffre-fort pour vous aider à protéger, gérer et auditer l’utilisation de vos clés de cryptage de disque. Toutes les données de vos disques VM est crypté au repos dans vos comptes de stockage Azure à l’aide de la technologie de cryptage standard. La solution de chiffrement de disque pour Windows est basée sur [Le chiffrement de lecteur BitLocker Microsoft](https://technet.microsoft.com/library/cc732774.aspx)et la solution de Linux est basée sur le [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Pour en savoir plus :

- [Cryptage disque Azure pour Windows et les ordinateurs virtuels de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Chambre forte de clé Azure

Cryptage de disque Azure utilise [Azure clé de coffre-fort](https://azure.microsoft.com/services/key-vault/) pour vous aider à contrôler et de gérer des clés de cryptage de disque et les secrets de votre abonnement au coffre-fort clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre stockage Azure. Vous devez utiliser la clé de coffre-fort pour auditer les clés et l’utilisation de la stratégie.

Pour en savoir plus :

- [Quel est le coffre-fort de clé Azure ?](../key-vault/key-vault-whatis.md)
- [Mise en route de la chambre forte de clé Azure](../key-vault/key-vault-get-started.md)
