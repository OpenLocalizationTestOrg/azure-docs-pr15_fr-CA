<properties
   pageTitle="Étude de cas Azure - Snelstart de la base de données SQL Azure | Microsoft Azure"
   description="Découvrez comment SnelStart utilise de la base de données SQL de développé rapidement de ses services d’entreprise à un débit de 1 000 nouvelles Azure SQL bases de données par mois"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Avec Azure, SnelStart a développé rapidement de ses services d’entreprise à un débit de 1 000 nouvelles Azure SQL bases de données par mois

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart permet de logiciels populaires - et entreprise-gestion financière pour les petites et moyennes entreprises (PME) aux pays-bas. Son de 55 000 clients sont pris en charge par une équipe de 110 employés, y compris le personnel informatique de 35. En déplaçant des logiciels de bureau à un logiciel-comme-a-service (SaaS offrant basé sur Azure), SnelStart fait le plus de services intégrés, automatisation de la gestion à l’aide d’environnement familier dans C# et optimiser les performances et l’évolutivité par aucun des deux entreprises sur ou sous-provisionnement élastique de la base de données à l’aide. À l’aide d’Azure fournit des SnelStart la fluidité du déplacement de clients entre local et le nuage.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Pourquoi SnelStart services étendus à partir du bureau vers le nuage

> « Travail avec Azure signifie que nous pouvons fournir des logiciels plus rapides, rapidement réagissent aux demandes des clients et à faire évoluer les solutions lors de l’augmentent des demandes. »

> — Henry été, architecte logiciel

SnelStart a une entreprise de logiciels depuis des années, à l’aide d’un modèle de développement traditionnel : code de package, expédier et répéter. Au fil du temps, le rythme des changements ont augmenté plus rapidement et plus rapidement. Règlements modifiés fréquemment et aux besoins des clients les plus simples pour traiter les enregistrements financiers et de collaborer avec leurs comptables et le gouvernement à faire face à ces modifications.

« Logiciel de livraison aux clients était cher et limitation, » en fonction de l’été de Henry, architecte logiciel. « Production, d’emballage et les frais d’expédition limité la fréquence à laquelle nous pourrions version de logiciel. Mises à jour de package pour les expéditions périodiques, nous avons dû, mais il était difficile de répondre aux besoins de nos clients. Nous pourrions jamais la garantie que nos clients mis à niveau vers la dernière version du produit. Par conséquent, nous avons dû prennent en charge plusieurs versions, effectue le travail de prise en charge très dur bien. »

Été ajoute, « nous voulions un moyen de mises à jour de programme et de libération à une accélération rythmer, afin que nous aurions pu innover plus rapidement et créer de nouveaux services pour nos clients. Nous voulions également mettre un moyen d’automatiser davantage de processus afin de simplifier les besoins d’administration des affaires de nos clients. »

Pour SnelStart, la solution était d’étendre ses services en devenant un fournisseur SaaS en nuage. La plate-forme de base de données de SQL Azure a aidé à SnelStart y accéder sans encourir les frais de gestion informatiques principaux qui aurait nécessité une solution d’infrastructure-as-a-service (IaaS).

Le modèle de cloud permet également SnelStart résoudre des bogues et offrent de nouvelles fonctionnalités rapidement, sans avoir besoin de télécharger et mettre à niveau des logiciels des clients. En fonction de l’été, la « à l’aide de services cloud Azure nous permet d’agir rapidement lors de la modification des exigences en matière de fournisseurs tiers. Au lieu de fournir une nouvelle version à des milliers de clients, nous pouvons adapter les informations envoyées à partir de notre application de bureau dans de nouveaux formats requis par des tiers. »

##<a name="a-modern-company-with-traditional-roots"></a>Une société moderne avec racines traditionnels

SnelStart est une entreprise moderne, agile et de haute technologie avec racines humble rencontres à 1964, démarrage les fondateurs de la société en tant que fabricant de pièces d’instrument de musique. Au cœur de l’activité du logiciel SnelStart a réellement démarré TREFILAGE dans les années 1980, au cours de la prolifération de l’ordinateur personnel. La société avait besoin d’une meilleure alternative au logiciel de gestion disponible dans le temps, il a donc fallu questions dans ses propres mains. En 1982, la société créée la base de ce qui serait éventuellement devenir SnelStart logiciel de comptabilité. À partir du début, le logiciel a été admirée pour sa simplicité et de la vitesse, de la société a finalement modifié le focus et réinventé dans un éditeur de logiciels.

