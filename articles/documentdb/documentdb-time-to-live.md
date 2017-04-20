<properties
   pageTitle="Données de DocumentDB avec la durée de vie d’expiration | Microsoft Azure"
   description="Avec durée de vie, Microsoft Azure DocumentDB offre la possibilité d’avoir des documents automatiquement purgées du système après une période de temps."
   services="documentdb"
   documentationCenter=""
   keywords="durée de vie"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Expiration des données dans des collections DocumentDB automatiquement avec la durée de vie

Applications capables de produire et de stocker de grandes quantités de données. Certaines de ces données, telles que les informations ne sont utiles que pendant un laps de temps du session d’utilisateur, des journaux et des données d’événement générée par machine. Une fois les données excédentaires aux besoins de l’application, il est recommandé de purger les données et de réduire les besoins de stockage d’une application.

« Durée de vie » ou la durée de vie, Microsoft Azure DocumentDB fournit la possibilité d’avoir des documents automatiquement purgés à partir de la base de données après une certaine période de temps. La durée par défaut de vie pouvant être définie au niveau de la collection et substituée sur une base par document. Une fois que la durée de vie est définie comme une valeur par défaut de la collection ou au niveau du document, DocumentDB supprimera automatiquement les documents qui existent après ce laps de temps, en secondes, depuis leur dernière modification.

Durée de vie de DocumentDB utilise un décalage par rapport aux lorsque le document a été modifié. Pour ce faire, il utilise le champ DTS qui existe sur tous les documents. Le champ de DTS est un horodatage d’époque de style unix représentant la date et l’heure. Le champ DTS est mis à jour chaque fois qu’un document est modifié. 

## <a name="ttl-behavior"></a>Comportement de la durée de vie

La fonction de la durée de vie est contrôlée par les propriétés de durée de vie à deux niveaux : le niveau collection et le document. Les valeurs sont définies en secondes et sont traités comme un delta de la DTS qui le document a été modifié à.

 1.  DefaultTTL pour la collection.
  * S’il est manquant (ou null), les documents ne sont pas supprimés automatiquement.
  
  * Si et la valeur est « -1 » = infinie – documents n’expirent pas par défaut
  
  * Si et la valeur est un nombre (« n ») – expirent de documents « n » secondes après la dernière modification

 2.  Durée de vie pour les documents : 
  * Propriété est applicable uniquement si DefaultTTL est présent pour la collection parent.
  
  * Remplace la valeur DefaultTTL pour la collection parent.

Dès que le document a expiré (durée de vie + DTS > = heure actuelle du serveur), le document est marqué comme « expiré ». Aucune opération n’est autorisée sur ces documents après cette heure et ils seront exclus des résultats de toutes les requêtes effectuées. Les documents sont supprimés physiquement dans le système et sont supprimés dans l’arrière-plan de façon opportuniste ultérieurement. Il ne consomme pas toute [Demande unités (RUs)](documentdb-request-units.md) à partir du budget de la collection.

La logique ci-dessus peut être affichée dans le tableau suivant :

|       | DefaultTTL manquante, non définie dans la collection | DefaultTTL = -1 dans la collection | DefaultTTL = « n » dans la collection|
| ------------- |:-------------|:-------------|:-------------|
| Durée de vie est manquant sur le document| Rien à substituer au niveau du document, le document et collection ayant aucune notion de durée de vie. | Aucun expirent dans cette collection. | Les documents de cette collection va expirer lorsque n de l’intervalle est écoulé. |
| Durée de vie = -1 sur le document | Rien à substituer au niveau du document, étant donné que la collection ne définit pas la propriété DefaultTTL qui peut remplacer les un document. Durée de vie d’un document est non interprétée par le système. | Aucun expirent dans cette collection. | Le document avec la durée de vie =-1 dans cette collection n’expirera jamais. Tous les autres documents expire après l’intervalle de « n ». |
|  Durée de vie = n sur le document | Rien à substituer au niveau du document. Durée de vie d’un document dans non interprétées par le système. | Le document avec la durée de vie = n expirera au bout de n de l’intervalle, en secondes. Autres documents hériteront de l’intervalle de -1 et n’expirent jamais. | Le document avec la durée de vie = n expirera au bout de n de l’intervalle, en secondes. Autres documents héritent intervalle de « n » de la collection. |


## <a name="configuring-ttl"></a>Configuration de durée de vie

Par défaut, la durée de vie est désactivée par défaut dans toutes les collections de DocumentDB et sur tous les documents.

## <a name="enabling-ttl"></a>L’activation de durée de vie

Pour activer la durée de vie sur une collection, ou les documents au sein d’une collection, vous devez définir la propriété DefaultTTL d’une collection à -1 ou un nombre positif non nul. Affectation le DefaultTTL à-1 signifie que par défaut que tous les documents de la collection seront éternelle mais de la DocumentDB service doit surveiller cette collection pour les documents qui ont remplacé cette valeur par défaut.

## <a name="configuring-default-ttl-on-a-collection"></a>Configuration par défaut de durée de vie sur une collection

Vous pouvez configurer un délai par défaut en direct à un niveau de la collection. 

Pour définir la durée de vie d’une collection, vous devez fournir un nombre positif non nul qui indique le délai, en secondes, à l’expiration de tous les documents dans la collection après le dernier horodatage modifié du document (DTS).

Ou bien, vous pouvez définir la valeur par défaut-1, ce qui implique que tous les documents insérés dans à la collection indéfiniment par défaut.

## <a name="setting-ttl-on-a-document"></a>Paramètre durée de vie d’un document

En plus de définir une durée de vie par défaut d’une collection, vous pouvez définir les TTL spécifique au niveau d’un document. Cette opération remplace la valeur par défaut de la collection.

