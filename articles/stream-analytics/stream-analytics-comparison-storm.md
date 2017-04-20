<properties
    pageTitle="Les plates-formes Analytique : comparaison de tempête de Apache à flux Analytique | Microsoft Azure"
    description="Obtenez des conseils de choix d’une plate-forme d’analytique de cloud à l’aide d’une comparaison de la tempête de Apache à flux Analytique. Comprendre les fonctionnalités et les différences."
    keywords="plate-forme d’analytique, les plates-formes analytique, plateforme en nuage analytique, comparaison de tempête"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Aide du choix d’une plate-forme analytique en continu : comparaison de tempête d’Apache pour Azure flux Analytique

Obtenez des conseils de choix d’une plate-forme d’analytique de cloud à l’aide de cette comparaison Apache Storm pour Azure flux Analytique. Comprendre les cas d’usage les atouts d’Analytique de flux par rapport à la vague d’Apache sous la forme d’un service géré sur Azure HDInsight, vous pouvez sélectionner la bonne solution pour votre entreprise.

Les plates-formes d’analytique offrent les avantages d’une solution PaaS, il existe quelques fonctionnalités majeures distinctives qui les différencient. Fonctionnalités ainsi que les limitations de ces services sont répertoriées ci-dessous afin de vous placer sur la solution que vous avez besoin pour atteindre vos objectifs.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Renverser la comparaison à flux Analytique : fonctions générales ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analytique de flux Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tempête de Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ouvrir la Source</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non, Azure flux Analytique est un propriétaire de Microsoft offre.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui, Apache Storm est une technologie d’Apache sous licence.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge de Microsoft</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Oui </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Configuration matérielle requise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’existe aucune configuration matérielle requise. Analytique de flux Azure est un Service d’Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Il n’existe aucune configuration matérielle requise. Tempête de Apache est un Service d’Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unité de niveau supérieure</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avec des clients d’Analytique de flux d’Azure, déployer et surveiller les tâches de diffusion en continu.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tempête de Apache sur HDInsight clients de déployer et de surveiller un cluster entier, ce qui peut héberger plusieurs travaux de tempête ainsi que les autres charges de travail (lot inclus).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prix</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Analytique de flux de données est tarifé par volume de données traitées et le nombre d’unités de transmission en continu (par heure que la tâche est en cours d’exécution) est requis.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Plus les informations de tarification se trouve ici.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Tempête de Apache sur HDInsight, l’unité d’achat est basé sur le cluster et est facturée en fonction du temps, que le cluster est en cours d’exécution, indépendant des travaux de déploiement.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Plus les informations de tarification se trouve ici.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Opérations d’auteur sur chaque plate-forme analytique##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analytique de flux Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tempête de Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctions : SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Oui, une prise en charge SQL facile à utiliser est disponible.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Non, les utilisateurs doivent écrire du code dans Java C# ou utiliser les API de Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités : Opérateurs temporelles</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les agrégats avec fenêtres et jointures temporelles sont pris en charge prête à l’emploi.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Opérateurs temporelles doivent être implémenté par l’utilisateur.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Expérience de développement</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interactif : création et débogage grâce à Azure Portal sur les exemples de données.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Développement, débogage et de surveillance d’expérience sont fourni via le Visual Studio expérience pour les utilisateurs de .NET, tandis que pour Java et d’autres développeurs de langages peuvent utiliser l’IDE de leur choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge du débogage</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Analytique de flux offre le statut de la tâche de base et les journaux des opérations sous la forme d’une méthode de débogage, mais actuellement n’offre pas la souplesse dans quoi/comment bien est inclus dans les journaux d’ie : mode détaillé.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Des journaux détaillés sont disponibles pour le débogage. Il existe deux façons de journaux de surface pour l’utilisateur, via visual Studio ou un utilisateur pouvez RDP dans le cluster pour accéder aux journaux.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge UDF (fonctions définies par l’utilisateur)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’existe actuellement aucune prise en charge UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les UDF peuvent être écrits en C#, Java ou la langue de votre choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensible - code personnalisé</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’y a aucune prise en charge extensible code Analytique de flux de données.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui, il existe de disponibilité à écrire du code personnalisé dans C#, Java ou autres langages pris en charge sur la tempête.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Les sources de données et les sorties##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analytique de flux Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tempête de Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Sources de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Les sources d’entrée pris en charge sont les concentrateurs d’événement Azure et d’Azure BLOB.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Les connecteurs sont disponibles pour les événements concentrateurs, Bus de Service, Kafka, etc.. Les connecteurs non pris en charge peuvent être implémentées via un code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formats d’entrée pris en charge sont Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
N’importe quel format peut-être être implémenté via un code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sorties</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Un travail en continu peut avoir plusieurs sorties. Sorties prises en charge : Événement Azure concentrateurs, stockage Blob Azure, les Tables Azure, SQL Azure DB et PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Prise en charge pour beaucoup de sorties dans une topologie, chaque sortie peut avoir une logique personnalisée pour le traitement en aval. Prêts à l’emploi Storm inclut les connecteurs PowerBI, Azure événement et des concentrateurs, banque de Blob Azure, DocumentDB d’Azure, SQL HBase. Les connecteurs non pris en charge peuvent être implémentées via un code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de codage de données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Analytique de flux nécessite un format de données UTF-8 pour être utilisés.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
N’importe quel format de codage de données peut être implémentée via un code personnalisé.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Gestion et opérations##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analytique de flux Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tempête de Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modèle de déploiement de projet</strong>
                </p>
                <p>
                    - <strong>Azure Portal</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Déploiement est implémentée via Azure Portal, PowerShell et autres API.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment est implémentée via le portail Azure, PowerShell, Visual Studio et autres API.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Surveillance (statistiques, compteurs, etc.).</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Surveillance est implémentée via le portail Azure et autres API.
                </p>
                <p>
