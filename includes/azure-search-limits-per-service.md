Stockage est limité par l’espace disque ou par une limite sur le *nombre maximal* d’index ou de documents, selon ce qui se produit en premier. 

Ressources|Gratuit|Base|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Contrat de niveau de service (SLA)|Aucun <sup>1</sup> |Oui |Oui  |Oui |Oui |Oui
Stockage par partition|50 MO |2 GO|25 GO|100 GO|200 GO|200 GO
Partitions par service|N/A|1|12|12|12|3
Taille de la partition|N/A|2 GO|25 GO|100 GO|200 GO |200 GO
Réplicas|N/A|1|12|12|12|12
Index maximales|3|5|50|200|200|1000 par partition ou 3000 par service
Maximum de documents|10 000|1 million|15 millions de partition ou 180 millions de service |60 millions par partition ou 720 millions de service |120 millions par partition ou 1,4 milliard par service|1 million par index ou 200 millions par partition |
Estimation de requêtes par seconde (QPS)|N/A|~ 3 par le réplica|~ 15 par réplica|~ 60 par réplica|~ 60 par réplica|> 60 par réplica

<sup>1</sup> libre et les points de stock aperçu ne proviennent pas avec les contrats de niveau de service (SLA). SLA est appliquées une fois qu’un point de stock est généralement disponible.