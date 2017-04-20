Le tableau suivant répertorie la configuration requise pour les passerelles VPN de la RouteBased et de PolicyBased. Ce tableau s’applique aux modèles de déploiement classiques et le Gestionnaire de ressources. Pour le modèle classique, passerelles VPN de PolicyBased sont les mêmes que les passerelles statiques et basée sur la gamme de passerelles sont les mêmes que les passerelles dynamiques.


|   | **Passerelle VPN PolicyBased Basic** | **Passerelle VPN RouteBased Basic** | **Passerelle VPN Standard de RouteBased**   | **Passerelle VPN RouteBased haute Performance** |
|---|---------------------------------------|---------------------------------------|----------------------------|----------------------------------|
|    **Connectivité de site à Site (S2S)**  | Configuration VPN de PolicyBased        | Configuration VPN de RouteBased  | Configuration VPN de RouteBased     | Configuration VPN de RouteBased    |
| Connectivité de **point-à-Site (P2S**)      | Non pris en charge   | Prise en charge (peuvent coexister avec S2S)  | Prise en charge (peuvent coexister avec S2S)  | Prise en charge (peuvent coexister avec S2S) |
| **Méthode d’authentification**                 |    Clé prépartagée  | Clé prépartagée pour la connectivité des S2S, des certificats pour la connectivité des P2S | Clé prépartagée pour la connectivité des S2S, des certificats pour la connectivité des P2S | Clé prépartagée pour la connectivité des S2S, des certificats pour la connectivité des P2S |
| **Nombre maximal de connexions de S2S**       | 1                              | 10                                                                    | 10                                | 30                               |
| **Nombre maximal de connexions de P2S**       | Non pris en charge                  | 128                                                                   | 128                               | 128                              |
|**Support de routage active (BGP)**           | Non pris en charge                  | Non pris en charge                                                         | Prise en charge                     | Prise en charge                   |
 