L’utilisateur peut également configurer des alertes Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Surveillance est implémentée via l’interface utilisateur de la tempête et d’autres API.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Évolutivité</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nombre d’unités de transmission en continu pour chaque tâche. Traite de chaque unité de diffusion en continu jusqu'à 1 Mo/s. Maximum de 50 unités par défaut. Pour augmenter la limite de l’appel.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nombre de nœuds dans le cluster HDI tempête. Aucune limite sur le nombre de nœuds (limite supérieure définie par votre quota Azure). Pour augmenter la limite de l’appel.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites du traitement des données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les utilisateurs peuvent mettre à l’échelle vers le haut ou vers le bas du nombre d’unités de transmission en continu pour améliorer le traitement de données ou optimiser les coûts.
                </p>
                <p>
Mettre à l’échelle à 1 GBIT/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilisateur peut augmenter ou réduire la taille de cluster afin de répondre aux besoins.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Suspendre/reprendre</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Arrêter et reprendre l’emplacement du dernier arrêté.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Arrêt et reprise de dernière placer arrêté selon le filigrane.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Mise à jour de service et d’infrastructure</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Une correction automatique sans interruption de service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Une correction automatique sans interruption de service.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuité d’activité grâce à un Service hautement disponible avec les contrats SLA garanti</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de 99,9 % </p>
                <p>
Récupération automatique des défaillances </p>
                <p>
Récupération des opérateurs de temporelles avec état est intégrée.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA du cluster tempête de 99,9 %. Tempête de Apache est une plate-forme de diffusion en continu à tolérance de pannes, mais il est s’engage le client à assurer que leur travail en continu s’exécuter sans interruption.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Fonctionnalités avancées##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Analytique de flux Azure</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Tempête de Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrivée tardive et gestion des événements de manière désordonnée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Des règles configurables intégrées à réorganiser, déplacer ou ajuster l’heure de l’événement.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilisateur doit implémenter une logique pour gérer ce scénario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Données de référence</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Données de référence disponibles à partir des objets de BLOB Azure avec une taille maximale de 100 Mo de cache de recherche en mémoire. L’actualisation des données de référence est géré par le service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aucune limite sur la taille des données. Connecteurs disponibles pour HBase, DocumentDB, SQL Server et Azure. Les connecteurs non pris en charge peuvent être implémentées via un code personnalisé.
                </p>
                <p>
L’actualisation des données de référence doit être manipulé par du code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Intégration avec apprentissage automatique</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
En configurant publié Azure Machine cursus proposés en tant que fonctions lors de création de travail ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(aperçu privé)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponible par le biais de boulons de tempête.
                </p>
            </td>
        </tr>
    </tbody>
</table>
