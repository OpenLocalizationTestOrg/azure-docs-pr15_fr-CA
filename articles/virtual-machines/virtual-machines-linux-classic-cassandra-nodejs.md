<properties
    pageTitle="Exécutez Cassandra avec Linux sur Azure | Microsoft Azure"
    description="Comment faire pour exécuter un cluster Cassandra sur Linux dans Azure Virtual Machines à partir d’une application Node.js"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>En cours d’exécution Cassandra avec Linux sur Azure et y accéder à partir de Node.js

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Vue d’ensemble
Microsoft Azure est une plateforme de cloud ouverte qui s’exécute à la fois Microsoft ainsi que des logiciels non Microsoft qui inclut les systèmes d’exploitation, serveurs d’application, middleware de messagerie ainsi que des bases de données NoSQL et de SQL à partir de ces deux modèles commerciaux et open source. Création de services robustes sur des nuages publics, y compris d’Azure requiert une planification et architecture délibérée pour les serveurs d’applications en tant que couches de stockage bien. Architecture de stockage distribué de Cassandra permet naturellement dans la création de systèmes à haute disponibilité qui sont la tolérance de pannes pour les échecs de cluster. Cassandra est une échelle de nuage de la base de données NoSQL maintenue par Apache Software Foundation à cassandra.apache.org ; Cassandra est écrit en Java et s’exécute donc à la fois sur Windows et Linux sur les plates-formes.

L’objectif de cet article est de montrer des déploiement de Cassandra sur Ubuntu comme un cluster unique et multi-centre de données exploitant Microsoft Azure Virtual Machines et réseaux virtuels. Le déploiement de cluster pour les charges de travail optimisé est hors de portée de cet article car il nécessite la configuration du nœud de disque multiples, conception d’une topologie anneau approprié et modélisation pour prendre en charge la réplication, la cohérence des données, le débit en matière de haute disponibilité des données.

Ce prend l’article une approche fondamentale pour afficher ce qui est impliquée dans la création d’un cluster Cassandra comparé Docker, Chef ou Marionnette qui peut faciliter le déploiement d’une infrastructure beaucoup.  

## <a name="the-deployment-models"></a>Les modèles de déploiement
Mise en réseau de Microsoft Azure permet le déploiement de clusters de privé isolés, dont l’accès peut être limité à la réalisation de la sécurité du réseau de granularité fine.  Dans la mesure où cet article est sur l’affichage du déploiement Cassandra à un niveau fondamental, nous nous concentrerons pas sur le niveau de cohérence et de la conception du stockage optimal pour le débit. Voici la liste de mise en réseau de la configuration requise pour notre hypothétique cluster :

- Cassandra de base de données à partir de dans ou en dehors d’Azure ne peut pas accéder à des systèmes externes
- Cluster de Cassandra doit se trouver derrière un équilibreur de charge pour le trafic de thrift
- Déploiement de nœuds de Cassandra dans deux groupes dans chaque centre de données pour une disponibilité améliorée
- Verrouiller le cluster ainsi que seulement de la batterie de serveurs d’application a accès à la base de données directement
- Aucun point de terminaison de réseau public que SSH
- Chaque nœud Cassandra a besoin d’une adresse IP interne de fixe

Cassandra peut être déployé à une région Azure unique ou dans plusieurs régions, en fonction de la nature distribuée de la charge de travail. Modèle de déploiement de plusieurs régions peut être exploitée pour prendre en charge les utilisateurs finaux plus près une géographie particulière via la même infrastructure Cassandra. Nœud intégré réplication prend en charge de Cassandra de la synchronisation de plusieurs maîtres écrit à partir de plusieurs centres de données d’origine et présente une vue cohérente des données pour les applications. Déploiement de plusieurs région peut également aider à l’atténuation des risques la plus large Azure d’interruption des services. Cohérence d’ACCORDABLE de Cassandra et la topologie de réplication vous aidera à répondre à des besoins RPO différents des applications.

### <a name="single-region-deployment"></a>Déploiement de zone unique
Nous démarre avec un déploiement unique de zone et les apprentissages que lors de la création d’un modèle de plusieurs région de la récolte. Un réseau virtuel Azure permet de créer des sous-réseaux isolés afin que les exigences de sécurité réseau mentionnées ci-dessus peuvent être satisfaites.  Le processus décrit dans Création du déploiement de la même région utilise Ubuntu 14.04 LTS et Cassandra, 2.08 ; Toutefois, le processus peut facilement arrêté aux autres variantes de Linux. Voici quelques-unes des caractéristiques du déploiement seule région systémiques.  

