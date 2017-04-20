<properties
    pageTitle="Accéder à clé Vault pare-feu | Microsoft Azure"
    description="Découvrez comment accéder aux coffre-fort de clé à partir d’une application derrière un pare-feu"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="accessing-key-vault-behind-firewall"></a>L’accès au coffre-fort de clé derrière le pare-feu
### <a name="q-my-key-vault-client-application-needs-to-be-behind-a-firewall-what-portshostsip-addresses-should-i-open-to-enable-access-to-key-vault"></a>Q : mon application de client de coffre-fort de clé doit se trouver derrière un pare-feu, les adresses d’hôtes/ports/IP dois-je ouvrir pour accéder à la clé coffre-fort ?

Pour accéder à un coffre-fort clé que votre application cliente de coffre-fort de clé doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.

- Authentification (via Azure Active Directory)
- Gestion de clé de chambre forte (qui inclut la création/lecture/mise à jour/suppression, ainsi que les stratégies d’accès paramètre) par le biais du Gestionnaire de ressources Azure
- Accéder à et gérer des objets (clés et secrets) stockées dans la clé coffre-fort lui-même, passe par le point de terminaison spécifique vault clé (par exemple, [https://yourvaultname.vault.azure.net](https://yourvaultname.vault.azure.net)).  

En fonction de votre configuration et votre environnement, il existe quelques différences.   

## <a name="ports"></a>Ports

Tout le trafic vers la chambre forte de clé pour toutes les 3 fonctions (accès de plan d’authentification, de gestion et de données) va sur HTTPS : Port 443. Toutefois pour la révocation de certificats, il peut parfois le trafic HTTP (port 80). Les clients qui prennent en charge le protocole OCSP ne doivent pas atteindre des CRL, mais peuvent occasionnellement atteindre la [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl).  

## <a name="authentication"></a>Authentification

Chambre forte de clé client application doit accéder aux points de terminaison Azure Active Directory pour l’authentification. Le point de terminaison utilisé dépend de la configuration de clients DAS et le type d’identité : utilisateur principal, principal du service et le type de compte, par exemple Microsoft Account ou ID d’organisation.  

| Type de l’entité de sécurité | Port de point de terminaison : |
|----------------|---------------|
| Utilisateur à l’aide de Microsoft Account<br> (par exemple :user@hotmail.com) | **Global :**<br> Login.microsoftonline.com:443<br><br> **Chine Azure :**<br> Login.chinacloudapi.CN:443<br><br>**Azure du gouvernement américain :**<br> connexion-us.microsoftonline.com:443<br><br>**Allemagne Azure :**<br> Login.microsoftonline.de:443<br><br> et <br>Login.Live.com:443   |
| Principal utilisateur/Service à l’aide d’ID d’organisation avec DAS (par exemple :user@contoso.com) | **Global :**<br> Login.microsoftonline.com:443<br><br> **Chine Azure :**<br> Login.chinacloudapi.CN:443<br><br>**Azure du gouvernement américain :**<br> connexion-us.microsoftonline.com:443<br><br>**Allemagne Azure :**<br> Login.microsoftonline.de:443 |
| Entité de Service des utilisateurs à l’aide de ID + ADFS d’Org ou autre point de terminaison fédéré (par exempleuser@contoso.com) | Tous les points de terminaison ci-dessus pour l’ID d’organisation plus ADFS ou d’autres points de terminaison fédérés |

Autres scénarios sont possibles complexes. Pour plus d’informations, reportez-vous à [ [Azure d’un flux de l’authentification Active Directory](/documentation/articles/active-directory-authentication-scenarios/), [Intégration des Applications avec Azure Active Directory](/documentation/articles/active-directory-integrating-applications/) Active Directory protocoles d’authentification](https://msdn.microsoft.com/library/azure/dn151124.aspx) et.  

## <a name="key-vault-management"></a>Gestion des clés de coffre-fort

Pour la gestion de stockage en chambre forte de clé (CRUD et définition de la stratégie d’accès), l’application cliente de clé coffre-fort doit accéder Azure le Gestionnaire de ressources de point de terminaison.  

| Type d’opération | Port de point de terminaison : |
|----------------|---------------|
| Opérations du plan de contrôle de stockage en chambre forte de clé<br> via le Gestionnaire de ressources Azure | **Global :**<br> Management.Azure.com:443<br><br> **Chine Azure :**<br> Management.chinacloudapi.CN:443<br><br> **Azure du gouvernement américain :**<br> Management.usgovcloudapi.NET:443<br><br> **Allemagne Azure :**<br> Management.microsoftazure.de:443 |
| Graphique d’Azure Active Directory API | **Global :**<br> Graph.Windows.NET:443<br><br> **Chine Azure :**<br> Graph.chinacloudapi.CN:443<br><br> **Azure du gouvernement américain :**<br> Graph.Windows.NET:443<br><br> **Allemagne Azure :**<br> Graph.cloudapi.de:443 |

## <a name="key-vault-operations"></a>Clé de billets

Pour toute la gestion d’objet (clés et secrets) coffre-fort clés et opérations cryptographiques, coffre-fort clé client doit accéder au point de fin de chambre forte de clé. En fonction de l’emplacement de votre clé de coffre-fort, le suffixe DNS du point de terminaison est différent. Le format est le point de terminaison en chambre forte de la clé : < nom de coffre-fort >. < région--suffixe dns spécifique-> comme indiqué dans le tableau ci-dessous.  

| Type d’opération | Port de point de terminaison : |
|----------------|---------------|
| Clé de billets comme les opérations de chiffrement des clés, clés de création/lecture/mise à jour/suppression et secrets, set/get balises et autres attributs sur des objets de coffre-fort clés (clés/secrets)     | **Global :**<br> &lt;nom de la chambre forte&gt;. vault.azure.net:443<br><br> **Chine Azure :**<br> &lt;nom de la chambre forte&gt;. vault.azure.cn:443<br><br> **Azure du gouvernement américain :**<br> &lt;nom de la chambre forte&gt;. vault.usgovcloudapi.net:443<br><br> **Allemagne Azure :**<br> &lt;nom de la chambre forte&gt;. vault.microsoftazure.de:443 |

## <a name="ip-address-ranges"></a>Plages d’adresses IP

Service de chambre forte de clé utilise à son tour les autres ressources Azure comme infrastructure de PaaS, il n’est pas possible de fournir une plage spécifique d’adresses IP ayant des points de terminaison de service clés vault à tout moment. Toutefois si votre pare-feu prend en charge les adresses IP plages, veuillez vous reporter au document de [Plages d’IP de Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) .   Pour l’authentification et d’identité (Azure Active Directory), votre application doit être en mesure de se connecter aux points de terminaison décrites dans [l’authentification et des adresses d’identité](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez des questions sur le coffre-fort de la clé, visitez les [Forums de coffre-fort clé Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
