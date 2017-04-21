#<a name="data-management-and-business-analytics"></a>Gestion des données et Business Analytique

Gérer et analyser les données dans le nuage sont tout aussi important qu’il se trouve ailleurs. Pour vous permettre de le faire, Azure propose une gamme de technologies pour travailler avec des données relationnelles et non relationnelles. Cet article présente chacune des options. 

##<a name="table-of-contents"></a>Table des matières      

- [Stockage des objets BLOB](#blob)
- [Un SGBD en cours d’exécution sur une Machine virtuelle](#dbinvm)
- [Base de données SQL](#sqldb)
    - [Synchronisation des données SQL](#datasync)
    - [Création de rapports de données SQL à l’aide de Machines virtuelles](#datarpt)
- [Stockage de table](#tblstor)
- [Hadoop](#hadoop)

## <a name="blob"></a>Stockage des objets BLOB

Le mot « blob » est l’abréviation de « Binary Large OBject », et il décrit exactement quel un blob est : une collection d’informations binaires. Encore même si elles sont simples, les objets BLOB est très utiles. [La figure 1](#Fig1) illustre les notions de base de stockage des objets Blob Azure.

<a name="Fig1"></a>![Diagramme des objets BLOB][blobs]
 
**Figure 1 : Stockage de Blob Azure stocke des données binaires - BLOB - dans des conteneurs.**

Pour utiliser les objets BLOB, vous créez un *compte de stockage*d' Azure. Dans le cadre de cette, vous spécifiez le centre de données Azure qui stockera les objets que vous créez à l’aide de ce compte. Partout où il réside, chaque objet blob que vous créez appartient à un conteneur dans votre compte de stockage. Pour accéder à un objet blob, une application fournit une URL avec la forme :

http://&lt;*StorageAccount*&gt;.blob.core.windows.net/&lt;*conteneur*&gt;/&lt;*BlobName*&gt;

&lt;*StorageAccount* &gt; est un identificateur unique affecté lors de la création d’un nouveau compte de stockage, tandis que &lt; *conteneur de* &gt; et &lt; *BlobName* &gt; sont les noms d’un conteneur spécifique et un blob dans ce conteneur. 

Azure fournit deux types d’objets BLOB. Les choix sont :

- BLOB de *bloc* , chacun d’eux peut contenir jusqu'à 200 Go de données. Comme son nom l’indique, un blob de bloc est divisé en plusieurs blocs. Si une défaillance se produit pendant le transfert d’un blob de bloc, retransmission reprend avec le bloc la plus récent plutôt que d’envoyer de nouveau entièrement. BLOB de bloc est une approche assez générale pour le stockage, et le type de blob la plus couramment utilisés aujourd'hui.

- BLOB de *page* , qui peut être aussi grand à un téraoctet. BLOB de page est conçues pour un accès aléatoire, et pour chacun d’eux est divisé en plusieurs pages. Une application peut lire et écrire les pages individuelles au hasard dans le blob. Dans Azure ordinateurs virtuels, par exemple, ordinateurs virtuels que vous créez utilisent les blobs de page sous la forme d’un stockage persistant pour les disques du système d’exploitation et des disques de données.

Si vous choisissez bloc BLOB ou des objets de la page, applications peuvent accéder aux données blob de différentes manières. Les options sont les suivantes :

- Directement par l’intermédiaire d’un RESTful (c'est-à-dire, basé sur HTTP) protocole d’accès. Les applications Azure et les applications externes, y compris les applications en cours d’exécution dans les locaux, peuvent utiliser cette option.
- À l’aide de la bibliothèque cliente du stockage Azure, qui fournit une interface plus conviviales et le protocole d’accès brut blob RESTful. Une fois de plus, les applications Azure et les applications externes accessibles BLOB à l’aide de cette bibliothèque.
- Utilisation de lecteurs d’Azure, une option qui permet à une application Azure de traiter un objet blob de page sous la forme d’un lecteur local avec un système de fichiers NTFS. L’application, le blob de page se présente comme un système de fichiers Windows ordinaire, accédé à l’aide d’e/s de fichier standard. En fait, lit et écrit sont envoyés à l’objet blob page sous-jacente qui implémente le lecteur Azure. 

Pour se protéger contre les défaillances matérielles et d’améliorer la disponibilité, chaque objet blob est répliqué sur trois ordinateurs dans un centre de données Azure. L’écriture dans un blob met à jour toutes les copies de trois, afin que les lectures ultérieures ne sont pas voir des résultats incohérents. Vous pouvez également spécifier que les données d’un blob doivent être copiées vers un autre centre de données Azure dans le même géo, mais au moins 500 miles. Cette copie, appelée *réplication géographique*, qui se produit après quelques minutes d’une mise à jour pour le blob, et il est utile de reprise après sinistre.

Données d’objets BLOB peuvent également mises à disposition par le biais du *Réseau CDN (Content Delivery)*d' Azure. En mettant en cache des copies des données blob à des dizaines de serveurs dans le monde entier, le CDN peut accélérer l’accès aux informations qui se trouve à plusieurs reprises. 

Simple comme ils le sont, les objets BLOB sont la solution idéale dans de nombreuses situations. Stockage et de diffusion en continu de vidéo et audio sont des exemples évidents, tout comme les sauvegardes et autres types de données d’archivage. Les développeurs peuvent utiliser également de BLOB pour contenir n’importe quel type de données non structurées, qu'ils le souhaitent. Ayant un moyen simple de stocker et d’accéder aux données binaires peut être étonnamment utile.


## <a name="dbinvm"></a>Un SGBD en cours d’exécution sur une Machine virtuelle

Aujourd'hui, de nombreuses applications reposent sur un certain type de système de gestion de base de données (SGBD). Des systèmes de relationnelle de SQL Server sont les choix les plus fréquemment utilisés, mais des approches non relationnelles, communément appelé *NoSQL* technologies, obtenir plus populaires chaque jour. Pour permettre à des applications de cloud ces options de gestion de données, ordinateurs virtuels Azure vous permet d’exécuter un SGBD (relationnel ou NoSQL) dans un ordinateur virtuel. [La figure 2](#Fig2) montre comment cela se présente avec SQL Server.

<a name="Fig2"></a>![Diagramme de SQL Server sur une Machine virtuelle][SQLSvr-vm]
 
**Figure 2 : Des Machines virtuelles Azure permet exécutant un SGBD dans une machine virtuelle, avec persistance fournie par les objets BLOB.**

Pour les développeurs et les administrateurs de base de données, ce scénario ressemble beaucoup le même logiciel en cours d’exécution dans son propre centre de données. Dans l’exemple présenté ici, par exemple, presque toutes les fonctionnalités de SQL Server peuvent être utilisé, et vous avez accès administratif total sur le système. Vous avez également la responsabilité de la gestion du serveur de base de données, bien sûr, comme s’il était exécuté localement.

Comme le montre la [Figure 2](#Fig2) , vos bases de données semblent être stockées sur le disque local de l’ordinateur virtuel que le serveur s’exécute dans. Cependant, en coulisses, chacun de ces disques est écrite pour un blob Azure. (Il est similaire à l’utilisation d’un réseau SAN dans votre propre centre de données, avec un objet blob agissant plus comme une LUN). Comme avec n’importe quel objet blob Azure, les données qu’il contient sont répliquées trois fois au sein d’un centre de données et, si vous le demandez, géo-répliquées vers un autre centre de données dans la même région. Il est également possible d’utiliser les options de mise en miroir pour améliorer la fiabilité de la base de données SQL Server. 

Une autre façon d’utiliser SQL Server sur un ordinateur virtuel est de créer une application hybride, où les données résident sur Azure pendant l’exécution de la logique d’application local. Par exemple, cela peut être utile lorsque les applications en cours d’exécution dans plusieurs emplacements ou sur différents périphériques mobiles doivent partager les mêmes données. Pour faciliter la communication entre le nuage de base de données et en local la logique plus simple, une organisation peut utiliser des réseaux virtuels d’Azure pour créer une connexion de réseau privé virtuel (VPN) entre un centre de données Azure et son propre centre de données sur site.


## <a name="sqldb"></a>Base de données SQL

Pour de nombreuses personnes, un SGBD en cours d’exécution sur un ordinateur virtuel est la première option qui vient à l’esprit pour la gestion des données structurées dans le nuage. Il n’est pas le seul choix, ni toujours le meilleur choix. Dans certains cas, il est préférable de gestion des données à l’aide d’une plate-forme comme une approche de Service (PaaS). Azure fournit une technologie PaaS appelée base de données SQL qui vous permet d’effectuer cette opération pour les données relationnelles. [La figure 3](#Fig3) illustre cette option. 

<a name="Fig3"></a>![Diagramme de base de données SQL][SQL-db]
 
**Figure 3 : Base de données SQL fournit un service de stockage relationnel PaaS partagé.**

Base de données de SQL ne donne pas chaque client sa propre instance physique de SQL Server. Au lieu de cela, il fournit un service mutualisée, avec un serveur de base de données SQL logique pour chaque client. Tous les clients partagent la capacité de calcul et de stockage que le service fournit. Et comme avec le stockage Blob, toutes les données de la base de données SQL est stockée sur trois ordinateurs différents au sein d’un centre de données Azure, en donnant à vos bases de données intégrée haute disponibilité (HA).

Pour une application, base de données de SQL ressemble beaucoup de SQL Server. Applications peuvent émettre des requêtes sur les tables relationnelles de SQL, utilisez les procédures stockée de T-SQL et exécuter des transactions sur plusieurs tables. Et parce que la base de données SQL en utilisant le protocole de flux de données tabulaires (TDS), le même protocole que celui utilisé pour accéder à SQL Server, d’accéder aux applications ils peuvent travailler avec des données à l’aide d’Entity Framework, ADO.NET, JDBC et autres interfaces d’accès aux données familières. 

Mais, parce que la base de données de SQL est un service en nuage en cours d’exécution dans les centres de données Azure, vous n’avez pas besoin de gérer les aspects du système physique, notamment l’utilisation du disque. Vous ne devez également à vous soucier de la mise à jour de logiciels ou gestion des autres tâches d’administration de bas niveau. Chaque organisation client contrôle toujours ses propres bases de données, bien entendu, y compris leurs schémas et les connexions de l’utilisateur, mais la plupart des tâches d’administration courantes sont effectuées automatiquement. 

Pendant que la base de données de SQL ressemble de SQL Server pour les applications, il ne comporte pas exactement le même qu’un SGBD en cours d’exécution sur un ordinateur physique ou virtuel. Dans la mesure où il s’exécute sur du matériel partagé, ses performances varie avec la charge placée sur ce matériel par l’ensemble de ses clients. Cela signifie que les performances de, par exemple, une procédure stockée dans la base de données de SQL peut varier d’un jour à l’autre. 

Aujourd'hui, base de données de SQL vous permet de créer une base de données contenant jusqu'à 150 Go. Si vous avez besoin travailler avec des bases de données volumineuses, le service fournit une option appelée *fédération*. Pour ce faire, un administrateur de base de données crée deux ou plusieurs *membres de la fédération*, chacun d’eux est une base de données distincte avec son propre schéma. Données sont réparties sur ces membres, ce qui est souvent appelé *ont*, avec chaque membre affecté une unique *clé de fédération*. Une application émet des requêtes SQL par rapport à ces données en spécifiant la clé de fédération qui identifie le membre de la fédération la requête doivent cibler. Cela permet à l’aide d’une approche relationnelle traditionnelle avec de grandes quantités de données. Comme toujours, il existe des compromis ; requêtes, ni les transactions peuvent s’étendre sur membres de la fédération, par exemple. Mais lorsqu’un service PaaS relationnel constitue le meilleur choix et ces inconvénients sont acceptables, l’à l’aide de la fédération de SQL peut être une bonne solution.

Base de données de SQL peut être utilisé par les applications qui s’exécutent sur Azure, ou ailleurs, par exemple dans votre centre de données sur site. Cela permet de réaliser des applications qui ont besoin de données relationnelles en nuage, ainsi que les applications peuvent bénéficier de stocker des données dans le nuage local. Une application mobile peut-être compter sur la base de données SQL pour gérer des données relationnelles partagées, par exemple, comme peut à une application d’inventaire qui s’exécute à plusieurs distributeurs dans le monde entier.

Réflexion à propos de la base de données SQL déclenche un problème évident (et important) : quand devez-vous exécuter SQL Server dans une machine virtuelle, et que la base de données de SQL est un meilleur choix ? Comme d’habitude, il existe des compromis, et donc quelle approche est meilleure dépend de vos besoins. 

Une simple à y penser consiste à afficher de la base de données SQL comme étant pour les nouvelles applications, alors que SQL Server sur un ordinateur virtuel est un meilleur choix lorsque vous déplacez un existant sur place les applications dans le nuage. Il peut également être utile d’examiner cette décision d’une manière plus précise, cependant. Par exemple, la base de données de SQL est plus facile à utiliser, dans la mesure où il existe un minimum d’installation et d’administration. Mais SQL Server en cours d’exécution sur un ordinateur virtuel peut avoir des performances plus prévisibles - il n’est pas un service partagé - et prend également en charge des bases de données volumineuses non fédéré à SQL de base de données. Toujours, base de données de SQL fournit une réplication intégrée des données et de traitement, efficacement ce qui vous donne un SGBD haute disponibilité avec très peu de travail. Tandis que SQL Server offre un plus grand contrôle et un ensemble un peu plus large d’options, la base de données de SQL est plus simple à installer et beaucoup moins de travail pour gérer.

Enfin, il est important de souligner que base de données de SQL n’est pas le seul service de données PaaS disponible sur Azure. Les partenaires de Microsoft proposent des autres options ainsi. Par exemple, ClearDB propose un MySQL PaaS offre, tandis que Cloudant vend une option NoSQL. PaaS des services de données sont la solution idéale dans de nombreux cas, et donc cette approche de la gestion des données est une partie importante d’Azure.


### <a name="datasync"></a>Synchronisation des données SQL

Pendant que la base de données de SQL ne conserve pas les trois copies de chaque base de données dans un seul centre de données Azure, il ne réplique automatiquement des données entre des centres de données Azure. Au lieu de cela, il fournit SQL synchronisation de données, un service que vous pouvez utiliser pour cela. [La figure 4](#Fig4) montre comment cela se présente.

<a name="Fig4"></a>![Schéma de synchronisation de données SQL][SQL-datasync]
 
**Figure 4 : Synchronisation des données SQL synchronise les données dans la base de données de SQL avec des données dans les autre Azure et centres de données sur site.**

Comme l’indique le diagramme, la synchronisation de données SQL peut synchroniser les données sur les différents sites. Supposons que vous exécutez une application dans plusieurs centres de données Azure, par exemple, les données stockées dans la base de données de SQL. Synchronisation de données SQL vous permet de garder les données synchronisées. Synchronisation des données SQL peuvent également synchroniser des données entre un centre de données Azure et une instance de SQL Server en cours d’exécution dans un centre de données sur site. Cela peut être utile pour gérer à la fois une copie locale des données utilisées par les applications sur site et une copie de nuage utilisé par les applications qui s’exécutent sur Azure. Et bien qu’il n’est pas affiché dans l’illustration, la synchronisation de données SQL peut également être utilisée pour synchroniser les données entre la base de données de SQL et de SQL Server en cours d’exécution sur un ordinateur virtuel sur Azure ou ailleurs.

Synchronisation peut être bidirectionnel, et vous déterminez exactement les données à synchroniser et la fréquence à laquelle il est fait. (La synchronisation entre les bases de données n’est pas atomique, cependant, il existe toujours au moins un retard). Et cependant, il est utilisé, configuration de la synchronisation avec la synchronisation de données SQL est entièrement pilotée par configuration ; Il n’y a aucun code à écrire.


### <a name="datarpt"></a>Création de rapports de données SQL à l’aide de Machines virtuelles

Une fois qu’une base de données contient des données, quelqu'un souhaiterez probablement créer des rapports à l’aide de ces données. Azure peut exécuter SQL Server Reporting Services (SSRS) dans Azure Machines virtuelles, qui est fonctionnellement équivalent à l’exécution de SQL Server Reporting Services sur site. Puis, vous pouvez utiliser SSRS pour exécuter des rapports sur des données stockées dans une base de données de SQL Azure.  [La figure 5](#Fig5) illustre le fonctionnement du processus.

<a name="Fig5"></a>![Schéma de création de rapports SQL][SQL-report]
 
**Figure 5 : SQL Server Reporting Services en cours d’exécution dans un ordinateurs virtuels Azure fournit des services de création de rapports des données dans la base de données de SQL. .**

Un utilisateur peut afficher un rapport, une personne définit ce que ce rapport doit se présenter comme (étape 1). Avec SSRS sur un ordinateur virtuel, ceci est possible à l’aide d’un des deux outils : outils de données de SQL Server, de SQL Server 2012 une partie ou de son prédécesseur, Business Intelligence (BI) Development Studio. À l’instar de SSRS, ces définitions de rapport sont exprimées dans le langage RDL (Report Definition). Après avoir créé les fichiers RDL pour un rapport, ils sont téléchargés vers une machine virtuelle dans le nuage (étape 2). La définition de rapport est maintenant prête à l’emploi.

Ensuite, un utilisateur de l’application accède à l’état (étape 3). L’application transmet cette demande de SSRS VM (étape 4), qui contacte la base de données de SQL ou d’autres sources de données pour obtenir les données dont il a besoin (étape 5). SSRS utilise ces données et les fichiers RDL appropriés pour restituer l’état (étape 6), puis renvoie l’état de l’application (étape 7), qui l’affiche à l’utilisateur (étape 8).

L’incorporation d’un rapport dans une application, le scénario illustré ici, n’est pas la seule option. Il est également possible d’afficher des rapports sur l’ordinateur virtuel, SharePoint sur l’ordinateur virtuel dans le Gestionnaire de rapports ou par d’autres moyens. Les rapports peuvent également être combinées, avec un rapport contenant un lien vers un autre.

SSRS sur une machine virtuelle d’Azure vous offre toutes les fonctionnalités sous la forme d’une solution de création de rapports dans le nuage. Les rapports peuvent utiliser n’importe quelle source de données pris en charge par SSRS. Les applications et les rapports peuvent inclure un code incorporé ou les assemblys pour prendre en charge des comportements personnalisés. Le rendu et l’exécution du rapport sont rapides, car le moteur et le contenu du rapport sont exécutées ensemble sur le même serveur virtuel.



## <a name="tblstor"></a>Stockage de table

Données relationnelles sont utiles dans de nombreuses situations, mais il n’est pas toujours la solution idéale. Si votre application a besoin d’un accès rapide et simple de très grandes quantités de données faiblement structurées, par exemple, une base de données relationnelle peut ne pas fonctionne correctement. Une technologie de NoSQL est susceptible d’être une meilleure option.

Stockage par Table Azure est un exemple de ce type d’approche de NoSQL. Malgré son nom, stockage de Table ne prend en charge les tables relationnelles standard. Au lieu de cela, il fournit ce que l'on appelle une *clé/valeur stocker*, association d’un jeu de données avec une clé particulière, puis laisser l’accès d’une application que les données par la fourniture de la clé. [La figure 6](#Fig6) illustre les notions de base.

<a name="Fig6"></a>![Schéma de stockage de table][SQL-tblstor]
 
**Figure 6 : Stockage par Table Azure est un magasin de clé/valeur qui garantit un accès simple à grandes quantités de données rapide.**

Comme les objets BLOB, chaque table est associé à un compte de stockage Azure. Tables sont également nommés beaucoup comme objets BLOB, avec une URL du formulaire

http://&lt;*StorageAccount*&gt;.table.core.windows.net/&lt;*TableName*&gt;

Comme le montre la figure, chaque table est divisé en plusieurs partitions, chacune d'entre elles peut être stockée sur un ordinateur distinct. (C’est une forme d’ont, comme avec la fédération de SQL). Les applications Azure et les applications en cours d’exécution ailleurs peuvent accéder à une table en utilisant le protocole OData RESTful ou la bibliothèque cliente du stockage Azure.

Chaque partition d’une table conserve un certain nombre d' *entités*, contenant chacun jusqu'à 255 *Propriétés*. Chaque propriété a un nom, un type (par exemple, le fichier binaire, Bool, DateTime, Int ou String) et une valeur. Contrairement à un stockage relationnel, ces tables n’ont aucun schéma fixe, et donc différentes entités dans la même table peuvent contenir des propriétés avec différents types. Une entité peut avoir seulement une propriété de type chaîne contenant un nom, par exemple, alors qu’une autre entité dans la même table a deux propriétés Int contenant un numéro d’ID de client et d’un taux de crédit.

Pour identifier une entité particulière dans une table, une application fournit cette clé. La clé comporte deux parties : une *clé de partition* qui identifie une partition spécifique et une *clé de ligne* qui identifie une entité au sein de cette partition. Dans [la Figure 6](#Fig6), par exemple, le client demande l’entité avec clé de partition A et de la clé de la ligne 3 et stockage de Table retourne cette entité, y compris toutes les propriétés qu’il contient.

Cette structure permet d’être volumineux tables - une seule table peut contenir jusqu'à 100 téraoctets de données, et il permet un accès rapide aux données qu’ils contiennent. Il apporte également des limitations, toutefois. Par exemple, il n’existe aucune prise en charge pour les mises à jour transactionnelles qui s’étendent sur des tables ou même des partitions d’une table. Un ensemble de mises à jour d’une table peut uniquement être regroupé dans une transaction atomique si toutes les entités impliquées sont dans la même partition. Il n’est également aucun moyen pour interroger une table basée sur la valeur de ses propriétés, ni aucune prise en charge des jointures entre plusieurs tables. Et contrairement aux bases de données relationnelles, les tables n’ont aucune prise en charge des procédures stockées.

Stockage par Table Azure est un bon choix pour les applications nécessitant un accès rapide et bon marché de grandes quantités de données faiblement structurées. Par exemple, une application Internet qui stocke les informations de profil pour un grand nombre d’utilisateurs peut utiliser des tables. Accès rapide est important dans cette situation, et que l’application n’a pas besoin probablement pas la puissance de SQL. Abandonner cette fonctionnalité pour obtenir la vitesse et la taille peut être parfois intéressant et stockage de Table n’est donc la solution pour certains problèmes.


## <a name="hadoop"></a>Hadoop

Les organisations ont été création d’entrepôts de données depuis des décennies. Ces collections d’informations, plus souvent stockées dans des tables relationnelles, permettent aux utilisateurs de manipuler et d’apprendre à partir des données de différentes façons. Avec SQL Server, par exemple, il est courant d’utiliser des outils comme SQL Server Analysis Services pour effectuer cette opération.

Mais supposons que vous vouliez effectuer des analyses de données non relationnelles. Vos données peuvent prendre de nombreuses formes : informations provenant des capteurs ou des balises RFID, les fichiers journaux dans les batteries de serveurs, les données de parcours produites par les applications web, des images à partir de dispositifs médicaux de diagnostics et bien plus encore. Ces données peuvent également être très volumineuses, trop grand pour être utilisé efficacement à l’entrepôt de données traditionnel. Des problèmes de données grand à ceci, rares quelques années sont maintenant assez courant.

Pour analyser ce type de données volumineuses, notre secteur a largement mené à une solution unique : la technologie open source Hadoop. Hadoop s’exécute sur un cluster d’ordinateurs physiques ou virtuels, répartissant les données qu’il fonctionne sur ces machines et de traitement en parallèle. Les ordinateurs plus Hadoop doit utiliser, plus il peut terminer quel que soit le travail fait.

Ce type de problème est une solution naturelle pour le nuage public. Au lieu de maintenir une VM armée de serveurs qui peuvent restez inactif la plupart du temps, en cours d’exécution Hadoop dans le cloud vous permet de créer (et un salaire pour) sur site uniquement lorsque vous en avez besoin. Mieux encore, de plus en plus des données que vous souhaitez analyser avec Hadoop grandes est créé dans le nuage, vous évite de les déplacer. Pour vous aider à exploiter les synergies, Microsoft propose un service de Hadoop sur Azure. [La figure 7](#Fig7) illustre les éléments les plus importants de ce service.

<a name="Fig7"></a>![Diagramme d’hadoop][hadoop]

**Figure 7 : Hadoop sur Azure s’exécute les travaux MapReduce qui traitent les données en parallèle à l’aide de plusieurs machines virtuelles.**

Pour utiliser Hadoop sur Azure, vous demandez tout d’abord cette plateforme en nuage pour créer un cluster Hadoop, indiquant le nombre d’ordinateurs virtuels dont vous avez besoin. La définition d’un cluster d’Hadoop vous-même est une tâche non triviale, et donc laisser Azure à le faire pour vous de sens. Lorsque vous avez terminé à l’aide du cluster, vous l’arrêtez. Il n’est pas nécessaire de payer pour des ressources de calcul que vous n’utilisez pas.

Une application Hadoop, couramment appelée *tâche*, utilise un modèle de programmation appelé *MapReduce*. Comme le montre la figure, la logique pour un travail MapReduce s’exécute simultanément au sein de machines virtuelles. Traitement des données en parallèle, Hadoop analyse des données beaucoup plus rapidement que les solutions de la seule machine.

Sur Azure, les données un MapReduce tâche fonctionne sur est généralement conservée dans le stockage blob. Toutefois, dans Hadoop, MapReduce travaux attendent des données qui seront stockées dans le *Système de fichier distribué (très) Hadoop*. TRÈS est similaire au stockage d’objets Blob dans une certaine mesure ; Il réplique les données sur plusieurs serveurs physiques, par exemple. Plutôt que de dupliquer cette fonctionnalité, Hadoop sur Azure expose plutôt stockage Blob via l’API très, comme le montre la figure. Alors que la logique d’un travail MapReduce pense qu’il accède à des fichiers très ordinaires, fonctionne en fait le travail avec des données diffusées en continu vers elle à partir des objets BLOB. Et pour prendre en charge le cas où plusieurs tâches sont exécutées sur les mêmes données, Hadoop sur Azure permettent également de copier les données d’objets BLOB dans le très complet en cours d’exécution sur les ordinateurs virtuels. 

MapReduce travaux sont généralement écrits en Java aujourd'hui, une approche qui prend en charge les Hadoop sur Azure. Microsoft a également ajouté la prise en charge de la création de travaux de MapReduce dans d’autres langages, notamment C#, F# et JavaScript. L’objectif est de rendre cette technologie de données plus facilement accessibles à un plus grand groupe de développeurs.

TRÈS et MapReduce, Hadoop comprend d’autres technologies qui permettent aux utilisateurs d’analyser les données sans écrire une tâche MapReduce eux-mêmes. Par exemple, porc est un langage de haut niveau conçu pour l’analyse des données, tandis que la ruche offre un langage de type SQL appelé HiveQL. Les porcs et la ruche effectivement génèrent des travaux MapReduce qui traitent les données très, mais ils masquent cette complexité à leurs utilisateurs. Les deux sont fournis avec Hadoop sur Azure.

Microsoft fournit également un pilote HiveQL pour Excel. En utilisant un complément Excel, les analystes d’entreprise peuvent créer des requêtes de HiveQL (et donc des travaux de MapReduce) directement à partir de Excel, traiter puis visualiser les résultats à l’aide de PowerPivot et autres outils d’Excel. Hadoop sur Azure inclut des autres technologies, telles que l’apprentissage bibliothèques Mahout, le système d’exploration de données de graphique Pegasus, etc. de l’ordinateur.

Analyse de données est important, et Hadoop est donc également important. En fournissant des Hadoop sous la forme d’un service géré sur Azure, ainsi que des liens vers des outils familiers tels que Excel, Microsoft vise à rendre cette technologie accessible à un plus large ensemble d’utilisateurs.

Plus généralement, les données de tous les types sont importantes. C’est pourquoi Azure inclut une gamme d’options pour l’analytique de gestion et de données. Nom de l’application que vous tentez de créer, il est probable que vous trouverez quelque chose dans cette plateforme en nuage qui fonctionnera pour vous.

[blobs]: ./media/cloud-storage/Data_01_Blobs.png
[SQLSvr-vm]: ./media/cloud-storage/Data_02_SQLSvrVM.png
[SQL-db]: ./media/cloud-storage/Data_03_SQLdb.png
[SQL-datasync]: ./media/cloud-storage/Data_04_SQLDataSync.png
[SQL-report]: ./media/cloud-storage/Data_05_SQLReporting.png
[SQL-tblstor]: ./media/cloud-storage/Data_06_TblStorage.png
[hadoop]: ./media/cloud-storage/Data_07_Hadoop.png
