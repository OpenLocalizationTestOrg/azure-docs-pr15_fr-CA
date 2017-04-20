<properties
    pageTitle="Annonce Azure se connecter : Ports | Microsoft Azure"
    description="Cette page est une page d’informations techniques de référence pour les ports qui doivent être ouverts pour Azure Connect de publicité"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-required-ports-and-protocols"></a>Hybride identité obligatoire des Ports et protocoles
Le document suivant est une référence technique pour fournir des informations sur les ports et protocoles requis qui sont nécessaires à l’implémentation d’une solution d’identification hybride. Utilisez l’illustration suivante et faire référence à la table correspondante.

![Nouveautés d’Azure AD Connect](./media/active-directory-aadconnect-ports/required1.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tableau 1 - AD Azure se connecter et AD local
Ce tableau décrit les ports et protocoles requis pour la communication entre le serveur Azure Connect d’Active Directory et AD local.

Protocole | Ports | Description
--------- | --------- |---------
DNS|53 (TCP/UDP)| Recherches DNS de la forêt de destination.
Kerberos|88 (TCP/UDP)| Authentification Kerberos pour la forêt Active Directory.
MS-RPC |135 (TCP/UDP)| Utilisé lors de la configuration initiale de l’Assistant d’Azure AD Connect lorsqu’il établit la forêt Active Directory.
LDAP|389 (TCP/UDP)| Utilisé pour l’importation de données à partir d’Active Directory. Données sont cryptées avec Kerberos signe & joint.
LDAP SUR SSL|636 (TCP/UDP)| Utilisé pour l’importation de données à partir d’Active Directory. Le transfert de données est crypté et signé. Utilisé uniquement si vous utilisez SSL.
RPC |Entre 49152 et 65535 (aléatoire élevé RPC Port)(TCP/UDP)| Utilisé lors de la configuration initiale d’Azure AD Connect lorsqu’il établit les forêts Active Directory. Pour plus d’informations, consultez [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)et [KB224196](https://support.microsoft.com/kb/224196) .

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tableau 2 - AD Azure se connecter et Azure AD
Ce tableau décrit les ports et protocoles requis pour la communication entre l’Azure Connect d’AD server et AD Azure.

Protocole |Ports |Description
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Utilisé pour le téléchargement des listes CRL (listes révocation de certificats) pour vérifier les certificats SSL.
HTTPS|443(TCP/UDP)| Utilisé pour la synchronisation avec Active Directory Azure.

Pour obtenir la liste des URL et IP adresses, vous devez ouvrir dans le pare-feu, consultez [Office 365 URL et les plages d’adresses IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-federation-serverswap"></a>Tableau 3 - AD Azure se connecter et les serveurs de fédération/WAP
Ce tableau décrit les ports et protocoles requis pour la communication entre les serveurs de fédération/WAP Azure Connect d’AD server.  

Protocole |Ports |Description
--------- | --------- |---------
HTTP|80 (TCP/UDP)| Utilisé pour le téléchargement des listes CRL (listes révocation de certificats) pour vérifier les certificats SSL.
HTTPS|443(TCP/UDP)| Utilisé pour la synchronisation avec Active Directory Azure.
WinRM|5985| Écouteur WinRM

## <a name="table-4---wap-and-federation-servers"></a>Tableau 4 : WAP et les serveurs de fédération
Ce tableau décrit les ports et protocoles requis pour la communication entre les serveurs de fédération et WAP.

Protocole |Ports |Description
--------- | --------- |---------
HTTPS|443(TCP/UDP)| Utilisé pour l’authentification.

## <a name="table-5---wap-and-users"></a>Tableau 5 - WAP et les utilisateurs
Ce tableau décrit les ports et protocoles requis pour la communication entre les utilisateurs et les serveurs WAP.

Protocole |Ports |Description
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Utilisé pour l’authentification du périphérique.
TCP|49443 (TCP)| Utilisé pour l’authentification par certificat.

## <a name="table-6a--6b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6 a la table & 6 b - agent de santé de connexion AD Azure pour (AD FS/Sync) et Azure AD
Les tableaux suivants décrivent les points de terminaison, les ports et les protocoles qui sont nécessaires pour la communication entre les agents d’intégrité de connexion AD Azure et d’Azure AD

### <a name="table-6a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tableau 6 a - Ports et protocoles pour l’agent de la santé se connecter Azure AD pour (AD FS/Sync) et Azure AD
Ce tableau décrit les ports sortants suivants et les protocoles qui sont nécessaires pour la communication entre les agents d’intégrité de connexion AD Azure Azure AD.  

Protocole |Ports  |Description
--------- | --------- |--------- |
HTTPS|443(TCP/UDP)| Sortant
Bus des services Azure|5671 (TCP/UDP)| Sortant

### <a name="6b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>6 b - AD Azure et les points de terminaison pour l’agent de la santé se connecter Azure AD pour (AD FS/Sync)
Pour une liste des points de terminaison, consultez [la section Configuration requise pour l’agent d’intégrité de connexion AD Azure](active-directory-aadconnect-health-agent-install.md#requirements).
