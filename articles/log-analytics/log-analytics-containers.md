<properties
    pageTitle="Solution de conteneurs dans journal Analytique | Microsoft Azure"
    description="Les conteneurs dans journal Analytique vous aide à afficher et gérer vos hôtes du conteneur Docker dans un seul emplacement."
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



# <a name="containers-preview-solution-log-analytics"></a>Solution de conteneurs (aperçu) journal Analytique

Cet article décrit comment configurer et utiliser la solution de conteneurs dans journal Analytique, ce qui vous permet d’afficher et de gérer vos hôtes du conteneur Docker dans un seul emplacement. Docker est un système de virtualisation de logiciel utilisé pour créer des conteneurs qui automatisent le déploiement de logiciels à leur infrastructure informatique.

Avec la solution, vous pouvez voir quels conteneurs sont en cours d’exécution sur les hôtes de votre conteneur et les images qui sont exécutés dans les conteneurs. Vous pouvez afficher les informations d’audit détaillées affichant les commandes utilisées avec les conteneurs. Et bien, vous pouvez résoudre les conteneurs en affichant et en recherche de journaux centralisés sans avoir à consulter à distance les hôtes de Docker. Vous pouvez rechercher les conteneurs pouvant être bruyants et beaucoup de ressources en excès sur un hôte. Et bien, vous pouvez afficher centralisée de processeur, mémoire, stockage et informations sur l’utilisation et des performances réseau pour les conteneurs.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution.

Ajouter la solution de conteneurs à votre espace de travail de l’OMS à l’aide de la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).

Il existe deux manières d’installer et d’utiliser Docker avec OMS :

- Sur les systèmes d’exploitation Linux pris en charge, installer et exécuter Docker puis installer et configurer l’OMS Agent pour Linux
- Sur CoreOS, installer et exécuter Docker et de configurer le OMSAgent à exécuter à l’intérieur d’un conteneur

