<properties 
   pageTitle="Sur les périphériques VPN pour les connexions de passerelle VPN de Site à Site pour des réseaux virtuels Azure | Microsoft Azure"
   description="Cet article décrit les paramètres IPsec pour les connexions de passerelle VPN de S2S et périphériques VPN et contient des liens vers des exemples et des instructions de configuration."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
  tags="azure-resource-manager, azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/13/2016"
   ms.author="yushwang;cherylmc" />

# <a name="about-vpn-devices-for-site-to-site-vpn-gateway-connections"></a>Sur les périphériques VPN pour les connexions de passerelle VPN de Site à Site

Un périphérique VPN est nécessaire pour configurer une connexion VPN de Site à Site (S2S). Les connexions de site à Site peuvent être utilisées pour créer une solution hybride, ou chaque fois que vous souhaitez une connexion sécurisée entre votre réseau local et le réseau virtuel. Cet article traite des périphériques VPN compatibles et des paramètres de configuration. 

>[AZURE.NOTE] Lorsque vous configurez une connexion de Site à Site, une adresse IP IPv4 d’orienté public est requise pour votre périphérique VPN.                                                                                                                                                                               

Si votre périphérique n’apparaît pas dans la table des [périphériques VPN de validation](#devicetable) , consultez la section [périphériques Non validées des VPN](#additionaldevices) de cet article. Il est possible que votre périphérique peut toujours travailler avec Azure. Pour la prise en charge VPN, veuillez contacter le fabricant de votre périphérique.

**Éléments à noter lors de l’affichage des tables :**

- Il y a eu une modification de la terminologie de routage statique et dynamique. Vous allez probablement exécuter dans les deux termes. S’il n’y a aucune modification de fonctionnalité, seuls les noms changent.
    - Routage statique = PolicyBased
    - Routage dynamique = RouteBased
- Spécifications de passerelle VPN de performances élevées et de passerelle VPN de RouteBased sont les mêmes, sauf indication contraire. Par exemple, les périphériques VPN validés qui sont compatibles avec les passerelles VPN de RouteBased sont également compatibles avec la passerelle Azure haute Performance VPN. 


## <a name="devicetable"></a>Périphériques VPN validés 

Nous avons validé un ensemble de périphériques VPN standard en partenariat avec les fabricants de périphériques. Tous les périphériques dans les familles de périphérique contenues dans la liste suivante fonctionne avec les passerelles VPN d’Azure. Consultez la section [Sur la passerelle de VPN](vpn-gateway-about-vpngateways.md) pour vérifier le type de passerelle que vous devez créer pour la solution que vous souhaitez configurer. 

Pour vous aider à configurer votre périphérique VPN, reportez-vous aux liens qui correspondent à la famille de périphérique approprié. Pour la prise en charge VPN, veuillez contacter le fabricant de votre périphérique.



| **Fournisseur**                      | **Gamme d’équipements**                                        | **Version minimale de système d’exploitation**                             | **PolicyBased**                                                                                                                                                                                                             | **RouteBased**                                                                                                                                                                    |
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis                  | Routeurs VPN de série AR                                    | 2.9.2                                              | À venir                                                                                                                                                                                                                                          | N’est pas compatible                                                                                                                                                                                               |
| Barracuda Networks, Inc.        | Série F Barracuda NextGen pare-feu             | PolicyBased : 5.4.3, RouteBased : 6.2.0  | [Instructions de configuration](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) | [Instructions de configuration](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW)                                                                                                                                                                                              |
| Barracuda Networks, Inc.        |  Série X de pare-feu NextGen Barracuda                 | Le pare-feu Barracuda 6.5 | [Pare-feu Barracuda](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) | N’est pas compatible                                                                                                                                                                                               |
| Brocade                         | VRouter de Vyatta 5400                                      | Routeur virtuel 6.6R3 GA                            | [Instructions de configuration](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)                                       | N’est pas compatible                                                                                                                                                                                               |
| Point de vérification                     | Passerelle de sécurité                                         | R75.40, R75.40VS                                     | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)                                         | [Instructions de configuration](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco                           | ASA                                                      | 8.3                                                | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASA)                                                                                                                                                                        | N’est pas compatible                                                                                                                                                                                               |
| Cisco                           | RÉCUPÉRATION AUTOMATIQUE DU SYSTÈME                                                      | IOS 15.1 (PolicyBased), e/s 15.2 (RouteBased)                | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                                                        | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ASR)                                                                                                                                 |
| Cisco                           | ISR                                                      | IOS 15.0 (PolicyBased), e/s 15.1 (RouteBased *)               | [Exemples de Cisco](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                                                        | [Exemples de Cisco *](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Cisco/Current/ISR)                                                                                                                                |
| Citrix                          | NetScaler MPX, SDX, VPX      |10.1 et versions ultérieures                                           | [Instructions d’intégration](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html)                                                                                                                                                                            | N’est pas compatible                                                                                                                                                                                               |
| SonicWALL de Dell                  | Série TZ, NSA, séries SuperMassive, E-classe NSA séries | SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646 )          | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                   | [Instructions - SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [Instructions - SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)                                                                                                                                                                                      |
| F5                              | Série de BIG-IP                                 |           12.0                                            | [Instructions de configuration](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)                                                                                                                                                                          | [Instructions de configuration](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling)                                                                                                                                                                                         |
| Fortinet                        | FortiGate                                                | FortiOS 5.2.7                                      | [Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                                                          | [Instructions de configuration](http://docs.fortinet.com/d/fortigate-configuring-ipsec-vpn-between-a-fortigate-and-microsoft-azure)                                                                                                                                  |
| Internet, Japon Initiative (IIJ) | Série SEIL                                              | SEIL / X 4.60, SEIL/B1, 4.60, SEIL/x86 3,20            | [Instructions de configuration](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)                                                                                                                                                                   | N’est pas compatible                                                                                                                                                                                               |
| Juniper                         | SRX                                                      | JunOS 10.2 (PolicyBased), JunOS 11.4 (RouteBased)            | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SRX)                                                                                                                              |
| Juniper                         | J-Series                                                 | JunOS 10.4r9 (PolicyBased), JunOS 11.4 (RouteBased)          | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                                                                 | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/JSeries)                                                                                                                         |
| Juniper                         | ISG                                                      | ScreenOS 6.3 (PolicyBased et RouteBased)                  | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/ISG)                                                                                                                              |
| Juniper                         | SSG                                                      | ScreenOS 6.2 (PolicyBased et RouteBased)                  | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                                                                      | [Exemples de Juniper](https://github.com/Azure/Azure-vpn-config-samples/tree/master/Juniper/Current/SSG)                                                                                                                              |
| Microsoft                       | Service d’accès distant et de routage                        | Windows Server 2012                                | N’est pas compatible                                                                                                                                                                                                                                       | [Exemples de Microsoft](http://go.microsoft.com/fwlink/p/?LinkId=717761)                                                                                           |
| AG de systèmes ouverts | Passerelle de sécurité contrôle de mission | N/A | [Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) | [Guide d’installation](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |
| Openswan                        | Openswan                                                 | 2.6.32                                             | (Disponible prochainement)                                                                                                                                                                                                                                        | N’est pas compatible                                                                                                                                                                                               |
| Palo Alto Networks              | Tous les périphériques PAN-système d’exploitation en cours d’exécution     | PAN-OS 6.1.5 ou ultérieure (PolicyBased), PAN-OS 7.0.5 ou version ultérieure (RouteBased)       | [Instructions de configuration]( https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065)                                                                                                                                                                                          | [Instructions de configuration](https://live.paloaltonetworks.com/t5/Integration-Articles/Configuring-IKEv2-VPN-for-Microsoft-Azure-Environment/ta-p/60340)                                                                                                                                                                                    |
| WatchGuard                      | Tous les                                                      | V11.x de Fireware XTM                                 | [Instructions de configuration](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)                                                                                                                                                                          | N’est pas compatible                                                                                                                                                                                               |

(*) Les routeurs ISR 7200 série prennent uniquement en charge les réseaux privés virtuels de PolicyBased.

## <a name="additionaldevices"></a>Périphériques VPN non validées

Si vous ne voyez pas votre périphérique répertorié dans la table de périphériques VPN de validation, il peut fonctionner avec une connexion de Site à Site. Vérifiez que votre périphérique VPN répond à la configuration minimale requise décrite dans la section Configuration requise de passerelle de l’article [Sur les passerelles VPN](vpn-gateway-about-vpngateways.md#gateway-requirements) . Dispositifs satisfaisant aux exigences minimales fonctionne également parfaitement avec les passerelles VPN. Pour des instructions supplémentaires sur la configuration et la prise en charge, contactez le fabricant de votre périphérique.


## <a name="editing-device-configuration-samples"></a>Modification des exemples de configuration de périphérique

Après le téléchargement de l’exemple de configuration de périphérique VPN fourni, vous devrez remplacer certaines des valeurs afin de refléter les paramètres de votre environnement. 

**Pour modifier un échantillon :**

1. Ouvrez l’exemple à l’aide du bloc-notes. 
1. Rechercher et remplacer toutes les chaînes <*texte*> avec les valeurs qui se rapportent à votre environnement. Veillez à inclure < et >. Lorsqu’un nom est spécifié, le nom que vous sélectionnez doit être unique. Si une commande ne fonctionne pas, consultez la documentation du fabricant du périphérique.

| **Exemple de texte**                | **Passer à**                                                                                                        |
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| &lt;RP_OnPremisesNetwork&gt;           | Le nom choisi pour cet objet. Exemple : myOnPremisesNetwork                                                       |
| &lt;RP_AzureNetwork&gt;                | Le nom choisi pour cet objet. Exemple : myAzureNetwork                                                            |
| &lt;RP_AccessList&gt;                  | Le nom choisi pour cet objet. Exemple : myAzureAccessList                                                         |
| &lt;RP_IPSecTransformSet&gt;           | Le nom choisi pour cet objet. Exemple : myIPSecTransformSet                                                       |
| &lt;RP_IPSecCryptoMap&gt;              | Le nom choisi pour cet objet. Exemple : myIPSecCryptoMap                                                          |
| &lt;SP_AzureNetworkIpRange&gt;         | Spécifiez la plage. Exemple : 192.168.0.0                                                                                  |
| &lt;SP_AzureNetworkSubnetMask&gt;      | Spécifier le masque de sous-réseau. Exemple : 255.255.0.0                                                                            |
| &lt;SP_OnPremisesNetworkIpRange&gt;    | Spécifier la plage de locaux. Exemple : 10.2.1.0                                                                         |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; | Spécifier le masque de sous-réseau local. Exemple : 255.255.255.0                                                              |
| &lt;SP_AzureGatewayIpAddress&gt;       | Ces informations spécifiques à votre réseau virtuel et se trouve dans le portail de gestion en tant **qu’adresse IP de la passerelle**. |
| &lt;SP_PresharedKey&gt;                | Cette information est spécifique à votre réseau virtuel et se trouve dans le portail de gestion en tant que clé de gérer.          |



## <a name="ipsec-parameters"></a>Paramètres IPsec

>[AZURE.NOTE] Bien que les valeurs répertoriées dans le tableau suivant sont pris en charge par la passerelle de VPN d’Azure, il n’existe actuellement aucun moyen de spécifier ou de sélectionner une combinaison spécifique à partir de la passerelle VPN Azure. Vous devez spécifier des contraintes à partir du périphérique VPN sur site. En outre, vous devez PINCE MSS à 1350.

### <a name="ike-phase-1-setup"></a>Programme d’installation de Phase 1 d’IKE

| **Propriété**                                       | **PolicyBased** | **Passerelle RouteBased et Standard ou haute Performance VPN** |
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| Version du protocole IKE                                        | IKEv1                          | IKEv2                                                            |
| Groupe Diffie-Hellman                               | Groupe 2 (1024 bits)             | Groupe 2 (1024 bits)                                               |
| Méthode d’authentification                              | Clé prépartagée                 | Clé prépartagée                                                   |
| Algorithmes de cryptage                              | AES256 AES128 3DES             | AES256 3DES                                                      |
| Algorithme de hachage                                  | SHA1(SHA128)                   | SHA1(SHA128), SHA2(SHA256)                                                     |
| Durée de vie phase 1 de la sécurité (l’Association) (heure) | 28 800 secondes                 | 10,800 secondes                                                   |


### <a name="ike-phase-2-setup"></a>Programme d’installation de IKE Phase 2

| **Propriété**                                                             | **PolicyBased**                 | **Passerelle RouteBased et Standard ou haute Performance VPN**   |
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| Version du protocole IKE                                                              | IKEv1                                          | IKEv2                                                              |
| Algorithme de hachage                                                        | SHA1(SHA128)                                   | SHA1(SHA128)                                                       |
| Durée de vie phase 2 sécurité Association (SA) (heure)                        | 3 600 secondes                                  | 3 600 secondes                                                                  |
| Durée de vie phase 2 sécurité Association (SA) (débit)                  | 102,400,000 KO                                 | -                                                                  |
| Cryptage d’association de sécurité IPsec et offre d’authentification (par ordre de préférence) | 1. 2 ESP AES256. ESP-AES128 3. ESP-3DES 4. N/A | Consultez *l’offre de l’Association de sécurité IPsec de passerelle de RouteBased (SA)* (ci-dessous) |
| Confidentialité de transmission parfaite (PFS)                                            | N°                                             | Aucun (*)|
| Détection de homologue morts                                                      | Non pris en charge                                  | Prise en charge                                                          |

(*) Passerelle Azure comme répondeur IKE peut accepter PFS Diffie-Hellman groupe 1, 2, 5, 14, 24.

### <a name="routebased-gateway-ipsec-security-association-sa-offers"></a>Offre d’Association de sécurité IPsec de passerelle de RouteBased (SA)

Le tableau suivant répertorie les offre d’authentification et de cryptage d’association de sécurité IPsec. Offres figurent l’ordre de préférence que l’offre est présentée ou accepté.

| **Association de sécurité IPsec chiffrement et authentification offre** | **Passerelle Azure en tant qu’initiateur**                               | **Passerelle Azure comme répondeur**                                   |
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1                                                 | ESP AES_256 SHA                                              | ESP AES_128 SHA                                              |
| 2                                                 | ESP AES_128 SHA                                              | ESP 3_DES MD5                                                |
| 3                                                 | ESP 3_DES MD5                                                | ESP 3_DES SHA                                                |
| 4                                                 | ESP 3_DES SHA                                                | SHA1 AH avec ESP AES_128 avec HMAC null                      |
| 5                                                 | SHA1 AH avec ESP AES_256 avec HMAC null                      | SHA1 du protocole AH avec ESP 3_DES avec HMAC null                        |
| 6                                                 | SHA1 AH avec ESP AES_128 avec HMAC null                      | MD5 AH avec ESP 3_DES avec HMAC null, aucune durée de vie proposé |
| 7                                                 | SHA1 du protocole AH avec ESP 3_DES avec HMAC null                        | SHA1 AH avec ESP 3_DES SHA1, aucune durée de vie                    |
| 8                                                 | MD5 AH avec ESP 3_DES avec HMAC null, aucune durée de vie proposé | MD5 AH avec ESP 3_DES MD5, aucune durée de vie                     |
| 9                                                 | SHA1 AH avec ESP 3_DES SHA1, aucune durée de vie                    | ESP DES MD5                                                  |
| 10                                                | MD5 AH avec ESP 3_DES MD5, aucune durée de vie                     | ESP DES SHA1, aucune durée de vie                                   |
| 11                                                | ESP DES MD5                                                  | AH SHA1 avec ESP DES code HMAC null, aucune durée de vie proposés        |
| 12                                                | ESP DES SHA1, aucune durée de vie                                   | AH MD5 avec ESP DES code HMAC null, aucune durée de vie proposés        |
| 13                                                | AH SHA1 avec ESP DES code HMAC null, aucune durée de vie proposés        | SHA1 AH avec ESP DES SHA1, aucune durée de vie                      |
| 14                                                | AH MD5 avec ESP DES code HMAC null, aucune durée de vie proposés        | MD5 AH avec ESP DES MD5, aucune durée de vie                       |
| 15                                                | SHA1 AH avec ESP DES SHA1, aucune durée de vie                      | SHA ESP, aucune durée de vie                                        |
| 16                                                | MD5 AH avec ESP DES MD5, aucune durée de vie                       | ESP MD5, aucune durée de vie                                        |
| 17                                                | -                                                            | AH SHA, aucune durée de vie                                         |
| 18                                                | -                                                            | AH, MD5, aucune durée de vie                                        |


- Vous pouvez spécifier le cryptage IPsec ESP NULL avec les passerelles VPN de performances élevées et de RouteBased. Chiffrement NULL ne fournit pas de protection des données en transit et doit être utilisée uniquement lorsque maximale latence de débit et de minimum est requise.  Les clients peuvent choisir de l’utiliser dans les scénarios de communication-VNet à VNet, ou lorsque le cryptage est appliqué ailleurs dans la solution.

- Pour la connectivité de coexistence via Internet, utilisez les paramètres de passerelle par défaut Azure VPN avec chiffrement et les algorithmes de hachage répertoriés dans les tableaux ci-dessus pour garantir la sécurité de vos communications critiques.





