## <a name="traffic-manager-profile"></a>Profil du Gestionnaire de trafic

Gestionnaire de trafic et sa ressource de point de terminaison enfant activer DNS routage aux points de terminaison dans Azure et en dehors d’Azure. Cette distribution de trafic est régie par les méthodes de routage de stratégie. Gestionnaire de trafic permet également la santé du point de terminaison à surveiller et le trafic détourné de manière appropriée en fonction de l’état de santé d’un point de terminaison. 

| Propriété | Description |
|---|---|
|**trafficRoutingMethod**| les valeurs possibles sont les *performances*, *Weighted*et *priorité* | 
| **dnsConfig** | Nom de domaine complet pour le profil | 
| **Protocole** | protocole de surveillance, les valeurs possibles sont *HTTP* et *HTTPS*|
| **Port** | surveillance du port |  
| **Chemin d’accès** | analyse du chemin d’accès |
| **Points de terminaison** |  conteneur pour les ressources du point de terminaison | 

### <a name="endpoint"></a>Point de terminaison 

Un point de terminaison est une ressource de l’enfant d’un profil de gestionnaire de trafic. Il représente un service ou de point de terminaison web à quel utilisateur le trafic est réparti en fonction de la stratégie configurée dans la ressource Profils du Gestionnaire de trafic. 

| Propriété | Description | 
|---|---| 
| **Type de** |  le type du point de terminaison, les valeurs possibles sont *Azure End point*, *Point de terminaison externe*et *Le point de terminaison imbriqués* | 
| **targetResourceId** |  adresse IP publique d’un point de terminaison de service ou site web. Cela peut être un point de terminaison Azure ou externe. | 
| **Poids** | poids du point de terminaison utilisé dans la gestion du trafic. | 
| **Priorité** | priorité du point de terminaison, utilisée pour définir une action de basculement |

Exemple de gestionnaire de trafic au format Json : 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Ressources supplémentaires

Lisez la [documentation de l’API REST de Traffic Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx) pour plus d’informations.
