Vous pouvez créer de nombreux services au sein d’un abonnement, chacun d’eux mis en service à un niveau spécifique, limité uniquement par le nombre de services autorisés à chaque niveau. Par exemple, vous pouvez créer jusqu'à 12 services au niveau de la couche de base et un autre 12 du niveau %s1 dans le même abonnement. Pour plus d’informations sur les couches, consultez [Choisir un point de stock ou couche pour la recherche d’Azure](../articles/search/search-sku-tier.md).

Limites de service maximale peuvent être déclenchés sur demande. Si vous avez besoin d’autres services au sein de la même abonnement, contactez le support technique de Azure.

Ressources|Gratuit|Base|S1|S2|S3 |S3 HD <sup>1</sup>
---|---|---|---|----|---|----
Services maximum |1 |12 |12  |6 |6 |6 
Échelle maximale dans SU <sup>2</sup>|N/a <sup>3</sup>|SU 3 <sup>4</sup> |SU 36|SU 36|SU 36|12 SU, 3 SU <sup>5</sup>

<sup>1</sup> HD de S3 ne gère pas [d’indexeurs](../articles/search/search-indexer-overview.md) pour l’instant. 

<sup>2</sup> unités de recherche (SU) sont des unités facturables par service, réparti comme un *réplica* ou une *partition*. Vous devez les ressources de stockage, d’indexation et les opérations de requête. Pour en savoir plus sur les combinaisons valides qui restent dans les limites maximales, consultez [niveaux d’échelle des ressources pour les requêtes et les index des charges de travail](../articles/search/search-capacity-planning.md). 

<sup>3</sup> libre est basé sur les ressources partagées utilisées par plusieurs abonnés. À ce niveau, il n’existe aucune des ressources dédiées à un abonné particulier. Pour cette raison, échelle maximale est marqué comme non applicable.

<sup>4</sup> basic a une seule partition fixe. À ce niveau, SUs supplémentaires sont utilisés pour allouer davantage de réplicas pour les charges de travail accrue de requête.

<sup>5</sup> S3 HD dispose d’une structure de répartition différentes en termes de combinaisons autorisées. Pour les réplicas, vous pouvez avoir un maximum de 12. Pour les partitions, le maximum est 3.




