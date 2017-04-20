<properties
   pageTitle="Guide de mise en cache | Microsoft Azure"
   description="Conseils sur la mise en cache pour améliorer les performances et l’évolutivité."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="masashin"/>


# <a name="caching-guidance"></a>Guide de mise en cache

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

La mise en cache est une technique courante qui vise à améliorer les performances et l’évolutivité d’un système. Il effectue cette opération en copiant temporairement les données fréquemment utilisées pour un stockage rapide se trouve fermer pour l’application. Si cette capacité de stockage rapide se trouve plus près à l’application que la source d’origine, puis la mise en cache peut considérablement améliorer les temps de réponse pour les applications clientes en servant les données plus rapidement.

La mise en cache est plus efficace lorsqu’une instance du client lit plusieurs fois les mêmes données, en particulier si toutes les conditions suivantes s’appliquent au magasin de données d’origine :
- Il reste relativement statique.
- Il est lente par rapport à la vitesse du cache.
- Il est soumis à un niveau élevé de conflits.
- Il est loin lorsque la latence du réseau peut entraîner des accès est lente.

## <a name="caching-in-distributed-applications"></a>La mise en cache dans les applications distribuées

Les applications distribuées en général implémentent une des deux stratégies suivantes lors de la mise en cache des données :

- À l’aide d’un cache privé, où les données sont stockées localement sur l’ordinateur qui exécute une instance d’une application ou un service.
- À l’aide d’un cache partagé, agissant comme une source commune, qui est accessible par plusieurs processus ou des ordinateurs.

Dans les deux cas, la mise en cache peut être effectuée côté client ou côté serveur. Mise en cache côté client est effectuée par le processus qui fournit l’interface utilisateur pour un système, tel qu’un navigateur web ou une application de bureau.
La mise en cache du côté serveur est effectuée par le processus qui fournit les services d’entreprise qui sont en cours d’exécution à distance.

### <a name="private-caching"></a>La mise en cache privé

Le type de cache la plus simple est un magasin en mémoire. Celui-ci a maintenu dans l’espace d’adressage d’un processus unique et accessible directement par le code qui s’exécute dans ce processus. Ce type de cache est très rapide pour accéder à. Il peut également fournir un moyen extrêmement efficace pour le stockage de faible quantité de données statiques, dans la mesure où la taille d’un cache est généralement limitée par le volume de mémoire disponible sur l’ordinateur qui héberge le processus.

Si vous avez besoin pour mettre en cache n’est pas physiquement possible dans la mémoire, vous pouvez écrire les données mises en cache pour le système de fichiers local. Cela sera plus lente que celles des données qui sont conservées en mémoire, mais doit toujours être plus rapide et plus fiable que la récupération des données sur un réseau.

Si vous avez plusieurs instances d’une application qui utilise ce modèle qui s’exécutent simultanément, chaque instance de l’application a son propre cache indépendante contenant sa propre copie des données.

Considérez un cache sous la forme d’une capture instantanée des données d’origine à un moment donné dans le passé. Si ces données ne soient pas statiques, il est probable que les instances d’application différents contiennent différentes versions des données dans leur cache. Par conséquent, la même requête effectuée par ces instances peut retourner des résultats différents, comme illustré dans la Figure 1.

![À l’aide d’un cache en mémoire dans différentes instances d’une application](media/best-practices-caching/Figure1.png)

_Figure 1 : À l’aide d’un cache en mémoire dans différentes instances d’une application_

### <a name="shared-caching"></a>Partager la mise en cache

À l’aide d’un cache partagé peut aider à résoudre les problèmes que les données peuvent être différentes dans chaque cache, ce qui peut se produire avec la mise en cache en mémoire. La mise en cache partagée garantit que les instances d’application voient la même vue de données en mémoire cache. Pour cela, il recherche le cache dans un emplacement distinct, généralement utilisé dans le cadre d’un service distinct, comme illustré à la Figure 2.

![À l’aide d’un cache partagé](media/best-practices-caching/Figure2.png)

_Figure 2 : À l’aide d’un cache partagé_

Un avantage important de l’approche de mise en cache partagé est l’évolutivité qu’il fournit. De nombreux services de cache partagé sont mises en œuvre à l’aide d’un cluster de serveurs et utilisent le logiciel qui distribue les données sur le cluster de manière transparente. Une instance d’application envoie simplement une demande au service de cache.
L’infrastructure sous-jacente est responsable de la détermination de l’emplacement des données mises en cache dans le cluster. Vous pouvez facilement mettre à l’échelle le cache en ajoutant des serveurs.

Il existe deux principaux inconvénients de l’approche de mise en cache partagé :
- Le cache est plus lent, car il n’est plus maintenu localement pour chaque instance de l’application.
- L’exigence d’implémenter un service de cache distinct peut compliquer la solution.

## <a name="considerations-for-using-caching"></a>Considérations sur l’utilisation de la mise en cache

Les sections suivantes décrivent plus en détail les considérations pour la conception et l’utilisation d’un cache.

### <a name="decide-when-to-cache-data"></a>Décider quand mettre en cache des données

La mise en cache peut améliorer considérablement les performances, l’évolutivité et la disponibilité. Plus de données que vous avez et plus le nombre d’utilisateurs qui doivent accéder à ces données, deviennent de plus les avantages de la mise en cache. C’est parce que la mise en cache réduit la latence et la contention associé à gérer d’importants volumes de demandes simultanées dans le magasin de données d’origine.

Par exemple, une base de données peut prendre en charge un nombre limité de connexions simultanées. Récupération de données à partir d’un cache partagé, toutefois, au lieu de la base de données sous-jacente, rend possible pour une application cliente pour accéder à ces données même si le nombre de connexions disponibles est actuellement épuisé. En outre, si la base de données devient indisponible, les applications clientes peuvent être en mesure de continuer en utilisant les données qui sont conservées dans le cache.

Envisagez la mise en cache des données lues fréquemment, mais modifiées rarement (par exemple, les données qui a un plus grand nombre d’opérations de lecture que les opérations d’écriture). Toutefois, nous ne vous conseillons le cache comme magasin faisant autorité des informations critiques. Au lieu de cela, vérifiez que votre application ne peut pas vous permettre de perdre toutes les modifications sont toujours enregistrées dans une banque de données persistantes. Cela signifie que si le cache n’est pas disponible, votre application peut continuer à fonctionner à partir du magasin de données, et que vous ne perdiez pas des informations importantes.

### <a name="determine-how-to-cache-data-effectively"></a>Déterminer comment mettre en cache les données efficacement

La clé pour l’utilisation efficace d’un cache se trouve dans la détermination des données plus appropriées à mettre en cache et la mise en cache au moment approprié. Les données peuvent être ajoutées au cache à la demande la première fois, qu'il est extrait par une application. Cela signifie que l’application doit extraire les données une seule fois à partir du magasin de données, et que l’accès ultérieur peuvent être satisfaites par le cache.

Également, un cache peut être partiellement ou complètement rempli de données à l’avance, généralement au démarrage de l’application (une approche appelée amorçage). Toutefois, il peut être opportun de mettre en œuvre l’amorçage pour un cache volumineux car cette approche peut imposer une charge soudaine, élevée sur le magasin de données d’origine au démarrage de l’application en cours d’exécution.

Souvent une analyse des modèles d’utilisation peut vous aider à décider si entièrement ou partiellement préremplir un cache et de choisir les données à mettre en cache. Par exemple, il peut être utile amorcer le cache avec les données de profil utilisateur statique pour les clients qui utilisent régulièrement l’application (par exemple tous les jours), mais pas pour les clients qui utilisent l’application seulement une fois par semaine.

En règle générale de la mise en cache fonctionne bien avec les données immuable ou qui changent rarement. Des exemples des informations de référence produit et les informations de tarification dans une application de commerce électronique ou des ressources statiques partagées qui sont coûteuses à construire. Certaines ou toutes ces données peuvent être chargées dans le cache au démarrage de l’application afin de réduire la demande en ressources et à améliorer les performances. Il peut être également approprié d’avoir un processus d’arrière-plan qui met régulièrement à jour la référence des données dans le cache pour s’assurer qu’il sont à jour, ou qui actualise le cache de données de référence lorsque les modifications.

La mise en cache est moins utile pour les données dynamiques, bien qu’il existe des exceptions à cette considération (voir la section Cache des données hautement dynamiques plus loin dans cet article pour plus d’informations). Lorsque les données changent régulièrement, les informations en cache deviennent très rapidement obsolètes ou la surcharge de synchronisation du cache avec le magasin de données d’origine réduit l’efficacité de la mise en cache.

Notez qu’un cache ne dispose pas d’inclure toutes les données d’une entité. Par exemple, si un élément de données représente un objet à valeurs multiples comme un client de la banque avec un nom, une adresse et un solde de compte, certains de ces éléments peuvent rester statiques (par exemple, le nom et l’adresse), alors que d’autres (par exemple, le solde du compte) est la plus dynamiques. Dans ces situations, il peut être utile mettre en cache les parties statiques des données et récupérer (ou calculer) seulement le reste des informations lorsqu’il est requis.

Nous vous recommandons d’effectuer analyse de l’utilisation et le test des performances pour déterminer si le chargement de population avant ou à la demande de la mémoire cache, ou une combinaison des deux, est approprié. La décision doit être basée sur la volatilité et au modèle d’utilisation des données. Analyse de l’utilisation et les performances du cache est particulièrement important dans les applications qui rencontrent des charges lourdes et doivent être hautement évolutive. Par exemple, dans les scénarios hautement évolutives, il peut être utile pour amorcer le cache afin de réduire la charge sur le magasin de données aux heures de pointe.