En 1995, SnelStart a publié sa première application de comptabilité pour Windows. L’application, reposant sur des bases de données Microsoft Access et de Microsoft Visual Basic 1.0, a permis d’atteindre le client base plus de 5 000 utilisateurs.

Aujourd'hui, SnelStart est des principaux fournisseurs d’un de métier (LOB) et l’application d’administration des affaires destiné aux PME/PMI néerlandais et entrepreneurs indépendants. Comme le dit, Carlo Kuip, architecte de l’informatique, « notre objectif est de fournir automation de 100 % des services d’administration des affaires de nos clients. »

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimisation des performances et coût avec des pools élastiques

SnelStart a été des premiers acquéreurs à grande échelle des pools d’élasticité de la base de données. Pools élastiques aident la société à limiter les coûts et de gérer plus efficacement les exigences de performances. En fonction de l’été, la « à l’aide de pools d’élastique de la base de données, que nous pouvons optimiser les performances en fonction des besoins de nos clients, sans remplacer la mise en service. Si nous avions de mise en service en fonction des pics de charge, il serait très coûteux. Au lieu de cela, l’option de partage des ressources entre plusieurs bases de données de faible utilisation permet de créer une solution qui fonctionne bien et est rentable. »

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bases de données SQL Azure aide containerize des données d’isolement et de sécurité 

Une base de données SQL Azure permet de SnelStart déplacer des clients sur site entreprise-administration des données Azure facilement et en toute transparence. La base de données de SQL Azure est un conteneur pratique qui offre une isolation, une limite pour l’authentification, l’autorisation et des capacités de sauvegarde et de restauration simples. Bases de données fournissent un modèle conceptuel bien adapté pour l’administration de l’entreprise. Selon Carlo Kuip, architecte informatique, « éléments à l’intérieur de ce conteneur contiennent des données sensibles sont cruciales pour l’entreprise, et stocker ces éléments dans une base de données isolé les conserve bien protégés. Nous pouvons gérer l’autorisation au niveau de la base de données et même à automatiser la gestion et l’évolutivité horizontale de ces bases de données sans exiger des administrateurs de base de données (DBA) personnel. »

Entrepôt de données SQL Azure joue également un rôle dans l’histoire de sécurité et de gestion de SnelStart en aidant l’entreprise à collecter des données de télémétrie, telles que la détection des intrusions, enregistrement de l’activité utilisateur et la connectivité.

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure supprime la surcharge afin que les développeurs peuvent consacrer plus de temps à valeur ajoutée 

Le modèle de la plateforme Azure supprimé des charges d’infrastructure et activé SnelStart automatiser les déploiements à l’aide des bibliothèques de gestion C#. Comme Kuip indique, « nous avons pu développer nos activités en cours avec une très petite équipe tout en augmentant les options de récupération après sinistre, la vitesse et l’évolutivité pour nos clients. La touche MAJ enfoncée pour libérer des ressources pour vous concentrer sur les fonctionnalités, au lieu de mettre à jour uniquement le code existant pour faire face aux nouvelles réglementations ou des codes taxe et de nouveaux services de développement de services. » Il ajoute, « en automatisant la gestion et à l’aide de l’offre, nous sommes en mesure de fournir plus de valeur pour nos clients, sans avoir à effectuer des investissements importants personnel de SaaS. » Par exemple, en utilisant des pools de base de données Azure et élastique, SnelStart a pu ajouter une variété de nouvelles fonctionnalités, y compris l’intégration de données des clients plus robuste avec des banques, facturation de nouveaux services, vérification en arrière-plan des petites entreprises et services de messagerie.

> « Dans tout les premiers mois de 2016, nous avons étendu nos déploiements de base de données de SQL Azure à partir d’environ 5 500 à plus de 12 000 et nous ajoutons actuellement plus de 1 000 bases de données par mois. »

> — Henry été, architecte logiciel

