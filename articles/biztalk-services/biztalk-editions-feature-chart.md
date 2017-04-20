<properties
    pageTitle="En savoir plus sur les fonctionnalités dans les éditions de BizTalk Services | Microsoft Azure"
    description="Comparer les fonctions des éditions BizTalk Services : libre, développeur, Basic, Standard et Premium. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>Services de BizTalk : Graphique des éditions

Les Services BizTalk Azure propose plusieurs éditions. Cet article permet de déterminer de quelle édition est la mieux adaptée à votre scénario et des besoins.


## <a name="compare-the-editions"></a>Comparaison des éditions

**Libre (aperçu)**

Peut créer et gérer les connexions de hybride. Une connexion hybride constitue un moyen facile pour un site Web d’Azure de se connecter à un système local, comme SQL Server.

**Développeur**

Comprend le traitement des messages hybride connexions, EAI et de l’EDI avec un portail de gestion de partenaire commercial facile à utiliser et la prise en charge pour les schémas EDI courants et traitement de EDI riche sur X12 et AS2. Pouvez créer des scénarios courants de EAI connexion services dans le nuage à l’aide des protocoles HTTP/S, de repos, FTP, WCF et SFTP pour lire et écrire des messages.  Utiliser la connectivité aux systèmes métier de locaux avec cartes SAP, Oracle e-Business, base de données Oracle, Siebel et SQL Server prêtes à l’emploi. Utilisez un environnement centré sur les développeurs avec les outils Visual Studio pour le déploiement et le développement facile. Limité à des fins de développement et de test uniquement avec aucun Service (contrat de niveau).

**Base**

Inclut la plupart des fonctionnalités de développeur des augmentations dans Connexions hybride connexions, des ponts, des accords EDI et Pack d’adaptateur BizTalk EAI. Offre également une disponibilité élevée et que l’option mise à l’échelle avec un contrat de niveau de Service (SLA).

**Standard**

Inclut toutes les fonctionnalités de base des augmentations dans Connexions hybride connexions, des ponts, des accords EDI et Pack d’adaptateur BizTalk EAI. Offre également une disponibilité élevée et que l’option mise à l’échelle avec un contrat de niveau de Service (SLA).

**Premium**

Inclut toutes les fonctions Standard des augmentations dans Connexions hybride connexions, des ponts, des accords EDI et Pack d’adaptateur BizTalk EAI. Inclut également l’archivage, de haute disponibilité et de l’option mise à l’échelle avec un contrat de niveau de Service (SLA).


## <a name="editions-chart"></a>Graphique des éditions
Le tableau suivant répertorie les différences.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Libre (aperçu)</th>
        <th>Développeur</th>
        <th>Base</th>
        <th>Standard</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Prix de départ</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Services BizTalk Azure tarification</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure calculateur de prix</a></td>