La mise en cache peut également servir à éviter la répétition des calculs pendant l’exécution de l’application. Si une opération transforme les données ou effectue un calcul complexe, il peut enregistrer les résultats de l’opération dans le cache. Si le même calcul est requis par la suite, l’application peut récupérer tout simplement les résultats à partir du cache.

Une application peut modifier les données qui sont conservées dans un cache. Toutefois, nous vous recommandons de considérer le cache sous la forme d’un magasin de données transitoires qui pourrait disparaître à tout moment. Ne stockez pas de données importantes dans le cache uniquement ; Assurez-vous que vous tenir à jour les informations contenues dans le magasin de données d’origine. Cela signifie que si le cache n’est plus disponible, vous réduisez le risque de perdre des données.

### <a name="cache-highly-dynamic-data"></a>Données hautement dynamiques du cache

Lorsque vous stockez des informations d’évolution dans un magasin de données permanentes, il peut imposer une charge sur le système. Par exemple, considérez un périphérique qui signale l’état ou certaines autres mesures en permanence. Si une application choisit de ne pas mettre en cache ces données sur la base que les informations en cache sera presque toujours obsolètes, le même compte peut être true lorsque le stockage et la récupération d’informations à partir du magasin de données. Dans le temps pour enregistrer et extraire ces données, il a peut-être changé.

Dans un cas comme celui-ci, tenez compte des avantages de stocker les informations dynamiques directement dans le cache et non dans le magasin de données permanentes. Si les données sont non critique et ne nécessitent pas d’audit, peu importe si le changement est perdu.

### <a name="manage-data-expiration-in-a-cache"></a>Gérer l’expiration des données dans une mémoire cache

Dans la plupart des cas, les données qui sont conservées dans un cache sont une copie des données qui sont contenues dans le magasin de données d’origine. Les données dans le magasin de données d’origine peuvent changer après que qu’il a été mis en cache, rendant les données mises en cache sont obsolètes. De nombreux systèmes de mise en cache permettent de configurer le cache pour l’expiration des données et de réduire la période pour laquelle les données peuvent être obsolètes.

Lorsque les données mises en cache expirent, il est supprimé du cache, et l’application doit extraire les données du magasin de données d’origine (il peut remettez les informations qui vient d’être lues dans le cache). Vous pouvez définir une stratégie d’expiration par défaut lorsque vous configurez le cache. Dans de nombreux services de cache, vous pouvez également stipuler la période d’expiration pour des objets individuels, lors de leur stockage par programme dans le cache.
Certains caches permettent de spécifier la période d’expiration sous la forme d’une valeur absolue, ou comme une valeur décalée, l’élément est supprimé du cache si elle n’est pas accessible dans le délai spécifié. Ce paramètre remplace toute stratégie d’expiration du cache à l’échelle, mais uniquement pour les objets spécifiés.

> [AZURE.NOTE] Pensez à la période d’expiration pour le cache et les objets qu’il contient avec soin. S’il est trop court, trop rapidement les objets expire et vous permet de réduire les avantages de l’utilisation de la mémoire cache. Si vous modifiez la période trop longue, vous risquez des données deviennent obsolètes.

Il est également possible que le cache peut remplir si les données sont autorisées à rester résident pendant une longue période. Dans ce cas, toutes les demandes pour ajouter de nouveaux éléments dans le cache peuvent provoquer certains éléments à être supprimées de façon forcée dans un processus appelé d’éviction. Les services de cache supprimer généralement les données selon le moins récemment utilisé (LRU), mais vous pouvez généralement ignorer cette stratégie et empêcher que des éléments en cours de suppression. Toutefois, si vous adoptez cette approche, vous risquez de dépassement de la mémoire qui est disponible dans le cache. Une application qui tente d’ajouter un élément au cache échoue avec une exception.

Certaines implémentations de mise en cache peuvent fournir des politiques d’éviction supplémentaires. Il existe plusieurs types de politiques d’éviction. Celles-ci comprennent :
- Stratégie des derniers fichiers utilisés (dans l’attente que les données ne sera pas requises à nouveau).
- Une stratégie de first-in-first-out (données plus anciennes sont supprimées en premier).
- Une stratégie de suppression explicite basée sur un événement déclenché (par exemple, les données en cours de modification).

### <a name="invalidate-data-in-a-client-side-cache"></a>Invalider les données dans un cache côté client

Les données qui sont conservées dans un cache côté client sont généralement considérée comme extérieure à la responsabilité du service qui fournit les données au client. Un service ne peut pas forcer directement d’un client à ajouter ou supprimer des informations dans un cache côté client.

Cela signifie qu’il est possible pour un client qui utilise un cache mal configuré pour continuer à utiliser des informations obsolètes. Par exemple, si les stratégies d’expiration du cache ne sont pas correctement mis en œuvre, un client peut utiliser les informations obsolètes qui sont mises en cache localement lorsque les informations de la source de données d’origine a été modifiée.

Si vous générez une application web qui sert de données via une connexion HTTP, vous pouvez implicitement forcer un client web (par exemple, un navigateur ou d’un proxy web) pour extraire les informations les plus récentes. Vous pouvez procéder de la sorte si une ressource est mis à jour par une modification de l’URI de la ressource. Les clients Web généralement d’utilisent l’URI d’une ressource comme la clé dans le cache côté client, si l’URI est modifié, le client web ignore les précédemment mis en cache de versions d’une ressource et extrait à la place de la nouvelle version.

## <a name="managing-concurrency-in-a-cache"></a>Gérer l’accès concurrentiel dans une mémoire cache

Les caches sont souvent conçus pour être partagés par plusieurs instances d’une application. Chaque instance de l’application peut lire et modifier des données dans le cache. Par conséquent, les mêmes problèmes d’accès concurrentiel se poser avec n’importe quel magasin de données partagées s’appliquent également à un cache. Dans une situation où une application a besoin pour modifier les données contenues dans le cache, vous devrez vous assurer que les mises à jour effectuées par une instance de l’application n’écrasent pas les modifications apportées par une autre instance.

En fonction de la nature des données et le risque de collisions, vous pouvez adopter l’une des deux approches d’accès concurrentiel :

- __Optimiste.__ Immédiatement avant la mise à jour des données, l’application vérifie si les données dans le cache a été modifiée dans la mesure où il a été récupéré. Si les données sont toujours les mêmes, la modification est possible. Dans le cas contraire, l’application doit décider s’il faut mettre à jour. (La logique métier que les lecteurs de la présente décision sera spécifique à l’application). Cette approche est appropriée pour les situations où les mises à jour sont peu fréquentes, ou lorsque les collisions sont peu probable.
- __Pessimiste.__ Lorsqu’il récupère les données, l’application bloque dans le cache pour empêcher une autre instance de le modifier. Ce processus garantit que les collisions ne peut pas se produire, mais ils peuvent également bloquer les autres instances doivent traiter les mêmes données. L’accès concurrentiel pessimiste peut affecter l’évolutivité d’une solution et est recommandé uniquement pour les opérations de courte durées. Cette approche peut être appropriée pour les situations où les collisions sont plus probables, surtout si une application met à jour plusieurs éléments dans le cache et devez vous assurer que ces modifications sont appliquées de manière cohérente.

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>Implémenter l’évolutivité et la disponibilité et améliorer les performances

Évitez d’utiliser un cache comme référentiel principal des données ; C’est le rôle de la banque de données d’origine à partir de laquelle le cache est rempli. Le magasin de données d’origine est chargé d’assurer la persistance des données.

Veillez à ne pas introduire de dépendances critiques sur la disponibilité d’un service de cache partagée dans vos solutions. Une application doit être capable de continuer à fonctionner si le service qui fournit la mémoire cache partagée n’est pas disponible. L’application ne doit pas se bloquer ou échouer en attendant que le service de cache à reprendre.

