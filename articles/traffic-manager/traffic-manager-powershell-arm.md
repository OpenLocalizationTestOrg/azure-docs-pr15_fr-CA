<properties
    pageTitle="Prise en charge du Gestionnaire de ressources Azure pour Traffic Manager | Microsoft Azure "
    description="Utilisation de PowerShell pour le Gestionnaire de trafic avec le Gestionnaire de ressources Azure"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Prise en charge du Gestionnaire de ressources Azure pour Azure Traffic Manager

Azure Resource Manager est l’interface de gestion par défaut pour les services dans Azure. Profils de Traffic Manager Azure peuvent être gérés à l’aide des API basées sur le Gestionnaire de ressources Azure et outils.

## <a name="resource-model"></a>Modèle de ressource

Azure Traffic Manager est configuré à l’aide d’un ensemble de paramètres d’un profil de gestionnaire de trafic. Ce profil contient les paramètres DNS, les paramètres de routage du trafic, les paramètres de contrôle de point de terminaison et une liste de points de terminaison de service à laquelle le trafic est routé.

Chaque profil de Traffic Manager est représenté par une ressource de type 'TrafficManagerProfiles'. Au niveau de l’API REST, l’URI pour chaque profil est la suivante :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Comparaison avec l’API classique Azure Traffic Manager

La prise en charge du Gestionnaire de ressources Azure de Traffic Manager utilise une terminologie différente que le modèle de déploiement classique. Le tableau suivant montre les différences entre les termes de gestionnaire de ressources et classique :

| Terme du Gestionnaire de ressources | Terme classique |
|-----------------------|--------------|
| Méthode de routage de trafic | Méthode d’équilibrage de la charge |
| Méthode de priorité | Méthode de basculement |
| Méthode pondérée | Méthode de répétition alternée |
| Méthode de performances | Méthode de performances |

En fonction des commentaires des clients, nous avons modifié la terminologie pour améliorer la clarté et de réduire les malentendus courants. Il n’y a aucune différence de fonctionnalité.

## <a name="limitations"></a>Limitations

Lors du référencement d’un point de terminaison de type « AzureEndpoints » pour une application Web, les points de terminaison Traffic Manager ne peuvent référencer que la valeur par défaut (production), [emplacement de l’application Web](../app-service-web/web-sites-staged-publishing.md). Des emplacements personnalisés ne sont pas pris en charge. Pour résoudre ce problème, des emplacements personnalisés peuvent être configurés à l’aide du type 'ExternalEndpoints'.

## <a name="setting-up-azure-powershell"></a>Configuration de PowerShell d’Azure

Ces instructions utilisent Microsoft Azure PowerShell. L’article suivant explique comment installer et configurer Azure PowerShell.

- [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)

Les exemples de cet article supposent que vous disposez d’un groupe de ressources existant. Vous pouvez créer un groupe de ressources à l’aide de la commande suivante :

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Le Gestionnaire de ressources Azure requiert qu’un emplacement de tous les groupes de ressources. Cet emplacement est utilisé par défaut pour les ressources créées dans ce groupe de ressources. Toutefois, étant des ressources de profil Traffic Manager global, pas régionaux, le choix de l’emplacement de groupe de ressource n’a aucun impact sur Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil de gestionnaire de trafic

Pour créer un profil de Traffic Manager, utilisez l’applet de commande New-AzureRmTrafficManagerProfile :

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Le tableau suivant décrit les paramètres :

| Paramètre | Description |
|-----------|-------------|
| Nom | Le nom de la ressource pour la ressource de profil du Gestionnaire de trafic. Les profils dans le même groupe de ressource doivent avoir des noms uniques. Ce nom est différent du nom DNS utilisé pour les requêtes DNS.|
| ResourceGroupName | Le nom du groupe de ressources contenant la ressource de profil.|
| TrafficRoutingMethod | Spécifie la méthode de routage de trafic utilisée pour déterminer quel point de terminaison est renvoyé en réponse à une requête DNS. Les valeurs possibles sont 'Performances', 'Weighted' ou 'Priority'.|
| RelativeDnsName | Spécifie la partie nom d’hôte du nom DNS fourni par ce profil Traffic Manager. Cette valeur est combinée avec le nom de domaine DNS utilisé par le Gestionnaire de trafic Azure pour former le nom de domaine pleinement qualifié (FQDN) du profil. Par exemple, la définition de la valeur de « contoso » devient « contoso.trafficmanager.net ».|
| DURÉE DE VIE | Spécifie la DNS Time-to-Live (TTL), en secondes. Durée de vie informe les programmes de résolution DNS Local et les clients DNS en cache les réponses DNS pour ce profil de Traffic Manager combien de temps.|
| MonitorProtocol | Spécifie le protocole à utiliser pour surveiller l’état du point de terminaison. Les valeurs possibles sont 'HTTP' et 'HTTPS'.|
| MonitorPort | Spécifie le port TCP utilisé pour surveiller l’état du point de terminaison.|
| MonitorPath | Spécifie le chemin d’accès par rapport au nom de domaine de point de terminaison utilisé pour tester la santé du point de terminaison.|

