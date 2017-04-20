<properties
    pageTitle="Services de domaine ActiveDirectory Azure : Fonctionnalités | Microsoft Azure"
    description="Fonctionnalités des Services de domaine ActiveDirectory Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Services de domaine d’Active Directory Azure

## <a name="features"></a>Fonctionnalités
Les fonctionnalités suivantes sont disponibles dans les domaines des Services de domaine Active Directory de Azure gérés.

- **Expérience de déploiement simple :** Vous pouvez activer les Services de domaine Active Directory de Azure pour vos clients d’Azure AD à l’aide de quelques clics. Que vos clients AD Azure est un nuage-locataire ou synchronisé avec votre répertoire local, votre domaine géré peut être mis en service rapidement.

- **Prise en charge de jointure domaine :** Vous pouvez facilement les ordinateurs la jointure de domaines dans le réseau virtuel Azure que n’est disponible dans votre domaine géré. L’expérience d’adjonction au domaine sur un client Windows et fonctionne de systèmes d’exploitation serveur en toute transparence par rapport aux domaines pris en charge par les Services de domaine Active Directory de Azure. Vous pouvez également utiliser la jointure de domaines automatisé outillage par rapport à ces domaines.

- **Instance un domaine par répertoire d’Azure AD :** Vous pouvez créer un seul domaine Active Directory pour chaque répertoire AD Azure.

- **Créer des domaines avec des noms personnalisés :** Vous pouvez créer des domaines avec des noms personnalisés (par exemple, ' contoso100.com') à l’aide des Services de domaine Active Directory de Azure. Vous pouvez utiliser des noms de domaine vérifié ou non vérifié. Si vous le souhaitez, vous pouvez également créer un domaine avec le suffixe de domaine intégré (c'est-à-dire, ' *. onmicrosoft.com') offert par votre annuaire AD Azure.

- **Intégré avec AD Azure :** Vous n’avez pas besoin de configurer ou de gérer la réplication des services de domaine Active Directory de Azure. Comptes utilisateur, les appartenances au groupe et les informations d’identification (mots de passe) de votre annuaire AD Azure sont automatiquement disponibles dans les Services de domaine Active Directory de Azure. Nouveaux utilisateurs, des groupes ou des modifications apportées aux attributs de votre client AD Azure ou votre répertoire local sont automatiquement synchronisées avec les Services de domaine Active Directory de Azure.

- **L’authentification NTLM et Kerberos :** Prend en charge l’authentification NTLM et Kerberos, vous pouvez déployer des applications qui reposent sur l’authentification intégrée de Windows.

- **Utiliser vos informations d’identification/mots de passe d’entreprise :** Mots de passe pour les utilisateurs de vos clients AD Azure fonctionnent avec les Services de domaine Active Directory de Azure. Les utilisateurs peuvent utiliser leurs informations d’identification d’entreprise à des machines de jonction de domaine se connecter interactivement ou via Bureau à distance et s’authentifier auprès du domaine géré.

- **Prise en charge de lecture de liaison LDAP et LDAP :** Vous pouvez utiliser les applications qui reposent sur des liaisons LDAP pour authentifier les utilisateurs dans des domaines pris en charge par les Services de domaine Active Directory de Azure. En outre, les applications qui utilisent des opérations de lecture LDAP pour interroger les attributs utilisateur ou l’ordinateur à partir du répertoire peuvent également fonctionner avec les Services de domaine Active Directory de Azure.

- **Sécurisé LDAP (LDAPS) :** Vous pouvez activer l’accès à l’annuaire sur LDAP sécurisé (LDAPS). Sécuriser l’accès LDAP est disponible au sein du réseau virtuel par défaut. Toutefois, vous pouvez également activer l’accès LDAP sécurisé via internet.

- **La stratégie de groupe :** Vous pouvez utiliser un seul objet GPO intégré chaque pour les utilisateurs et les ordinateurs conteneurs pour assurer la conformité avec les stratégies de sécurité requises pour les comptes d’utilisateurs et les ordinateurs joints au domaine.

- **Gérer le système DNS :** Les membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent gérer le système DNS pour votre domaine géré à l’aide des outils d’administration DNS familiers, tels que le composant logiciel enfichable MMC de l’Administration DNS.

- **Créer des unités d’organisation (UO) personnalisées :** Membres du groupe « Administrateurs de contrôleur de domaine DAS » peuvent créer des unités d’organisation personnalisées dans le domaine géré. Ces utilisateurs ont des privilèges administratifs complets sur les unités d’organisation personnalisées, afin qu’ils peuvent ajouter ou supprimer des comptes de service, des ordinateurs, des groupes, etc. au sein de ces unités d’organisation personnalisées.

- **Disponibles dans plusieurs régions Azure :** Consultez la page [des services Azure par région](https://azure.microsoft.com/regions/#services/) pour connaître les régions Azure dans lequel les Services de domaine Active Directory Azure est disponible.

- **Haute disponibilité :** Les Services de domaine Active Directory Azure offre la haute disponibilité pour votre domaine. Cette fonctionnalité offre la garantie de la disponibilité de service plus élevée et de tolérance aux pannes. Offre de surveillance de l’état intégré automatisé correction des défaillances en tournant les nouvelles instances de remplacer les instances ayant échoué et de fournir un service continu pour votre domaine.

- **Utiliser les outils de gestion familiers :** Vous pouvez utiliser les outils de gestion Windows Server Active Directory familiers tels que le centre d’administration Active Directory ou Active Directory PowerShell pour administrer des domaines gérés.