Par conséquent, l’application doit être préparée pour détecter la disponibilité du service de cache et y revenir au magasin de données d’origine si le cache n’est pas accessible. Le [modèle de disjoncteur](http://msdn.microsoft.com/library/dn589784.aspx) est utile pour gérer ce scénario. Le service qui fournit la mémoire cache peut être récupéré, et dès qu’il sera disponible, le cache peut être à nouveau rempli comme les données sont lues à former le magasin de données d’origine, en suivant une stratégie, tels que le [modèle de Cache-aside](http://msdn.microsoft.com/library/dn589799.aspx).

Toutefois, il peut être un impact de l’évolutivité sur le système si l’application se replie sur le magasin de données d’origine lorsque le cache est temporairement indisponible.
Alors que le magasin de données est en cours de restauration, le magasin de données d’origine peut être alors les demandes de données, ce qui entraîne des délais d’attente et Échec de connexions.

Envisagez d’implémenter un cache local privé dans chaque instance d’une application, ainsi que de la mémoire cache partagée qui accèdent à toutes les instances de l’application. Lorsque l’application récupère un élément, il peut vérifier tout d’abord dans son cache local, puis dans le partagé du cache et enfin stocker dans les données d’origine. Le cache local peut être rempli avec les données dans un cache partagé ou dans la base de données si la mémoire cache partagée n’est pas disponible.

Cette approche nécessite une configuration soigneuse pour éviter que le cache local de devenir trop obsolètes par rapport à la mémoire cache partagée. Toutefois, le cache local fait Office de tampon si la mémoire cache partagée est inaccessible. La figure 3 illustre cette structure.

![À l’aide d’un cache local privé avec un cache partagé](media/best-practices-caching/Caching3.png)
_Figure 3 : à l’aide d’un cache local privé avec un cache partagé_

Pour prendre en charge des caches de grande taille contenant les données de durée de vie relativement longues, certains services de cache proposent une option de haute disponibilité qui implémente le basculement automatique si le cache n’est plus disponible. Cette approche implique généralement la réplication des données mises en cache qui sont stockées sur un serveur de cache principal à un serveur de cache secondaire et de commutation vers le serveur secondaire en cas de défaillance du serveur principal ou de la connectivité est perdue.

Pour réduire la latence associée à écrire vers plusieurs destinations, la réplication vers le serveur secondaire peut se produire en mode asynchrone lorsque les données sont écrites dans le cache sur le serveur principal. Cette approche, le risque que certaines informations mises en cache risquent d’être perdues en cas de panne, mais la proportion de ces données doit être faible par rapport à la taille totale du cache.

Si un cache partagé est volumineux, il peut être avantageux de partitionner les données en mémoire cache sur les nœuds pour réduire les risques de contention et d’améliorer l’évolutivité. De nombreux caches partagés prise en charge de la capacité d’ajouter dynamiquement (et supprimer) des nœuds et rééquilibrent les données entre les partitions. Cette approche peut impliquer le clustering, dans lequel la collection de nœuds est présentée aux applications clientes comme un cache transparent, unique. Cependant, en interne, les données sont dispersées entre nœuds suit une stratégie de distribution prédéfinis qui équilibre la charge de manière uniforme. Le [document d’orientation partitionnement des données](http://msdn.microsoft.com/library/dn589795.aspx) sur le site Web de Microsoft fournit plus d’informations sur les stratégies de partitionnement possibles.

Le cluster peut également augmenter la disponibilité du cache. Si un nœud échoue, le reste de la mémoire cache est toujours accessible.
Le clustering est fréquemment utilisé en conjonction avec la réplication et le basculement. Chaque nœud peut être répliqué, et le réplica peut être rapidement mis en ligne si le nœud tombe en panne.

Nombre de lecture et les opérations d’écriture sont susceptibles d’impliquer des valeurs de données uniques ou des objets. Toutefois, dans certains cas il peut être nécessaire stocker ou récupérer rapidement de gros volumes de données.
Par exemple, l’amorçage d’un cache peut impliquer écrire des centaines ou des milliers d’éléments dans le cache. Une application peut également devez récupérer un grand nombre d’éléments connexes dans le cache, en tant que partie de la même demande.

De nombreux caches de grande envergure fournissent des opérations de traitement par lots à ces fins. Cela permet à une application cliente pour empaqueter un grand nombre d’éléments dans une seule demande et réduit la surcharge associée effectue un grand nombre de demandes de petite taille.

## <a name="caching-and-eventual-consistency"></a>Cohérence de la mise en cache et une éventuelle

Le modèle de cache-aside fonctionne, l’instance de l’application qui remplit le cache doit avoir accès à la version la plus récente et cohérente des données. Dans un système qui implémente la cohérence éventuelle (par exemple, un magasin de données répliqué) cela peut-être pas le cas.

Une instance d’une application peut modifier un élément de données et invalider la version mise en cache de l’élément. Une autre instance de l’application peut essayer de lire cet article à partir d’un cache, ce qui entraîne une absence de cache, afin qu’il lit les données dans le magasin de données et l’ajoute au cache. Toutefois, si le magasin de données n’a pas été entièrement synchronisé avec les autres réplicas, l’instance d’application peut lire et remplir le cache avec l’ancienne valeur.

Pour plus d’informations sur la gestion de la cohérence des données, consultez la page [d’introduction de la cohérence des données](http://msdn.microsoft.com/library/dn589800.aspx) sur le site Web de Microsoft.

### <a name="protect-cached-data"></a>Protéger les données en mémoire cache

Quel que soit le service de cache que vous utilisez, envisagez comment protéger les données qui sont conservées dans le cache d’accès non autorisés. Il existe deux principales préoccupations :

- La confidentialité des données dans le cache
- La confidentialité des données lorsqu’elles circule entre le cache et l’application qui utilise le cache

Pour protéger les données dans le cache, le service de cache peut implémenter un mécanisme d’authentification qui requiert que les applications spécifient les éléments suivants :
- Les identités qui peuvent accéder à des données dans le cache.
- Les opérations (lecture et écriture) ces identités sont autorisées à effectuer.

Pour réduire cette charge qui a associé à lire et écrire des données, une fois une identité a reçu l’écriture et/ou l’accès en lecture au cache, qu’identité peut utiliser toutes les données dans le cache.

Si vous devez restreindre l’accès à des sous-ensembles de données mis en cache, vous pouvez effectuer l’une des opérations suivantes :

- Fractionner le cache en partitions (à l’aide de serveurs cache différentes) et d’accorder uniquement l’accès aux identités pour les partitions qui ils doivent être autorisés à utiliser.
- Chiffrer les données de chaque sous-ensemble à l’aide de différentes clés et fournir les clés de cryptage uniquement aux identités qui doivent avoir accès à chaque sous-ensemble. Une application cliente peut toujours être en mesure de récupérer toutes les données dans le cache, mais elle sera uniquement en mesure de décrypter les données pour lesquelles il a les clés.

Vous devez également protéger les données qu’elles se trouvent dans le cache. Pour ce faire, vous dépendez des fonctionnalités de sécurité fournies par l’infrastructure du réseau qui utilisent des applications clientes pour se connecter au cache. Si le cache est implémenté à l’aide d’un serveur sur site au sein de la même organisation qui héberge les applications clientes, l’isolation du réseau ne peut pas vous demander de prendre des mesures supplémentaires. Si le cache est situé à distance et nécessite une connexion TCP ou HTTP via un réseau public (comme Internet), envisagez d’implémenter SSL.

## <a name="considerations-for-implementing-caching-with-microsoft-azure"></a>Considérations pour l’implémentation de la mise en cache avec Microsoft Azure

Azure fournit le Cache Redis d’Azure. Il s’agit d’une implémentation de cache Redis qui s’exécute comme un service dans un centre de données Azure open source. Il fournit un service de mise en cache qui est accessible à partir de n’importe quelle application Azure, si l’application est implémentée sous la forme d’un service en nuage, un site Web, ou à l’intérieur d’une machine virtuelle Azure. Caches peuvent être partagées par les applications clientes qui possèdent la clé d’accès approprié.

Cache de Redis Azure est une solution de cache hautes performances qui fournit la disponibilité, l’évolutivité et la sécurité. Il est généralement exécuté en tant que service répartis sur un ou plusieurs ordinateurs dédiés. Il tente de stocker autant d’informations que possible dans la mémoire pour assurer un accès rapide. Cette architecture est destinée à fournir une faible latence et un débit élevé en réduisant la nécessité d’effectuer des opérations d’e/s lentes.

 Cache de Redis Azure est compatible avec la plupart des API différentes qui sont utilisées par les applications clientes. Si vous possédez des applications existantes qui utilisent déjà Azure Redis Cache est en cours d’exécution sur site, le Cache de Redis Azure fournit un chemin de migration rapide pour la mise en cache dans le nuage.

> [AZURE.NOTE] Azure fournit également le Service du Cache géré. Ce service est basé sur le moteur de Cache de tissu Azure Service. Il vous permet de créer un cache distribué qui peut être partagé par les applications à couplage lâche. Le cache est hébergé sur des serveurs hautes performances dans un centre de données Azure.
Toutefois, cette option n’est plus recommandée et n’est fournie pour prendre en charge des applications existantes qui ont été générées pour l’utiliser. Pour tout nouveau développement, utilisez plutôt le Cache Redis d’Azure.
>
> En outre, Azure prend en charge la mise en cache dans le rôle. Cette fonctionnalité vous permet de créer un cache qui est spécifique à un service en nuage.
Le cache est hébergé par les instances d’un rôle web ou travailleur et sont accessibles uniquement par les rôles qui fonctionnent dans le cadre de la même unité de déploiement de service cloud. (Une unité de déploiement est l’ensemble des instances de rôles qui sont déployées sous la forme d’un service en nuage à une région spécifique.) Le cache est ordonné en clusters, et toutes les instances du rôle dans la même unité de déploiement qui héberge le cache deviennent partie intégrante du même cluster de cache. Toutefois, cette option n’est plus recommandée et n’est fournie pour prendre en charge des applications existantes qui ont été générées pour l’utiliser. Pour tout nouveau développement, utilisez plutôt le Cache Redis d’Azure.
>
> Azure Managed Service de Cache et de Cache de dans le rôle Azure sont actuellement prévu pour préparer sa retraite sur le 16 novembre 2016.
Il est recommandé de migrer au Cache de Redis d’Azure dans la préparation de ce déclassement. Pour plus d’informations, visitez la page   [Nouveautés offre de Cache Redis d’Azure et quelle taille dois-je utiliser ?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) sur le site Web de Microsoft.


### <a name="features-of-redis"></a>Fonctionnalités de Redis

 Redis est supérieure à un serveur de cache simple. Il fournit une base de données en mémoire distribuée avec un jeu de commandes complet qui prend en charge de nombreux scénarios courants. Elles sont décrites plus loin dans ce document, dans la section à l’aide de Redis mise en cache. Cette section décrit quelques-unes des fonctionnalités clés qui fournit des Redis.

### <a name="redis-as-an-in-memory-database"></a>Redis sous la forme d’une base de données en mémoire

Redis prend en charge les opérations d’écriture et de lecture. Dans Redis, les écritures peuvent être protégées de défaillance du système soit par périodiquement stockées dans un fichier d’instantané local ou dans un fichier journal ajout uniquement. Ce n’est pas le cas dans de nombreux caches (qui doivent être considérés comme des magasins de données temporaires).

 Toutes les écritures sont asynchrones et ne bloquent pas les clients à partir de la lecture et l’écriture des données. Lorsque Redis exécute, elle lit les données dans le fichier journal ou de capture instantanée et l’utilise pour construire le cache en mémoire. Pour plus d’informations, reportez-vous à la section [Redis de persistance](http://redis.io/topics/persistence) sur le site Web de Redis.

> [AZURE.NOTE] Redis ne garantit pas que toutes les écritures seront enregistrées en cas de panne catastrophique, mais au pire vous risquez de perdre uniquement quelques secondes de données. N’oubliez pas qu’un cache n'est pas conçu pour agir comme une source de données faisant autorité, et il est de la responsabilité des applications qui utilisent le cache pour vous assurer que les données critiques sont enregistrées sur une banque de données approprié. Pour plus d’informations, consultez le [modèle de cache de mise en jachère](http://msdn.microsoft.com/library/dn589799.aspx).

#### <a name="redis-data-types"></a>Redis des types de données

Redis est un magasin de clé / valeur, où les valeurs peuvent contenir des types simples ou des structures de données complexes comme les hachages, listes et. Il prend en charge un ensemble d’opérations atomiques sur ces types de données. Les clés peuvent être permanents ou marqués avec limité time-to-live, moment auquel la clé et sa valeur correspondante sont automatiquement supprimés du cache. Pour plus d’informations sur les clés de Redis et de valeurs, consultez la page [introduction à la Redis des captages et des types de données](http://redis.io/topics/data-types-intro) sur le site Web de Redis.

#### <a name="redis-replication-and-clustering"></a>Redis de réplication et de clustering

Redis prend en charge la réplication maître/subordonné pour aider à garantir la disponibilité et de maintenir le débit. Écrire un nœud maître Redis des opérations sont répliquées sur un ou plusieurs nœuds secondaire. Les opérations de lecture peuvent être servies par le capitaine ou un des subordonnés.

Dans le cas d’une partition réseau, les subordonnés peuvent continuer à fournir des données et puis, en toute transparence, resynchroniser avec le masque lorsque la connexion est rétablie. Pour plus d’informations, visitez la page de [réplication](http://redis.io/topics/replication) sur le site Web de Redis.

Redis fournit également la gestion de clusters, qui vous permet de partitionner des données dans le milieu des fragments sur serveurs et répartir la charge de façon transparente. Cette fonction améliore l’évolutivité, car de nouveaux serveurs Redis peuvent être ajoutés et les données réparties à mesure que la taille du cache augmentent.

En outre, chaque serveur du cluster peut être répliquée à l’aide de la réplication maître/esclave. Cela garantit la disponibilité sur chaque nœud du cluster. Pour plus d’informations sur la mise en cluster et ont, visitez la [page du didacticiel cluster de Redis](http://redis.io/topics/cluster-tutorial) sur le site Web de Redis.

### <a name="redis-memory-use"></a>Redis utilisation de la mémoire

Un cache de Redis a une taille limitée qui varie selon les ressources disponibles sur l’ordinateur hôte. Lorsque vous configurez un serveur Redis, vous pouvez spécifier la quantité maximale de mémoire, qu'il peut utiliser. Vous pouvez également configurer une clé dans un cache de Redis à l’heure d’expiration, après quoi il est automatiquement supprimé du cache. Cette fonctionnalité peut aider à empêcher le remplissage avec des données d’anciennes ou obsolètes du cache en mémoire.

Comme de la mémoire est pleine, les Redis peut automatiquement supprimer les clés et leurs valeurs en suivant un certain nombre de stratégies. La valeur par défaut est LRU (dernier récemment utilisé), mais vous pouvez également sélectionner d’autres stratégies telles que la suppression de clés de manière aléatoire ou désactiver complètement éviction (dans lequel, cas tente d’ajouter des éléments à l’échec du cache s’il est saturé). La page [à l’aide de Redis comme un cache LRU](http://redis.io/topics/lru-cache) fournit plus d’informations.

### <a name="redis-transactions-and-batches"></a>Redis des transactions et des lots

Redis permet à une application cliente de soumettre une série d’opérations de lecture et d’écriture de données dans le cache sous la forme d’une transaction atomique. Toutes les commandes de la transaction sont garanties pour s’exécuter de façon séquentielle, et aucun commandes émises par d’autres clients simultanés ne va être interwoven entre eux.

Toutefois, ceux-ci ne sont pas transactions trues comme une base de données relationnelle exécuterait les. Traitement des transactions se compose de deux étapes : la première est lorsque les commandes sont en attente, et le second lorsque les commandes sont exécutées. Au cours de la phase de files d’attente de commande, les commandes qui composent la transaction sont soumis par le client. Si un type d’erreur se produit à ce stade (par exemple, une erreur de syntaxe, ou un nombre incorrect de paramètres) puis Redis refuse de traiter la totalité de la transaction et le supprime.

Au cours de la phase d’exécution, Redis exécute chaque commande en file d’attente dans l’ordre. Si une commande échoue au cours de cette phase, le Redis se poursuit à partir de la commande en file d’attente suivante et ne restaure pas les effets de toutes les commandes qui ont déjà été exécutés. Ce formulaire simplifié de transaction permet de maintenir des performances et d’éviter les problèmes de performances provoqués par des conflits.

Redis n’implémente pas une forme de verrouillage optimiste pour aider à maintenir la cohérence. Pour obtenir des informations détaillées sur les transactions et verrouillage avec Redis, visitez la [page des Transactions](http://redis.io/topics/transactions) sur le site Web de Redis.

Redis prend également en charge le traitement par lots non transactionnelles de demandes. Le protocole Redis que les clients utilisent pour envoyer des commandes vers un serveur Redis permet au client d’envoyer une série d’opérations dans le cadre de la même demande. Cela peut aider à réduire la fragmentation des paquets sur le réseau. Lorsque le lot est traité, chaque commande est exécutée. Si une de ces commandes sont incorrecte, ils seront rejetées (qui ne se produit pas avec une transaction), mais les commandes restantes seront effectuées. Il n’existe également aucune garantie concernant l’ordre dans lequel les commandes dans le lot seront traités.

### <a name="redis-security"></a>Redis de sécurité

Redis vise exclusivement à fournir un accès rapide aux données et est conçu pour s’exécuter dans un environnement sécurisé accessible uniquement par les clients approuvés. Redis prend en charge un modèle de sécurité limité basé sur l’authentification de mot de passe. (Il est possible de supprimer l’authentification, bien que nous ne le recommandons.)

Tous les clients authentifiés partagent le même mot de passe global et ont accès aux mêmes ressources. Si vous avez besoin de sécurité de connexion plus complète, vous devez implémenter votre propre couche de sécurité devant le serveur Redis, et toutes les requêtes client doivent passer par cette couche supplémentaire. Redis ne doivent pas être exposées directement à des clients non approuvés ou non authentifiés.

Vous pouvez limiter l’accès aux commandes de les désactiver ou les renommer (et en fournissant uniquement des clients privilégiés avec les nouveaux noms).

Redis ne pas directement en charge toute forme de cryptage des données, tout encodage doit être effectuée par les applications clientes. En outre, les Redis ne fournit pas de n’importe quel formulaire de sécurité du transport. Si vous avez besoin protéger les données circulant sur le réseau, nous vous recommandons d’implémenter un proxy SSL.

Pour plus d’informations, visitez la page [Redis de sécurité](http://redis.io/topics/security) sur le site Web de Redis.

> [AZURE.NOTE] Cache de Redis Azure fournit sa propre couche de sécurité à travers laquelle les clients se connectent. Les serveurs Redis sous-jacente ne sont pas exposés au réseau public.

### <a name="using-the-azure-redis-cache"></a>L’utilisation du cache d’Azure Redis

Le Cache de Redis Azure fournit l’accès aux serveurs de Redis en cours d’exécution sur des serveurs hébergés à un centre de données Azure ; il fonctionne comme une façade qui fournit un contrôle d’accès et de sécurité. Vous pouvez prévoir un cache à l’aide du portail de gestion d’Azure. Le portail fournit un certain nombre de configurations prédéfinies, allant d’un cache de 53 Go exécute comme un service dédié qui prend en charge des communications SSL (pour la confidentialité) et la réplication du maître/subordonné avec un contrat SLA de disponibilité de 99,9 %, à une 250 Mo cache sans réplication (aucune garantie de disponibilité) en cours d’exécution sur le matériel partagé.

À l’aide du portail de gestion d’Azure, vous pouvez également configurer la stratégie d’éviction du cache et contrôler l’accès au cache en ajoutant des utilisateurs aux rôles fournies ; Propriétaire, collaborateur, lecteur de. Ces rôles définissent les opérations que les membres peuvent effectuer. Par exemple, les membres du rôle de propriétaire ont le contrôle total sur le cache (y compris la sécurité) et de son contenu, les membres du rôle Collaborateur peuvent lire et écrire des informations dans le cache, et les membres du rôle Lecteur peuvent uniquement récupérer des données à partir du cache.

La plupart des tâches d’administration sont effectuées via le portail de gestion d’Azure, et pour cette raison que la plupart des commandes d’administration disponibles dans la version standard de Redis ne sont pas disponibles, y compris la possibilité de modifier la configuration par programme, arrêtez le serveur Redis, configurer des esclaves supplémentaires, ou force d’enregistrer des données sur le disque.

Le portail de gestion Azure inclut un affichage graphique pratique qui vous permet de surveiller les performances du cache. Par exemple, vous pouvez afficher le nombre de connexions en cours, le nombre de demandes effectuées, le volume de lecture et d’écriture, et le nombre de cache atteint par rapport à des absences dans le cache. À l’aide de ces informations, vous pouvez déterminer l’efficacité du cache, et si nécessaire basculer vers une autre configuration ou modifier la stratégie d’éviction. En outre, vous pouvez créer des alertes qui envoient des messages de courrier électronique à un administrateur si une ou plusieurs mesures critiques compris dans une plage attendue. Par exemple, si le nombre d’échecs du cache dépasse la valeur spécifiée dans la dernière heure, un administrateur pourrait être averti que le cache peut être trop petit ou de données peuvent être en cours de suppression trop rapidement.

Vous pouvez également surveiller les UC, la mémoire et l’utilisation du réseau pour le cache.

Pour plus d’informations et d’exemples montrant comment créer et configurer un Cache de Redis d’Azure, visitez la page de [Cache Redis d’Azure visite guidée](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog Azure.

## <a name="caching-session-state-and-html-output"></a>L’état de session mise en cache et de la sortie HTML

Si vous concevez ASP.NET web applications qu’exécuter à l’aide de rôles d’Azure web, vous pouvez enregistrer sortie HTML et les informations d’état de session dans un Cache de Redis d’Azure. Le fournisseur d’état de Session pour le Cache de Redis Azure vous permet de partager des informations de session entre les différentes instances d’une application web ASP.NET et est très utile dans les situations de batterie de serveurs web où l’affinité client-serveur n’est pas disponible et la mise en cache des données session en mémoire ne serait pas approprié.

À l’aide du fournisseur d’état de Session avec Cache de Redis Azure offre plusieurs avantages, notamment :

- Il peut partager l’état de session entre un grand nombre d’instances d’une application web ASP.NET, offrant une plus grande évolutivité,
- Il prend en charge un accès simultané contrôlé pour les mêmes données d’état de session pour plusieurs lecteurs et un seul rédacteur, et
- Il peut utiliser la compression pour économiser de la mémoire et améliorer les performances réseau.

Pour plus d’informations visitez la page de [Fournisseur d’état de Session ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-session-state-provider.md) sur le site Web de Microsoft.

> [AZURE.NOTE] N’utilisez pas le fournisseur d’état de Session pour le Cache de Azure Redis pour les applications ASP.NET qui s’exécutent en dehors de l’environnement Azure. La latence de l’accès au cache d’en dehors d’Azure permet d’éliminer les avantages de la mise en cache de données.

De même, le fournisseur de Cache de sortie pour le Cache de Redis Azure vous permet d’enregistrer les réponses HTTP générées par une application web ASP.NET. À l’aide du fournisseur de caches de sortie avec Azure Redis mémoire Cache peut améliorer les temps de réponse des applications qui restituent une sortie HTML complexe ; instances d’application permettent de générer des réponses similaires utilisent des fragments de sortie partagés dans le cache, plutôt que de générer ce code HTML nouveau de sortie.  Pour plus d’informations visitez la page [Fournisseur de Cache de sortie ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-output-cache-provider.md) sur le site Web de Microsoft.

### <a name="azure-redis-cache"></a>Azure Redis cache

Cache de Redis Azure fournit l’accès aux serveurs Redis qui sont hébergés dans un centre de données Azure. Il fonctionne comme une façade qui fournit un contrôle d’accès et de sécurité. Vous pouvez prévoir un cache via le portail Azure.

Le portail fournit un certain nombre de configurations prédéfinies. Ces vont d’un cache de 53 Go exécute comme un service dédié qui prend en charge les communications SSL (pour la confidentialité) et réplication de maître/esclave avec un contrat SLA de disponibilité de 99,9 %, à un cache de 0 Mo 25 sans réplication (aucune garantie de disponibilité) en cours d’exécution sur le matériel partagé.

Utilisation du portail Azure, vous pouvez également configurer la stratégie d’éviction du cache et contrôler l’accès au cache en ajoutant des utilisateurs aux rôles fournies.  Ces rôles, qui définissent les opérations que les membres peuvent effectuer, incluent propriétaire, collaboration et lecture. Par exemple, les membres du rôle de propriétaire ont le contrôle total sur le cache (y compris la sécurité) et de son contenu, les membres du rôle Collaborateur peuvent lire et écrire des informations dans le cache, et les membres du rôle Lecteur peuvent uniquement récupérer des données à partir du cache.

La plupart des tâches d’administration sont effectuées via le portail Azure. Pour cette raison, la plupart des commandes d’administration qui sont disponibles dans la version standard de Redis ne sont pas disponibles, y compris la possibilité de modifier la configuration par programme, arrêtez le serveur Redis, configurer des subordonnés supplémentaires ou force enregistrer des données sur le disque.

Le portail Azure inclut un affichage graphique pratique qui vous permet de surveiller les performances du cache. Par exemple, vous pouvez afficher le nombre de connexions en cours, le nombre de demandes en cours d’exécution, le volume de lecture et d’écriture et le nombre de correspondances dans le cache par rapport à des absences dans le cache. À l’aide de ces informations, vous pouvez déterminer l’efficacité du cache et si nécessaire, basculez vers une autre configuration ou modifier la stratégie d’éviction.

En outre, vous pouvez créer des alertes qui envoient des messages de courrier électronique à un administrateur si une ou plusieurs mesures critiques compris dans une plage attendue. Par exemple, vous pouvez souhaiter alerter un administrateur si le nombre d’échecs du cache dépasse une valeur spécifiée dans la dernière heure, car cela signifie que le cache peut être trop petit ou de données peuvent être en cours de suppression trop rapidement.

Vous pouvez également surveiller le processeur, la mémoire et l’utilisation du réseau pour le cache.

Pour plus d’informations et d’exemples montrant comment créer et configurer un Cache de Redis d’Azure, visitez la page de [Cache Redis d’Azure visite guidée](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog Azure.

## <a name="caching-session-state-and-html-output"></a>L’état de session mise en cache et de la sortie HTML

Si vous créez des applications web ASP.NET que vous exécutez à l’aide de rôles d’Azure web, vous pouvez enregistrer session état d’informations et de la sortie HTML dans un Cache de Redis d’Azure. Le fournisseur d’état de session pour le Cache de Redis Azure vous permet de partager des informations de session entre les différentes instances d’une application web ASP.NET et est très utile dans les situations de batterie de serveurs web où l’affinité client-serveur n’est pas disponible et la mise en cache des données session en mémoire ne serait pas approprié.

À l’aide du fournisseur d’état de session avec Cache de Redis Azure offre plusieurs avantages, notamment :

- Partage d’état de session avec un grand nombre d’instances d’ASP.NET web applications.
- Offrant une plus grande évolutivité.
- Prise en charge des accès simultané contrôlé pour les mêmes données d’état de session pour plusieurs lecteurs et un seul rédacteur.
- Utilisation de la compression pour économiser de la mémoire et améliorer les performances réseau.

Pour plus d’informations, visitez la page de [fournisseur d’état de session ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-session-state-provider.md) sur le site Web de Microsoft.

> [AZURE.NOTE] N’utilisez pas le fournisseur d’état de session pour le Cache de Redis Azure avec les applications ASP.NET qui s’exécutent en dehors de l’environnement Azure. La latence de l’accès au cache d’en dehors d’Azure permet d’éliminer les avantages de la mise en cache de données.

De même, le fournisseur de cache de sortie pour le Cache de Redis Azure vous permet d’enregistrer les réponses HTTP générées par une application web ASP.NET. Le fournisseur de cache de sortie avec Azure Redis mémoire Cache contribue à améliorer les temps de réponse des applications qui restituent la sortie HTML complexe. Instances d’applications qui génèrent des réponses similaires peuvent faire usage des fragments de sortie partagés dans le cache, plutôt que de générer ce code HTML nouveau de sortie. Pour plus d’informations, visitez la page [fournisseur de cache de sortie ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-output-cache-provider.md) sur le site Web de Microsoft.

## <a name="building-a-custom-redis-cache"></a>Création d’un cache de Redis personnalisé

Cache de Redis Azure fonctionne comme une façade pour les serveurs Redis sous-jacente. Actuellement, il prend en charge un ensemble fixe de configurations mais ne fournit pas de gestion de clusters Redis. Si vous avez besoin d’une configuration avancée qui n’est pas couvert par le cache Azure Redis (tel qu’un cache supérieur à 53 Go), vous pouvez créer et héberger vos propres serveurs Redis à l’aide des ordinateurs virtuels Azure.

Il s’agit d’un processus potentiellement complexe, car vous devrez peut-être créer plusieurs des ordinateurs virtuels en tant que maîtres et subordonnés des nœuds si vous souhaitez implémenter la réplication. En outre, si vous le souhaitez créer un cluster, vous devez plusieurs formes de base et les serveurs subordonnés. Une topologie de réplication de cluster minimale qui fournit un niveau élevé de disponibilité et d’évolutivité comprend au moins six des machines virtuelles organisées sous forme de trois paires de serveurs maître/subordonné (un cluster doit contenir au moins trois nœuds maître).

Chaque paire de maître/esclave doit être placé proches pour réduire la latence. Toutefois, chaque jeu de paires peut s’exécuter dans différents centres de données Azure situés dans différentes régions, si vous souhaitez rechercher des données mises en cache, fermer les applications les plus susceptibles de l’utiliser. La page [En cours d’exécution Redis sur une machine virtuelle Linux de CentOS dans Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) sur le site Web Microsoft Guide un exemple qui montre comment créer et configurer un nœud de Redis s’exécutant comme un Azure VM.

[AZURE.NOTE] Veuillez noter que si vous implémentez votre propre cache Redis de cette façon, vous êtes responsable de la surveillance, la gestion et la sécurisation du service.

## <a name="partitioning-a-redis-cache"></a>Partitionnement d’un cache de Redis

Partitionnement du cache suppose de scinder le cache sur plusieurs ordinateurs. Cette structure présente plusieurs avantages l’utilisation d’un serveur de cache unique, y compris :

- Création d’un cache qui est beaucoup plus important que peuvent être stockés sur un serveur unique.
- Distribution des données entre serveurs, amélioration de la disponibilité. Si un serveur tombe en panne ou deviennent inaccessible, les données qu’il contient n’est pas disponible, mais les données sur les serveurs restants sont toujours accessibles. Cache, cela n’est pas crucial, car les données mises en cache sont uniquement une copie temporaire des données qui sont stockées dans une base de données. Les données mises en cache sur un serveur qui devient inaccessible peuvent être mis en cache sur un autre serveur à la place.
- Répartition de la charge sur les serveurs, ce qui améliore les performances et l’évolutivité.
- Les données Geolocating fermer pour les utilisateurs qui y accéder, ce qui réduit la latence.

Cache, la forme la plus courante de partitionnement est ont. Dans cette stratégie, chaque partition (ou partagé) est un cache de Redis dans son propre droit. En utilisant une logique ont, ce qui permet à diverses approches pour distribuer les données, les données sont dirigées vers une partition spécifique. Le [modèle d’ont](http://msdn.microsoft.com/library/dn589797.aspx) fournit plus d’informations sur l’implémentation ont.

Pour l’implémentation du partitionnement dans un cache de Redis, vous pouvez prendre une des approches suivantes :

- _Routage de la requête du côté serveur._ Dans cette technique, une application cliente envoie une demande à tous les serveurs Redis qui composent le cache (probablement le serveur le plus proche). Chaque serveur Redis stocke les métadonnées qui décrivent la partition qu’il conserve et contient également des informations sur les partitions se trouvent sur d’autres serveurs. Le serveur Redis examine la demande du client. S’il peut être résolu localement, il effectue l’opération demandée. Dans le cas contraire, il transmettra la demande sur le serveur approprié. Ce modèle est implémenté par les Redis de gestion de clusters et est décrite plus en détail sur la page [Redis du didacticiel de cluster](http://redis.io/topics/cluster-tutorial) sur le site Web de Redis. Redis le clustering est transparent pour les applications clientes et serveurs de Redis supplémentaires peuvent être ajoutés au cluster (et les données engendreront) sans nécessiter la reconfiguration des clients.

- _Partitionnement du côté client._ Dans ce modèle, l’application cliente contienne une logique (et éventuellement dans le formulaire d’une bibliothèque) qui achemine les demandes vers le serveur de Redis approprié. Cette approche peut être utilisée avec Cache Redis d’Azure. Créer plusieurs Caches de Redis Azure (une pour chaque partition de données) et implémenter la logique côté client qui achemine les demandes vers le cache correct. Si le schéma de partitionnement change (si les Caches Redis Azure supplémentaires sont créés, par exemple), les applications clientes peuvent doivent être reconfigurées.

- _Proxy-assisté de partitionnement._ Dans ce schéma, le client applications envoyer des demandes à un service proxy intermédiaire qui comprend la façon dont les données sont partitionnées et puis achemine la demande vers le Redis server. Cette approche peut également être utilisée avec Azure Redis du Cache ; le service du proxy peut être implémenté sous la forme d’un service cloud Azure. Cette approche nécessite un niveau supplémentaire de complexité pour implémenter le service et les demandes peuvent prendre plus longtemps qu’à l’aide du partitionnement du côté client.

La page [partitionnement : comment répartir les données entre plusieurs instances de Redis](http://redis.io/topics/partitioning) sur le Redis site Web fournit des informations supplémentaires sur l’implémentation du partitionnement avec Redis.

### <a name="implement-redis-cache-client-applications"></a>Implémenter des applications de client de cache Redis

Redis prend en charge les applications clientes écrites dans plusieurs langages de programmation. Si vous créez des nouvelles applications à l’aide du.NET Framework, l’approche recommandée consiste à utiliser la bibliothèque de client StackExchange.Redis. Cette bibliothèque fournit un modèle objet.NET Framework qui résume les détails de la connexion à un serveur de Redis, l’envoi de commandes et recevoir des réponses. Il est disponible dans Visual Studio sous la forme d’un package NuGet. Vous pouvez utiliser cette même bibliothèque pour se connecter à un Cache de Redis d’Azure, ou un cache Redis personnalisé hébergé sur un ordinateur virtuel.

Pour vous connecter à un serveur de Redis vous utilisez la méthode statique `Connect` méthode de le `ConnectionMultiplexer` classe. La connexion créée par cette méthode est conçue pour être utilisée tout au long de la durée de vie de l’application cliente et la même connexion peut être utilisée par plusieurs threads simultanés. Ne pas se reconnecter et déconnecter à chaque fois que vous effectuez une opération de Redis, car cela peut diminuer les performances.

Vous pouvez spécifier les paramètres de connexion, telles que l’adresse de l’hôte Redis et le mot de passe. Si vous utilisez un Cache de Redis d’Azure, le mot de passe est soit la clé primaire ou secondaire qui est générée pour le Cache de Redis Azure via le portail de gestion d’Azure.

Après que vous être connecté au serveur Redis, vous pouvez obtenir un handle sur la base de données Redis qui agit comme le cache. La connexion Redis fournit le `GetDatabase` méthode pour cela. Vous pouvez ensuite récupérer les éléments dans le cache et stocker les données dans le cache à l’aide de la `StringGet` et `StringSet` méthodes. Ces méthodes s’attendent une clé comme paramètre et retourner l’élément dans le cache qui a une valeur correspondante (`StringGet`) ou ajouter l’élément au cache avec cette clé (`StringSet`).

En fonction de l’emplacement du serveur Redis, de nombreuses opérations peuvent subir une latence pendant une demande est transmise au serveur et une réponse est retournée au client. La bibliothèque StackExchange offre des versions asynchrones de la plupart des méthodes qu’il expose pour aider les applications clientes à rester réactif. Ces méthodes prennent en charge le [Modèle asynchrone basé sur des tâches](http://msdn.microsoft.com/library/hh873175.aspx) dans le.NET Framework.

L’extrait de code suivant montre une méthode nommée `RetrieveItem`. Il illustre une implémentation du modèle de mise en jachère cache basé sur la bibliothèque StackExchange et de Redis. La méthode prend une valeur de clé de chaîne et qu’il tente de récupérer l’élément correspondant dans le cache de Redis en appelant le `StringGetAsync` méthode (la version asynchrone de `StringGet`).

Si l’élément n’est pas trouvé, il est extrait à partir de la source de données sous-jacente à l’aide du `GetItemFromDataSourceAsync` méthode (procédé local et ne faisant pas partie de la bibliothèque de StackExchange). Il est ensuite ajouté au cache à l’aide de la `StringSetAsync` méthode il peut donc être récupéré plus rapidement lors de votre prochaine.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

La `StringGet` et `StringSet` les méthodes ne sont pas limitées à l’extraction ou le stockage de valeurs de chaîne. Ils peuvent prendre n’importe quel élément qui est sérialisé comme un tableau d’octets. Si vous avez besoin d’enregistrer un objet .NET, vous pouvez les sérialiser en tant que flux d’octets et utiliser le `StringSet` méthode pour écrire dans le cache.

De même, vous pouvez lire un objet à partir du cache à l’aide de la `StringGet` de méthode et de la désérialisation d’elle sous la forme d’un objet .NET. Le code suivant illustre un ensemble de méthodes d’extension pour l’interface IDatabase (le `GetDatabase` méthode d’une connexion Redis retourne un `IDatabase` objet) et un exemple de code qui utilise ces méthodes pour lire et écrire un `BlogPost` objet dans le cache :

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Le code suivant illustre une méthode nommée `RetrieveBlogPost` qui utilise ces méthodes d’extension pour lire et écrire un sérialisable `BlogPost` l’objet dans le cache, suivant le modèle de cache-aside :

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis prend en charge la commande pipeline si une application cliente envoie plusieurs requêtes asynchrones. Redis pouvez multiplex les demandes à l’aide de la même connexion plutôt que de recevoir et de répondre aux commandes dans une séquence stricte.

Cette approche permet de réduire la latence par une utilisation plus efficace du réseau. L’extrait de code suivant montre un exemple qui Récupère les détails de deux clients simultanément. Le code envoie deux demandes, puis effectue d’autres opérations (non illustrée) avant d’attendre de recevoir les résultats. Le `Wait` méthode de l’objet cache est similaire pour le.NET Framework `Task.Wait` méthode :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La page de [Cache de Azure Redis documentation](https://azure.microsoft.com/documentation/services/cache/) sur le site Web de Microsoft fournit plus d’informations sur la façon d’écrire des applications clientes qui peuvent utiliser le Cache Redis d’Azure. Informations supplémentaires sont disponibles sur la [page de base de l’utilisation](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sur le site Web StackExchange.Redis.

La page [Pipelines et multiplexeurs](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) sur le même site Web fournit plus d’informations sur les opérations asynchrones et de canalisation avec Redis et la bibliothèque StackExchange.  La section suivante de cet article, à l’aide de Redis la mise en cache, fournit des exemples de quelques-unes des techniques plus avancées que vous pouvez appliquer aux données conservées dans un cache de Redis.

## <a name="using-redis-caching"></a>La mise en cache Redis

L’utilisation la plus simple de Redis pour les problèmes de mise en cache est paires clé-valeur dont la valeur est une chaîne non interprétée de longueur arbitraire qui peut contenir toutes les données binaires. (Il est essentiellement un tableau d’octets qui peut être traitée comme une chaîne). Les applications du client de Cache de mise en œuvre Redis section plus haut dans cet article illustre ce scénario.

Notez que clés contiennent également des données non interprétées, vous pouvez utiliser les informations binaires comme clé. Plus la clé est, cependant, plus l’espace nécessaire pour stocker et plus longue pour les opérations de recherche. Pour la facilité d’utilisation et de facilité de maintenance, votre keyspace avec soin et clés explicites (mais pas de commentaires).

Par exemple, utiliser structurés clés tels que « client : 100 » pour représenter la clé du client avec l’ID 100 au lieu de simplement « 100 ». Ce modèle vous permet de différencier facilement les valeurs de stockent différents types de données. Par exemple, vous pouvez également utiliser la clé « commandes : 100 » pour représenter la clé pour la commande avec l’ID 100.

Outre les chaînes binaires unidimensionnels, une valeur dans une paire clé-valeur de Redis peut également contenir plus structurée des informations, y compris les listes, définit (triées et non trié) et hache. Redis fournit un jeu de commandes complet que vous pouvez manipuler ces types, et la plupart de ces commandes sont disponibles pour les applications.NET Framework via une bibliothèque client tel que StackExchange. La page [introduction à la Redis des captages et des types de données](http://redis.io/topics/data-types-intro) sur le site Web de Redis fournit une vue d’ensemble plus détaillée de ces types et les commandes que vous pouvez utiliser pour les manipuler.

Cette section récapitule quelques exemples d’utilisation courante de ces types de données et des commandes.

### <a name="perform-atomic-and-batch-operations"></a>Effectuer atomiques et les opérations de commandes

Redis prend en charge une série d’opérations atomiques d’obtenir et à définir des valeurs de chaîne. Ces opérations de supprimer les risques de concurrence possibles qui peuvent se produire lors de l’utilisation de distinct `GET` et `SET` commandes. Les opérations qui sont disponibles sont les suivantes :

- `INCR`, `INCRBY`, `DECR`, et `DECRBY`, qui effectue des opérations d’incrémentation et de décrémentation atomiques sur les valeurs de données numériques de type entier. La bibliothèque StackExchange fournit des versions surchargées de la `IDatabase.StringIncrementAsync` et `IDatabase.StringDecrementAsync` méthodes pour effectuer ces opérations et retourne la valeur résultante qui est stockée dans le cache. L’extrait de code suivant illustre l’utilisation de ces méthodes :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`, qui Récupère la valeur associée à une clé et la remplace par une nouvelle valeur. La bibliothèque StackExchange rend cette opération disponible via le `IDatabase.StringGetSetAsync` méthode. L’extrait de code ci-dessous montre un exemple de cette méthode. Ce code retourne la valeur actuelle qui est associée à la clé « : compteur de données » de l’exemple précédent. Puis il réinitialise la valeur de cette clé à zéro, le tout dans le cadre de la même opération :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET`et `MSET`, qui permet de renvoyer ou de modifier un ensemble de valeurs de chaîne en une seule opération. La `IDatabase.StringGetAsync` et `IDatabase.StringSetAsync` méthodes sont surchargées pour prendre en charge cette fonctionnalité, comme illustré dans l’exemple suivant :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

Vous pouvez également combiner plusieurs opérations en une seule transaction Redis comme décrit dans la section Redis de transactions et de lots plus haut dans cet article. La bibliothèque StackExchange prend en charge les transactions par le biais de la `ITransaction` interface.

Vous créez un `ITransaction` objet à l’aide de la `IDatabase.CreateTransaction` méthode. Vous appelez des commandes à la transaction à l’aide des méthodes fournies par le `ITransaction` objet.

Le `ITransaction` interface fournit un accès à un ensemble de méthodes qui est similaire à celles accessibles par le `IDatabase` de l’interface, sauf que toutes les méthodes sont asynchrones. Cela signifie qu’ils ne sont effectuées que lorsque la `ITransaction.Execute` méthode est appelée. La valeur qui est retournée par le `ITransaction.Execute` méthode indique si la transaction a été créée avec succès (true) ou si elle a échoué (false).

L’extrait de code suivant illustre un exemple qui s’incrémente et décrémente de deux compteurs dans le cadre de la même transaction :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

N’oubliez pas que les transactions Redis Contrairement aux transactions dans les bases de données relationnelles. Le `Execute` méthode simplement des files d’attente toutes les commandes qui composent la transaction à exécuter, et si un d’eux est mal formé dans la transaction est arrêtée. Si toutes les commandes ont été mis en attente, chaque commande s’exécute de façon asynchrone.

Si n’importe quelle commande échoue, les autres toujours poursuivre le traitement. Si vous avez besoin vérifier qu’une commande s’est bien terminée, doivent extraire les résultats de la commande à l’aide de la propriété **Result** de la tâche correspondante, comme indiqué dans l’exemple ci-dessus. Lecture de la propriété **Result** bloque le thread appelant jusqu'à ce que la tâche est terminée.

Pour plus d’informations, consultez la page [Transactions dans Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) sur le site Web StackExchange.Redis.

Lorsque vous effectuez des opérations de traitement par lots, vous pouvez utiliser la `IBatch` interface de la bibliothèque StackExchange. Cette interface fournit l’accès à un ensemble de méthodes similaires à celles accessibles par le `IDatabase` de l’interface, sauf que toutes les méthodes sont asynchrones.

Vous créez un `IBatch` objet à l’aide de la `IDatabase.CreateBatch` méthode, puis exécutez le traitement par lots à l’aide de la `IBatch.Execute` méthode, comme illustré dans l’exemple suivant. Ce code est simplement définit une valeur de chaîne, incrémente et décrémente les mêmes compteurs utilisés dans l’exemple précédent et affiche les résultats :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Il est important de comprendre que contrairement à une transaction, si une commande dans un lot échoue, car il est incorrect, les autres commandes peuvent toujours s’exécuter. Le `IBatch.Execute` méthode ne retourne pas d’indication de réussite ou d’échec.

### <a name="perform-fire-and-forget-cache-operations"></a>Effectuer d’incendie et d’oublier les opérations du cache

Redis prend en charge incendie et oubliez les opérations à l’aide d’indicateurs de commande. Dans ce cas, le client simplement lance une opération mais n’a aucun intérêt dans le résultat et n’attend pas de la commande à effectuer. L’exemple ci-dessous montre comment exécuter la commande INCR sous la forme d’un incendie et oubliez l’opération :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>Spécifiez les clés automatiquement arrivant à expiration

Lorsque vous stockez un article dans un cache de Redis, vous pouvez spécifier un délai après lequel l’élément est automatiquement supprimé du cache. Vous pouvez également interroger plus de combien de temps une clé a avant son expiration à l’aide de la `TTL` commande. Cette commande n’est disponible pour les applications de StackExchange à l’aide de la `IDatabase.KeyTimeToLive` méthode.

L’extrait de code suivant montre comment définir un délai d’expiration de 20 secondes sur une clé et la durée de vie restante de la clé de requête :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Vous pouvez également définir le délai d’expiration à une date et une heure à l’aide de la commande d’expiration, ce qui est disponible dans la bibliothèque de StackExchange en tant que la `KeyExpireAsync` méthode :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Conseil :_ Vous pouvez supprimer manuellement un élément à partir du cache à l’aide de la commande DEL, qui est disponible par le biais de la bibliothèque StackExchange comme le `IDatabase.KeyDeleteAsync` méthode.

### <a name="use-tags-to-cross-correlate-cached-items"></a>Utiliser des balises pour cross-mis en corrélation des éléments mis en cache

Un jeu de Redis est une collection de plusieurs éléments qui partagent une clé unique. Vous pouvez créer un jeu à l’aide de la commande SADD. Vous pouvez récupérer les éléments dans un ensemble à l’aide de la commande SMEMBERS. La bibliothèque StackExchange implémente la commande SADD avec la `IDatabase.SetAddAsync` la SMEMBERS et procédé de commande avec la `IDatabase.SetMembersAsync` méthode.

Vous pouvez également combiner les jeux existants pour créer de nouveaux jeux à l’aide de la SDIFF (différence), frittage (intersection de l’ensemble) et les commandes SUNION (union définie). La bibliothèque StackExchange unifie ces opérations dans le `IDatabase.SetCombineAsync` méthode. Le premier paramètre de cette méthode spécifie l’opération de définition d’effectuer.

Les extraits de code suivants montrent comment les jeux peuvent s’avérer utiles pour rapidement de stocker et de récupérer des collections d’éléments connexes. Ce code utilise le `BlogPost` type qui a été décrit dans la section Redis de mettre en œuvre des Applications clientes Cache plus haut dans cet article.

A `BlogPost` objet contient quatre champs : un ID, un titre, un score de classement et une collection de balises. Le premier extrait de code ci-dessous montre les exemples de données qui sont utilisés pour le remplissage d’une liste de C# `BlogPost` objets :

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

Vous pouvez stocker les balises pour chaque `BlogPost` objet sous la forme d’un ensemble dans un cache de Redis et d’associer chaque ensemble avec l’ID de la `BlogPost`. Cela permet à une application trouver rapidement toutes les balises qui appartiennent à un billet de blog spécifique. Pour activer la recherche dans la direction opposée et rechercher tous les billets de blog qui partagent une balise spécifique, vous pouvez créer un autre jeu qui conserve les billets, référence à l’ID de la balise dans la clé :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Ces structures vous permettent d’effectuer de nombreuses requêtes communes très efficacement. Par exemple, vous pouvez rechercher et afficher toutes les balises de billet de blog 1 comme suit :

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Vous pouvez rechercher toutes les balises qui sont communs à un blog publier le billet de blog et 1 2 en effectuant une opération d’intersection définie, comme suit :

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Et vous pouvez trouver tous les billets de blog qui contiennent une balise spécifique :

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>Rechercher les éléments utilisés récemment

Une tâche courante que de nombreuses applications est de trouver la plupart des éléments utilisés récemment. Par exemple, un site de blog souhaitez afficher des informations sur les billets de blog de lecture le plus récemment.

Vous pouvez implémenter cette fonctionnalité à l’aide d’une liste de Redis. Une liste Redis contient plusieurs éléments qui partagent la même clé. La liste fonctionne comme une file d’attente de deux pointes. Vous pouvez distribuer des articles à des extrémités de la liste en utilisant les commandes de (push de droite) de RPUSH et de LPUSH (push de gauche). Vous pouvez récupérer des éléments à partir de des extrémités de la liste en utilisant les commandes LPOP et RPOP. Vous pouvez également renvoyer un jeu d’éléments à l’aide des LRANGE et réorganiser les commandes.

Les extraits de code ci-dessous montrent comment vous pouvez effectuer ces opérations à l’aide de la bibliothèque StackExchange. Ce code utilise le `BlogPost` type des exemples précédents. Comme un billet de blog est lu par un utilisateur, le `IDatabase.ListLeftPushAsync` méthode pousse le titre de la publication du blog sur une liste qui est associée à la clé « blog:recent_posts » dans le cache de Redis.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

Comme autres billets de blog sont lues, leurs titres sont placés à la même liste. La liste est classée par la séquence dans laquelle les titres ont été ajoutés. Les billets de blog plus récemment lus sont vers l’extrémité gauche de la liste. (Si le même billet de blog est lu plus d’une fois, il possède plusieurs entrées de la liste.)

Vous pouvez afficher les titres des articles plus récemment lus à l’aide de la `IDatabase.ListRange` méthode. Cette méthode prend la clé qui contient la liste, un point de départ et un point de fin. Le code suivant extrait les titres des 10 billets de blog (éléments de 0 à 9) à la fin de la plus à gauche de la liste :

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Notez que la `ListRangeAsync` méthode ne supprime pas les éléments de la liste. Pour ce faire, vous pouvez utiliser la `IDatabase.ListLeftPopAsync` et `IDatabase.ListRightPopAsync` méthodes.

Pour empêcher la liste de croître indéfiniment, vous pouvez régulièrement éliminez les éléments par ajustement de la liste. L’extrait de code ci-dessous montre comment supprimer tous les mais les cinq la plus à gauche les éléments de la liste :

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>Implémenter un forum leader

Par défaut, les éléments d’un jeu ne sont pas conservés dans un ordre spécifique. Vous pouvez créer un ensemble ordonné à l’aide de la commande ZADD (le `IDatabase.SortedSetAdd` méthode dans la bibliothèque de StackExchange). Les éléments sont triés à l’aide d’une valeur numérique appelée un score, qui est fourni comme paramètre à la commande.

L’extrait de code suivant ajoute le titre d’un billet de blog à une liste ordonnée. Dans cet exemple, chaque billet de blog possède également un champ score qui indique le niveau de priorité de la publication du blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Vous pouvez récupérer les titres des messages de blog et notes dans l’ordre du score dans l’ordre croissant à l’aide de la `IDatabase.SortedSetRangeByRankWithScores` méthode :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] La bibliothèque de StackExchange fournit également la `IDatabase.SortedSetRangeByRankAsync` méthode, qui renvoie les données dans l’ordre du score, mais ne renvoie pas les résultats.

Vous pouvez également extraire des éléments dans l’ordre décroissant des scores et limiter le nombre d’éléments qui sont retournés en fournissant des paramètres supplémentaires à la `IDatabase.SortedSetRangeByRankWithScoresAsync` méthode. L’exemple suivant affiche les titres et les scores du top 10 rang des publications du blog :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

L’exemple suivant utilise le `IDatabase.SortedSetRangeByScoreWithScoresAsync` méthode, ce qui vous permet de limiter les éléments qui sont retournés à celles qui relèvent d’un score donné plage :

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>Message à l’aide de canaux

Outre agissant comme un cache de données, un serveur Redis fournit la messagerie grâce à un mécanisme de haute performance éditeur/abonné. Les applications clientes peuvent s’abonner à un canal, et les autres applications ou services peuvent publier des messages sur le canal. Applications d’abonnement puis recevez ces messages et les traiter.

Redis fournit la commande de s’abonner pour les applications clientes à utiliser pour vous abonner à des chaînes. Cette commande attend le nom d’un ou plusieurs canaux sur lequel l’application accepte des messages. La bibliothèque StackExchange inclut la `ISubscription` interface, ce qui permet à une application.NET Framework s’abonner et publier sur les canaux.

Vous créez un `ISubscription` objet à l’aide de la `GetSubscriber` méthode de la connexion au serveur Redis. Puis vous écoutez les messages sur un canal à l’aide de la `SubscribeAsync` méthode de cet objet. L’exemple de code suivant montre comment s’abonner à un canal nommé « messages : blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Le premier paramètre de la `Subscribe` méthode est le nom du canal. Ce nom suit les mêmes conventions qui sont utilisées par des clés dans le cache. Le nom peut contenir des données binaires, bien qu’il soit conseillé d’utiliser des chaînes relativement courts et explicites pour garantir la facilité de maintenance et de bonnes performances.

Notez également que l’espace de noms utilisé par les canaux est distinct de celui utilisé par clés. Cela signifie que vous pouvez avoir des canaux et des touches qui ont le même nom, même si cela peut rendre plus difficile à gérer votre code d’application.

Le deuxième paramètre est un délégué d’Action. Ce délégué s’exécute de façon asynchrone chaque fois qu’un nouveau message s’affiche sur le canal. Cet exemple affiche simplement le message sur la console (le message contiendra le titre d’un billet de blog).

Pour publier sur un canal, une application peut utiliser la commande Redis la publication. La bibliothèque StackExchange fournit le `IServer.PublishAsync` méthode pour effectuer cette opération. L’extrait de code suivant montre comment publier un message sur le canal « messages : blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Il existe plusieurs points, que vous devez comprendre sur le mécanisme de publication et d’abonnement :

- Plusieurs abonnés peuvent s’abonner au même canal et qu’ils recevront tous les messages qui sont publiés dans ce canal.
- Les abonnés reçoivent uniquement les messages qui ont été publiés après que qu’ils ont souscrit. Canaux ne sont pas mis en mémoire tampon, et une fois qu’un message est publié, l’infrastructure Redis envoie le message à chaque abonné, puis le supprime.
- Par défaut, les messages sont reçus par les abonnés dans l’ordre dans lequel ils sont envoyés. Dans un système très actif, avec un grand nombre de messages et de nombreux abonnés et les éditeurs, garantit la distribution séquentielle des messages peut ralentir les performances du système. Si chaque message est indépendante et que la commande n’est pas importance, vous pouvez activer le traitement simultané par le système Redis, ce qui contribue à améliorer la réactivité. Vous pouvez obtenir cela dans un client de StackExchange en définissant le PreserveAsyncOrder de la connexion utilisée par l’abonné sur false :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

## <a name="related-patterns-and-guidance"></a>Conseils et des modèles associés

Le modèle suivant peut également être pertinent pour votre scénario lorsque vous implémentez la mise en cache dans vos applications :

- [Modèle de cache-aside](http://msdn.microsoft.com/library/dn589799.aspx): ce modèle décrit comment charger des données à la demande dans un cache à partir d’un magasin de données. Ce modèle permet également de maintenir la cohérence entre les données qui sont conservées dans le cache et les données dans le magasin de données d’origine.
- Le [modèle d’ont](http://msdn.microsoft.com/library/dn589797.aspx) fournit des informations sur l’implémentation du partitionnement horizontal afin d’améliorer l’évolutivité lorsque le stockage et l’accès à grands volumes de données.

## <a name="more-information"></a>Plus d’informations

- La page [MemoryCache classe](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) sur le site Web de Microsoft
- La page de [Cache de Azure Redis documentation](https://azure.microsoft.com/documentation/services/cache/) sur le site Web de Microsoft
- La page [Forum aux questions Azure Redis du Cache](redis-cache/cache-faq.md) sur le site Web de Microsoft
- La page de [modèle de Configuration](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) sur le site Web de Microsoft
- La page de [Modèle asynchrone basé sur des tâches](http://msdn.microsoft.com/library/hh873175.aspx) sur le site Web de Microsoft
- La page de [Pipelines et les multiplexeurs](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) la mis en pension StackExchange.Redis GitHub
- La page sur le site Web de Redis [Redis de persistance](http://redis.io/topics/persistence)
- La [page de réplication](http://redis.io/topics/replication) sur le site Web de Redis
- La page [Redis du didacticiel de cluster](http://redis.io/topics/cluster-tutorial) sur le site Web de Redis
- Le [partitionnement : comment répartir les données entre plusieurs instances de Redis](http://redis.io/topics/partitioning) page sur le site Web de Redis
- La page [à l’aide de Redis comme un Cache LRU](http://redis.io/topics/lru-cache) sur le site Web de Redis
- La page de [Transactions](http://redis.io/topics/transactions) sur le site Web de Redis
- La page sur le site Web de Redis de [Redis de sécurité](http://redis.io/topics/security)
- La page de [Cache Redis d’Azure visite guidée](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog Azure
- La page [En cours d’exécution Redis sur une machine virtuelle Linux de CentOS dans Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) sur le site Web de Microsoft
- La page de [fournisseur d’état de session ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-session-state-provider.md) sur le site Web de Microsoft
- La [fournisseur de cache de sortie ASP.NET pour le Cache de Azure Redis](redis-cache/cache-aspnet-output-cache-provider.md) page sur le site Web de Microsoft
- La page [An Introduction to Redis des captages et des types de données](http://redis.io/topics/data-types-intro) sur le site Web de Redis
- La page sur le site Web de StackExchange.Redis de [l’utilisation de base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md)
- La page [Transactions dans Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) le mis en pension StackExchange.Redis
- Le [guide de partitionnement de données](http://msdn.microsoft.com/library/dn589795.aspx) sur le site Web de Microsoft