Passez en revue les versions de système d’exploitation Docker et Linux pris en charge pour l’hôte du conteneur sur [GitHub](https://github.com/Microsoft/OMS-docker).

>[AZURE.IMPORTANT] Docker doit être en cours d’exécution **avant** d’installer l' [Agent de l’OMS pour Linux](log-analytics-linux-agents.md) sur vos hôtes de conteneur. Si vous avez déjà installé l’agent avant d’installer Docker, vous devrez réinstaller l’Agent de l’OMS pour Linux. Pour plus d’informations sur Docker, consultez le [site Web de Docker](https://www.docker.com).

Vous devez les paramètres suivants sont configurés sur les hôtes de conteneur avant de pouvoir analyser les conteneurs.

## <a name="configure-settings-for-the-linux-container-host"></a>Configurer les paramètres de l’hôte de conteneur de Linux

Une fois que vous avez installé Docker, utilisez les paramètres suivants pour votre hôte de conteneur à configurer l’agent pour une utilisation avec Docker. CoreOS ne prend pas en charge cette méthode de configuration.

### <a name="to-configure-settings-for-the-container-host---systemd-suse-opensuse-centos-7x-rhel-7x-and-ubuntu-15x-and-higher"></a>Pour configurer les paramètres de l’hôte de conteneur - systemd (SUSE, openSUSE, CentOS 7.x, RHEL 7.x et Ubuntu 15.x et ultérieure)

1. Modifiez docker.service pour ajouter le texte suivant :

    ```
    [Service]
    ...
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ...
    ```

2. Ajouter des $DOCKER\_a donné son consentement &quot;démon de = / usr/bin/docker ExecStart&quot; dans le fichier docker.service. À l’aide de l’exemple suivant.

    ```
    [Service]
    Environment="DOCKER_OPTS=--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ExecStart=/usr/bin/docker daemon -H fd:// $DOCKER_OPTS
    ```

3. Redémarrez le service Docker. Par exemple :

    ```
    sudo systemctl restart docker.service
    ```

### <a name="to-configure-settings-for-the-container-host---upstart-ubuntu-14x"></a>Pour configurer les paramètres de l’hôte de conteneur - Upstart (Ubuntu 14.x)

1. Modifier /etc/default/docker et ajoutez le code suivant :

    ```
    DOCKER_OPTS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Enregistrez le fichier, puis redémarrez les services Docker et l’OMS.

    ```
    sudo service docker restart
    ```

### <a name="to-configure-settings-for-the-container-host---amazon-linux"></a>Pour configurer les paramètres de l’hôte de conteneur - Amazon Linux

1. Modifier /etc/sysconfig/docker et ajoutez le code suivant :

    ```
    OPTIONS="--log-driver=fluentd --log-opt fluentd-address=localhost:25225"
    ```

2. Enregistrez le fichier, puis redémarrez le service Docker.

    ```
    sudo service docker restart
    ```

## <a name="configure-settings-for-coreos-containers"></a>Configurer les paramètres pour les conteneurs de CoreOS

Une fois que vous avez installé Docker, utiliser les paramètres suivants pour CoreOS pour exécuter Docker et créer un conteneur. Vous pouvez utiliser toute version prise en charge de Linux, y compris les CoreOS, avec cette méthode de configuration. Vous aurez besoin de votre [ID d’espace de travail OMS et la clé](log-analytics-linux-agents.md).

### <a name="to-use-oms-for-all-containers-with-coreos"></a>Pour utiliser OMS pour tous les conteneurs avec CoreOS

- Démarrer le conteneur OMS que vous souhaitez surveiller. Modifier et utiliser l’exemple suivant.

  ```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25224:25224/udp -p 127.0.0.1:25225:25225 --name="omsagent" --log-driver=none --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-agent-to-one-in-a-container"></a>Commutation d’à l’aide d’un agent installé sur un dans un conteneur

Si vous précédemment utilisé l’agent directement installé et que vous souhaitez utiliser à la place d’un agent qui s’exécute dans un conteneur, vous devez d’abord supprimer OMSAgent. Consultez les [étapes à suivre pour installer l’Agent de l’OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md).

## <a name="containers-data-collection-details"></a>Détails de collecte de données de conteneurs

La solution de conteneurs collecte différentes données statistiques et des journaux de performances à partir des hôtes du conteneur et des conteneurs à l’aide des Agents de l’OMS pour Linux que vous avez activés et de OMSAgent en cours d’exécution dans des conteneurs.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la façon dont les données sont collectées pour les conteneurs.

| plate-forme | Agent d’OMS pour Linux | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Linux|![Oui](./media/log-analytics-containers/oms-bullet-green.png)|![N°](./media/log-analytics-containers/oms-bullet-red.png)|![N°](./media/log-analytics-containers/oms-bullet-red.png)|            ![N°](./media/log-analytics-containers/oms-bullet-red.png)|![N°](./media/log-analytics-containers/oms-bullet-red.png)| toutes les trois minutes|


Le tableau suivant affiche des exemples de types de données collectées par la solution de conteneurs :

| Type de données | Champs |
| --- | --- |
| Performances des hôtes et des conteneurs | Ordinateur, NomObjet, CounterName & #40 ; % temps processeur, Lect Mo, disque écrit Mo, mémoire l’utilisation, recevoir des octets réseau, réseau envoyer octets, processeur utilisation s, réseau & #41 ; contre-valeur, TimeGenerated, CounterPath, SourceSystem |
| Stock de conteneur | TimeGenerated, ordinateur, le nom de conteneur, le code de sortie, ContainerHostname, Image, SourceSystem, ImageTag, ContinerState, EnvironmentVar, commande, StartedTime, CreatedTime, FinishedTime, ImageID, Id_conteneur |
| Stock d’image de conteneur | TimeGenerated, ordinateur, Image, ImageTag, ImageSize, VirtualSize, en cours d’exécution, suspendu, arrêté, a échoué, SourceSystem, ImageID, TotalContainer |
| Journal de conteneur | TimeGenerated, ordinateur, ID de l’image, nom du conteneur, LogEntrySource, LogEntry, SourceSystem, Id_conteneur |
| Journal du service de conteneur | TimeGenerated, ordinateur, TimeOfCommand, Image, commande, SourceSystem, Id_conteneur |

## <a name="monitor-containers"></a>Conteneurs de moniteur

Après avoir ouvert la solution activée dans le portail de l’OMS, vous verrez la mosaïque de **conteneurs** affichant des informations récapitulatives sur vos hôtes de conteneur et les conteneurs en cours d’exécution dans des hôtes.

![Mosaïque de conteneurs](./media/log-analytics-containers/containers-title.png)

La mosaïque affiche une vue d’ensemble des conteneurs combien vous avez dans l’environnement et qu’ils sont a échoué, en cours d’exécution ou arrêté.

### <a name="using-the-containers-dashboard"></a>En utilisant le tableau de bord de conteneurs

Cliquez sur la mosaïque de **conteneurs** . À partir de là, vous verrez des vues organisées par :

- Événements de conteneur
- Erreurs
- État de conteneurs
- Conteneur Image stock
- Performances du processeur et de mémoire

Chaque volet dans le tableau de bord est une représentation visuelle d’une recherche exécutée sur les données collectées.

![Tableau de bord de conteneurs](./media/log-analytics-containers/containers-dash01.png)

![Tableau de bord de conteneurs](./media/log-analytics-containers/containers-dash02.png)

Cliquez sur la lame du **Conteneur État** , dans la zone supérieure, comme illustré ci-dessous.

![État de conteneurs](./media/log-analytics-containers/containers-status.png)

Recherche de journal s’ouvre, affichant des informations sur les hôtes et les conteneurs en cours d’exécution dans les.

![Recherche des journaux pour les conteneurs](./media/log-analytics-containers/containers-log-search.png)

À partir de là, vous pouvez modifier la requête de recherche pour le modifier pour trouver les informations spécifiques que qui vous intéressent. Pour plus d’informations sur les recherches de journaux, voir [recherche de journal de journal Analytique](log-analytics-log-searches.md).

Par exemple, vous pouvez modifier la requête de recherche afin qu’il affiche tous les conteneurs arrêtés au lieu des conteneurs en cours d’exécution par la modification **en cours d’exécution** sur **bloqué** dans la requête de recherche.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Résoudre les problèmes en recherchant un conteneur ayant échoué

OMS marque un conteneur comme étant **en panne** si elle s’est arrêté avec un code de sortie différent de zéro. Vous pouvez voir une vue d’ensemble des erreurs et des pannes dans l’environnement de la lame de **Conteneurs d’a échoué** .

### <a name="to-find-failed-containers"></a>Pour trouver des conteneurs a échoué

1. Cliquez sur la lame **Les événements de conteneur** .  
  ![événements de conteneurs](./media/log-analytics-containers/containers-events.png)
2. Recherche de journal s’ouvre, affichant l’état de conteneurs, semblables à la suivante.  
  ![état de conteneurs](./media/log-analytics-containers/containers-container-state.png)
3. Ensuite, cliquez sur la valeur Échec pour afficher des informations supplémentaires telles que la taille de l’image et le nombre d’images arrêtés et a échoué. Développez **Afficher plus** pour afficher l’ID de l’image.  
  ![conteneurs a échoué](./media/log-analytics-containers/containers-state-failed.png)
4. Ensuite, recherchez le conteneur dans lequel cette image est en cours d’exécution. Tapez la commande suivante dans la requête de recherche.
  `Type=ContainerInventory <ImageID>`Cette option affiche les journaux. Vous pouvez faire défiler pour voir le conteneur a échoué.  
  ![conteneurs a échoué](./media/log-analytics-containers/containers-failed04.png)


## <a name="search-logs-for-container-data"></a>Recherche des journaux de données de conteneur

Lorsque vous effectuez un dépannage une erreur spécifique, il peut vous aider à voir où il se produit dans votre environnement. Les types de journaux suivants vous aideront à créer des requêtes pour renvoyer les informations que vous souhaitez.

- **ContainerInventory** – Utilisez ce type lorsque vous souhaitez que les informations sur l’emplacement du conteneur, quels sont leurs noms et les images qu’ils sont en cours d’exécution.
- **ContainerImageInventory** – Utilisez ce type lorsque vous essayez de rechercher des informations organisées par image et pour afficher des informations d’image comme image ID ou tailles.
- **ContainerLog** – Utilisez ce type lorsque vous souhaitez rechercher des écritures et les informations de journal d’erreur spécifiques.
- **ContainerServiceLog** – Utilisez ce type lorsque vous essayez de rechercher les informations de piste d’audit pour le démon de Docker, Démarrer, arrêter, supprimer ou commandes d’extraction.

### <a name="to-search-logs-for-container-data"></a>Pour rechercher les journaux de données de conteneur

- Choisissez une image dont vous savez qu’il n’a pas pu récemment et rechercher les journaux d’erreurs. Commencez par la recherche d’un nom de conteneur qui exécute cette image avec une recherche **ContainerInventory** . Par exemple, rechercher`Type=ContainerInventory ubuntu Failed`  
    ![Recherche pour les conteneurs d’Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Notez le nom du conteneur en regard de **nom**et rechercher ces fichiers journaux. Dans cet exemple, il est `Type=ContainerLog adoring_meitner`.

**Afficher les informations de performances**

Lorsque vous êtes commence à construire des requêtes, il peut vous aider à voir ce qui est possible tout d’abord. Par exemple, pour afficher toutes les données de performances, essayez une requête large en tapant la requête suivante de la recherche.

```
Type=Perf
```

![performances de conteneurs](./media/log-analytics-containers/containers-perf01.png)



Vous pouvez voir cela sous une forme graphique plus lorsque vous cliquez sur le mot **métrique** dans les résultats.

![performances de conteneurs](./media/log-analytics-containers/containers-perf02.png)



Vous pouvez limiter l’étendue des données de performances que vous voyez à un conteneur spécifique en tapant le nom de ce dernier vers la droite de votre requête.

```
Type=Perf <containerName>
```

Qui affiche la liste des mesures de performance collectées pour un conteneur.

![performances de conteneurs](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Requêtes de recherche de journal d’exemple

Il est souvent utile de créer des requêtes en partant d’un exemple ou les deux, puis les modifier pour les adapter à votre environnement. Comme point de départ, vous pouvez tester la lame **Notables de requêtes** pour vous aider à créer des requêtes plus avancées.

![Requêtes de conteneurs](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>Enregistrement du journal des requêtes de recherche

Enregistrement de requêtes est une fonctionnalité standard de journal Analytique. En les enregistrant, vous disposerez de ceux que vous avez trouvé utile pratique pour une utilisation ultérieure.

Après avoir créé une requête que vous trouvez utile, enregistrez-le en cliquant sur **Favoris** en haut de la page de recherche des journaux. Puis vous pouvez facilement y accéder ultérieurement à partir de la page de **Mon tableau de bord** .

## <a name="next-steps"></a>Étapes suivantes

- [Journaux de recherche](log-analytics-log-searches.md) pour afficher les enregistrements de données de conteneur détaillées.
