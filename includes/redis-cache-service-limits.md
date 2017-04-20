| Ressources                                    | Limite de                                  |
|---------------------------------------------|----------------------------------------|
| Taille du cache                                  | 530 Go ([Contactez-nous](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) pour plus d’informations)                                  |
| Bases de données                                   | 64                                     |
| Max de clients connectés                       | 40 000                                 |
| Redis Cache répliques (haute disponibilité) | 1 |
| Milieu des fragments dans un cache premium avec les clusters    | 10 |

Limite de Cache de Redis Azure et les tailles sont différentes pour chaque niveau de tarification. Pour voir les niveaux de prix et de leur taille associée, consultez [Tarification d’Azure Redis Cache](https://azure.microsoft.com/pricing/details/cache/).

Pour plus d’informations sur les limites de configuration de Cache Redis d’Azure, reportez-vous à la section [configuration du serveur par défaut Redis](redis-cache/cache-configure.md#default-redis-server-configuration).

Parce que la configuration et la gestion d’instances de Cache Redis d’Azure est effectuée par Microsoft, pas de toutes les commandes de Redis sont pris en charge dans le Cache de Redis Azure. Pour plus d’informations, voir [Redis Cache]((redis-cache/cache-configure.md#redis-commands-not-supported-in-azure-redis-cache) de Redis Azure non pris en charge dans les commandes.