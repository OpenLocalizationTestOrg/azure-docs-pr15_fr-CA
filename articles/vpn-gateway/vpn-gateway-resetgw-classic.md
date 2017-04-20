<properties
   pageTitle="Réinitialiser une passerelle VPN Azure | Microsoft Azure"
   description="Cet article vous guide dans le processus de réinitialisation de votre passerelle de VPN Azure. L’article s’applique aux passerelles VPN dans le standard et les modèles de déploiement du Gestionnaire de ressources."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Réinitialiser une passerelle de VPN Azure à l’aide de PowerShell


Cet article vous guide dans le processus de réinitialisation de votre passerelle de VPN Azure à l’aide des applets de commande PowerShell. Ces instructions incluent à la fois le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources.

La réinitialisation de la passerelle VPN d’Azure est utile si vous perdez la connectivité VPN de coexistence sur un ou plusieurs tunnels VPN de S2S. Dans ce cas, vos périphériques VPN sur site sont tous fonctionne correctement, mais ne sont pas en mesure d’établir des tunnels IPsec avec les passerelles VPN d’Azure. 

Chaque passerelle VPN d’Azure se compose de deux instances de machine virtuelle en cours d’exécution dans une configuration active-de réserve. Lorsque vous utilisez l’applet de commande PowerShell de réinitialiser la passerelle, il redémarre la passerelle et puis appliquer de nouveau les configurations de coexistence à elle. La passerelle conserve l’adresse IP publique qu’il possède déjà. Cela signifie que vous n’aurez pas à mettre à jour la configuration du routeur VPN avec une nouvelle adresse IP publique pour la passerelle VPN d’Azure.  

Une fois que la commande est émise, l’instance active actuelle de la passerelle VPN d’Azure est immédiatement réinitialisé. Il y aura un bref intervalle pendant la reprise de l’instance active (en cours de redémarrage,) à l’instance en attente. L’intervalle doit être inférieure à une minute.

Si la connexion n’est pas rétablie après le premier redémarrage, lancez la même commande pour redémarrer la deuxième instance de la machine virtuelle (la nouvelle passerelle active). Si les deux redémarrages sont demandés après l’autre, il y aura un délai légèrement plus long si les deux instances de la machine virtuelle (actifs / veille) sont en cours de redémarrage. Cela va provoquer une discontinuité de plus de temps sur la connectivité VPN, jusqu'à 2 à 4 minutes pour les machines virtuelles terminer le redémarrage.

Après le redémarrage de deux, si vous rencontrez encore des problèmes de connectivité de coexistence, ouvrez une demande de support à partir du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Avant de réinitialiser la passerelle, vérifiez les éléments clés répertoriées ci-dessous pour chaque tunnel VPN IPsec de Site à Site (S2S). Toute incompatibilité dans les éléments entraînera la déconnexion des tunnels VPN de S2S. Vérifier et corriger les configurations de vos locaux et les passerelles VPN d’Azure vous évite de redémarrages inutiles et les interruptions de service pour les autres connexions de travail sur les passerelles.

Avant de réinitialiser la passerelle, vérifiez les éléments suivants :

- Les adresses IP Internet (VIP) pour à la fois la passerelle VPN d’Azure et la passerelle VPN locale sont configurés correctement dans l’Azure et les stratégies VPN sur site.
- La clé prépartagée doit être le même sur les passerelles VPN à la fois sur site et Azure.
- Si vous appliquez la configuration d’IPsec/IKE spécifique, comme le cryptage, les algorithmes de hachage et PFS (Perfect Forward Secrecy), assurez-vous que les passerelles VPN le Azure et locaux ont les mêmes configurations.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Réinitialiser une passerelle VPN en utilisant le modèle de déploiement de la gestion des ressources

L’applet de commande PowerShell le Gestionnaire de ressources pour réinitialiser la passerelle est `Reset-AzureRmVirtualNetworkGateway`. L’exemple suivant réinitialise la passerelle VPN d’Azure, « VNet1GW », dans le groupe de ressources « TestRG1 ».

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Réinitialiser une passerelle VPN en utilisant le modèle de déploiement classique

L’applet de commande PowerShell pour réinitialiser la passerelle VPN d’Azure est `Reset-AzureVNetGateway`. L’exemple suivant réinitialise la passerelle VPN d’Azure pour le réseau virtuel, appelé « ContosoVNet ».
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Résultat :

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Étapes suivantes
    
Reportez-vous à la [référence d’applet de commande PowerShell la gestion des services](https://msdn.microsoft.com/library/azure/mt617104.aspx) et la [référence de l’applet de commande PowerShell Resource Manager](http://go.microsoft.com/fwlink/?LinkId=828732) pour plus d’informations.






