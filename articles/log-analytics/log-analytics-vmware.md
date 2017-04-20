<properties
    pageTitle="Solution de surveillance de VMware dans journal Analytique | Microsoft Azure"
    description="Découvrez comment la solution de surveillance de VMware peut aider à gérer les journaux et de surveiller les hôtes ESXi."
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
    ms.date="10/28/2016"
    ms.author="banders"/>

# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solution de VMware contrôle (aperçu) dans le journal Analytique

La solution de surveillance de VMware dans le journal Analytique est une solution qui permet de créer un enregistrement centralisé et le contrôle d’approche pour les grands journaux de VMware. Cet article décrit comment vous pouvez résoudre les problèmes, capturer et gérer les hôtes ESXi dans un emplacement unique à l’aide de la solution. Avec la solution, vous pouvez afficher des données détaillées pour tous vos hôtes ESXi dans un emplacement unique. Vous pouvez voir le nombre d’événement supérieur, l’état et les tendances des hôtes de machine virtuelle et ESXi fournies par les journaux de l’hôte ESXi. Vous pouvez résoudre en affichant et en recherche de journaux ESXi centralisés de l’hôte. Et bien, vous pouvez créer des alertes basées sur des requêtes de recherche de journal.

La solution utilise la fonctionnalité de syslog natif de l’hôte de ESXi pour envoyer des données à une cible virtuelle, qui est l’Agent de l’OMS. Cependant, la solution n’écrire des fichiers dans le journal système de la machine virtuelle de la cible. L’agent de l’OMS ouvre le port 1514 et il écoute. Une fois qu’il reçoit les données, l’agent de l’OMS envoie les données dans l’OMS.

## <a name="installing-and-configuring-the-solution"></a>L’installation et la configuration de la solution

Utilisez les informations suivantes pour installer et configurer la solution.

