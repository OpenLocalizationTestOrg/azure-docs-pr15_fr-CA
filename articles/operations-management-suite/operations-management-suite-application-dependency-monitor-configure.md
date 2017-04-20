<properties
   pageTitle="Configuration de l’Analyseur de dépendance d’Application (ADM) dans la Suite de gestion des opérations (OMS) | Microsoft Azure"
   description="Moniteur de dépendance d’application (ADM) est une solution de Suite de gestion des opérations (OMS) qui détecte les composants de l’application sur les systèmes Windows et Linux et mappe la communication entre services automatiquement.  Cet article fournit des détails pour le déploiement des ADM dans votre environnement et de l’utiliser dans une variété de scénarios."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configuration du moniteur de dépendance d’Application dans Operations Management Suite (OMS)
![Icône de gestion des alerte](media/operations-management-suite-application-dependency-monitor/icon.png) Moniteur de dépendance d’application (ADM) détecte les composants de l’application sur les systèmes Windows et Linux et mappe la communication entre services automatiquement. Il vous permet d’afficher vos serveurs, que vous en pensez – comme les systèmes interconnectés qui offrent des services critiques.  Moniteur de dépendance d’application affiche les connexions entre les serveurs, les processus, et les ports sur n’importe quelle architecture de connexion TCP sans configuration requis autre que l’installation d’un agent.