Gestion de la base de données est plus automatisée à l’aide de la fonctionnalité tâches élastique. Comme Kuip indique, « hautement Merci de la découverte automatique de bases de données sur une instance de [serveur] de la base de données SQL. » Cela permet de SnelStart exécuter des opérations de gestion sur leur clientèle croissante de manière dynamique sans frais supplémentaires.

SnelStart développe également une API qui agit comme un intermédiaire entre les données des clients et des applications créées par les partenaires de logiciels tiers. En tant qu’États de Kuip, « cette API permettra autres fournisseurs ajouter des fonctionnalités de notre logiciel, en supprimant la saisie des données pour les factures et autres documents par exemple. » Les clients n’auront plus à le taper manuellement les factures dans leur logiciel de comptabilité pour petites entreprises ; le logiciel SnelStart échangent les informations nécessaires directement. Ainsi, les clients leurs tâches d’administration des affaires avec l’écosystème des informations qui émerge de transformation numérique dans l’industrie.  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Comment activer les services Azure SaaS pour SnelStart

À l’aide d’Azure, SnelStart peut servir ses clients et leurs comptables de manière plus transparente dans le nuage. Par exemple, les clients et les comptables peuvent partager des informations en accédant directement à client API du SnelStart hébergée sur Azure. Kuip indique, « ces services réutilisables sont disponibles à nos applications de web côté client et qu’ils fournissent l’infrastructure et les fonctions afin de permettre l’accès à l’administration d’entreprise pour les clients et à des logiciels tiers utilisés par nos clients communs. Exemples fournissant des fonctions de configuration des produits, la gestion des règles de pare-feu et la gestion des processus longs comme des sauvegardes. »

> Notre objectif est de fournir l’automation de 100 % des services d’administration des affaires de nos clients. » 

> : Carlo Kuip, architecte informatique

En outre, des services web SnelStart permettent à la fois clients et comptables facilement accéder aux données dans les pools élastiques de base de données de SQL Azure. Ce modèle SaaS, associé à l’élasticité de la base de données et le Gestionnaire de ressources Azure, fournit des SnelStart avec les fonctionnalités d’évolutivité qui complètent chaque déploiement d’Azure. La mise en oeuvre est entièrement automatisée en utilisant les bibliothèques de gestion C#.

![Architecture de SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

La figure 1. À partir de juin 2016, SnelStart dispose de plus de 11 000 bases de données et de plus de 50 pools d’élasticité de la base de données
 
##<a name="simplicity-from-the-cloud"></a>Simplicité du nuage

Depuis le passage à une solution de nuage Azure, SnelStart a été capable de prendre en charge la croissance rapide de client tout en offrant des services et des fonctionnalités innovantes. En fonction de l’été, la « Azure, nous pouvons vous livrer les mises à jour quasi continue pour nos clients, sans développer notre personnel chargé des opérations. Et nous obtenons tous les autres Azure fonctionnalités importantes, telles que l’évolutivité et la reprise après sinistre, fourni dans. »

> « Lorsque nous avons effectivement sur à Redmond... J’ai reçu un appel d’un développeur de revenir aux pays-bas, me téléphoner à propos d’un problème spécifique. Nous avons pu obtenir de Microsoft pour offrir une modification dans leur environnement de production dans les 48 heures pour résoudre notre problème. »

> — Henry été, architecte logiciel

SnelStart apprécie également le partenariat renforcé que leur avez développé avec l’équipe Microsoft Azure SQL DB. En tant qu’États de Kuip, « nous avons des discussions sur les fonctionnalités et augmentation de l’utilisation de la technologie, qui est un élément des deux côtés ».
Le premier objectif de SnelStart est assurer une croissance son client satisfait base. Comme indique, « Sans les limitations techniques et les ressources qu’il nous était comme un éditeur de logiciels, il n’existe aucune limite à la distance sur laquelle nous pouvons développer. »


## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations sur les pools d’élastique Azure de la base de données, voir [pools d’élasticité de la base de données](sql-database-elastic-pool.md).

- Pour plus d’informations sur les rôles Web et worker, consultez [rôles worker](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur le magasin de données SQL Azure, consultez [SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Pour en savoir plus sur SnelStart, reportez-vous à [SnelStart](http://www.snelstart.nl).