- Ajouter la solution de surveillance de VMware dans votre espace de travail OMS en utilisant la procédure décrite dans les [solutions d’Analytique de journal ajouter à partir de la galerie de Solutions](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Hôtes VMware ESXi pris en charge
vSphere ESXi hôte 5.5 et 6.0

#### <a name="prepare-a-linux-server"></a>Préparation d’un serveur Linux
Créer un système d’exploitation Linux VM pour recevoir toutes les données syslog des hôtes ESXi. L' [Agent de Linux OMS](log-analytics-linux-agents.md) est le point de regroupement pour toutes les données de journal système hôte ESXi. Vous pouvez utiliser plusieurs hôtes ESXi de transférer des journaux à un seul serveur Linux, comme dans l’exemple suivant.  

   ![flux de syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurer la collection de syslog

1. Permet de paramétrer le transfert de syslog pour VSphere. Pour obtenir des informations détaillées pour aider à configurer la transmission de syslog, reportez-vous à la section [configuration syslog sur ESXi 5.x et 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Accédez à **Configuration de l’hôte de ESXi** > **Software** > **Paramètres avancés** > **Syslog**.
  ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  

2. Dans le champ *Syslog.global.logHost* , ajoutez votre serveur Linux et le numéro de port *1514*. Par exemple, `tcp://hostname:1514` ou`tcp://123.456.789.101:1514`

3. Ouvrir le pare-feu d’hôte ESXi de syslog. **Configuration de l’hôte ESXi** > **Software** > **Profil de sécurité** > **pare-feu** et ouvrir les **Propriétés**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  

4. Vérifiez le vSphere Console pour vérifier que ce journal système est correctement configuré. Confirmer la ce port sur l’hôte ESXI **1514** est configuré.

5. Tester la connectivité entre le serveur Linux et de l’hôte ESXi à l’aide de la `nc` commande sur l’hôte ESXi. Par exemple :

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

6. Téléchargez et installez l’Agent de l’OMS pour Linux sur le serveur Linux. Pour plus d’informations, consultez la [Documentation de l’Agent d’OMS pour Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).

7. Après l’installation de l’Agent de l’OMS pour Linux, accédez au répertoire /etc/opt/microsoft/omsagent/sysconf/omsagent.d et copier le fichier vmware_esxi.conf dans le répertoire de /etc/opt/microsoft/omsagent/conf/omsagent.d et de la modification du propriétaire/groupe et les autorisations du fichier. Par exemple :

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```

8.  Redémarrez l’Agent de l’OMS pour Linux en exécutant `sudo /opt/microsoft/omsagent/bin/service_control restart`.

9. Dans le portail de l’OMS, effectuer une recherche de journal de `Type=VMware_CL`. Lors de l’OMS recueille les données syslog, il conserve le format syslog. Dans le portail, certains champs spécifiques sont capturés, telles que le *nom d’hôte* et *ProcessName*.  

    ![type de](./media/log-analytics-vmware/type.png)  

    Si vos résultats de recherche de journal de vue sont similaires à l’image ci-dessus, vous permet d’utiliser le tableau de bord solution de surveillance de VMware OMS.  

## <a name="vmware-data-collection-details"></a>Détails de collecte de données de VMware

La solution VMware surveillance collecte différentes données statistiques et des journaux de performances à partir des hôtes ESXi en utilisant les Agents de l’OMS pour Linux que vous avez activés.

Le tableau suivant indique les méthodes de collecte de données et d’autres détails sur la manière dont les données sont collectées.

| plate-forme | Agent d’OMS pour Linux | Agent SCOM | Stockage Azure | SCOM requis ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection |
|---|---|---|---|---|---|---|
|Linux|![Oui](./media/log-analytics-vmware/oms-bullet-green.png)|![N°](./media/log-analytics-vmware/oms-bullet-red.png)|![N°](./media/log-analytics-vmware/oms-bullet-red.png)|            ![N°](./media/log-analytics-containers/oms-bullet-red.png)|![N°](./media/log-analytics-vmware/oms-bullet-red.png)| toutes les trois minutes|


Le tableau suivant affiche des exemples de champs de données collectées par la solution de surveillance de VMware :

| nom de champ | Description |
| --- | --- |
| Device_s| Périphériques de stockage VMware |
| ESXIFailure_s | types d’échec |
| EventTime_t | temps lorsque l’événement s’est produit |
| HostName_s | Nom d’hôte ESXi |
| Operation_s | créer la machine virtuelle ou supprimer la machine virtuelle |
| ProcessName_s | nom de l’événement |
| ResourceId_s | nom de l’hôte VMware |
| ResourceLocation_s | VMware |
| ResourceName_s | VMware |
| ResourceType_s | Hyper-V |
| SCSIStatus_s | État SCSI de VMware |
| SyslogMessage_s | Données syslog |
| UserName_s | utilisateur qui a créé ou supprimé des VM |
| VMName_s | Nom de la machine virtuelle |
| Ordinateur | ordinateur hôte |
| TimeGenerated | heure de que génération de données |
| DataCenter_s | Centre de données VMware |
| StorageLatency_s | latence de stockage (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Vue d’ensemble de la solution de surveillance de VMware

La mosaïque de VMware s’affiche dans le portail de l’OMS. Il fournit une vue d’ensemble des échecs. Lorsque vous cliquez sur la mosaïque, vous accédez à un affichage tableau de bord.

![Mosaïque](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Naviguer dans l’affichage du tableau de bord

Dans la vue du tableau de bord de **VMware** , les lames sont organisés par :

- Nombre d’échecs d’état
- Compte d’hôte supérieur par événement
- Nombre d’événements supérieur
- Activités de la Machine virtuelle
- Événements de disque ESXi hôte


![Solution1](./media/log-analytics-vmware/solutionview1-1.png)

![Solution2](./media/log-analytics-vmware/solutionview1-2.png)

Cliquez sur une lame pour ouvrir le volet de recherche de journal Analytique qui affiche des informations détaillées spécifiques de la lame.

À ce stade, vous pouvez modifier la requête de recherche pour l’adapter à un élément spécifique. Pour un didacticiel sur les principes fondamentaux de la recherche de l’OMS, consultez le [didacticiel de recherche de journal OMS.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Rechercher des événements hôte ESXi

Un seul hôte ESXi génère plusieurs journaux, en fonction de leurs processus. La solution de surveillance de VMware centralise les et résume le nombre d’événements. Cette perspective vous aide à comprendre quel hôte ESXi a un volume élevé d’événements et les événements qui se produisent plus fréquemment dans votre environnement.

![événement](./media/log-analytics-vmware/events.png)

Vous pouvez effectuer un zoom avant en cliquant sur un hôte ESXi ou un type d’événement.

Lorsque vous cliquez sur un nom d’hôte ESXi, vous permet d’afficher des informations à partir de cet hôte ESXi. Si vous souhaitez affiner les résultats avec le type d’événement, ajoutez `“ProcessName_s=EVENT TYPE”` dans votre requête de recherche. Vous pouvez sélectionner **ProcessName** dans le filtre de recherche. Qui restreint les informations pour vous.

![effectuer un zoom avant](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Rechercher les activités de machine virtuelle hautes

Une machine virtuelle peut être créée et supprimée sur n’importe quel hôte ESXi. Il est utile pour un administrateur afin d’identifier le nombre de machines virtuelles un hôte ESXi crée. Ce tour, vous permet de comprendre les performances et la planification de la capacité. Suivi des événements d’activité VM est essentiel lors de la gestion de votre environnement.

![effectuer un zoom avant](./media/log-analytics-vmware/vmactivities1.png)

Si vous souhaitez voir ESXi hôte VM création des données supplémentaires, cliquez sur un nom d’hôte ESXi.

![effectuer un zoom avant](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Requêtes de recherche communes

La solution comprend d’autres requêtes utiles qui peuvent vous aider à gérer vos hôtes ESXi, tels qu’espace de stockage haute latence de stockage et Échec du chemin d’accès.

![requêtes](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Enregistrer des requêtes

L’enregistrement des requêtes de recherche est une fonctionnalité standard de OMS et peut vous aider dans toutes les requêtes que vous avez trouvé utile. Après avoir créé une requête que vous trouvez utile, enregistrez-le en cliquant sur **Favoris**. Une requête enregistrée vous permet de réutiliser facilement ultérieurement à partir de la page de [Mon tableau de bord](log-analytics-dashboards.md) dans lequel vous pouvez créer vos propres tableaux de bord personnalisés.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Créer des alertes à partir de requêtes

Une fois que vous avez créé vos requêtes, vous pouvez souhaiter utiliser les requêtes pour vous avertir lorsque des événements spécifiques se produisent. Pour plus d’informations sur la création d’alertes, consultez [alertes dans le journal Analytique](log-analytics-alerts.md) . Pour des exemples d’alertes de requêtes et autres exemples de requête, consultez le blog [à l’aide de VMware de moniteur OMS journal Analytique](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Que dois-je faire héberger sur la ESXi paramètre ? Quel impact aura-t-elle sur mon environnement actuel ?
La solution utilise le Syslog d’hôte ESXi natif mécanisme de transfert. Vous n’avez pas besoin des logiciels Microsoft supplémentaires sur l’hôte ESXi pour capturer les journaux. Il doit avoir un faible impact sur votre environnement existant. Toutefois, vous n’avez pas besoin de définir le transfert de syslog, qui est une fonctionnalité ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Je dois redémarrer mon hôte ESXi ?
N° Ce processus ne nécessite pas le redémarrage de l’ordinateur. Parfois, vSphere n’actualise pas correctement le journal système. Dans ce cas, ouvrez une session sur l’hôte ESXi et recharger le journal système. Encore une fois, vous n’avez pas à redémarrer l’hôte, afin que ce processus n’est pas sans interruption de service pour votre environnement.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-oms"></a>Puis-je augmenter ou diminuer le volume de données de journaux envoyés aux OMS ?
Si, tu peux. Vous pouvez utiliser les paramètres de niveau de journal ESXi hôte dans vSphere. Collection de journaux est basée sur le niveau *info* . Ainsi, si vous souhaitez auditer VM création ou suppression, vous devez conserver le niveau *info* sur Hostd. Pour plus d’informations, consultez la [Base de connaissances de VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-oms-my-log-setting-is-set-to-info"></a>Pourquoi Hostd pas fournit des données pour OMS ? Mon journal est défini sur info.
Il a été un bogue d’hôte ESXi de l’estampille de syslog. Pour plus d’informations, consultez la [Base de connaissances de VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Après avoir appliqué la solution de contournement, Hostd doit fonctionner normalement.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Puis-je avoir plusieurs hôtes ESXi le transfert des données de journal système sur un ordinateur virtuel unique avec omsagent ?
Oui. Vous pouvez avoir plusieurs hôtes de ESXi transfert vers un ordinateur virtuel unique avec omsagent.

### <a name="why-dont-i-see-data-flowing-into-oms"></a>Pourquoi ne voir les données qui transitent dans l’OMS ?

Il peut y avoir plusieurs raisons :

- L’hôte ESXi est poussant pas correctement les données de la machine virtuelle en cours d’exécution omsagent. Pour tester, effectuez les opérations suivantes :
    1. Pour confirmer, connectez-vous à l’hôte ESXi via ssh et exécutez la commande suivante :`nc -z ipaddressofVM 1514`

        Si ce n’est pas réussi, vSphere des paramètres dans la Configuration avancée sont probables pas corriger. Pour plus d’informations sur la façon de configurer l’hôte ESXi pour la transmission de syslog, reportez-vous à la section [configurer syslog collection](#configure-syslog-collection) .

    2. Si la connectivité du port syslog est correcte, mais que vous ne voyez toujours pas toutes les données, puis rechargez le journal système sur l’hôte ESXi à l’aide de ssh pour exécuter la commande suivante :` esxcli system syslog reload`

- La machine virtuelle avec OMS Agent n’est pas définie correctement. Pour tester cela, effectuez les opérations suivantes :
    1. OMS est à l’écoute sur le port 1514 et exécute un push de données dans l’OMS. Pour vérifier qu’elle est ouverte, exécutez la commande suivante :`netstat -a | grep 1514`
    2. Vous devez voir le port `1514/tcp` ouvrir. Si vous ne le faites pas, vérifiez que l’omsagent est installé correctement. Si vous ne voyez pas les informations de port, le port syslog n’est pas ouvert sur l’ordinateur virtuel.
        1. Vérifiez que l’Agent de l’OMS s’exécute à l’aide de `ps -ef | grep oms`. Si elle n’est pas en cours d’exécution, démarrez le processus en exécutant la commande` sudo /opt/microsoft/omsagent/bin/service_control start`
        2. Ouvrir le `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fichier.

            Vérifiez que l’utilisateur approprié et le paramètre de groupe est valide, comme :`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

            Si le fichier n’existe pas ou l’utilisateur et le paramètre de groupe est incorrect, prendre une action corrective en [Préparation d’un serveur Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Étapes suivantes

- Utiliser des [Recherches de journaux](log-analytics-log-searches.md) dans journal Analytique permet d’afficher les données détaillées de l’hôte VMware.
- [Créer vos propres tableaux de bord](log-analytics-dashboards.md) affichant des données de l’hôte VMware.
- [Créer alertes](log-analytics-alerts.md) lorsque surviennent certains événements spécifiques hôte VMware.