Pour définir la durée de vie d’un document, vous devez fournir un nombre positif non nul qui indique le délai, en secondes, à l’expiration du document après le dernier horodatage modifié du document (DTS).

Pour définir ce décalage d’expiration, définir le champ de durée de vie du document.

Si un document n’a pas de champ de durée de vie, la valeur par défaut de la collection s’appliquent.

Si la durée de vie est désactivée au niveau de la collection, le champ durée de vie du document sera ignoré tant que la durée de vie est activée sur la collection.


## <a name="extending-ttl-on-an-existing-document"></a>Extension de durée de vie d’un document existant

Vous pouvez réinitialiser la durée de vie d’un document en effectuant une opération d’écriture sur le document. Cela définira le DTS à l’heure actuelle, et commence le compte à rebours, à l’expiration du document, tel que défini par la durée de vie.

Si vous souhaitez modifier la durée de vie d’un document, vous pouvez mettre à jour le champ comme vous pouvez le faire avec n’importe quel autre champ définissable.


## <a name="removing-ttl-from-a-document"></a>Suppression de durée de vie d’un document

Si une durée de vie a été définie sur un document et que vous ne souhaitez plus ce document expire, puis vous pouvez extraire le document, supprimez le champ de durée de vie et remplacer le document sur le serveur.

Lorsque le champ TTL est supprimé du document, la valeur par défaut de la collection sera appliquée.

Pour arrêter un document à partir de la date d’expiration et n’hérite pas de la collection vous devez définir la valeur de durée de vie-1.


## <a name="disabling-ttl"></a>Durée de vie de désactivation

Pour désactiver la durée de vie entièrement sur une collection et arrêter le processus d’arrière-plan de la recherche de documents expirés, que la propriété DefaultTTL sur la collection doit être supprimée.

La suppression de cette propriété est différente de la valeur -1. Paramètre à-1 signifie que les nouveaux documents ajoutés à la collection résident toujours, mais vous pouvez la remplacer sur des documents spécifiques dans la collection.

La suppression de cette propriété entièrement à partir de la collection signifie qu’aucun expirent, même s’il existe des documents qui ont le substituer explicitement une valeur par défaut précédente.


## <a name="faq"></a>FAQ

**Quelle durée de vie coûtera me ?**

Il n’existe aucun coût supplémentaire à la définition d’une durée de vie d’un document.

**Combien de temps faut-il pour supprimer le document une fois la durée de vie ?**

Les documents sont marqués comme non disponibles dès que le document a expiré (durée de vie + DTS > = heure actuelle du serveur). Aucune opération n’est autorisée sur ces documents après cette heure et ils seront exclus des résultats de toutes les requêtes effectuées. Les documents sont physiquement supprimées par le système en arrière-plan. Cela ne consommera pas tout RUs à partir du budget de la collection.

**Si elle prend un certain temps à supprimer les documents, ils compteront pour mon quota (et nomenclature) jusqu'à ce qu’ils sont supprimés ?**

Non, une fois que le document a expiré vous ne serez pas facturé pour le stockage de ces documents et la taille des documents n'est pas pris en compte le quota de stockage pour une collection.

**Durée de vie d’un document aura un impact sur les frais de RU ?**

Non, il y aura aucun impact sur les frais de RU pour les opérations effectuées sur n’importe quel document dans DocumentDB.

**Est la suppression de l’impact des documents sur le débit que j’ai mis en service dans ma collection ?**

Non, desservant les demandes auprès de votre collection auront la priorité sur le processus en arrière-plan pour supprimer vos documents en cours d’exécution. Ajout de durée de vie à un document n’affecteront pas cela.

**Lorsqu’un document arrive à expiration, combien de temps il restera dans ma collection jusqu'à ce qu’elle soit supprimée ?**

Dès l’expiration du document qu’il ne sera plus accessible. L’heure exacte à laquelle un document restera dans votre collection avant suppression effective est non déterministe et reposera sur le processus d’arrière-plan est en mesure de supprimer le document.

**Sont des documents arrivés à expiration sont supprimés sur tous les nœuds, ou il est « finalement consistent? »**

Le document ne sera plus disponible en même temps sur tous les nœuds et dans toutes les régions.

**Y a-t-il un coût RU pour surveillance de durée de vie des tâches en arrière-plan ?**

Non, il n’existe aucun coût du RU.

**La fréquence à laquelle sont vérifiées les expirations de durée de vie ?**

Les expirations de durée de vie de vérification ne se produit pas en tant que processus d’arrière-plan. Lors de la réponse à une demande le service back-end effectuer le vérifications inline et exclure tous les documents qui ont expiré. La suppression du document physique est le seul processus qui est exécuté de manière asynchrone en arrière-plan. La fréquence de ce processus est déterminée par le RUs disponible dans la collection.

**La fonction de la durée de vie s’applique-t-elle uniquement à des documents entiers ou puis-je expiration des valeurs de propriété de document individuel ?**

Durée de vie s’applique à l’ensemble du document. Si vous souhaitez que le point d’expirer uniquement une partie d’un document, il est recommandé que vous extrayez la partie du document principal dans un document distinct de « lié » et ensuite utilisez TTL sur ce document extrait.

**La fonction de la durée de vie a-t-il des exigences spécifiques de l’indexation**

Oui. La collection doit avoir [indexation stratégie définie](documentdb-indexing-policies.md) Lazy ou cohérent. Tentative de définition DefaultTTL sur une collection avec indexation défini sur aucun entraîne une erreur, car vous essayez de désactiver l’indexation dans une collection qui a un DefaultTTL déjà défini.


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les DocumentDB d’Azure, reportez-vous à la page de [*documentation*](https://azure.microsoft.com/documentation/services/documentdb/) de service.




