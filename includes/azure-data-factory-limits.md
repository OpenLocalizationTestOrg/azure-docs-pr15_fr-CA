Usine de données est un service de mutualisée qui dispose les limites par défaut suivantes pour vous assurer que les abonnements des clients sont protégés contre d’autres charges de travail. La plupart des limites peuvent être facilement déclenchées pour votre abonnement à la limite maximale en contactant le support. 

**Ressources** | **Limite par défaut** | **Limite maximale**
-------- | ------------- | -------------
fabriques de données dans un abonnement Azure | 50 | [Contactez le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
pipelines dans une usine de données | 2500 | [Contactez le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
groupes de données dans une usine de données | 5000 | [Contactez le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
tranches simultanées par le groupe de données | 10 | 10
octets par objet pour des objets pipeline <sup>1</sup> | 200 KO | 2 000 KO
octets par objet de groupe de données et d’objets service liée <sup>1</sup> | 100 KO | 2 000 KO
Noyaux de cluster de la demande de HDInsight dans un abonnement <sup>2</sup> | 48 | [Contactez le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Unité de déplacement de données, <sup>3</sup> le cloud | 8 | [Contactez le support technique](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Nombre de séries d’activité pipeline tentatives | 1000 | MaxInt (32 bits)

<sup>1</sup> le pipeline, groupe de données et les objets de service liés représentent un regroupement logique de votre charge de travail. Les limites de ces objets ne sont pas liées à la quantité de données, vous pouvez déplacer et traiter avec le service Factory de données Azure. Usine de données est conçue pour évoluer pour gérer plusieurs pétaoctets de données.

<sup>2</sup> à la demande HDInsight cœurs sont allouées en dehors de l’abonnement qui contient le factory de données. Par conséquent, la limite indiquée ci-dessus est le Factory de données appliquées limite core pour noyaux de HDInsight à la demande et est différent de la limite de noyau associée à votre abonnement Azure.

<sup>3</sup> unité de déplacement de données cloud (DMU) est utilisée dans une opération de copie de cloud-nuage. Il s’agit d’une mesure qui représente la puissance (il s’agit d’une combinaison de l’unité centrale, de mémoire et d’allocation de ressources réseau) d’une seule unité dans une usine de données. Vous pouvez parvenir à un débit de copie grâce à plus de DMUs pour certains scénarios. Reportez-vous à la section [d’unités de déplacement de données Cloud](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) sur Détails.

**Ressources** | **Limite inférieure par défaut** | **Limite minimale**
-------- | ------------------- | -------------
Intervalle de planification | 15 minutes | 15 minutes
Intervalle entre les tentatives | 1 seconde | 1 seconde
Réessayer la valeur du délai d’attente | 1 seconde | 1 seconde


### <a name="web-service-call-limits"></a>Limites d’appel de service Web

Le Gestionnaire de ressources Azure a des limites pour les appels API. Vous pouvez effectuer des appels d’API à un taux dans les [limites de l’API du Gestionnaire de ressources Azure](../azure-subscription-service-limits.md#resource-group-limits). 


