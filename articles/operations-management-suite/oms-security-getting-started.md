<properties
   pageTitle="Mise en route avec les opérations de gestion Suite de Solution de sécurité et d’Audit | Microsoft Azure"
   description="Ce document vous aide à vous familiariser avec les opérations de gestion Suite de sécurité et d’Audit solution pour surveiller votre cloud hybride."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Mise en route avec les opérations de gestion Suite de Solution de sécurité et d’Audit
Ce document vous aide à vous familiariser rapidement avec les possibilités de solution d’Audit et de sécurité de Suite (OMS) de gestion des opérations, en vous guidant tout au long de chaque option.

## <a name="what-is-oms"></a>Nouveautés d’OMS ?
Suite de gestion des opérations Microsoft (OMS) est en nuage solution de gestion informatique de Microsoft qui vous aide à gérer et protéger vos locaux et cloud d’infrastructure. Pour plus d’informations à propos de l’OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Tableau de bord d’Audit et de sécurité de l’OMS

La solution d’Audit et de sécurité de l’OMS fournit une vue complète de votre organisation des défenses informatiques avec les requêtes de recherche intégrée pour les problèmes qui nécessitent votre attention. Le tableau de bord **d’Audit et de sécurité** est l’écran d’accueil pour tous les éléments liés à la sécurité de l’OMS. Il fournit un aperçu de haut niveau de l’état de la sécurité de vos ordinateurs. Il inclut également la possibilité d’afficher tous les événements dans les dernières 24 heures, 7 jours, ou une autre image de temps personnalisé. Pour accéder au tableau de bord **d’Audit et de sécurité** , procédez comme suit :

