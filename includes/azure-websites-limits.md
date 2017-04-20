Ressources|Gratuit|Partagé (aperçu)|Base|Standard|Prime (aperçu)</th>
---|---|---|---|---|---
[Web, mobile ou applications d’API](https://azure.microsoft.com/services/app-service/) par le [service d’application](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Un nombre illimité de<sup>2</sup>|Un nombre illimité de<sup>2</sup>|Un nombre illimité de<sup>2</sup>
[Applications de logique](https://azure.microsoft.com/services/app-service/logic/) par le [service d’application](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 par cœur|20 par cœur
[Plan de Service d’application](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 par région|10 par groupe de ressources|100 par groupe de ressources|100 par groupe de ressources|100 par groupe de ressources
Type de l’instance de calcul|Partagé|Partagé|Dédiés<sup>3</sup>|Dédiés<sup>3</sup>|Dédiés<sup>3</sup></p>
[Évolution cloisonnée](../articles/app-service-web/web-sites-scale.md) (max instances)|1 partagée|1 partagée|dédié 3<sup>3</sup>|dédié 10<sup>3</sup>|20 dédié (50 dans ASE)<sup>3, 4</sup>
Stockage<sup>5</sup>|1 Go<sup>5</sup>|1 Go<sup>5</sup>|10 Go<sup>5</sup>|50 Go<sup>5</sup>|500 Go,<sup>4,5</sup></p>
(Courte) de temps UC<sup>6</sup>|3 minutes|3 minutes|Illimité, paie à des [taux](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|Illimité, salaire à taux standard|Illimité, salaire à taux standard
Processeur de temps (jour)<sup>6</sup>|60 minutes|240 minutes|Illimité, paie à des [taux](https://azure.microsoft.com/pricing/details/app-service/) standard</a>|Illimité, salaire à taux standard|Illimité, salaire à taux standard
Mémoire (1 heure)|1 024 Mo par plan de Service d’application|1 024 Mo par application|N/A|N/A|N/A
Bande passante|165 MO|Illimité, appliquer des [taux de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/)|Illimité, appliquent des taux de transfert de données|Illimité, appliquent des taux de transfert de données|Illimité, appliquent des taux de transfert de données
Architecture de l’application|32 bits|32 bits|32 bits/64 bits|32 bits/64 bits|32 bits/64 bits
Web Sockets par instance<sup>7</sup>|5|35|350|Un nombre illimité|Un nombre illimité
[Connexions de débogueur](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) de simultanées par application|1|1|1|5|5
[sous-domaine de azurewebsites.NET avec FTP/S et SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Prise en charge [personnalisée domaine](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
Domaine personnalisé [prise en charge de SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|||Un nombre illimité|Illimité, 5 SNI SSL et les connexions IP SSL 1 inclus|Illimité, 5 SNI SSL et les connexions IP SSL 1 inclus
Équilibrage de la charge intégré||X|X|X|X
[Toujours allumé](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Sauvegardes planifiées](../articles/app-service-web/web-sites-backup.md)||||Une fois par jour|Une fois toutes les 5 minutes<sup>8</sup>
[Échelle automatique](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
Prise en charge du [Planificateur d’Azure](https://azure.microsoft.com/services/scheduler/)||X|X|X|X
[Contrôle de point de terminaison](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Emplacements de transit (aperçu)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Domaines personnalisés par application</a>||500|500|500|500
CONTRAT SLA||<p>|99,9 %|99,95 %<sup>10</sup>|99,95 %<sup>10</sup>

<sup>1</sup> Les quotas de stockage et les applications sont par plan de Service d’application, sauf indication contraire.  
<sup>2</sup> Le nombre réel d’applications que vous pouvez héberger ces machines dépend de l’activité des applications, la taille des instances de l’ordinateur et l’utilisation des ressources correspondantes.  
<sup>3</sup> Instances dédiées peuvent être de différentes tailles. Pour plus d’informations, consultez [Application de tarification Service](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) .  
<sup>4</sup> Niveau de prime autorise un maximum de 50 calcule les instances (sous réserve de disponibilité) et 500 Go d’espace disque lors de l’utilisation des environnements de Service d’application, et 20 calculer des instances et 250 Go de stockage dans le cas contraire.  
<sup>5</sup> La limite de stockage est la taille totale du contenu sur toutes les applications dans le même plan de Service de l’application. Plusieurs options de stockage sont disponibles dans un [Environnement de Service d’application](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Ces ressources sont limitées par les ressources physiques sur les instances dédiées (la taille de l’instance et le nombre d’instances).  
<sup>7</sup> Si vous redimensionnez une application dans la couche de base pour les deux instances, vous avez 350 connexions simultanées pour chacun des deux instances.  
<sup>8</sup> Niveau de prime permet à des intervalles de sauvegarde vers le bas jusqu'à 5 minutes lors de l’utilisation des environnements de Service d’application et 50 fois par jour dans le cas contraire.  
<sup>9</sup> À la demande, selon une planification ou en permanence en tâche de fond au sein de l’instance du Service de l’application, exécuter des programmes exécutables personnalisés ou des scripts. Toujours actif est requis pour l’exécution de WebJobs en continu. Azure planificateur libre ou Standard est requise pour WebJobs planifiée. Il n’y a aucune limite prédéfinie sur le nombre de WebJobs qui peuvent s’exécuter dans une instance de Service de l’application, mais il existe des limites pratiques qui dépendent de ce que le code de l’application essaie de faire.   
<sup>10</sup> SLA de 99,95 % est fourni pour les déploiements qui utilisent plusieurs instances avec Azure Traffic Manager sont configurés pour le basculement.  
