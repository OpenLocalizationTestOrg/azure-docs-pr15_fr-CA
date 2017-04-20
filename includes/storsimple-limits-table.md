<!--author=alkohli last changed: 12/15/15-->

| Identificateur de la limite | Limite de | Commentaires |
|----------------- | ------|--------- |
| Nombre maximal d’informations d’identification du compte de stockage | 64 | |
| Nombre maximal de conteneurs de volume | 64 | |
| Nombre maximum de volumes | 255 | |
| Nombre maximal de planifications par le modèle de la bande passante | 168 | Une planification pour chaque heure, chaque jour de la semaine (24 * 7). |
| Taille maximale d’un volume monté en cascade sur des périphériques physiques | 64 To 8100 et 8600 | 8100 et 8600 sont des périphériques physiques. |
| Taille maximale d’un volume monté en cascade sur des périphériques virtuels dans Azure | 30 To pour 8010 <br></br> 64 To pour 8020 | 8010 et 8020 sont des périphériques virtuels dans Azure qui utilisent le stockage Standard et Premium respectivement. |
| Taille maximale d’un volume local épinglé sur des périphériques physiques | 9 To pour 8100 <br></br> 24 To pour 8600 | 8100 et 8600 sont des périphériques physiques. |
| Nombre maximal de connexions iSCSI | 512 | |
| Nombre maximal de connexions iSCSI d’initiateurs | 512 | |
| Nombre maximal d’enregistrements de contrôle d’accès par périphérique | 64 | |
| Nombre maximal de volumes par la stratégie de sauvegarde | 24 | |
| Nombre maximal de sauvegardes conservées par la stratégie de sauvegarde | 64 | |
| Nombre maximal de planifications par la stratégie de sauvegarde | 10 | |
| Nombre maximal de clichés de n’importe quel type qui peuvent être conservées par volume | 256 | Cela inclut des snapshots locaux et des snapshots en nuage. |
| Nombre maximal d’instantanés qui peuvent être présents dans n’importe quel périphérique | 10 000 | |
| Nombre maximum de volumes qui peuvent être traitées en parallèle pour la sauvegarde, de restauration ou cloner | 16 |<ul><li>S’il y a plus de 16 volumes, ils seront traités dans l’ordre comme emplacements de traitement sont disponibles.</li><li>Nouvelles sauvegardes de cloné ou un volume monté en cascade restauré ne peut pas se produire jusqu'à ce que l’opération est terminée. Cependant, pour un volume local, les sauvegardes sont autorisées après que le volume est en ligne.</li></ul>|
| Restaurer et cloner les temps de récupération pour les volumes hiérarchisés | < 2 minutes | <ul><li>Le volume est rendu disponible dans les 2 minutes d’opération de restauration ou de clone, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, comme la plupart des données et des métadonnées réside toujours dans le nuage. Performances peut augmenter en tant que flux de données dans le nuage sur le périphérique StorSimple.</li><li>La durée totale de téléchargement de métadonnées dépend de la taille du volume alloué. Métadonnées sont automatiquement placée dans le périphérique en arrière-plan au taux de 5 minutes par To de données de volume alloué. Ce taux peut être affecté par la bande passante Internet vers le nuage.</li><li>La restauration ou le clonage est terminé lorsque toutes les métadonnées sont sur le périphérique.</li><li>Les opérations de sauvegarde ne peut pas être effectuées jusqu'à ce que la restauration ou le clonage est entièrement terminée.|
| Restaurer les temps de récupération pour les volumes ajoutés localement | < 2 minutes | <ul><li>Le volume est rendu disponible dans les 2 minutes de l’opération de restauration, quelle que soit la taille du volume.</li><li>Les performances du volume peuvent initialement être inférieures à la normale, comme la plupart des données et des métadonnées réside toujours dans le nuage. Performances peut augmenter en tant que flux de données dans le nuage sur le périphérique StorSimple.</li><li>La durée totale de téléchargement de métadonnées dépend de la taille du volume alloué. Métadonnées sont automatiquement placée dans le périphérique en arrière-plan au taux de 5 minutes par To de données de volume alloué. Ce taux peut être affecté par la bande passante Internet vers le nuage.</li><li>Contrairement aux volumes de plusieurs niveaux, dans le cas de volumes ajoutés localement, les données du volume sont également téléchargées localement sur le périphérique. L’opération de restauration est terminée lorsque toutes les données de volume a été porté à l’appareil.</li><li>Les opérations de restauration peuvent être longues et la durée totale de la restauration dépend de la taille du volume local mis en service, votre bande passante Internet et les données existantes sur le périphérique. Les opérations de sauvegarde sur le volume local épinglé sont autorisées pendant que l’opération de restauration est en cours.|
| Disponibilité de la fin de la restauration | Basculement de dernière | |
| Débit de lecture/écriture de nombre maximal de clients (lorsqu’il est pris en charge à partir de la couche SSD) * | 920/720 Mo/s avec un seul 10GbE interface de réseau | Jusqu'à 2 x avec MPIO et deux interfaces réseau. |
| Débit de lecture/écriture de nombre maximal de clients (lorsqu’il est pris en charge à partir du niveau du disque dur) * | 120/250 Mo/s |
| Débit de lecture/écriture de nombre maximal de clients (lorsqu’il est pris en charge à partir de la couche de nuage) * | 11/41 Mo/s | Débit de lecture dépend des clients de générer et maintenir la profondeur de file d’attente d’e/s suffisante. |

& #42 ; Débit maximal par type d’e/s a été mesuré avec les scénarios de 100 % en écriture et de lecture de 100 pour cent. Débit réel peut être inférieur et d’e/s dépend de mélanger et de conditions de réseau.
