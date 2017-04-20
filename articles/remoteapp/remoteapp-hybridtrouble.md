
<properties
    pageTitle="Résoudre les problèmes de créer des collections de RemoteApp hybride | Microsoft Azure"
    description="Apprenez à résoudre les échecs de création de collection RemoteApp hybride"
    services="remoteapp"
    documentationCenter=""
    authors="vkbucha"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Résoudre les problèmes de créer des collections de hybride Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Une collection hybride est hébergée dans et stocke les données dans le nuage Azure mais également des données d’access permet aux utilisateurs et des ressources stockées sur votre réseau local. Les utilisateurs peuvent accéder des applications en vous connectant avec leurs informations d’identification d’entreprise synchronisé ou fédéré avec Azure Active Directory. Vous pouvez déployer une collection hybride qui utilise un réseau virtuel Azure, ou vous pouvez créer un nouveau réseau virtuel. Il est recommandé que vous créez ou utilisez un sous-réseau de réseau virtuel avec une plage CIDR suffisamment grande pour la croissance attendue pour Azure RemoteApp.

N’avez pas créé votre collection encore ? Les étapes, consultez [créer une collection hybride](remoteapp-create-hybrid-deployment.md) .

Si vous rencontrez des problèmes lors de la création de votre collection, ou si la collection ne fonctionne pas comme il devrait, vérifiez les informations suivantes.

## <a name="your-image-is-invalid"></a>Votre image n’est pas valide ##
Si vous voyez un message du type « GoldImageInvalid » lorsque vous attendez Azure pour provisionner votre collection, cela signifie que l’image de votre modèle ne répond pas à la [définis par les exigences de l’image](remoteapp-imagereqs.md). Ainsi, accédez lire ces [exigences](remoteapp-imagereqs.md), corriger votre image, essayez de créer à nouveau de votre collection.



## <a name="does-your-vnet-have-network-security-groups-defined"></a>Votre VNET possède des groupes de sécurité de réseau définis ? ##
Si vous avez définis sur le sous-réseau que vous utilisez pour votre collection de groupes de sécurité réseau, assurez-vous que ces [ports et URL](remoteapp-ports.md) sont accessibles à partir de votre sous-réseau.

Vous pouvez ajouter des groupes de sécurité de réseau supplémentaires pour les ordinateurs virtuels déployés par vous dans le sous-réseau pour un contrôle plus strict.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Vous utilisez vos propres serveurs DNS ? Et ils sont accessibles à partir de votre sous-réseau VNET ? ##
>[AZURE.NOTE] Vous devez vous assurer que les serveurs DNS de votre VNET sont toujours et toujours en mesure de résoudre les ordinateurs virtuels hébergés dans la VNET. N’utilisez pas pour cela Google DNS.


Pour les collections de hybride vous utilisez vos propres serveurs DNS. Vous spécifiez les dans votre schéma de configuration du réseau ou via le portail de gestion lorsque vous créez le réseau virtuel. Les serveurs DNS sont utilisés dans l’ordre qu’ils sont spécifiés sous forme de basculement (par opposition à répétition alternée).  
Reportez-vous à la [Résolution de noms pour les ordinateurs virtuels et les Instances de rôles](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) pour vous assurer que vos serveurs DNS sont configurés de correcly.

Assurez-vous que les serveurs DNS de votre collection sont accessibles et disponibles à partir du sous-réseau VNET que vous avez spécifié pour cette collection.

Par exemple :

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Définir votre serveur DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Vous utilisez un contrôleur de domaine Active Directory dans votre collection ? ##
Actuellement seulement un domaine Active Directory peut être associé à Azure RemoteApp. La collection hybride prend en charge uniquement les comptes Azure Active Directory qui ont été synchronisées à l’aide d’outil de synchronisation d’annuaire à partir d’un déploiement Active Directory de Windows Server ; plus précisément, soit synchronisé avec l’option de synchronisation de mot de passe soit synchronisé avec la fédération Active Directory Federation Services (ADFS) configurée. Vous devez créer un domaine personnalisé correspondant le suffixe de domaine de votre domaine local et configurer l’intégration de l’annuaire.

Pour plus d’informations, reportez-vous à la section [Configuration de Active Directory pour Azure RemoteApp](remoteapp-ad.md) .

Assurez-vous que les informations de domaine fournies sont valides et que le contrôleur de domaine est accessible à partir de la machine virtuelle créée dans le sous-réseau utilisé pour l’application distante d’Azure. Vérifiez également les informations d’identification du compte de service fournies ont les autorisations nécessaires pour ajouter des ordinateurs au domaine fourni et que le nom de la publicité fourni peut être résolu à partir du DNS fourni dans le VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Le nom de domaine n’a spécifié lorsque vous avez créé votre collection ? ##

Vous avez créé ou ajouté, le nom de domaine doit être un nom de domaine interne (pas votre nom de domaine AD Azure) et doit être dans le format DNS peut être résolu (contoso.local). Par exemple, vous avez un nom interne (contoso.local) d’Active Directory et Active Directory nom (contoso.com), vous devez utiliser le nom interne lorsque vous créez votre collection.
