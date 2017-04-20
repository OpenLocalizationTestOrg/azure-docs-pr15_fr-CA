<properties
    pageTitle="Solution d’état de réplication d’annuaire actif dans journal Analytique | Microsoft Azure"
    description="Le pack de solution d’état de la réplication Active Directory régulièrement surveille votre environnement Active Directory pour les échecs de réplication et les résultats des rapports sur votre tableau de bord OMS."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="active-directory-replication-status-solution-in-log-analytics"></a>Solution d’état de réplication d’annuaire actif dans journal Analytique

Active Directory est un composant clé d’un environnement informatique d’entreprise. Pour garantir la haute disponibilité et hautes performances, chaque contrôleur de domaine a sa propre copie de la base de données Active Directory. Contrôleurs de domaine répliquent entre eux afin de propager les modifications dans l’entreprise. Échecs de ce processus de réplication peuvent provoquer de nombreux problèmes au sein de l’entreprise.

Le pack de solution d’état de la réplication AD surveille votre environnement Active Directory pour les échecs de réplication et les résultats des rapports sur votre tableau de bord OMS régulièrement.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Agents doivent être installés sur les contrôleurs de domaine qui sont membres du domaine à évaluer, ou sur les serveurs membres configurés pour envoyer des données de réplication Active Directory à OMS. Pour comprendre comment connecter des ordinateurs Windows à OMS, voir [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md). Si votre contrôleur de domaine fait déjà partie d’un environnement System Center Operations Manager existant que vous souhaitez connecter à OMS, consultez [Se connecter Operations Manager pour l’Analytique de journal](log-analytics-om-agents.md).
- Ajouter la solution de l’état de la réplication Active Directory dans votre espace de travail OMS en utilisant la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.


## <a name="ad-replication-status-data-collection-details"></a>Détails de collection de données de l’état de réplication AD

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées pour l’état de la réplication AD.

| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Oui](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![N°](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![N°](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Oui](./media/log-analytics-ad-replication-status/oms-bullet-green.png)| tous les 5 jours|


## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Éventuellement, permettre à un contrôleur de domaine non envoyer des données d’AD pour OMS
Si vous ne souhaitez pas un de vos contrôleurs de domaine de se connecter directement à l’OMS, vous pouvez utiliser n’importe quel autre ordinateur connecté à l’OMS dans votre domaine pour collecter des données pour le pack de solution d’état de la réplication Active Directory et qu’il envoie les données.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Pour activer un contrôleur de domaine non envoyer des données d’AD pour OMS
1.  Vérifiez que l’ordinateur est un membre du domaine que vous souhaitez surveiller à l’aide de la solution de l’état de la réplication Active Directory.
2.  [L’ordinateur Windows pour OMS de se connecter](log-analytics-windows-agents.md) ou [se connecter à l’aide de votre environnement Operations Manager existant, OMS](log-analytics-om-agents.md), s’il n’est pas déjà connecté.
3.  Sur l’ordinateur, définissez la clé de Registre suivante :
    - Clé : **groupes de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<ManagementGroupName > \Solutions\ADReplication**
    - Valeur : **IsTarge**
    - Données de la valeur : **true**

    >[AZURE.NOTE]Ces modifications ne prendront effet que le redémarrage du service de l’Agent de surveillance de Microsoft (HealthService.exe).

## <a name="understanding-replication-errors"></a>Présentation des erreurs de réplication
Une fois que vous avez des données d’état de la réplication AD envoyées à OMS, vous verrez une fenêtre semblable à la suivante sur le tableau de bord OMS indiquant des erreurs de réplication combien vous avez actuellement.  
![Mosaïque de l’état de la réplication AD](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Les erreurs de réplication critiques** sont celles qui sont au moins à 75 %, la durée de [vie de désactivation](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) de votre forêt Active Directory.

Lorsque vous cliquez sur la mosaïque, vous verrez plus d’informations sur les erreurs.
![Tableau de bord de l’état de réplication AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)


### <a name="destination-server-status-and-source-server-status"></a>Statut de serveur de destination et l’état du serveur Source
Ces blades indiquent l’état des serveurs de destination et source qui rencontrent des erreurs de réplication. Le nombre après le nom de chaque contrôleur de domaine indique le nombre d’erreurs de réplication sur ce contrôleur de domaine.

Les erreurs pour les serveurs de destination et source sont affichées car certains problèmes sont plus faciles à résoudre les problèmes à partir du point de vue du serveur source et d’autres du point de vue du serveur de destination.

Dans cet exemple, vous pouvez voir que de nombreux serveurs de destination ont à peu près le même nombre d’erreurs, mais il existe un serveur source (ADDC35) qui a le plus grand nombre d’erreurs de toutes les autres. Il est probable qu’il existe un problème dans ADDC35 qui provoque son échec envoyer des données à ses partenaires de réplication. Résoudre les problèmes sur ADDC35 probablement résoudre la plupart des erreurs qui s’affichent dans la lame de serveur de destination.

### <a name="replication-error-types"></a>Types d’erreurs de réplication
Cette carte vous donne des informations sur les types d’erreurs détectées au sein de votre entreprise. Chaque erreur possède un code numérique unique et un message qui peut vous aider à déterminer la cause de l’erreur.

L’anneau en haut vous donne une idée de quelles erreurs apparaissent plus et moins fréquemment dans votre environnement.

Cela peut vous indiquer quand plusieurs contrôleurs de domaine rencontrent la même erreur de réplication. Dans ce cas, vous pourrez découvrir identifier une solution sur un contrôleur de domaine, puis répéter sur d’autres contrôleurs de domaine affectés par la même erreur.

### <a name="tombstone-lifetime"></a>Durée de vie de désactivation
La durée de vie de désactivation détermine la durée pendant laquelle un objet supprimé, appelé un objet tombstone, est conservé dans la base de données Active Directory. Lorsqu’un objet supprimé passe la durée de vie de désactivation, un processus de nettoyage supprime automatiquement la base de données Active Directory.

La durée de vie de désactivation par défaut est 180 jours pour les versions les plus récentes de Windows, mais il a 60 jours sur des versions antérieures, et il peut être modifié explicitement par un administrateur Active Directory.

Il est important de savoir si vous êtes confronté à des erreurs de réplication qui sont approchent ou sont au-delà de la durée de vie de désactivation. Si deux contrôleurs de domaine de rencontrer une erreur de réplication qui persiste au-delà de la durée de vie de désactivation, sera désactivée la réplication entre les deux contrôleurs de domaine, même si l’erreur de réplication sous-jacente est fixe.

La lame de durée de vie de désactivation permet d’identifier les endroits où il s’agit de risque de problème. Chaque erreur de la **de 100 % TSL** catégorie représente une partition qui n’a pas répliqué entre son serveur source et de destination pour au moins la durée de vie de désactivation de la forêt.

Dans ce cas, simplement corriger l’erreur de réplication ne sera pas suffire. Au minimum, vous devrez examiner manuellement pour identifier et nettoyage des objets en attente avant vous peuvent redémarrer la réplication. Vous devrez peut-être même mettre hors service d’un contrôleur de domaine.

En plus d’identifier les erreurs de réplication qui ont conservé au-delà de la durée de vie de désactivation, vous souhaiterez également prêter attention aux erreurs relevant des catégories **TSL de 50 à 75 %** ou **TSL de 75 à 100 %** .

Ce sont des erreurs qui sont clairement en attente, non transitoires, afin qu’ils probablement besoin d’intervention de votre part pour résoudre. La bonne nouvelle est qu’ils n’ont pas encore atteint la durée de vie de désactivation. Si vous résoudre ces problèmes rapidement et *avant* qu’ils atteignent la durée de vie de désactivation, la réplication peut redémarrer avec une intervention manuelle minimale.

Comme indiqué plus haut, la mosaïque de tableau de bord pour la solution de l’état de la réplication AD indique le nombre de *critiques* des erreurs de réplication dans votre environnement, ce qui est défini en tant que les erreurs qui sont plus de 75 % de la durée de vie de désactivation (y compris les erreurs qui sont plus 100 % de TSL). Vous efforcer de conserver ce nombre à 0.

>[AZURE.NOTE] Tous les calculs du pourcentage de la durée de vie tombstone sont basés sur la durée de vie de désactivation réelle pour votre forêt Active Directory, afin que vous pouvez faire confiance que ces pourcentages sont exactes, même si vous avez une valeur de durée de vie de désactivation personnalisé défini.

### <a name="ad-replication-status-details"></a>Détails d’état de réplication d’Active Directory
Lorsque vous cliquez sur n’importe quel élément de l’une des listes, vous verrez des informations supplémentaires à l’aide de la recherche dans un journal. Les résultats sont filtrés pour afficher uniquement les erreurs liées à cet élément. Par exemple, si vous cliquez sur le premier contrôleur de domaine répertorié sous **État du serveur de Destination (ADDC02)**, vous verrez les résultats filtrés pour afficher les erreurs avec ce contrôleur de domaine répertorié comme le serveur de destination :

![Erreurs d’état de la réplication AD dans les résultats de la recherche](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

À partir de là, vous pouvez filtrer davantage, modifier la requête de recherche et ainsi de suite. Pour plus d’informations sur l’utilisation de la recherche du journal, voir [recherches de journaux](log-analytics-log-searches.md).

Le champ **HelpLink** affiche l’URL d’une page TechNet avec des détails supplémentaires sur cette erreur spécifique. Vous pouvez copier et coller ce lien dans la fenêtre de votre navigateur pour afficher des informations sur le dépannage et la résolution de l’erreur.

Vous pouvez également cliquer sur **Exporter** pour exporter les résultats vers Excel. Cela vous permet de visualiser les données d’erreur de réplication en aucune manière que vous le souhaitez.

![erreurs d’état réplication AD exportés dans Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>État de la réplication AD Forum aux questions
**Q : la fréquence sont des données d’état de la réplication Active Directory mis à jour ?**
R : les informations sont mises à jour tous les 5 jours.

**Q : y a-t-il un moyen de configurer la fréquence de mise à jour de ces données ?**
A: pas pour l’instant.

**Q : ai-je besoin pour tous mes contrôleurs de domaine ajouter à mon espace de travail de l’OMS pour voir l’état de la réplication ?**
R : non, uniquement un seul contrôleur de domaine doit être ajouté. Si vous avez plusieurs contrôleurs de domaine dans votre espace de travail de l’OMS, toutes les données sont envoyées à OMS.

**Q : je ne veux pas ajouter des contrôleurs de domaine de mon espace de travail de l’OMS. Puis-je toujours utiliser la solution de l’état de la réplication Active Directory ?**
R : Oui. Vous pouvez définir la valeur d’une clé de Registre pour activer cette option. Consultez [pour activer un contrôleur de domaine non envoyer des données de publicité à OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**Q : quel est le nom du processus qui effectue la collecte de données ?**
R : AdvisorAssessment.exe

**Q : combien de temps faut-il pour collecter les données ?**
A: heure de collecte de données de dépend de la taille de l’environnement Active Directory, mais il prend généralement moins de 15 minutes.

**Q : quel type de données est recueillie ?**
A: les informations de réplication sont collectées via LDAP.

**Q : y a-t-il un moyen de configurer les données collectées ?**
A: pas pour l’instant.

**Q : quelles sont les autorisations nécessaires collecter des données ?**
A: autorisations d’utilisateur normal à Active Directory sont généralement suffisantes.

## <a name="troubleshoot-data-collection-problems"></a>Résoudre les problèmes de collecte de données
Afin de recueillir les données, le pack de solution d’état de la réplication Active Directory requiert au moins un contrôleur de domaine d’être connecté à votre espace de travail de l’OMS. Jusqu'à ce que vous faites cela, vous voyez un message indiquant que **les données sont toujours en cours collectées**.

Si vous avez besoin d’une assistance de connexion d’un de vos contrôleurs de domaine, vous pouvez consulter la documentation sur les [ordinateurs Windows de se connecter au journal Analytique](log-analytics-windows-agents.md). Vous pouvez également, si votre contrôleur de domaine est déjà connecté à un environnement System Center Operations Manager existant, vous pouvez afficher la documentation à [Se connecter de System Center Operations Manager pour l’Analytique de journal](log-analytics-om-agents.md).

Si vous ne souhaitez pas connectez un de vos contrôleurs de domaine directement à OMS ou à SCOM, consultez [pour activer un contrôleur de domaine non envoyer des données d’AD pour OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).


## <a name="next-steps"></a>Étapes suivantes

- [Recherche de journal de journal Analytique](log-analytics-log-searches.md) permet d’afficher des données détaillées de statut de la réplication Active Directory.
