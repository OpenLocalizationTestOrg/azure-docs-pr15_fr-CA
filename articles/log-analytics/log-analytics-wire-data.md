<properties
    pageTitle="Solution de données dans le journal Analytique de fil | Microsoft Azure"
    description="Les données de fil sont données consolidées sur le réseau et les performances des ordinateurs avec des agents d’OMS, y compris les Operations Manager et les agents de connexion Windows. Données de réseau sont associées à vos données pour vous aider à mettre en corrélation des données de journal."
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
    ms.date="08/11/2016"
    ms.author="banders"/>

# <a name="wire-data-solution-in-log-analytics"></a>Fils de solution de données dans le journal Analytique

Les données de fil sont données consolidées sur le réseau et les performances des ordinateurs avec des agents d’OMS, y compris les Operations Manager et les agents de connexion Windows. Données de réseau sont associées à vos données pour vous aider à mettre en corrélation des données de journal. Agents d’OMS installés sur les ordinateurs de vos données de réseau IT infrastructure moniteur envoyées vers et à partir de ces ordinateurs pour les niveaux de réseau 2-3 dans le [modèle OSI](https://en.wikipedia.org/wiki/OSI_model) , y compris les différents protocoles et ports utilisés.

>[AZURE.NOTE] La solution de données de réseau n’est pas actuellement disponible pour être ajoutés aux espaces de travail. Les clients qui disposent déjà de la solution de fil données activée peuvent continuer à utiliser la solution Data de fils.

Par défaut, OMS collecte données enregistrées pour processeur, mémoire, disque et les données de performances réseau à partir de compteurs intégrés à Windows. Réseau et collecte d’autres données s’effectuent dans en temps réel pour chaque agent, y compris des sous-réseaux et des protocoles de niveau application utilisés par l’ordinateur. Vous pouvez ajouter d’autres compteurs de performance dans la page Paramètres sous l’onglet journaux.

Si vous avez utilisé [sFlow](http://www.sflow.org/) ou autres logiciels avec le [protocole de NetFlow de Cisco](http://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), puis les statistiques et les données que vous voyez à partir des données de fil sera familiers.

Parmi les types de requêtes de recherche de journal intégrées :

- Agents qui fournissent des données de fil
- Adresse IP des agents fournissant les données de fil
- Communications sortantes par adresse IP
- Nombre d’octets envoyés par l’application (protocoles)
- Nombre d’octets envoyés par un service d’application
- Octets reçus par différents protocoles
- Nombre total d’octets envoyés et reçus par IP
- Adresses IP qui ont communiqué avec les agents du sous-réseau 10.0.0.0/8
- Latence moyenne pour les connexions qui ont été évalués de façon fiable
- Processus de l’ordinateur qui lancée ou reçu le trafic réseau
- Volume de trafic de réseau pour un processus

Lorsque vous recherchez à l’aide de données de réseau, vous pouvez filtrer et regrouper les données pour afficher des informations sur les agents principaux et les principaux protocoles. Ou vous pouvez rechercher dans quand certains ordinateurs (les adresses MAC/adresses IP) communiquées entre eux, pour la durée et la quantité de données envoyée--en fait, vous permet d’afficher les métadonnées sur le trafic du réseau, qui est basé sur une recherche.

Toutefois, étant donné que vous visualisez les métadonnées, il n’est pas nécessairement utile pour un dépannage approfondi. Données de fil d’OMS ne sont pas une capture complète de données réseau. Par conséquent, il ne vise pour le dépannage de la profondeur au niveau des paquets.
Présente l’avantage de l’agent, par rapport à d’autres méthodes de collection, est que vous n’avez à installer les appliances, reconfigurez vos commutateurs réseau ou préforme de configurations compliquées. Données de fils sont simplement basée sur agent--vous installez l’agent sur un ordinateur, et il surveille son trafic réseau. Un autre avantage est lorsque vous souhaitez contrôler les charges de travail en cours d’exécution dans le fournisseur de nuage ou de fournisseur d’hébergement de service ou de Microsoft Azure, où l’utilisateur ne possède pas de la couche de tissu.

En revanche, vous ne disposez d’une visibilité complète de ce qui se passe sur le réseau si vous n’installez pas les agents sur tous les ordinateurs dans votre infrastructure réseau.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- La solution de fil données obtient des données à partir d’ordinateurs exécutant Windows Server 2012 R2, 8.1 de Windows et les systèmes d’exploitation ultérieurs.
- Microsoft.NET Framework version 4.0 ou ultérieure est requis sur les ordinateurs où vous souhaitez acquérir des données de fils à partir de.
- Ajouter la solution de données de fils pour votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’y a aucune configuration supplémentaire requise.
- Si vous souhaitez afficher les données de fil pour une solution spécifique, vous devez disposer de la solution déjà ajoutée à votre espace de travail de l’OMS.

## <a name="wire-data-data-collection-details"></a>Détails de collecte de données de données de câble

Données de fil collecte des métadonnées sur le trafic réseau en utilisant les agents que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur le mode de collecte de données pour les données de fils.


| plate-forme | Agent direct | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Windows (2012 R2 / 8.1 ou version ultérieure)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![Oui](./media/log-analytics-wire-data/oms-bullet-green.png)|![N°](./media/log-analytics-wire-data/oms-bullet-red.png)|            ![N°](./media/log-analytics-wire-data/oms-bullet-red.png)|![N°](./media/log-analytics-wire-data/oms-bullet-red.png)| toutes les minutes|


## <a name="combining-wire-data-with-other-solution-data"></a>Données de fils de combinaison avec d’autres données de la solution

Les données retournées par les requêtes intégrées ci-dessus peuvent être intéressantes par lui-même. Toutefois, l’utilité des données du réseau est réalisée lorsque vous le combinez avec les informations à partir d’autres solutions d’OMS. Par exemple, vous pouvez utiliser les données d’événements de sécurité collectées par la solution de sécurité et d’Audit et combiner avec les données de fil à rechercher pour les tentatives d’ouverture de session réseau inhabituel pour les processus nommés.  Dans cet exemple, vous utiliseriez les opérateurs IN et DISTINCT pour joindre les points de données dans votre requête de recherche.

Configuration requise : Pour utiliser l’exemple suivant, vous devrez avoir la solution de sécurité et d’Audit que vous installez. Toutefois, vous pouvez utiliser des données à partir d’autres solutions pour combiner des données de fil pour obtenir des résultats similaires.

### <a name="to-combine-wire-data-with-security-events"></a>Pour combiner des données de câble avec des événements de sécurité

1. Dans la page Vue d’ensemble, cliquez sur la mosaïque **WireData** .
2. Dans la liste des **Requêtes communes de WireData**, cliquez sur la **Quantité de trafic réseau (en octets) par un processus** pour afficher la liste de processus retournés.
    ![wiredata des requêtes](./media/log-analytics-wire-data/oms-wiredata-01.png)
3. Si la liste de processus est trop longue pour afficher facilement, vous pouvez modifier la requête de recherche ressemble à :

    ```
    Type WireData | measure count() by ProcessName | where AggregatedValue <40
    ```
    Indiqué dans l’exemple ci-dessous est un processus nommé DancingPigs.exe, qui peut apparaître suspect.
    ![résultats de la recherche wiredata](./media/log-analytics-wire-data/oms-wiredata-02.png)

4. À l’aide des données retournées dans votre liste, cliquez sur un processus nommé. Dans cet exemple, DancingPigs.exe l’utilisateur a cliqué. Les résultats ci-dessous décrivent le type de trafic réseau telles que les communications sortantes sur différents protocoles.
    ![résultats de wiredata un processus nommé](./media/log-analytics-wire-data/oms-wiredata-03.png)

5. Étant donné que la solution d’Audit et de sécurité est installée, vous pouvez sonder dans les événements de sécurité qui ont la même valeur ProcessName en modifiant votre requête de recherche en utilisant les opérateurs de requête de recherche dans et DISTINCT. Vous pouvez faire puis lorsqu’à la fois vos données de fils et les autres journaux de la solution ont des valeurs dans le même format. Modifiez votre requête de recherche ressemble à :

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName}
    ```    

    ![affichage combiné des données de résultats de wiredata](./media/log-analytics-wire-data/oms-wiredata-04.png)
6. Dans les résultats ci-dessus, vous verrez que les informations de compte s’affiche. Maintenant, vous pouvez affiner votre requête de recherche, à savoir la fréquence à laquelle le compte, en affichant les données d’Audit et de sécurité, a été utilisé par le processus avec une requête qui ressemble à :        

    ```
    Type=SecurityEvent ProcessName IN {Type:WireData "DancingPigs.exe" | distinct ProcessName} | measure count() by Account
    ```

    ![résultats de wiredata afficher des données de compte](./media/log-analytics-wire-data/oms-wiredata-05.png)



## <a name="next-steps"></a>Étapes suivantes

- [Journaux de recherche](log-analytics-log-searches.md) pour afficher les enregistrements de recherche de données détaillées des fils.
- Voir [à l’aide des données de fils dans le blog de recherche de journal de Suite de gestion des opérations de publication Daniel](http://blogs.msdn.com/b/dmuscett/archive/2015/09/09/using-wire-data-in-operations-management-suite.aspx) a plus d’informations sur la fréquence à laquelle les données sont collectées et comment vous pouvez modifier les propriétés de collection pour les agents d’Operations Manager.
