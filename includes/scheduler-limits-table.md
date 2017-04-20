Le tableau suivant décrit chacun des principaux contingents, limites, par défaut et des limitations dans le planificateur d’Azure.

|Ressources|Description de la limite|
|---|---|
|**Taille de la tâche**|La taille maximale du travail est de 16 Ko. Si une instruction PUT ou un correctif entraîne un travail dépasse ces limites, un code d’état 400 demande incorrecte est retourné.|
|**Taille de l’URL de la demande**|Taille maximale de l’URL de requête est 2048 caractères.|
|**Taille de l’en-tête d’agrégation**|Taille de l’en-tête d’agrégation maximale est de 4096 caractères.|
|**Nombre en-tête**|Nombre maximal d’en-tête est 50 en-têtes.|
|**Taille du corps**|Taille maximale de corps est 8192 caractères.|
|**Intervalle de périodicité**|Intervalle de périodicité maximale est de 18 mois.|
|**Heure de démarrage**|Maximum « heure à l’heure de début » est de 18 mois.|
|**Historique des travaux**|Corps de la réponse maximale stockées dans l’historique des travaux est 2048 octets.|
|**Fréquence**|Le quota de la fréquence maximale par défaut est 1 heure dans une collection de travail libre et 1 minute dans une collection de travail standard. La fréquence maximale est configurable sur une collection de travail soit inférieur à la valeur maximale. Toutes les tâches dans la collection de projet sont limités la valeur définie dans la collection de la tâche. Si vous essayez de créer une tâche avec une fréquence supérieure à la fréquence maximale sur la collection de travail la demande échoue avec un code d’état 409 conflit.|
|**Travaux**|Le quota de travaux maximale par défaut est 5 tâches dans une collection de travail libre et 50 tâches dans une collection de travail standard. Le nombre maximal de travaux est configurable sur une collection de tâches. Toutes les tâches dans la collection de projet sont limités la valeur définie dans la collection de la tâche. Si vous essayez de créer plusieurs travaux que le quota maximal de travaux, la demande échoue avec un code d’état 409 conflit.|
|**Conservation de l’historique des travaux**|L’historique des travaux sont conservé pendant 2 mois, voire les 1000 derniers exécutions.|
|**Rétention des tâches achevées et défectueux**|Tâches achevées et défectueux sont conservés pendant 60 jours.|
|**Délai d’attente**|Il y a un délai d’expiration de requête (non modifiable) statique de 60 secondes pour les actions de HTTP. De plus les opérations en cours d’exécution, suivez les protocoles HTTP asynchrones ; par exemple, retourner un 202 immédiatement, mais continuer à travailler dans l’arrière-plan.|