**Haute disponibilité :** Les nœuds Cassandra illustrés à la Figure 1 sont déployés sur les deux ensembles de disponibilité afin que les nœuds sont répartis entre plusieurs domaines d’erreur pour une haute disponibilité. Ordinateurs virtuels annotées avec chaque ensemble de disponibilité est mappé sur 2 domaines d’erreur.  Utilise Microsoft Azure le concept de domaine d’erreur pour gérer les temps d’indisponibilité (par exemple, les défaillances matérielles ou logicielles) lors de la notion de domaine mis à niveau (par exemple, un hôte ou invité du système d’exploitation correctifs/mises à niveau, mises à niveau de l’application) permet de gérer planifiée des temps d’arrêt. Consultez la [reprise après sinistre et une haute disponibilité pour les Applications Azure](http://msdn.microsoft.com/library/dn251004.aspx) pour le rôle de panne et mise à niveau des domaines à la réalisation de haute disponibilité.

![Déploiement de zone unique](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figure 1 : Un seul déploiement de région

Notez qu’au moment de la rédaction de ce document, Azure n’autorise pas le mappage explicit d’un groupe d’ordinateurs virtuels à un domaine d’erreur spécifiques ; Par conséquent, même avec le modèle de déploiement illustré à la Figure 1, il est statistiquement probable que tous les ordinateurs virtuels peuvent être mappés à deux domaines d’erreur au lieu de quatre.

**Thrift trafic d’équilibrage de la charge :** Les bibliothèques clientes Thrift dans le serveur web de se connectent au cluster via un équilibreur de charge interne. Ceci requiert le processus d’ajout de l’équilibreur de charge interne vers le sous-réseau de « données » (voir Figure 1) dans le contexte du service cloud qui héberge le cluster Cassandra. Une fois l’équilibreur de charge interne est défini, chaque nœud requiert le point de terminaison d’équilibrage de la charge pour être ajoutés avec les annotations d’un ensemble d’équilibrage de la charge avec nom d’équilibreur de charge préalablement définis. Pour plus d’informations, voir [l’Équilibrage de charge interne Azure ](../load-balancer/load-balancer-internal-overview.md).

**Graines de cluster :** Il est important de sélectionner la plupart des nœuds hautement disponibles pour les graines comme les nouveaux nœuds va communiquer avec les nœuds des semences à la découverte de la topologie du cluster. Un nœud à partir de chaque ensemble de disponibilité est désigné en tant que nœuds de semences pour éviter un point unique de défaillance.

**Facteur de réplication et le niveau de cohérence :** Génération de données et haute disponibilité durabilité de Cassandra se caractérise par le facteur de réplication (RF - nombre de copies de chaque ligne stockée sur le cluster) et le niveau de cohérence (nombre de réplicas soient en lecture/écriture avant de retourner le résultat à l’appelant). Facteur de réplication est spécifié lors de la création de KEYSPACE (semblable à une base de données relationnelle), alors que le niveau de cohérence est spécifié lors de l’exécution de la requête CRUD. Consultez la documentation de Cassandra à la [configuration de la cohérence](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) pour plus de détails de la cohérence et de la formule pour le calcul du quorum.

Cassandra prend en charge deux types de modèles de l’intégrité des données, la cohérence et la cohérence éventuelle ; le facteur de la réplication et le niveau de cohérence ensemble détermine si les données seront cohérentes dès qu’une opération d’écriture terminée, ou il sera finalement cohérente. Par exemple, spécifiant le QUORUM, comme le niveau de cohérence sera toujours garantit la cohérence de données tout en n’importe quel niveau de cohérence, ci-dessous le nombre de répliques à écrire que nécessaire pour atteindre QUORUM (par exemple une) donne un résultat qui est finalement cohérent.

Le cluster à 8 nœuds présentée ci-dessus, avec un facteur de réplication de 3 et QUORUM (2 nœuds lire ou écrits la cohérence) en lecture/écriture niveau de cohérence, peut surmonter la perte théorique au maximum 1 nœud par le groupe de réplication avant le démarrage de l’application constaté que l’échec. Cela suppose que tous les espaces de clé ont bien équilibrées en lecture-écriture demandes.  Les paramètres que nous utiliserons pour le cluster déployé sont les suivantes :

Configuration de cluster de Cassandra de zone unique :

| Paramètre de cluster | Valeur | Remarques |
| ----------------- | ----- | ------- |
| Nombre de nœuds (N) | 8   | Nombre total de nœuds dans le cluster |
| Facteur de réplication (RF) | 3 | Nombre de duplications d’une ligne donnée |
| Niveau de cohérence (écriture) | QUORUM[(RF/2) +1) = 2] le résultat de la formule est arrondi vers le bas | Écrit au plus 2 réplicas avant que la réponse est envoyée à l’appelant ; 3e réplica est écrit de manière cohérente par la suite. |
| Niveau de cohérence (lecture) | QUORUM [(RF/2) + 1 = 2] le résultat de la formule est arrondi vers le bas | Lit les 2 réplicas avant d’envoyer la réponse à l’appelant. |
| Stratégie de réplication | NetworkTopologyStrategy voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation Cassandra pour plus d’informations | Comprend la topologie de déploiement et place des réplicas sur les nœuds afin que tous les réplicas ne finissent dans le même rack |
| Snitch | GossipingPropertyFileSnitch voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation Cassandra pour plus d’informations | NetworkTopologyStrategy utilise un concept de snitch de comprendre la topologie. GossipingPropertyFileSnitch offre un meilleur contrôle dans le mappage de chaque nœud pour rack et centre de données. Le cluster utilise puis partagez les derniers ragots pour propager ces informations. Il est beaucoup plus simple dans les paramètres IP dynamiques par rapport à PropertyFileSnitch |


**Des considérations azure pour Cassandra Cluster :** Capacité des ordinateurs virtuels de Microsoft Azure utilise le stockage Blob de Azure pour la persistance du disque ; Stockage Azure enregistre 3 réplicas de chaque disque pour une durabilité élevée. Cela signifie que chaque ligne de données insérées dans une table Cassandra est déjà stocké dans 3 réplicas et, par conséquent, la cohérence des données est déjà prise en charge même si le facteur de réplication (RF) est 1. Le problème principal avec facteur de réplication 1 est que l’application sera immobilisée même si un seul nœud Cassandra échoue. Toutefois, si un nœud est en panne pour résoudre les problèmes (par exemple, matériel, défaillances du logiciel système) reconnus par le contrôleur de tissu Azure, il met en service un nouveau nœud à sa place en utilisant les mêmes lecteurs de stockage. Mise en service d’un nouveau nœud pour remplacer l’ancienne peut prendre quelques minutes.  De la même façon pour les activités de maintenance planifiée comme des modifications du système d’exploitation invité, Cassandra mises à niveau et les modifications d’application Azure Fabric Controller effectue le déploiement de mises à niveau des nœuds dans le cluster.  Mises à niveau propagées peuvent également prendre quelques nœuds à la fois et, par conséquent, le cluster peut rencontrer des temps d’arrêt brève pour quelques partitions. Toutefois, les données ne soient pas perdues en raison de la redondance intégrée de stockage Azure.  

Pour les systèmes déployés sur Azure ne nécessitant pas de haute disponibilité (par exemple, autour de 99,9 qui est l’équivalent de 8,76 heures par an ; pour plus d’informations, reportez-vous à la section [Haute disponibilité](http://en.wikipedia.org/wiki/High_availability) ), vous pourrez exécuter avec RF = 1 et le niveau de cohérence = 1.  Pour les applications avec les exigences de haute disponibilité, RF = 3 et le niveau de cohérence = QUORUM tolérées par le temps d’indisponibilité de l’un des nœuds de réplicas. RF = 1 dans traditionnel déploiements (par exemple, versions sur site) ne peut être utilisés en raison de la perte éventuelle de données dues à des problèmes tels que les défaillances de disque.   

## <a name="multi-region-deployment"></a>Déploiement de plusieurs région
Modèle de réplication et de la cohérence centre-bases de données de Cassandra décrit ci-dessus permet d’avec le déploiement de plusieurs région de la zone sans la nécessité pour les outils externes. Ceci est tout à fait différente des bases de données relationnelles traditionnelles où le programme d’installation de la mise en miroir de base de données pour les écritures de maîtres multiples peut être très complexe. Cassandra dans une région de multiples configurée peut aider les scénarios d’utilisation, y compris les éléments suivants :

**Déploiement basée sur la proximité :** Applications mutualisées, avec Effacer mappage des utilisateurs de clients-à-la région peut être bénéficié par des latences de faible du cluster multizone. Par exemple, une formation de gestion systèmes pour les établissements d’enseignement peuvent déployer un cluster distribué dans les régions US est et Ouest pour servir les campus respectifs pour transactionnelle et analytique. Les données peuvent être localement cohérentes à la fois lectures et écritures et peuvent être éventuellement cohérentes dans les deux régions. Il existe d’autres exemples de distribution de média, commerce électronique et quoi que ce soit, et tout ce qui fait Office de base géographique concentré utilisateur est un cas d’utilisation correcte pour ce modèle de déploiement.

**Haute disponibilité :** La redondance est un facteur clé dans la réalisation de haute disponibilité du logiciel et du matériel ; Pour plus d’informations, consultez Création des systèmes de Cloud fiables sur Microsoft Azure. Sur Microsoft Azure, la fiable uniquement atteindre la redondance consiste en déployant un cluster de plusieurs région. Les applications peuvent être déployées en mode actif-actif ou actif-passif et si l’une des régions est en panne, Azure Traffic Manager peuvent rediriger le trafic vers la zone active.  Avec le déploiement de la même région, si la disponibilité est 99,9, un déploiement de deux-région peut atteindre une disponibilité de 99,9999 calculée par la formule : (1-(1-0.999) *(1-0,999))*100) ; consultez le livre ci-dessus pour plus de détails.

**Reprise après sinistre :** Cluster Cassandra de plusieurs région, s’ils sont correctement conçus, peut résister aux pannes de centre de données catastrophique. Si une région est en panne, l’application déployée à d’autres régions peut démarrer desservant les utilisateurs finaux. Comme les autres implémentations de continuité d’activité commerciale, l’application doit être à tolérance de perte des données des données dans le pipeline asynchrone. Toutefois, Cassandra permet la récupération plus intensifs que du temps mis par le processus de récupération de base de données traditionnelle. La figure 2 illustre le modèle de déploiement standard de plusieurs régions avec huit nœuds dans chaque région. Les deux régions sont des images miroir de l’autre pour le même de symétrie ; conceptions du monde réel dépendant du type de charge de travail (par exemple, transactionnel ou analytique) RPO, RTO, la cohérence des données et des exigences de disponibilité.

![Déploiement de la région de multiples](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

La figure 2 : Déploiement de plusieurs régions Cassandra

### <a name="network-integration"></a>Intégration de réseau
Ensembles d’ordinateurs virtuels déployés à des réseaux privés situés sur deux régions communique avec eux à l’aide d’un tunnel VPN. Le tunnel VPN connecte deux passerelles logiciel mis en service au cours du processus de déploiement de réseau. Les deux régions ont la même architecture réseau en sous-réseaux « web » et « données » ; Réseau Azure permet la création de sous-réseaux autant que nécessaire et appliquer des ACL selon les besoins de la sécurité du réseau. Lors de la conception de la topologie de cluster inter latence de communication de centre de données et de l’impact économique de la nécessité de trafic de réseau à prendre en considération.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Cohérence des données pour le déploiement de centres de données multiples
Distribué les déploiements doit être conscient de l’impact de topologie de cluster sur le débit et la disponibilité. Les RF et le niveau de cohérence doivent être sélectionnée de telle façon que le quorum ne dépend pas de la disponibilité de tous les centres de données.
Pour un système qui a besoin de cohérence élevée, un LOCAL_QUORUM pour le niveau de cohérence (pour les lectures et écritures) vérifiera que le locales lectures et les écritures sont satisfaites à partir de nœuds locaux tandis que les données sont répliquées de façon asynchrone pour les centres de données distants.  Le tableau 2 récapitule les informations de configuration pour le cluster de plusieurs région décrites plus loin dans l’écriture des.

**Configuration de cluster Cassandra deux-région**


| Paramètre de cluster | Valeur | Remarques |
| ----------------- | ----- | ------- |
| Nombre de nœuds (N) | 8 + 8 | Nombre total de nœuds dans le cluster |
| Facteur de réplication (RF) | 3 | Nombre de duplications d’une ligne donnée |
| Niveau de cohérence (écriture) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] le résultat de la formule est arrondi vers le bas | 2 nœuds seront écrites dans le premier centre de données de façon synchrone ; les 2 nœuds supplémentaires nécessaires pour le quorum seront écrit de façon asynchrone au centre de données 2. |
| Niveau de cohérence (lecture) | LOCAL_QUORUM ((RF/2) + 1) = 2, le résultat de la formule est arrondi vers le bas | Des requêtes de lecture sont satisfaites à partir d’une seule région ; 2 nœuds sont lus avant que la réponse est envoyée au client. |
| Stratégie de réplication | NetworkTopologyStrategy voir [Réplication des données](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) dans la documentation Cassandra pour plus d’informations | Comprend la topologie de déploiement et place des réplicas sur les nœuds afin que tous les réplicas ne finissent dans le même rack |
| Snitch | GossipingPropertyFileSnitch voir [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) dans la documentation Cassandra pour plus d’informations | NetworkTopologyStrategy utilise un concept de snitch de comprendre la topologie. GossipingPropertyFileSnitch offre un meilleur contrôle dans le mappage de chaque nœud pour rack et centre de données. Le cluster utilise puis partagez les derniers ragots pour propager ces informations. Il est beaucoup plus simple dans les paramètres IP dynamiques par rapport à PropertyFileSnitch |


##<a name="the-software-configuration"></a>LA CONFIGURATION DU LOGICIEL
Au cours du déploiement, les versions logicielles suivantes sont utilisées :

<table>
<tr><th>Logiciel</th><th>Source</th><th>Version</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Étant donné que le téléchargement de JRE nécessite une acceptation manuelle de licence d’Oracle, pour simplifier le déploiement, télécharger tous les logiciels requis sur le bureau pour le téléchargement ultérieurement dans l’image du modèle Ubuntu que nous créerons au préalable le déploiement du cluster.

Téléchargez les logiciels ci-dessus dans un répertoire de téléchargement connus (par exemple, %TEMP%/downloads sur Windows ou ~/Downloads sur la plupart des distributions Linux ou Mac) sur l’ordinateur local.

### <a name="create-ubuntu-vm"></a>CRÉER LA MACHINE VIRTUELLE D’UBUNTU
Dans cette étape du processus, nous allons créer image d’Ubuntu avec les logiciels pré-requis afin que l’image peut être réutilisé pour plusieurs nœuds Cassandra de mise en service.  
####<a name="step-1-generate-ssh-key-pair"></a>ÉTAPE 1 : Générer la paire de clés SSH
Azure a besoin d’une clé publique qui est PEM ou DER codé au moment de la mise en service de X509. Générer une paire de clés publique/privée en suivant les instructions à comment utiliser SSH avec Linux sur Azure. Si vous envisagez d’utiliser putty.exe comme un client SSH sous Windows ou Linux, vous devez convertir le PEM codé la clé privée RSA format PPK à l’aide de puttygen.exe ; Vous trouverez les instructions pour cela dans la page web ci-dessus.

####<a name="step-2-create-ubuntu-template-vm"></a>ÉTAPE 2 : Créer le modèle Ubuntu VM
Pour créer le modèle de machine virtuelle, connectez-vous au portail classique Azure et respecter la séquence suivante : cliquez sur Nouveau calcul, MACHINE virtuelle, de galerie, UBUNTU, Ubuntu Server 14.04 LTS, puis cliquez sur la flèche vers la droite. Pour un didacticiel qui explique comment créer une VM Linux, voir créer un Linux exécutant d’ordinateur virtuel.

Entrez les informations suivantes sur l’écran « configuration de la machine virtuelle » #1 :

<table>
<tr><th>NOM DE CHAMP              </td><td>       VALEUR DE CHAMP               </td><td>         REMARQUES                </td><tr>
<tr><td>DATE DE PUBLICATION DE VERSION    </td><td> Sélectionnez une date à partir de la drow vers le bas</td><td></td><tr>
<tr><td>NOM DE LA MACHINE VIRTUELLE    </td><td> cass-modèle                </td><td> C’est le nom d’hôte de la machine virtuelle </td><tr>
<tr><td>COUCHE                     </td><td> STANDARD                        </td><td> Laissez la valeur par défaut              </td><tr>
<tr><td>TAILLE                     </td><td> A1                              </td><td>Sélectionnez la machine virtuelle en fonction des besoins d’e/s ; Pour cela, laissez la valeur par défaut </td><tr>
<tr><td> NOUVEAU NOM D’UTILISATEUR           </td><td> LocalAdmin                      </td><td> « admin » est un nom réservé dans Ubuntu 12.les xx et après</td><tr>
<tr><td> AUTHENTIFICATION      </td><td> Cliquez sur la case à cocher                 </td><td>Vérifiez si vous souhaitez sécuriser avec un code SSH </td><tr>
<tr><td> CERTIFICAT             </td><td> nom de fichier du certificat de clé publique </td><td> Utiliser la clé publique générée précédemment</td><tr>
<tr><td> Nouveau mot de passe   </td><td> mot de passe fort </td><td> </td><tr>
<tr><td> Confirmer le mot de passe   </td><td> mot de passe fort </td><td></td><tr>
</table>

Entrez les informations suivantes sur l’écran « configuration de la machine virtuelle » #2 :

<table>
<tr><th>NOM DE CHAMP             </th><th> VALEUR DE CHAMP                       </th><th> REMARQUES                                 </th></tr>
<tr><td> SERVICE DE CLOUD  </td><td> Créer un nouveau service de cloud    </td><td>Service cloud est un calcul des ressources conteneur comme des machines virtuelles</td></tr>
<tr><td> NOM DNS DU SERVICE CLOUD </td><td>Ubuntu-template.cloudapp.net   </td><td>Donner un nom d’équilibreur de charge agnostique machine</td></tr>
<tr><td> RÉGION/GROUPE D’AFFINITÉ/RÉSEAU VIRTUEL </td><td>    États-Unis Ouest </td><td> Sélectionnez une région à partir de laquelle le cluster Cassandra accèdent à vos applications web</td></tr>
<tr><td>COMPTE DE STOCKAGE </td><td>   Utiliser par défaut </td><td>Utiliser le compte de stockage par défaut ou un compte de stockage prédéfinis dans une région donnée</td></tr>
<tr><td>ENSEMBLE DE DISPONIBILITÉ </td><td>  Aucun </td><td>  Laissez le champ vide</td></tr>
<tr><td>POINTS DE TERMINAISON   </td><td>Utiliser par défaut </td><td>  Utiliser la configuration SSH par défaut </td></tr>
</table>

Cliquez sur la flèche vers la droite, laissez les valeurs par défaut dans l’écran #3 et cliquez sur le bouton « Rechercher » pour terminer la processus d’approvisionnement de VM. Après quelques minutes, l’ordinateur virtuel avec le nom « ubuntu-template » doit être dans un état « exécution ».

###<a name="install-the-necessary-software"></a>INSTALLER LES LOGICIELS NÉCESSAIRES
####<a name="step-1-upload-tarballs"></a>ÉTAPE 1 : Téléchargement tarballs
À l’aide de scp ou pscp, à copier le logiciel téléchargé précédemment au répertoire ~/downloads en utilisant le format de la commande suivante :

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp serveur-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Répétez la commande ci-dessus pour JRE ainsi que les bits Cassandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>ÉTAPE 2 : Préparer la structure de répertoire et extraire les archives
Connectez-vous à l’ordinateur virtuel et créer la structure de répertoire et extraire le logiciel en tant que super utilisateur en utilisant le script bash ci-dessous :

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Si vous collez ce script dans la fenêtre de vim, veillez à supprimer le retour chariot ('\r ») à l’aide de la commande suivante :

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Étape 3 : Modifier le profil etc.
Ajoutez le code suivant à la fin :

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Étape 4 : Installer JNA pour les systèmes de production
Utilisez la séquence de commandes suivante : la commande suivante installera jna-3.2.7.jar et jna-platform-3.2.7.jar /usr/share.java répertoire sudo apt-get installer java libjna

Créez des liens symboliques dans le répertoire $CASS_HOME/lib afin que le script de démarrage Cassandra trouve ces fichiers JAR :

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Étape 5 : Configuration de cassandra.yaml
Modifier le cassandra.yaml sur chaque ordinateur virtuel afin de refléter la configuration requise par tous les ordinateurs virtuels [nous sera le modifier au cours de la mise en service réel] :

<table>
<tr><th>Nom de champ   </th><th> Valeur  </th><th> Remarques </th></tr>
<tr><td>nom_cluster </td><td>  « CustomerService »   </td><td> Utilisez le nom qui reflète votre déploiement</td></tr>
<tr><td>listen_address  </td><td>[laissez ce champ vide]   </td><td> Supprimer « localhost » </td></tr>
<tr><td>rpc_addres   </td><td>[laissez ce champ vide]  </td><td> Supprimer « localhost » </td></tr>
<tr><td>graines   </td><td>« 10.1.2.4, 10.1.2.6, 10.1.2.8 » </td><td>Liste de toutes les adresses IP qui sont désignés en tant que semences.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Elle est utilisée par la NetworkTopologyStrateg qui permettent de déduire le centre de données et du rack de la machine virtuelle</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Étape 6 : Capturer l’image de machine virtuelle
Ouvrez une session sur l’ordinateur virtuel à l’aide du nom d’hôte (hk-AC-template.cloudapp.net) et la clé privée de SSH créé précédemment. Voir comment utiliser SSH avec Linux sur Azure pour plus d’informations sur la façon de se connecter à l’aide de la commande ssh ou putty.exe.

Exécuter la séquence d’actions pour capturer l’image suivante :
#####<a name="1-deprovision"></a>1. annulation de
Utilisez la commande « sudo waagent – annulation + utilisateur » pour supprimer les informations spécifiques d’instance Machine virtuelle. Voir pour [la Capture d’une Machine virtuelle Linux](virtual-machines-linux-classic-capture-image.md) pour utiliser en tant que modèle plus de détails sur le processus de capture d’image.

#####<a name="2-shutdown-the-vm"></a>2 : arrêt de la machine virtuelle
Assurez-vous que l’ordinateur virtuel est mise en surbrillance et cliquez sur le lien de fermeture de la barre de commande.

#####<a name="3-capture-the-image"></a>3 : capturer l’image
Assurez-vous que l’ordinateur virtuel est mise en surbrillance et cliquez sur le lien de la CAPTURE à partir de la barre de commande. Dans l’écran suivant, donnez un nom d’IMAGE (par exemple, hk-cas-2-08-ub-14-04-2014071), échéant DESCRIPTION de l’IMAGE et cliquez sur « activé » pour terminer le processus de CAPTURE.

Cela prendra quelques secondes et l’image doit être disponible dans la section Mes IMAGES de la galerie d’image. La machine virtuelle de la source sera automatiquement delated une fois que l’image est capturée correctement.

##<a name="single-region-deployment-process"></a>Processus de déploiement de zone unique
**Étape 1 : créer le réseau virtuel** Connectez-vous au portail classique Azure et créez un réseau virtuel avec l’émission d’attributs de la table. Pour obtenir la procédure détaillée de la procédure, reportez-vous à la section [configuration d’un réseau virtuel de Cloud-Only dans le portail classique Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) .      

<table>
<tr><th>Nom de l’attribut VM</th><th>Valeur</th><th>Remarques</th></tr>
<tr><td>Nom</td><td>vnet-cass-ouest-nous</td><td></td></tr>
<tr><td>Région</td><td>États-Unis Ouest</td><td></td></tr>
<tr><td>Serveurs DNS </td><td>Aucun</td><td>Ignorer ce que nous n’utilisons pas d’un serveur DNS</td></tr>
<tr><td>Configurer un VPN site-à-point</td><td>Aucun</td><td> Ignorer</td></tr>
<tr><td>Configurer un VPN de site à site</td><td>Nnone</td><td> Ignorer</td></tr>
<tr><td>Espace d’adressage</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>Adresse IP de début</td><td>10.1.0.0</td><td></td></tr>
<tr><td>ROUTAGE INTER-DOMAINES SANS CLASSE </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Ajoutez les sous-réseaux suivants :

<table>
<tr><th>Nom</th><th>Adresse IP de début</th><th>ROUTAGE INTER-DOMAINES SANS CLASSE</th><th>Remarques</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Sous-réseau pour la batterie de serveurs web</td></tr>
<tr><td>données</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>

Sous-réseaux du Web et données peuvent être protégées par l’intermédiaire de groupes de sécurité de réseau la couverture qui est hors de portée de cet article.  

**Étape 2 : configurer des ordinateurs virtuels** À l’aide de l’image créée précédemment, nous créer les ordinateurs virtuels suivants dans le serveur en nuage « hk-c-svc-ouest » et les lier aux sous-réseaux respectifs, comme indiqué ci-dessous :

<table>
<tr><th>Nom de l’ordinateur    </th><th>Sous-réseau </th><th>Adresse IP </th><th>Ensemble de disponibilité</th><th>Contrôleur de domaine ou un Rack</th><th>Semences ?</th></tr>
<tr><td>HK-c1-ouest-nous   </td><td>données   </td><td>10.1.2.4   </td><td>HK-c-aset-1    </td><td>contrôleur de domaine = WESTUS rack = racks 1 </td><td>Oui</td></tr>
<tr><td>HK-c2-ouest-nous   </td><td>données   </td><td>10.1.2.5   </td><td>HK-c-aset-1    </td><td>contrôleur de domaine = WESTUS rack = racks 1 </td><td>N° </td></tr>
<tr><td>HK-c3-ouest-nous   </td><td>données   </td><td>10.1.2.6   </td><td>HK-c-aset-1    </td><td>DC = rack WESTUS = rack2 </td><td>Oui</td></tr>
<tr><td>HK-c4-ouest-nous   </td><td>données   </td><td>10.1.2.7   </td><td>HK-c-aset-1    </td><td>DC = rack WESTUS = rack2 </td><td>N° </td></tr>
<tr><td>HK-c5-ouest-nous   </td><td>données   </td><td>10.1.2.8   </td><td>HK-c-aset-2    </td><td>DC = rack WESTUS = rack3 </td><td>Oui</td></tr>
<tr><td>HK-c6-ouest-nous   </td><td>données   </td><td>10.1.2.9   </td><td>HK-c-aset-2    </td><td>DC = rack WESTUS = rack3 </td><td>N° </td></tr>
<tr><td>HK-c7-ouest-nous   </td><td>données   </td><td>10.1.2.10  </td><td>HK-c-aset-2    </td><td>DC = rack WESTUS = rack4 </td><td>Oui</td></tr>
<tr><td>HK-c8-ouest-nous   </td><td>données   </td><td>10.1.2.11  </td><td>HK-c-aset-2    </td><td>DC = rack WESTUS = rack4 </td><td>N° </td></tr>
<tr><td>HK-w1-ouest-nous   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
<tr><td>HK-w2-ouest-nous   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
</table>

La création de la liste des ordinateurs virtuels requiert le processus suivant :

1.  Créer un service cloud vide dans une région donnée
2.  Créer un ordinateur virtuel à partir de l’image capturée précédemment et l’associer au réseau virtuel créé précédemment. Répétez cette procédure pour tous les ordinateurs virtuels
3.  Ajout d’un équilibreur de charge interne au service cloud et l’attacher au sous-réseau de « données »
4.  Pour chaque ordinateur virtuel créé précédemment, ajoutez un point de terminaison d’équilibrage de la charge de trafic thrift via un ensemble d’équilibrage de la charge connecté à l’équilibreur de charge d’interne créé précédemment

La procédure ci-dessus peut être exécutée à l’aide d’Azure portal classique ; Utilisez un ordinateur Windows (utilisez une machine virtuelle sur Azure si vous n’avez pas accès à un ordinateur Windows), permet de configurer tous les ordinateurs virtuels de 8 automatiquement le script PowerShell suivant.

**Liste 1 : Script PowerShell pour la mise en service des machines virtuelles**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Étape 3 : Configurer Cassandra sur chaque machine virtuelle**

Connectez-vous à la machine virtuelle et effectuez les opérations suivantes :

* Modifiez $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés en rack et centre de données :

       dc =EASTUS, rack =rack1

* Modifiez cassandra.yaml pour configurer les nœuds de semences comme indiqué ci-dessous :

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Étape 4 : Démarrer les ordinateurs virtuels et testez le cluster**

Connectez-vous à un des nœuds (par exemple, hk-c1-ouest-us) et exécutez la commande suivante pour afficher l’état du cluster :

       nodetool –h 10.1.2.4 –p 7199 status

Vous devez voir l’affichage similaire à celui ci-dessous pour un cluster à 8 nœuds :

<table>
<tr><th>État</th><th>Adresse  </th><th>Charge   </th><th>Jetons </th><th>Propriétaire </th><th>ID de l’hôte  </th><th>Rack</th></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.4   </td><td>87.81 KO   </td><td>256    </td><td>38.0 %  </td><td>GUID (supprimé)</td><td>racks 1</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.5   </td><td>41.08 KO   </td><td>256    </td><td>68.9 %  </td><td>GUID (supprimé)</td><td>racks 1</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.6   </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.7   </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack2</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.8   </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.9   </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack3</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.10  </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack4</td></tr>
<tr><th>NATIONS UNIES  </td><td>10.1.2.11  </td><td>55.29 KO   </td><td>256    </td><td>68.8 %  </td><td>GUID (supprimé)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testez le Cluster seule région
Utilisez les étapes suivantes pour tester le cluster :

1.    À l’aide de l’applet de commande Get-AzureInternalLoadbalancer de commande Powershell, obtenir l’adresse IP de l’équilibreur de charge interne (par exemple :  10.1.2.101). Voici la syntaxe de la commande : Get-AzureLoadbalancer-ServiceName « hk-c-svc-ouest-us » [affiche les détails de l’équilibreur de charge interne ainsi que son adresse IP]
2.  Connectez-vous à la batterie de serveurs web VM (par exemple, hk-w1-ouest-us) à l’aide de Putty ou ssh
3.  Exécuter CASS_HOME/bin/cqlsh $ 10.1.2.101 9160
4.  Pour vérifier si le cluster fonctionne, utilisez les commandes CQL suivantes :

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Vous devriez voir un affichage similaire à celui ci-dessous :

<table>
  <tr><th> customer_id </th><th> Prénom </th><th> nom </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Veuillez noter que le keyspace créé à l’étape 4 utilise SimpleStrategy avec un replication_factor de 3. SimpleStrategy est recommandé pour les données unique centres que NetworkTopologyStrategy pour les données de plusieurs déploiements de centres. Un replication_factor de 3 donnera à tolérance de pannes de nœud.

##<a id="tworegion"> </a>Processus de déploiement de plusieurs régions
Va tirer parti du déploiement de zone unique terminé et répétez le même processus pour l’installation de la deuxième région. La principale différence entre le déploiement uniques et multiples de la région est le programme d’installation de tunnel VPN pour une communication inter-REGIONALE ; Nous débute par l’installation réseau, mettre en service les ordinateurs virtuels et configurer Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Étape 1 : Créer le réseau virtuel à la zone 2
Connectez-vous au portail classique Azure et créez un réseau virtuel avec l’émission d’attributs de la table. Pour obtenir la procédure détaillée de la procédure, reportez-vous à la section [configuration d’un réseau virtuel de Cloud-Only dans le portail classique Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) .      

<table>
<tr><th>Nom de l’attribut    </th><th>Valeur    </th><th>Remarques</th></tr>
<tr><td>Nom    </td><td>vnet-cass-Moyen-Orient-nous</td><td></td></tr>
<tr><td>Région  </td><td>Les États-Unis</td><td></td></tr>
<tr><td>Serveurs DNS     </td><td></td><td>Ignorer ce que nous n’utilisons pas d’un serveur DNS</td></tr>
<tr><td>Configurer un VPN site-à-point</td><td></td><td>     Ignorer</td></tr>
<tr><td>Configurer un VPN de site à site</td><td></td><td>      Ignorer</td></tr>
<tr><td>Espace d’adressage   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Adresse IP de début </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>ROUTAGE INTER-DOMAINES SANS CLASSE    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Ajoutez les sous-réseaux suivants :
<table>
<tr><th>Nom    </th><th>Adresse IP de début    </th><th>ROUTAGE INTER-DOMAINES SANS CLASSE   </th><th>Remarques</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Sous-réseau pour la batterie de serveurs web</td></tr>
<tr><td>données    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Sous-réseau pour les nœuds de base de données</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Étape 2 : Créer des réseaux locaux
Un réseau Local dans la gestion de réseau virtuelle Azure est un espace d’adressage de proxy qui correspond à un site distant, y compris un cloud privé ou une autre région Azure. Cet espace d’adresse proxy est lié à une passerelle distante du réseau de routage vers les destinations réseau droite. Pour les instructions sur l’établissement de la connexion de VNET-à-VNET, voir la rubrique [Configure un VNet à VNet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) .

Créer deux réseaux locaux par les détails suivants :

| Nom de réseau | Adresse de la passerelle VPN | Espace d’adressage | Remarques |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Lors de la création du réseau Local donnent un espace réservé de l’adresse de passerelle. L’adresse de la passerelle réel est remplie lorsque la passerelle est créée. Assurez-vous que l’espace d’adressage correspond exactement à la VNET à distance respectif ; Dans ce cas la VNET est créé dans la région des États-Unis. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Lors de la création du réseau Local donnent un espace réservé de l’adresse de passerelle. L’adresse de la passerelle réel est remplie lorsque la passerelle est créée. Assurez-vous que l’espace d’adressage correspond exactement à la VNET à distance respectif ; Dans ce cas la VNET est créé dans la région Ouest. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Étape 3 : Carte réseau « Local » aux VNETs respectifs
À partir du portail Azure classique, sélectionnez chaque vnet et cliquez sur « Configurer », vérifiez « Se connecter au réseau local », sélectionnez les réseaux Local par les informations suivantes :


| Réseau virtuel | Réseau local |
| --------------- | ------------- |
| HK-vnet-ouest-nous | HK-lnet-Map-to-East-US |
| HK-vnet-Moyen-Orient-nous | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Étape 4 : Créer des passerelles sur VNET1 et VNET2
Du tableau de bord des deux réseaux virtuels, cliquez sur Créer passerelle, qui déclenchera la passerelle VPN, le processus de fourniture. Après quelques minutes, le tableau de bord de chaque réseau virtuel doit afficher l’adresse de la passerelle réelle.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Étape 5 : Mise à jour « Local » des réseaux avec les adresses respectives « passerelle »###
Modifiez les réseaux locaux pour remplacer l’adresse IP de la passerelle espace réservé avec l’adresse IP réelle des passerelles simplement mis en service. Utilisez le mappage suivant :

<table>
<tr><th>Réseau local    </th><th>Passerelle réseau virtuel</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Passerelle de hk-vnet-ouest-nous</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Passerelle de hk-vnet-Moyen-Orient-nous</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Étape 6 : Mettre à jour la clé partagée
Le script Powershell suivant permet de mettre à jour la clé IPSec de chaque passerelle VPN [utiliser la clé saké pour les deux passerelles] : Set-AzureVNetGatewayKey - VNetName hk-vnet-Moyen-Orient-us - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-ouest-us - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Étape 7 : Établir la connexion VNET à VNET
À partir du portail Azure classique, utilisez le menu « Tableau de bord » de deux réseaux virtuels pour établir la connexion de passerelle à passerelle. Dans la barre d’outils du bas, utilisez les éléments de menu « Se connecter ». Après quelques minutes le tableau de bord doit afficher les détails de connexion sous forme graphique.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Étape 8 : Créer les ordinateurs virtuels dans la région #2
Créez l’image Ubuntu, comme indiqué dans déploiement de région #1 en suivant la même procédure ou copie le fichier d’image disque dur virtuel pour le compte de stockage Azure situé dans une région #2 et l’image. Utiliser cette image et créer la liste suivante des machines virtuelles dans un service en nuage hk-c-svc-Moyen-Orient-us :


| Nom de l’ordinateur | Sous-réseau | Adresse IP | Ensemble de disponibilité | Contrôleur de domaine ou un Rack | Semences ? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-Moyen-Orient-nous | données  | 10.2.2.4   | HK-c-aset-1      | contrôleur de domaine = EASTUS rack = racks 1 | Oui |
| HK-c2-Moyen-Orient-nous | données  | 10.2.2.5   | HK-c-aset-1      | contrôleur de domaine = EASTUS rack = racks 1 | N°  |
| HK-c3-Moyen-Orient-nous | données  | 10.2.2.6   | HK-c-aset-1      | DC = rack EASTUS = rack2 | Oui |
| HK-c5-Moyen-Orient-nous | données  | 10.2.2.8   | HK-c-aset-2      | DC = rack EASTUS = rack3 | Oui |
| HK-c6-Moyen-Orient-nous | données  | 10.2.2.9   | HK-c-aset-2      | DC = rack EASTUS = rack3 | N°  |
| HK-c7-Moyen-Orient-nous | données  | 10.2.2.10  | HK-c-aset-2      | DC = rack EASTUS = rack4 | Oui |
| HK-c8-Moyen-Orient-nous | données  | 10.2.2.11  | HK-c-aset-2      | DC = rack EASTUS = rack4 | N°  |
| HK-w1-Moyen-Orient-nous | Web   | 10.2.1.4   | HK-w-aset-1      | N/A                    | N/A |
| HK-w2-Moyen-Orient-nous | Web   | 10.2.1.5   | HK-w-aset-1      | N/A                    | N/A |


Suivez les mêmes instructions en tant que zone #1, mais utiliser l’espace d’adressage 10.2.xxx.xxx.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Étape 9 : Configurer Cassandra sur chaque machine virtuelle
Connectez-vous à la machine virtuelle et effectuez les opérations suivantes :

1. Modifiez $CASS_HOME/conf/cassandra-rackdc.properties pour spécifier les propriétés en rack et centre de données dans le format : dc = EASTUS rack = racks 1
2. Modifier cassandra.yaml pour configurer les nœuds de semences : semences : « 10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10 »

###<a name="step-10-start-cassandra"></a>Étape 10 : Démarrer Cassandra
Connectez-vous à chaque ordinateur virtuel et démarrer Cassandra en arrière-plan en exécutant la commande suivante : $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testez le Cluster multizone
À présent, Cassandra a été déployé à 16 nœuds avec 8 nœuds dans chaque région Azure. Ces nœuds sont dans le même cluster par le nom commun du cluster et la configuration de nœuds de semences. Pour tester le cluster, utilisez le processus suivant :

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Étape 1 : Obtenir l’adresse IP équilibreur de charge interne pour les deux régions à l’aide de PowerShell
- Get-AzureInternalLoadbalancer - ServiceName « hk-c-svc-ouest-nous »
- Get-AzureInternalLoadbalancer - ServiceName « hk-c-svc-Moyen-Orient-nous »  

    Notez les adresses IP (par exemple, ouest - 10.1.2.101, east - 10.2.2.101) s’affiche.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Étape 2 : Exécutez la commande suivante dans la région ouest, après la connexion à hk-w1-ouest-nous
1.    Exécuter CASS_HOME/bin/cqlsh $ 10.1.2.101 9160
2.  Exécutez les commandes CQL suivantes :

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Vous devriez voir un affichage similaire à celui ci-dessous :

| customer_id | Prénom | Nom |
| ----------- | --------- | -------- |
| 1           | John      | Doe      |
| 2           | Jane      | Doe      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Étape 3 : Exécutez la commande suivante dans la région est, après la connexion à hk-w1-Moyen-Orient-us :
1.    Exécuter CASS_HOME/bin/cqlsh $ 10.2.2.101 9160
2.  Exécutez les commandes CQL suivantes :

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Vous devez voir l’affichage même comme pour la région Ouest :


| customer_id | Prénom | Nom   |
|------------ | --------- | ---------- |
| 1           | John      | Doe        |
| 2           | Jane      | Doe        |


Effectuer des insertions plus quelques et voir qu’elles sont répliquées vers l’ouest-nous fait partie du cluster.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cluster de Cassandra de test à partir de Node.js
À l’aide d’un des ordinateurs virtuels Linux créé dans le « web » de niveau précédemment, nous exécutera un script Node.js simple pour lire des données précédemment insérées

**Étape 1 : Installer Node.js et Cassandra Client**

1. Installer Node.js et npm
2. Installez le nœud package « cassandra client » à l’aide de npm
3. Exécutez le script suivant à l’invite du shell qui affiche la chaîne json des données récupérées :

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusion
Microsoft Azure est une plate-forme flexible qui permet l’exécution de Microsoft ainsi que des logiciels open source, comme illustré par cet exercice. Clusters à haute disponibilité Cassandra peuvent être déployées sur un centre de données unique par l’intermédiaire de la répartition des nœuds de cluster sur plusieurs domaines d’erreur. Les clusters Cassandra peuvent également être déployées dans plusieurs régions Azure géographiquement distantes pour les systèmes de preuve de reprise après sinistre. Azure et Cassandra ensemble permet la construction de hautement évolutive, hautement disponible et les services en nuage récupérable après sinistre nécessaire par l’internet aujourd'hui à l’échelle les services.  

##<a name="references"></a>Références##
- [http://Cassandra.Apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