</tr>
<tr>
<td><strong>Configuration minimale par défaut</strong></td>
<td>1 unité gratuite</td>
<td>Unité de 1 développement</td>
<td>1 unité de base</td>
<td>1 unité standard</td>
<td>Unité de 1 prime</td>
</tr>
<tr>
<td><strong>Échelle</strong></td>
<td>Aucune échelle</td>
<td>Aucune échelle</td>
<td>Oui, par incréments de 1 unité de base</td>
<td>Oui, par incréments de 1 unité Standard</td>
<td>Oui, par incréments de 1 unité de prime</td>
</tr>
<tr>
<td><strong>Mise à l’échelle maximale.</strong></td>
<td>Aucune échelle</td>
<td>Aucune échelle</td>
<td>Jusqu'à 8 unités</td>
<td>Jusqu'à 8 unités</td>
<td>Jusqu'à 8 unités</td>
</tr>
<tr>
<td><strong>Ponts EAI par unité</strong></td>
<td>Non inclus</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI, AS2</strong>
<br/><br/>
Inclut les accords du module de plateforme sécurisée</td>
<td>Non inclus</td>
<td>Inclus. accords de 10 par unité.</td>
<td>Inclus. accords de 50 par unité.</td>
<td>Inclus. accords de 250 par unité.</td>
<td>Inclus. accords de 1000 par unité.</td>
</tr>
<tr>
<td><strong>Connexions hybride par unité</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Transfert de données de connexion hybride (Go) par unité</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Connexions de Service de l’adaptateur BizTalk pour systèmes de métier sur site</strong></td>
<td>Non inclus</td>
<td>1 connexion</td>
<td>2 connexions</td>
<td>5 connexions</td>
<td>25 connexions</td>
</tr>
<tr>
<td align="left"><strong>Sur les systèmes/protocoles pris en charge :</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Bus de services (SB)</li>
<li>Blob Azure</li>
<li>AUTRES API</li>
</ul>
</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Haute disponibilité</strong>
<br/><br/>
Pour accord de niveau de Service (SLA), consultez <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Tarification d’Azure BizTalk Services</a>.
</td>
<td>Non inclus</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Sauvegarde et restauration</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Suivi</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>L’archivage</strong><br/><br/>
Inclut la Non répudiation de réception (NRR) et le téléchargement des messages suivis</td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Non inclus</td>
<td>Non inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation de code personnalisé</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
<tr>
<td><strong>Utilisation des transformations, notamment XSLT personnalisé</strong></td>
<td>Non inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
<td>Inclus</td>
</tr>
</table>

> [AZURE.NOTE] Pour la résilience contre les défaillances matérielles, haute disponibilité suppose que vous disposiez de plusieurs ordinateurs virtuels au sein d’une seule unité de BizTalk.


## <a name="faqs"></a>Questions fréquentes

#### <a name="what-is-a-biztalk-unit"></a>Ce qui est une unité de BizTalk ?
Une « unité » est le niveau atomique d’un déploiement d’Azure BizTalk Services. Chaque édition est fourni avec une unité qui a mémoire et la capacité de traitement différents. Par exemple, une unité de base a un calcul plus que le développeur, Standard plus compute de base et ainsi de suite. Lorsque vous redimensionnez un BizTalk Service, vous mettre à l’échelle en termes d’unités.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>Quelle est la différence entre les Services BizTalk et Azure VM de BizTalk ?
Les Services BizTalk fournit une architecture de plate-forme-as-a-Service (PaaS) true pour créer des solutions d’intégration dans le nuage. Avec le modèle PaaS, vous concentrer entièrement sur la logique d’application et laisser l’ensemble de la gestion de l’infrastructure Microsoft, notamment :

- Pas nécessaire de gérer ou de correctif des ordinateurs virtuels.
- Microsoft garantit la disponibilité.
- Vous contrôlez échelle sur demande par demande simplement de plus ou moins de capacité via le portail Azure.

BizTalk Server Azure machines virtuelles en fonctionnement offre une architecture de l’Infrastructure-as-a-Service (IaaS). Vous créez des ordinateurs virtuels et les configurez exactement comme votre environnement local, ce qui facilite l’exécution des applications existantes dans le nuage avec aucune modification de code. Avec IaaS, vous devez toujours configurer les ordinateurs virtuels, la gestion des machines virtuelles (par exemple, installation de logiciel et des correctifs de système d’exploitation) et architecture de l’application pour une haute disponibilité.

Si vous cherchez à la construction de nouvelles solutions d’intégration qui réduire votre effort de gestion d’infrastructure, utiliser les Services BizTalk. Si vous cherchant à migrer rapidement vos solutions existantes de BizTalk ou recherchez un environnement à la demande développer et tester des applications de BizTalk Server, vous pouvez utiliser BizTalk Server sur une Machine virtuelle de Azure.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>Quelle est la différence entre le Service de l’adaptateur BizTalk et les connexions hybride ?
Le Service d’adaptateur BizTalk est utilisé par un Service de BizTalk Azure. Le Service d’adaptateur BizTalk utilise le Pack d’adaptateur BizTalk pour se connecter à un système de ligne d’activité (LOB) sur site. Une connexion hybride offre un moyen facile et pratique pour les applications Azure, comme la fonctionnalité d’applications Web dans le Service d’application Azure et d’Azure les Services mobiles, de se connecter à une ressource locale.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>Que signifie « Hybride connexion transfert de données (Go) par unité » ? Est-ce par minute/heure/jour/semaine/mois ? Que se passe-t-il lorsque la limite est atteinte ?