Cet article décrit les détails de la configuration du moniteur de dépendance d’Application et l’ajout des agents.  Pour plus d’informations sur l’utilisation d’ADM, voir [Moniteur de dépendance d’Application à l’aide de solution dans la Suite Gestion des opérations (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Moniteur de dépendance d’application est en cours d’aperçu privé.  Vous pouvez demander l’accès à l’aperçu privé d’ADM à [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Lors de l’aperçu privé, tous les comptes de l’OMS ont un accès illimité à ADM.  Nœuds d’ADM sont gratuites, mais comme toute autre solution de dosage données Analytique de journal pour les types AdmComputer_CL et AdmProcess_CL.
>
>Après que l’ADM entrée public preview, il sera disponible uniquement pour les clients de Insight & Analytique dans le Plan de tarification OMS gratuites et payantes.  Comptes de couche libre sera limitées à 5 nœuds ADM.  Si vous participez à l’aperçu privé et ne sont pas inscrits dans le Plan de tarification OMS lorsqu’ADM entre la version d’évaluation, ADM sera désactivée à ce moment-là. 



## <a name="connected-sources"></a>Sources connectées
Le tableau suivant décrit les sources connectées qui sont pris en charge par la solution ADM.

| Source connecté | Prise en charge | Description |
|:--|:--|:--|
| [Agents Windows](../log-analytics/log-analytics-windows-agents.md) | Oui | Analyse et collecte des données à partir d’ordinateurs de l’agent Windows.  <br><br>Notez que, en plus de l’agent de l’OMS, les agents Windows requièrent la dépendance de Microsoft Agent.  Consultez la [prise en charge des systèmes d’exploitation](#supported-operating-systems) pour obtenir une liste complète des versions de système d’exploitation. |
| [Agents de Linux](../log-analytics/log-analytics-linux-agents.md) | Oui | Analyse et collecte des données à partir d’ordinateurs agents de Linux.  <br><br>Notez que, en plus de l’agent de l’OMS, les agents de Linux nécessitent la dépendance de Microsoft Agent.  Consultez la [prise en charge des systèmes d’exploitation](#supported-operating-systems) pour obtenir une liste complète des versions de système d’exploitation. |
| [Groupe de gestion SCOM](../log-analytics/log-analytics-om-agents.md) | Oui | Analyse et collecte des données à partir de Windows et Linux agents dans un groupe de gestion de SCOM connecté. <br><br>Une connexion directe à partir de l’ordinateur de l’agent SCOM d’OMS est requise. Envoi de données directement transféré du groupe d’administration dans le référentiel de l’OMS.|
| [Compte de stockage Azure](../log-analytics/log-analytics-azure-storage.md) | N° | ADM collecte des données à partir d’ordinateurs de l’agent, afin qu’il n’y a aucune donnée à collecter à partir du stockage Azure. |

Notez que ADM prend uniquement en charge les plates-formes 64 bits.

Sous Windows, l’Agent de surveillance de Microsoft (MMA) est utilisé par SCOM et OMS pour recueillir et envoyer des données d’analyse.  (Cet agent est appelé Agent SCOM, Agent de l’OMS, MMA ou Agent directe, selon le contexte.)  SCOM et OMS fournissent différents sur les versions de la zone de MMA, mais ces versions peuvent chaque rapport à SCOM, OMS ou les deux.  Sur Linux, l’Agent de l’OMS pour Linux collecte et envoie données à OMS de surveillance.  Vous pouvez utiliser ADM sur des serveurs avec des Agents Direct OMS ou sur des serveurs qui sont joints à l’OMS via les groupes de gestion de SCOM.  Aux fins de cette documentation, nous ferons référence à tous ces agents – sur Linux ou Windows, si connecté à un MG de SCOM ou directement à OMS – « Agent OMS », à moins que le nom de déploiement spécifique de l’agent est nécessaire pour le contexte.

L’agent d’ADM ne transmet pas les données elles-mêmes, et il ne requiert pas les modifications apportées aux pare-feux ou des ports.  Données d’ADM sont toujours transmises par l’Agent de l’OMS pour OMS, soit directement, soit via la passerelle de l’OMS.

![Agents d’ADM](media/operations-management-suite-application-dependency-monitor/agents.png)

Si vous êtes un client SCOM avec un groupe d’administration connecté d’OMS :

- Si vos agents SCOM peuvent accéder à internet pour vous connecter à OMS, aucune configuration supplémentaire n’est nécessaire.  
- Si vos agents SCOM ne peut pas accéder à OMS sur internet, vous devez configurer la passerelle OMS pour travailler avec le SCOM.
  
Si vous utilisez l’Agent Direct OMS, vous devez configurer l’Agent d’OMS de se connecter à OMS ou à votre passerelle OMS.  La passerelle de l’OMS peut être téléchargée à partir de [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Éviter les données en double

Si vous êtes un client SCOM, vous ne devez pas configurer vos agents SCOM de communiquer directement avec l’OMS, ou les données sont signalées deux fois.  Dans ADM, ainsi, les ordinateurs qui apparaît deux fois dans la liste des ordinateurs.

Configuration de l’OMS doit se produire que dans l’un des emplacements suivants :

- Le panneau SCOM Console Operations Management Suite pour des ordinateurs gérés
- Configuration de perspectives opérationnelles Azure dans les propriétés MMA

À l’aide de ces deux configurations avec le même espace de travail de chaque provoquera une duplication des données. À l’aide des espaces de travail différents peut entraîner une configuration en conflit (celui avec la solution ADM activé et l’autre sans) qui peut empêcher des données circulant à ADM complètement.  

Notez que même si la machine elle-même n’est pas spécifiée dans la configuration de la Console SCOM OMS, si un groupe d’Instance comme un « Groupe des Instances Windows Server » est actif, il peut toujours entraîner la configuration OMS réception d’ordinateur via SCOM.



## <a name="management-packs"></a>Packs d’administration
Lorsqu’ADM est activé dans un espace de travail de l’OMS, un 300 Ko Management Pack est envoyé à tous les Microsoft analyse des Agents dans cet espace de travail.  Si vous utilisez des agents SCOM dans un [groupe d’administration connecté](../log-analytics/log-analytics-om-agents.md), le Pack d’administration ADM seront déployé à partir de SCOM.  Si les agents sont connectés directement, ne le MP est remis par l’OMS.

Le panneau de gestion est nommé Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Il est écrit dans *Service State\Management Packs %Programfiles%\Microsoft surveillance Agent\Agent\Health\*.  La source de données utilisée par le pack d’administration est *% programme files%\Microsoft analyse Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuration de
En plus de Windows et Linux ordinateurs disposent d’un agent installé et connecté à l’OMS, le programme d’installation de l’Agent de la dépendance doit être téléchargé à partir de la solution ADM et ensuite installé comme racine ou administrateur sur chaque serveur géré.  Une fois que l’agent ADM est installé sur un serveur de rapports pour OMS, mappages de dépendance ADM apparaîtra dans les 10 minutes.  Si vous rencontrez des problèmes, veuillez envoyer un e-mail [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migration à partir de BlueStripe FactFinder
Moniteur de dépendance d’application sont des technologies BlueStripe dans OMS en phases. FactFinder est toujours pris en charge pour les clients existants, mais n’est plus disponible pour un achat individuel.  Cette version d’évaluation de l’Agent de dépendance ne peut communiquer qu’avec l’OMS.  Si vous êtes un client actuel de FactFinder, Veuillez identifier un ensemble de serveurs de test pour ADM qui ne sont pas gérés par FactFinder. 

### <a name="download-the-dependency-agent"></a>Télécharger l’Agent de la dépendance
En plus de l’Agent de gestion Microsoft (MMA) et d’un Agent de Linux d’OMS qui assurent la connexion entre l’ordinateur et l’OMS, tous les ordinateurs analysés par l’Analyseur de dépendance d’Application doivent avoir installé l’Agent de dépendance.  Sous Linux, l’Agent de l’OMS pour Linux doit être installé avant l’Agent de la dépendance. 

![Mosaïque de moniteur de dépendance d’application](media/operations-management-suite-application-dependency-monitor/tile.png)

Pour télécharger l’Agent de la dépendance, cliquez sur **Configurer la Solution** dans la mosaïque de **Moniteur de dépendance d’Application** à ouvrir la lame de **l’Agent de la dépendance** .  La lame de dépendance Agent comporte des liens pour les fenêtres et les agents de Linux. Cliquez sur le lien approprié pour télécharger chaque agent. Consultez les sections suivantes pour plus d’informations sur l’installation de l’agent sur des systèmes différents.

### <a name="install-the-dependency-agent"></a>Installation de l’Agent de la dépendance

#### <a name="microsoft-windows"></a>Microsoft Windows
Privilèges d’administrateur sont requis pour installer ou désinstaller l’agent.

L’Agent de dépendance est installé sur les ordinateurs Windows avec ADM-Agent-Windows.exe. Si vous exécutez ce programme sans aucune option, elle démarre un Assistant que vous pouvez suivre pour effectuer l’installation de manière interactive.  

Utilisez les étapes suivantes pour installer l’Agent de dépendance sur chaque ordinateur.

1.  Assurez-vous que l’agent de l’OMS est installé en suivant les instructions sur les ordinateurs de se connecter directement à l’OMS.
2.  Téléchargez l’agent Windows et l’exécuter avec la commande suivante.<br>*ADM-Agent-Windows.exe*
3.  Suivez l’Assistant pour installer l’agent.
4.  Si l’Agent de dépendance ne démarre pas, vérifiez les journaux pour les informations d’erreur détaillées. Sur les agents Windows, le répertoire des journaux est *C:\Program Files\BlueStripe\Collector\logs*. 

L’Agent pour Windows de dépendance peut être désinstallé par un administrateur via le panneau de configuration.


#### <a name="linux"></a>Linux
Accès à la racine est nécessaire pour installer ou configurer l’agent.

L’Agent de dépendance est installé sur les ordinateurs avec ADM-Agent-Linux64.bin, un script de shell avec un fichier binaire auto-extractible Linux. Vous pouvez exécuter le fichier avec sh ou ajouter des autorisations d’exécution pour le fichier lui-même.
 
Utilisez les étapes suivantes pour installer l’Agent de dépendance sur chaque ordinateur Linux.

1.  Assurez-vous que l’agent de l’OMS est installé en suivant les instructions à [de collecter et de gérer des données à partir d’ordinateurs de Linux.  Il doit être installé avant l’Agent de dépendance Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installation de l’agent de la dépendance de Linux en tant que racine à l’aide de la commande suivante.<br>*sh ADM-Agent-Linux64.bin*.
3.  Si l’Agent de dépendance ne démarre pas, vérifiez les journaux pour les informations d’erreur détaillées. Sur les agents de Linux, le répertoire du journal est */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Désinstallation de l’Agent de dépendances sous Linux
Pour désinstaller complètement l’Agent de la dépendance de Linux, vous devez supprimer l’agent lui-même et le serveur proxy qui est installé automatiquement avec l’agent.  Vous pouvez désinstaller les deux avec la commande ci-dessous.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>L’installation à partir d’une ligne de commande
La section précédente fournit des conseils sur l’installation de l’agent du moniteur de la dépendance à l’aide des options par défaut.  Les sections suivantes fournissent des instructions pour l’installation de l’agent à partir d’une ligne de commande à l’aide des options personnalisées.

#### <a name="windows"></a>Windows
Utilisez les options dans le tableau ci-dessous pour effectuer l’installation à partir d’une ligne de commande. Pour afficher la liste de l’installation indicateurs d’exécutent le programme d’installation avec le / ? Marquer comme suit.

    ADM-Agent-Windows.exe /?

| Indicateur | Description |
|:--|:--|
| /S | Effectuer une installation en mode silencieux sans invite utilisateur. |

Par défaut, les fichiers de l’Agent de dépendance de Windows sont placés dans *C:\Program Files\BlueStripe\Collector* .


#### <a name="linux"></a>Linux
Utilisez les options dans le tableau ci-dessous pour effectuer l’installation. Pour afficher une liste d’indicateurs d’exécutent l’installation de l’installation du programme avec l’option - aide indicateur comme suit.

    ADM-Agent-Linux64.bin -help

| Description de l’indicateur
|:--|:--|
| -s | Effectuer une installation en mode silencieux sans invite utilisateur. |
| --Vérifiez | Vérifie les autorisations et le système d’exploitation, mais n’installe pas l’agent. |

Fichiers de l’Agent de dépendance sont placés dans les répertoires suivants.

| Fichiers | Emplacement |
|:--|:--|
| Fichiers principaux | /usr/lib/bluestripe-collector |
| Fichiers journaux | /var/opt/Microsoft/Dependency-agent/log |
| Fichiers de configuration | /etc/opt/Microsoft/Dependency-agent/config |
| Fichiers exécutables du service | /sbin/bluestripe-collector<br>/sbin/bluestripe-collector-Manager |
| Fichiers binaires de stockage | /var/opt/Microsoft/Dependency-agent/Storage |



## <a name="troubleshooting"></a>Résolution des problèmes
Si vous rencontrez des problèmes avec le moniteur de dépendance d’Application, vous pouvez rassembler des informations de dépannage à partir de plusieurs composants à l’aide des informations suivantes.

### <a name="windows-agents"></a>Agents Windows

#### <a name="microsoft-dependency-agent"></a>Dépendance de Microsoft Agent
Pour générer des données de dépannage de l’Agent de la dépendance, ouvrez une invite de commande en tant qu’administrateur et exécutez le script CollectBluestripeData.vbs à l’aide de la commande suivante.  Vous pouvez ajouter l’indicateur de l’aide pour afficher des options supplémentaires.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

Le lot de données de prise en charge est enregistré dans le répertoire % userprofile% pour l’utilisateur actuel.  Vous pouvez utiliser le--fichier <filename> option pour enregistrer dans un emplacement différent.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Dépendance de Microsoft Agent Management Pack pour MMA
Le Pack de gestion de l’Agent de dépendance s’exécute à l’intérieur de l’Agent de gestion Microsoft.  Il reçoit les données de l’Agent de dépendance et le transmet au service cloud ADM.
  
Vérifiez que le pack d’administration est téléchargé, effectuez les opérations suivantes.

1.  Recherchez un fichier nommé Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp dans C:\Program Files\Microsoft surveillance Agent\Agent\Health Service State\Management Packs.  
2.  Si le fichier n’est pas présent et si l’agent est connecté à un groupe de gestion de SCOM, puis vérifiez qu’il a été importé dans SCOM en vérifiant les Packs d’administration dans l’espace de travail d’Administration de la Console d’opérations.

Le panneau de gestion ADM écrit des événements dans le journal des événements Windows du Gestionnaire des opérations.  Le journal peut être [recherché dans l’OMS](../log-analytics/log-analytics-log-searches.md) via la solution de journal système, dans lequel vous pouvez configurer les fichiers journaux à télécharger.  Si les événements de débogage sont activés, ils sont écrits dans le journal des événements Application, avec la source de l’événement *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agent de surveillance Microsoft
Pour collecter les suivis de diagnostic, ouvrez une invite de commande en tant qu’administrateur et exécutez les commandes suivantes : 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Traces sont enregistrés dans c:\Windows\Logs\OpsMgrTrace.  Vous pouvez arrêter le traçage avec StopTracing.cmd.


### <a name="linux-agents"></a>Agents de Linux

#### <a name="microsoft-dependency-agent"></a>Dépendance de Microsoft Agent
Pour générer des données de dépannage de l’Agent de la dépendance, ouverture de session avec un compte disposant de privilèges sudo ou racine et exécutez la commande suivante.  Vous pouvez ajouter l’indicateur de l’aide pour afficher des options supplémentaires.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

Le lot de données de prise en charge est enregistré dans /var/opt/microsoft/dependency-agent/log (si racine) sous le répertoire d’installation de l’Agent, ou le répertoire de base de l’utilisateur qui exécute le script (si non root).  Vous pouvez utiliser le--fichier <filename> option pour enregistrer dans un emplacement différent.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dépendance de Microsoft Fluentd de l’Agent du plug-in pour Linux
Le plug-in Fluentd de dépendance de l’Agent s’exécute à l’intérieur de l’Agent de Linux OMS.  Il reçoit les données de l’Agent de dépendance et le transmet au service cloud ADM.  

Les journaux sont écrits dans les deux fichiers suivants.

- /var/opt/Microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agent d’OMS pour Linux
Une ressource de résolution des problèmes de connexion des serveurs Linux à OMS se trouve ici : [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Les journaux de l’Agent de l’OMS pour Linux se trouvent dans */var/opt/microsoft/omsagent/journal/*.  

Les journaux d’omsconfig (configuration de l’agent) se trouvent dans */var/opt/microsoft/omsconfig/journal/*.
 
Le journal de l’OMI et SCX des composants qui fournissent des données métriques de performances se trouvent dans */var/opt/omi/log/* et */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Collecte de données
Chaque agent de transmission d’environ 25 Mo par jour, en fonction de la complexité les dépendances de votre système sont attendues.  Données de dépendance ADM sont envoyées par chaque agent toutes les 15 secondes.  

L’Agent ADM consomme généralement de 0,1 % de la mémoire système et de 0,1 % du système du processeur.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les sections suivantes répertorient les systèmes d’exploitation pris en charge pour l’Agent de la dépendance.   les architectures 32 bits ne sont pas pris en charge n’importe quel système d’exploitation.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Bureau de Windows
- Remarque : Windows 10 n'est pas encore pris en charge
- 8.1 de Windows
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux et Oracle Linux (avec noyau RHEL)
- Seuls les par défaut et les versions du noyau Linux de SMP sont pris en charge.
- Versions de noyau non standard, tels que les PAE et Xen, ne sont pas pris en charge pour les distributions de Linux. Par exemple, un système avec la chaîne de version de « 2.6.16.21-0.8-xen » n’est pas pris en charge.
- Noyaux personnalisés, y compris les recompilations de noyaux standard, ne sont pas pris en charge.
- Noyau CentOS Plus n’est pas pris en charge.
- Oracle Unbreakable noyau (UEK) est traitée dans une autre section ci-dessous.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux avec noyau incassable (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Version du système d’exploitation | Version du noyau
|:--|:--|
| 6.2 | 2.6.32-300 d’Oracle (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (R2 UEK) |
| 6.4 | Oracle 2.6.39-400 (R2 UEK) |
| 6.5 | 2.6.39-400 d’Oracle (UEK R2 i386) |
| 6.6 | 2.6.39-400 d’Oracle (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Version du système d’exploitation | Version du noyau
|:--|:--|
| 5.8 | 2.6.32-300 d’Oracle (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (R2 UEK) |
| 5.10 | Oracle 2.6.39-400 (R2 UEK) |
| 5.11 | Oracle 2.6.39-400 (R2 UEK) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Version du système d’exploitation | Version du noyau
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| SP4 11 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Version du système d’exploitation | Version du noyau
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Données de diagnostic et d’utilisation
Microsoft recueille automatiquement les données d’utilisation et de performances par le biais de votre utilisation du service Moniteur de dépendances d’Application. Microsoft utilise ces données pour fournir et améliorer la qualité, de sécurité et l’intégrité du service Moniteur de dépendances d’Application. Données inclut des informations sur la configuration de vos logiciels, comme le système d’exploitation et de version et inclut également une adresse IP, nom DNS et nom de la station de travail afin de fournir des fonctions de dépannage précises et efficaces. Nous ne collectons pas de noms, des adresses ou autres informations de contact.

Pour plus d’informations sur l’utilisation et de collecte de données, consultez [Déclaration de confidentialité de Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [utiliser le moniteur de dépendance d’Application](operations-management-suite-application-dependency-monitor.md) de fois il a été déployé et configuré.
