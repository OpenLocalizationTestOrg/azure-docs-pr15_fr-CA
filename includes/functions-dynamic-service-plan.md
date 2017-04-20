## <a name="dynamic-service-plan"></a>Plan de Service dynamique

Dans le Plan de Service dynamique, vos applications de fonction seront fixées à une instance d’application de fonction. Besoin davantage d’instances seront ajoutés dynamiquement.
Ces instances peuvent couvrir sur plusieurs ressources informatiques, apporter le meilleur parti de l’infrastructure Azure disponible. En outre, vos fonctions seront exécute en parallèle, en réduisant le temps total nécessaire pour traiter les demandes. Temps d’exécution de chaque fonction est ajouté, en secondes et agrégées par l’application conteneur de la fonction. Avec des coûts pilotées par le nombre d’instances, taille de la mémoire et temps d’exécution total en secondes du gigaoctet. Il s’agit d’une excellente option si vos besoins de calcul sont intermittentes ou vos heures de travail ont tendance à être très court, car il vous permet de payer uniquement pour les ressources de calcul lorsqu’ils sont en cours d’utilisation.   

### <a name="memory-tier"></a>Niveau de la mémoire

Selon votre fonction doit vous pouvez sélectionner la quantité de mémoire requise pour les exécuter dans l’application de la fonction (conteneur de fonctions).
Les options de taille de mémoire varient de **128 Mo à 1 536 Mo**. La taille de la mémoire sélectionnée correspond à la plage de travail requises par toutes les fonctions qui font partie de votre application de la fonction. Si votre code requiert plus de mémoire que la taille sélectionnée, l’instance d’application de fonction va être arrêté en raison du manque de mémoire disponible.

### <a name="scaling"></a>Mise à l’échelle

La plateforme Azure fonctions évalue les besoins du trafic, basés sur les déclencheurs configurés, à décider quand mettre à l’échelle vers le haut ou vers le bas. La granularité de la mise à l’échelle est l’application de la fonction. Mise à l’échelle signifie dans ce cas ajouter plusieurs instances d’une application de la fonction. À l’inverse comme le trafic tombe en panne, fonction app instances sont désactivé-finalement mise à l’échelle vers le bas à zéro lorsqu’aucune sont en cours d’exécution.  

### <a name="resource-consumption-and-billing"></a>La facturation et la consommation des ressources

Dans la dynamique allocation de ressources en mode s’effectue différemment que le plan de Service d’application standard, le modèle de la consommation est également différent, permettant à un modèle de « paiement par utilisation ». La consommation est signalée par l’application de la fonction, que pour le temps lorsque le code est exécuté.  
Elle est calculée en multipliant la taille de la mémoire (en Go) par le montant total de la durée d’exécution (en secondes) pour toutes les fonctions en cours d’exécution à l’intérieur de cette application de la fonction. L’unité de consommation est **Go-s (gigaoctet secondes)**.