Le coût de la connexion de hybride par unité dépend de l’édition de BizTalk Services. En bref, les coûts varient en fonction de la quantité de données que vous transférez. Par exemple, transfert de données de 10 Go quotidiennement moins onéreuse transfert quotidien de 100 Go. Utilisez la [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/?scenario=full) pour les Services BizTalk pour déterminer les coûts spécifiques. En règle générale, les limites sont appliquées quotidiennement. Si vous dépassez la limite, tout excédent est facturée au taux de 1 $ par Go.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Lorsque je crée un accord dans les Services BizTalk, pourquoi le nombre de ponts a-t-elle deux au lieu d’une ?

Chaque contrat se compose de deux ponts différents : un pont de communication du côté envoi et un pont de communication côté réception.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>Que se passe-t-il lorsque j’atteins la limite de quota sur le nombre de ponts ou accords ?

Vous n’êtes pas en mesure de déployer les tout nouveaux ponts ou créer de tout nouveaux accords. Pour déployer plusieurs, vous devez évoluer à plus d’unités du service BizTalk ou mise à niveau vers une édition supérieure.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Comment migrer d’un niveau de Services BizTalk à un autre ?

L’édition gratuite ne peut pas être migré ou « mise à l’échelle » à une autre couche et ne peut pas être sauvegardé et restauré vers un autre niveau. Si vous avez besoin d’une autre couche, créez un nouveau BizTalk Service à l’aide de la nouvelle couche. Tous les artefacts créés à l’aide de l’édition gratuite, y compris les connexions hybride doivent être recréés dans le nouveau BizTalk Service. 

Pour les autres éditions, utilisez la sauvegarde et restauration pour migrer vos artefacts à partir d’une couche à une autre. Par exemple, sauvegarder vos artefacts dans la couche Standard et ensuite les restaurer dans le niveau de prime. [Les Services BizTalk : sauvegarde et restauration](biztalk-backup-restore.md) décrit les chemins de migration pris en charge et répertorie les artefacts sont sauvegardés. Notez que les connexions hybride ne sont pas sauvegardées. Après la sauvegarde et la restauration vers une nouvelle couche, vous recréez les connexions hybride.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>Est le Service d’adaptateur BizTalk inclus dans le service ? Comment pour recevoir le logiciel ?

Oui, le Service d’adaptateur BizTalk avec le Pack d’adaptateur BizTalk sont inclus avec le SDK des Services BizTalk Azure [Télécharger](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Étapes suivantes

Pour créer des Services de BizTalk Azure dans Azure portal, accédez à [les Services BizTalk : mise en service de l’utilisation du portail Azure](biztalk-provision-services.md). Pour démarrer la création d’applications, consultez [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Ressources supplémentaires
- [Les Services BizTalk : Mise en service de l’utilisation du portail Azure](biztalk-provision-services.md)<br/>
- [Services de BizTalk : Graphique de l’état de mise en service](biztalk-service-state-chart.md)<br/>
- [Services de BizTalk : Onglets de tableau de bord, du moniteur et échelle](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Services de BizTalk : Sauvegarde et restauration](biztalk-backup-restore.md)<br/>
- [Les Services BizTalk : limitation](biztalk-throttling-thresholds.md)<br/>
- [Les Services BizTalk : Nom de l’émetteur et la clé de l’émetteur](biztalk-issuer-name-issuer-key.md)<br/>
- [Comment faire démarrer à l’aide du SDK des Services BizTalk Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