1. Dans le tableau de bord **Microsoft Operations Management Suite** principal, cliquez sur mosaïque de **paramètres** dans le volet gauche.
2. Dans la lame de **paramètres** , cliquez sur option de **sécurité et Audit** sous **Solutions** .
3. Le tableau de bord **d’Audit et de sécurité** suivant s’affiche :

    ![Tableau de bord d’Audit et de sécurité de l’OMS](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Si vous accédez à ce tableau de bord pour la première fois et que vous n’avez pas les périphériques contrôlés par OMS, les mosaïques pas contiendra des données obtenues à partir de l’agent. Une fois l’agent installé, il peut prendre un certain temps à remplir, donc ce que vous voyez initialement manque peut-être des données qu’ils sont toujours téléchargement vers le nuage.  Dans ce cas, il est normal que certaines mosaïques sans informations tangibles. Pour plus d’informations sur la façon d’installer l’agent de l’OMS dans un système Windows et les [ordinateurs de se connecter de Linux pour OMS](https://technet.microsoft.com/library/mt622052.aspx) pour plus d’informations sur la façon d’effectuer cette tâche dans un système Linux, lisez [les ordinateurs Windows de se connecter directement à l’OMS](https://technet.microsoft.com/library/mt484108.aspx) .

> [AZURE.NOTE] L’agent collecte les informations basées sur les événements en cours sont activés, par exemple le nom d’ordinateur, nom d’utilisateur et d’adresse IP. Toutefois aucun document/fichiers, nom de base de données ou des données privées ne sont collectées.   

Les solutions sont une collection de règles d’acquisition logique, de visualisation et de données qui répondent aux défis des clients. Sécurité et Audit est une solution, d’autres peuvent être ajoutés séparément. Lire l’article [Ajouter solutions](https://technet.microsoft.com/library/mt674635.aspx) pour plus d’informations sur l’ajout d’une nouvelle solution.

Le tableau de bord d’Audit et de sécurité de l’OMS est organisé en quatre catégories principales :

- **Les domaines de sécurité**: Explorer les enregistrements de sécurité au fil du temps dans cette zone, vous pourrez plus accéder aux logiciels malveillants évaluation, mise à jour d’évaluation, la sécurité du réseau, les informations des identités et des accès, les ordinateurs avec des événements de sécurité et rapidement avoir accès au tableau de bord Azure le centre de sécurité.
- **Problèmes**: cette option vous permettra d’identifier rapidement le nombre de problèmes actifs et de la gravité de ces problèmes.
- **Détections (aperçu)**: vous permet d’identifier les modèles d’attaque en visualisant les alertes de sécurité lorsqu’ils prennent place par rapport à vos ressources.
- **Informations sur les menaces**: vous permet d’identifier les modèles d’attaque en visualisant le nombre total de serveurs avec le trafic malveillant IP sortant, le type de menace nuisible et une carte qui montre où sont provenant de ces adresses IP. 
- **Les requêtes de sécurité courantes**: cette option vous fournit une liste de requêtes de sécurité les plus courantes que vous pouvez utiliser pour surveiller votre environnement. Lorsque vous cliquez sur l’une de ces requêtes, il ouvre la lame de **recherche** avec les résultats de cette requête.

> [AZURE.NOTE] Pour plus d’informations sur comment OMS conserve vos données sécurisées, lisez que comment OMS permet de sécuriser vos données.

## <a name="security-domains"></a>Domaines de sécurité

Lors de l’analyse des ressources, il est important de pouvoir accéder rapidement à l’état actuel de votre environnement. Toutefois il est également important de pouvoir suivre les événements précédent qui s’est produite dans le passé qui peut contribuer à une meilleure compréhension de ce qui se passe dans votre environnement à certain point dans le temps. 

> [AZURE.NOTE] rétention des données est selon l’OMS plan de tarification. Pour plus d’informations, visitez le [Microsoft Operations Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) page de tarification.

Scénarios d’enquête post-mortem et de réponse aux incidents bénéficiera directement à partir des résultats disponibles dans la mosaïque de la **Sécurité des enregistrements au fil du temps** .

![Enregistrements de sécurité au fil du temps](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Lorsque vous cliquez sur ce carré, la lame de **recherche** s’ouvre, en affichant les résultats d’une requête pour des **Événements de sécurité** (Type = SecurityEvents) avec les données basées sur les sept derniers jours, comme illustré ci-dessous :

![Enregistrements de sécurité au fil du temps](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

Le résultat de la recherche est divisé en deux volets : le volet de gauche vous donne une répartition du nombre d’événements de sécurité qui ont été trouvés, les ordinateurs dans lesquels ces événements ont été trouvées, le nombre de comptes qui ont été découvertes sur ces ordinateurs et les types d’activités. Le volet de droite vous fournit les total des résultats et une vue chronologique des événements de sécurité avec l’activité de nom et de l’ordinateur. Vous pouvez également cliquer sur **Afficher plus** pour afficher plus de détails sur cet événement, telles que les données d’événement, l’ID d’événement et la source d’événements.

> [AZURE.NOTE] Pour plus d’informations sur la requête de recherche OMS, lire [l’OMS rechercher la référence](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Évaluation des logiciels anti-programmes malveillants

Cette option vous permet d’identifier rapidement les ordinateurs avec une protection insuffisante et les ordinateurs qui ont été compromis par un logiciel malveillant. État d’évaluation de logiciels malveillants et les menaces détectées sur les serveurs analysés sont lus, et puis les données sont envoyées au service OMS dans le nuage pour le traitement. Serveurs avec a détecté les menaces et les serveurs avec une protection insuffisante sont affichés dans le programme malveillant évaluation du tableau de bord, qui est accessible lorsque vous cliquez sur dans la mosaïque de **l’Évaluation des logiciels anti-programmes malveillants** . 

![évaluation des logiciels malveillants](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Comme toute autre ligne mosaïque disponibles dans le tableau de bord OMS, lorsque vous cliquez dessus, la lame de **recherche** s’ouvre avec le résultat de la requête. Pour cette option, si vous cliquez sur l’option **Pas de Reporting** sous **L’état de Protection**, vous avez des résultats de la requête qui affiche cette entrée unique qui contient le nom d’ordinateur et son rang, comme indiqué ci-dessous :

![résultat de la recherche](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *Rank* est un grade offrant pour refléter l’état de la protection (on, off, mis à jour, etc.) et les menaces trouvées. Ayant pour qui un nombre permet de créer des agrégations.

Si vous cliquez sur le nom de l’ordinateur, vous devez l’affichage chronologique de l’état de protection de cet ordinateur. Cela est très utile pour les scénarios dans lesquels vous devez comprendre si le logiciel anti-programme malveillant a été installé qu’une seule fois et à un certain moment, qu'il a été supprimé.   

### <a name="update-assessment"></a>Évaluation de la mise à jour 

Cette option vous permet de déterminer rapidement l’exposition globale à des problèmes de sécurité potentiels et si ou l’importance de ces mises à jour sont pour votre environnement. Solution d’Audit et sécurité de l’OMS assurent uniquement la visualisation de ces mises à jour, les données réelles proviennent de [Solutions de mises à jour système](https://technet.microsoft.com/library/mt484096.aspx), qui est un autre module au sein de l’OMS. Voici un exemple de la mise à jour :

![mises à jour du système](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Pour plus d’informations sur les solutions de mises à jour, lisez la [mise à jour de serveurs avec la solution de mises à jour du système](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Identités et des accès

L’identité doit être le plan de contrôle pour votre entreprise, protéger votre identité doit être votre première priorité. Tandis que dans le passé ont été périmètres autour des organisations et ces périmètres étaient parmi les limites de défensifs principales, aujourd'hui avec plus de données et des applications plus le déplacement vers le nuage l’identité devient le nouveau périmètre. 

> [AZURE.NOTE] actuellement, les données se base uniquement sur les données de connexion (événement ID 4624) les événements de sécurité dans les futures connexions d’Office 365 et données AD Azure sera également incluses.

En surveillant les activités de votre identité, vous serez en mesure de prendre des mesures proactives avant un incident prend place ou des actions réactives pour arrêter une tentative d’attaque. Le tableau de bord **des identités et des accès** vous fournit une vue d’ensemble de l’état de votre identité, y compris le nombre d’échecs de session, le compte d’utilisateur qui ont été utilisés au cours de ces tentatives, les comptes qui ont été verrouillés, les comptes avec modifié ou réinitialiser le mot de passe et actuellement le nombre de comptes qui sont connectés. 

Le tableau de bord suivant s’affiche lorsque vous cliquez sur dans la mosaïque des **identités et des accès** :

![identités et des accès](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

Les informations disponibles dans ce tableau de bord peuvent immédiatement vous aider à identifier une potentielle toute activité suspecte. Par exemple, il y a 338 tente d’ouvrir une session en tant **qu’administrateur** et 100 % de ces tentatives d’échec. Cela peut résulter d’une attaque en force par rapport à ce compte. Si vous cliquez sur ce compte, vous obtiendrez plus d’informations qui peuvent vous aider à déterminer la ressource cible de cette attaque potentielle :

![résultats de la recherche](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

Le rapport détaillé fournit des informations sur cet événement, y compris : l’ordinateur cible, le type d’ouverture de session (dans ce cas de la connexion réseau), l’activité (dans cet événement cas 4625) et une barre de planning complet de chaque tentative. 

### <a name="computers"></a>Ordinateurs

Cette mosaïque peut être utilisée pour accéder à tous les ordinateurs qui ont activement des événements de sécurité. Lorsque vous cliquez dans cette mosaïque vous verrez la liste des ordinateurs avec le nombre d’événements et les événements de sécurité sur chaque ordinateur :

![Ordinateurs](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Vous pouvez poursuivre votre enquête en cliquant sur chaque ordinateur et passez en revue les événements de sécurité qui ont été marqués.

### <a name="azure-security-center"></a>Le centre de sécurité Azure

Cette mosaïque est en fait un raccourci pour accéder aux tableaux de bord Azure le centre de sécurité. Pour plus d’informations sur cette solution, consultez [mise en route avec le centre de sécurité Azure](../security-center/security-center-get-started.md) .

## <a name="notable-issues"></a>Problèmes

L’objectif principal de ce groupe d’options est de fournir un bref aperçu des problèmes que vous avez dans votre environnement, en les classant dans critiques, d’avertissement et d’information. La mosaïque du type de problème actif il s’agit d’une visualisation de ces problèmes, mais il ne vous permet pas d’Explorer plus de détails les concernant, pour que vous devez utiliser la partie inférieure de cette mosaïque qui a le nom du problème (nom), le nombre d’objets a ceci de se produire (nombre) et combien il est important (gravité).

![Problèmes](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Vous pouvez voir que ces problèmes déjà couvertes dans différentes zones du groupe de **Domaines de sécurité** , qui renforce l’objectif de cette vue : visualiser les problèmes les plus importants dans votre environnement à partir d’un emplacement unique.

## <a name="detections-preview"></a>Détections (aperçu)

L’objectif principal de cette option est de permettre informatique pour identifier rapidement les menaces potentielles pesant sur son environnement via et la gravité de cette menace.

![Intel de la menace](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Cette option peut également être utilisée au cours d’une enquête de réponse aux incidents pour effectuer l’évaluation et obtenir plus d’informations sur l’attaque.

> [AZURE.NOTE] Pour plus d’informations sur la façon d’utiliser OMS pour la réponse aux incidents, regardez [comment exploiter le centre de sécurité Azure & Microsoft Operations Management Suite pour une réponse d’Incident](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Informations sur les menaces

La nouvelle section de threat intelligence de la solution de sécurité et d’Audit affiche les modèles d’attaque possible de plusieurs façons : le nombre total de serveurs avec le trafic malveillant IP sortant, le type de menace nuisible et une carte qui montre où sont provenant de ces adresses IP. Vous pouvez interagir avec la carte et cliquez sur les adresses IP pour plus d’informations.

Les clics-infos jaunes sur la carte permet d’indiquer le trafic entrant à partir d’IPs malveillant. Il n’est pas rare pour les serveurs qui sont exposés à internet pour voir le trafic malveillant entrant, mais nous vous recommandons de consulter ces tentatives pour vous assurer qu’aucun d’eux n’a réussi. Ces indicateurs sont basés sur les journaux IIS, les WireData et les journaux du pare-feu Windows.  

![Intel de la menace](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Requêtes de sécurité courantes

La liste des requêtes communes de sécurité disponibles peut être utile pour rapidement accéder aux informations sur la ressource et de le personnaliser en fonction des besoins de votre environnement. Ces requêtes courantes sont les suivantes :

- Toutes les activités de sécurité
- Activités de sécurité sur l’ordinateur « computer01.contoso.com » (Remplacez par le nom de votre ordinateur)
- Activités de sécurité sur l’ordinateur « computer01.contoso.com » pour le compte « Administrateur » (Remplacez par le nom de votre propre ordinateur et compte)
- Ouvrez une session sur l’activité par ordinateur
- Comptes qui s’est arrêté de Microsoft contre les logiciels malveillants sur n’importe quel ordinateur
- Ordinateurs où le processus de logiciel anti-programme malveillant de Microsoft a été arrêté
- Ordinateurs où « hash.exe » a été exécutée (Remplacez par le nom de processus différent)
- Tous les noms de processus qui ont été exécutées
- Ouvrez une session sur l’activité par compte
- Comptes connectés à distance sur l’ordinateur « computer01.contoso.com » (Remplacez par le nom de votre ordinateur)

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez abordé à la solution d’Audit et de sécurité de l’OMS. Pour en savoir plus sur la sécurité de l’OMS, consultez les articles suivants :

- [Vue d’ensemble de la Suite de gestion (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans les opérations Management Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Surveillance des ressources dans des opérations de sécurité Suite de gestion et de la Solution d’Audit](oms-security-monitoring-resources.md)