L’applet de commande crée un profil de gestionnaire de trafic dans Azure et retourne un objet de profil correspondant à PowerShell. À ce stade, le profil ne contient-elle pas les points de terminaison. Pour plus d’informations sur l’ajout de points de terminaison à un profil de gestionnaire de trafic, reportez-vous à la section [Ajout de points de terminaison de Traffic Manager](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obtenir un profil de gestionnaire de trafic

Pour récupérer un objet de profil Traffic Manager existant, utilisez l’applet de commande Get-AzureRmTrafficManagerProfle :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Cette applet de commande renvoie un objet de profil de Traffic Manager.

## <a name="update-a-traffic-manager-profile"></a>Mettre à jour un profil de gestionnaire de trafic

Modification de profils de Traffic Manager suit un processus en 3 étapes :

1. Extraire le profil à l’aide de Get-AzureRmTrafficManagerProfile ou utiliser le profil retourné par New-AzureRmTrafficManagerProfile.
2. Modifier le profil. Vous pouvez ajouter et supprimer des points de terminaison ou modifier les paramètres de point de terminaison ou de profil. Ces modifications sont des opérations hors ligne. Vous modifiez uniquement l’objet local en mémoire qui représente le profil.
3. Valider les modifications apportées à l’aide de l’applet de commande Set-AzureRmTrafficManagerProfile.

Toutes les propriétés de profil peuvent être modifiées, à l’exception RelativeDnsName du profil. Pour modifier la RelativeDnsName, vous devez supprimer le profil et un nouveau profil avec un nouveau nom.

L’exemple suivant montre comment modifier la durée de vie du profil :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Il existe trois types de points de terminaison de Traffic Manager :

1. **Les points de terminaison Azure** sont des services hébergés dans Azure
2. **Les points de terminaison externes** sont des services hébergés en dehors d’Azure
3. **Les points de terminaison imbriquées** sont utilisés pour construire des hiérarchies imbriquées de profils de gestionnaire de trafic. Les points de terminaison imbriqués activer des configurations de routage de trafic avancées pour les applications complexes.

Dans les trois cas, vous peuvent ajouter des points de terminaison de deux manières :

1. À l’aide d’un processus en 3 étapes décrit précédemment. L’avantage de cette méthode est que plusieurs modifications de point de terminaison peuvent être dans une seule mise à jour.
2. À l’aide de l’applet de commande New-AzureRmTrafficManagerEndpoint. Cette applet de commande ajoute un point de terminaison à un profil de gestionnaire de trafic existant en une seule opération.

## <a name="adding-azure-endpoints"></a>Ajout de points de terminaison Azure

Les points de terminaison Azure référencent des services hébergés dans Azure. Trois types de points de terminaison Azure sont prises en charge :

1. Applications Web Azure
2. Services (qui peuvent contenir un service PaaS ou machines virtuelles de IaaS) en nuage « Classiques »
3. Ressources PublicIpAddress Azure (qui peuvent être associés à un programme d’équilibrage de charge ou une carte d’interface réseau de machine virtuelle). Le PublicIpAddress doit avoir un nom DNS attribué à utiliser dans le Gestionnaire de trafic.

Dans chaque cas :

- Le service est spécifié en utilisant le paramètre 'targetResourceId' de Add-AzureRmTrafficManagerEndpointConfig ou New-AzureRmTrafficManagerEndpoint.
- Le 'Target' et 'EndpointLocation' sont impliquées par le TargetResourceId.
- Indiquant le « poids » est facultatif. Poids ne sont utilisés que si le profil est configuré pour utiliser la méthode de routage de trafic « Weighted ». Dans le cas contraire, ils sont ignorés. Si spécifié, la valeur doit être un nombre compris entre 1 et 1000. La valeur par défaut est '1'.
- Si vous spécifiez le 'Priority' est facultatif. Priorités ne sont utilisées que si le profil est configuré pour utiliser la méthode de routage de trafic 'Priority'. Dans le cas contraire, ils sont ignorés. Les valeurs valides vont de 1 à 1000 avec des valeurs inférieures qui indique une priorité plus élevée. Si spécifié pour un point de terminaison, il doivent être spécifiés pour tous les points de terminaison. Si omis, les valeurs par défaut à partir de '1' sont appliquées dans l’ordre que les points de terminaison sont répertoriés.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemple 1 : Ajout de points de terminaison Web App à l’aide de Add-AzureRmTrafficManagerEndpointConfig

Dans cet exemple, nous avons créer un profil de gestionnaire de trafic et ajouter deux points de terminaison Web App à l’aide de l’applet de commande Add-AzureRmTrafficManagerEndpointConfig.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout d’un point de terminaison de service cloud « classiques » à l’aide de la nouvelle-AzureRmTrafficManagerEndpoint

Dans cet exemple, un point de terminaison de Service en nuage « classique » est ajouté à un profil de gestionnaire de trafic. Dans cet exemple, nous avons spécifié le profil en utilisant les noms de groupe de profil et de ressource plutôt que de transmettre un objet de profil. Les deux approches sont pris en charge.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemple 3 : Ajout d’un point de terminaison publicIpAddress à l’aide de la nouvelle-AzureRmTrafficManagerEndpoint

Dans cet exemple, une ressource d’adresse IP publique est ajoutée au profil de Traffic Manager. L’adresse IP publique doit avoir un nom DNS configuré et peut être lié à la carte réseau d’un ordinateur virtuel ou d’un équilibreur de charge.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Ajout de points de terminaison externes

Traffic Manager utilise les points de terminaison externes pour diriger le trafic vers les services hébergés en dehors d’Azure. Comme avec les points de terminaison Azure, les points de terminaison externes peuvent être ajoutés à l’aide de Add-AzureRmTrafficManagerEndpointConfig, suivi de Set-AzureRmTrafficManagerProfile ou New-AzureRMTrafficManagerEndpoint.

Lorsque vous spécifiez les points de terminaison externes :

- Le nom de domaine du point de terminaison doit être spécifié en utilisant le paramètre 'Target'
- Si la méthode de routage de trafic 'Performances' est utilisée, la « EndpointLocation » est obligatoire. Dans le cas contraire, il est facultatif. La valeur doit être un [nom de région Azure valide](https://azure.microsoft.com/regions/).
- Le « Poids » et « Priorité » sont facultatifs.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Ajout de points de terminaison externes à l’aide de Add-AzureRmTrafficManagerEndpointConfig et AzureRmTrafficManagerProfile de l’ensemble

Dans cet exemple, nous avons créer un profil de gestionnaire de trafic ajouter deux points de terminaison externes et valider les modifications.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout de points de terminaison externes à l’aide de la nouvelle-AzureRmTrafficManagerEndpoint

Dans cet exemple, nous ajoutons un point de terminaison externe à un profil existant. Le profil est spécifié en utilisant les noms de groupe de profil et de ressource.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Ajout de points de terminaison 'Nested'

Chaque profil de Traffic Manager indique une seule méthode de routage du trafic. Toutefois, il existe des scénarios qui requièrent le routage du trafic plus sophistiqué que le routage fournie par un seul profil de Traffic Manager. Vous pouvez imbriquer des profils de Traffic Manager pour combiner les avantages de plus d’une méthode de routage du trafic. Profils imbriqués permettent de modifier le comportement de Traffic Manager par défaut pour la prise en charge de plus grande et les déploiements d’applications plus complexes. Pour des exemples plus détaillés, voir [les profils de gestionnaire de trafic imbriqués](traffic-manager-nested-profiles.md).

Des points de terminaison imbriqués sont configurées sur le profil parent, à l’aide d’un type de point de terminaison spécifique, 'NestedEndpoints'. Lorsque vous spécifiez les points de terminaison imbriqués :

- Le point de terminaison doit être spécifié en utilisant le paramètre 'targetResourceId'
- Si la méthode de routage de trafic 'Performances' est utilisée, la « EndpointLocation » est obligatoire. Dans le cas contraire, il est facultatif. La valeur doit être un [nom de région Azure valide](http://azure.microsoft.com/regions/).
- Le « Poids » et « Priorité » sont facultatifs, en ce qui concerne les points de terminaison Azure.
- Le paramètre 'MinChildEndpoints' est facultatif. La valeur par défaut est '1'. Si le nombre de points de terminaison disponibles tombe sous ce seuil, le profil parent considère comme le profil de l’enfant « dégradé » et dévie le trafic aux autres points de terminaison dans le profil du parent.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Ajout de points de terminaison imbriqués à l’aide de Add-AzureRmTrafficManagerEndpointConfig et AzureRmTrafficManagerProfile de l’ensemble

Dans cet exemple, nous créer des profils nouveaux Traffic Manager enfant et parent, ajouter l’enfant sous la forme d’un point de terminaison imbriqué pour le parent et valider les modifications.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Par souci de concision dans cet exemple, nous n’avez pas ajouté les autres points de terminaison aux profils parente ou enfant.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout de points de terminaison imbriqués à l’aide de la nouvelle-AzureRmTrafficManagerEndpoint

Dans cet exemple, nous ajouter un profil enfant existant comme point de terminaison imbriqué à un profil de parent existant. Le profil est spécifié en utilisant les noms de groupe de profil et de ressource.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Mise à jour d’un point de terminaison de Traffic Manager

Il existe deux façons de mettre à jour d’un point de terminaison Traffic Manager existant :

1. Obtenir le profil de Traffic Manager à l’aide de Get-AzureRmTrafficManagerProfile, les propriétés de point de terminaison dans le profil de mise à jour et validez les modifications apportées à l’aide de Set-AzureRmTrafficManagerProfile. Cette méthode présente l’avantage de pouvoir mettre à jour plus d’un point de terminaison en une seule opération.
2. Obtenir le point de terminaison de Traffic Manager à l’aide de Get-AzureRmTrafficManagerEndpoint, les propriétés de point de terminaison de mise à jour et validez les modifications apportées à l’aide de Set-AzureRmTrafficManagerEndpoint. Cette méthode est plus simple, car il ne nécessite pas dans le tableau de points de terminaison dans le profil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Mise à jour des points de terminaison à l’aide de Get-AzureRmTrafficManagerProfile et AzureRmTrafficManagerProfile de l’ensemble

Dans cet exemple, nous modifions la priorité sur les deux points de terminaison au sein d’un profil existant.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemple 2 : Mise à jour d’un point de terminaison à l’aide de Get-AzureRmTrafficManagerEndpoint et AzureRmTrafficManagerEndpoint de l’ensemble

Dans cet exemple, nous modifions le poids d’un point de terminaison unique dans un profil existant.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Activation et désactivation des points de terminaison et des profils

Traffic Manager permet de points de terminaison individuels être activé et désactivé, ainsi que d’autoriser l’activation et la désactivation de profils entières.
Ces modifications est possible par l’obtention/mise à jour/définition les ressources du point de terminaison ou de profil. Pour rationaliser ces opérations courantes, ils sont également pris en charge par des applets de commande dédiée.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemple 1 : L’activation et la désactivation d’un profil de Traffic Manager

Pour activer un profil de Traffic Manager, utilisez activer-AzureRmTrafficManagerProfile. Le profil peut être spécifié à l’aide d’un objet de profil. L’objet de profil peut être passé par l’intermédiaire du pipeline ou à l’aide de la '-TrafficManagerProfile' paramètre. Dans cet exemple, nous spécifions le profil par le nom de groupe de profil et de ressource.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Pour désactiver un profil de gestionnaire de trafic :

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

L’applet de commande Disable-AzureRmTrafficManagerProfile vous demande de confirmer l’opération. Ce message peut être supprimé à l’aide de la '-Force' paramètre.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemple 2 : Activation et désactivation d’un point de terminaison de Traffic Manager

Pour activer un point de terminaison de Traffic Manager, utilisez activer-AzureRmTrafficManagerEndpoint. Il existe deux façons de spécifier le point de terminaison

1. À l’aide d’un objet TrafficManagerEndpoint transmis via le pipeline ou à l’aide de la '-TrafficManagerEndpoint' paramètre
2. À l’aide du nom de point de terminaison, type de point de terminaison, nom de profil et nom du groupe de ressources :

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

De même, pour désactiver un point de terminaison de Traffic Manager :

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

À l’instar de AzureRmTrafficManagerProfile-désactiver, l’applet de commande Disable-AzureRmTrafficManagerEndpoint vous demande de confirmer. Ce message peut être supprimé à l’aide de la '-Force' paramètre.

## <a name="delete-a-traffic-manager-endpoint"></a>Supprimer un point de terminaison de Traffic Manager

Pour supprimer des points de terminaison individuels, utilisez l’applet de commande Remove-AzureRmTrafficManagerEndpoint :

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Cette applet de commande vous demande de confirmer l’opération. Ce message peut être supprimé à l’aide de la '-Force' paramètre.

## <a name="delete-a-traffic-manager-profile"></a>Supprimer un profil de gestionnaire de trafic

Pour supprimer un profil de Traffic Manager, utilisez l’applet de commande Remove-AzureRmTrafficManagerProfile, en spécifiant les noms de groupe de profil et de ressource :

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Cette applet de commande vous demande de confirmer l’opération. Ce message peut être supprimé à l’aide de la '-Force' paramètre.

Le profil à supprimer peut également être spécifié à l’aide d’un objet de profil :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Cette séquence peut également être transmise :

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Étapes suivantes

[Contrôle de Traffic Manager](traffic-manager-monitoring.md)

[Considérations relatives aux performances de Traffic Manager](traffic-manager-performance-considerations.md)
