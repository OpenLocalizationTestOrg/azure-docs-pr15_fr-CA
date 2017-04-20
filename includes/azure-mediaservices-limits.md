Ressources|Limite par défaut|Limite maximale
---|---|---
Comptes Azure Media Services (AMS) dans un seul abonnement||25
Actifs par le compte de l’AMS||1 000 000<sup>1</sup>
Tâches chaînées par projet||30
Ressources par tâche||50
Ressources par projet||100
Tâches par le compte de l’AMS ||50 000<sup>2</sup>
Localisateurs uniques associés à un actif à un moment donné||5<sup>4</sup>
Canaux direct par compte de AMS </p></td>|5</p></td>|N/a<sup>1</sup>
Programmes à l’état arrêté par canal </p></td>|50</p></td>|N/a<sup>1</sup>
Programmes en état par canal d’exécution </p></td>|3</p></td>|3
Diffusion en continu de points de terminaison lors de l’exécution d’état par le compte de l’AMS</p></td>|2</p></td>|N/a<sup>1</sup>
Unités de transmission en continu par diffusion en continu de point de terminaison </p></td>|10 </p></td>|N/a<sup>1</sup>
Codage d’unités par compte de AMS </p></td>|25</p></td>|N/a<sup>1</sup>
Comptes de stockage | |1 000<sup>5</sup>
Stratégies de || 1 000 000<sup>6</sup>

<sup>1</sup> vous pouvez demander à mettre à jour les limites de ce contingent en ouvrant un ticket de support. Ne créez pas plusieurs comptes AMS pour augmenter les limites, à la place de soumettre un ticket de support.

<sup>2</sup> ce nombre inclut les travaux en file d’attente, terminés, actives et annulées. Il n’inclut pas la suppression des travaux. Vous pouvez supprimer les anciens travaux à l’aide de **IJob.Delete** ou la requête HTTP **DELETE** .

<sup>3</sup> lors d’une demande pour des entités de travail de liste, un maximum de 1 000 sera retourné par la demande. Si vous avez besoin assurer le suivi de tous les travaux envoyés, vous pouvez utiliser haut/ignorer comme indiqué dans les [options de requête OData système](http://msdn.microsoft.com/library/gg309461.aspx).

<sup>4</sup> les localisateurs ne sont pas conçus pour gérer le contrôle d’accès par utilisateur. Pour donner des droits d’accès différents pour des utilisateurs individuels, utilisez les solutions de gestion des droits numériques (DRM).

<sup>5</sup> les comptes de stockage doivent être de la même abonnement Azure.

<sup>6</sup> est une limite de 1 000 000 stratégies pour différentes stratégies AMS (par exemple, pour la stratégie de recherche ou ContentKeyAuthorizationPolicy). Vous devez utiliser le même ID de stratégie si vous utilisez toujours les mêmes jours / autorisations d’accès, etc..
