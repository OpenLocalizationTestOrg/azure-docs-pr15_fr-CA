<properties 
   pageTitle="Les messages syslog dans journal Analytique | Microsoft Azure"
   description="Syslog est un protocole de journalisation d’événement qui est commun à Linux.   Cet article décrit comment configurer la collection de messages Syslog dans journal Analytique et les détails des enregistrements qu’ils créent dans le référentiel de l’OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="bwren" />


# <a name="syslog-data-sources-in-log-analytics"></a>Sources de données syslog dans journal Analytique

Syslog est un protocole de journalisation d’événement qui est commun à Linux.  Les applications envoient les messages pouvant être stockés sur l’ordinateur local ou remis à un collecteur de Syslog.  Lorsque l’Agent de l’OMS pour Linux est installée, elle configure le démon Syslog locaux pour transférer des messages à l’agent.  L’agent envoie ensuite le message à Analytique journal où un enregistrement correspondant est créé dans le référentiel de l’OMS.  

> [AZURE.NOTE]Analytique de journal prend en charge la collection de messages envoyés par rsyslog ou syslog-ng. Le démon syslog par défaut dans la version 5 de Red Hat Enterprise Linux et Oracle Linux CentOS (sysklog) n’est pas prise en charge de la collecte d’événements syslog. Pour collecter des données de journal système à partir de cette version de ces distributions, le [démon de rsyslog](http://rsyslog.com) doit être installé et configuré pour remplacer les sysklog.

![Collection de syslog](media/log-analytics-data-sources-syslog/overview.png)


## <a name="configuring-syslog"></a>Configuration de Syslog
L’Agent de l’OMS pour Linux collecte uniquement des événements avec les installations et les niveaux de gravité est spécifiés dans la configuration.  Vous pouvez configurer Syslog via le portail de l’OMS, ou par la gestion des fichiers de configuration sur vos agents de Linux.


### <a name="configure-syslog-in-the-oms-portal"></a>Configurer le journal système dans le portail de l’OMS

Configurer le journal système dans le [menu données de paramètres de journal Analytique](log-analytics-data-sources.md#configuring-data-sources).  Cette configuration est remise au fichier de configuration sur chaque agent de Linux.

Vous pouvez ajouter une nouvelle installation en tapant son nom en cliquant sur **+**.  Pour chaque site, seuls les messages avec les niveaux de gravité sélectionné seront collectées.  Vérifiez les niveaux de gravité pour la fonctionnalité particulière que vous souhaitez collecter.  Vous ne pouvez pas fournir des critères supplémentaires pour filtrer les messages.

![Configurer le journal système](media/log-analytics-data-sources-syslog/configure.png)


Par défaut, toutes les modifications de configuration sont automatiquement envoyées à tous les agents.  Si vous voulez configurer manuellement des Syslog sur chaque agent de Linux, puis désactivez la case *appliquer sous configuration de mes machines Linux*.


### <a name="configure-syslog-on-linux-agent"></a>Configurer Syslog sur Linux agent

Lorsque l' [agent de l’OMS est installé sur un client Linux](log-analytics-linux-agents.md), installe un fichier de configuration de journal système par défaut qui définit l’installation et la gravité des messages qui sont collectées.  Vous pouvez modifier ce fichier pour modifier la configuration.  Le fichier de configuration est différent selon le démon Syslog que le client a installé.

> [AZURE.NOTE] Si vous modifiez la configuration du journal système, vous devez redémarrer le démon syslog pour que les modifications soient prises en compte.

#### <a name="rsyslog"></a>rsyslog

Le fichier de configuration pour rsyslog se trouve à **/etc/rsyslog.d/95-omsagent.conf**.  Sa valeur par défaut est affichées ci-dessous.  Cela permet de rassembler des messages syslog envoyées par l’agent local pour toutes les installations avec un niveau d’avertissement ou supérieur.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

Vous pouvez supprimer une fonctionnalité en supprimant de sa section du fichier de configuration.  Vous pouvez limiter les niveaux de gravité est collectées pour une installation donnée en modifiant entrée de ce dispositif.  Par exemple, pour limiter l’installation de l’utilisateur pour les messages avec un niveau de gravité erreur ou supérieure vous souhaitez modifier cette ligne du fichier de configuration pour les éléments suivants :

    user.error  @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng

Le fichier de configuration pour rsyslog est l’emplacement **/etc/syslog-ng/syslog-ng.conf**.  Sa valeur par défaut est affichées ci-dessous.  Cela permet de rassembler des messages syslog envoyées par l’agent local pour tous les équipements et tous les niveaux de gravité.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };
    
    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };
    
    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };
    
    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };
    
    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };
    
    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };
    
    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

Vous pouvez supprimer une fonctionnalité en supprimant de sa section du fichier de configuration.  Vous pouvez limiter les niveaux de gravité est collectées pour une installation donnée en les supprimant de sa liste.  Par exemple, pour limiter l’installation de l’utilisateur pour les messages critiques et alertes seulement, vous souhaitez modifier cette section du fichier de configuration pour les éléments suivants :

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="changing-the-syslog-port"></a>Modification du port Syslog

L’agent de l’OMS est à l’écoute pour les messages Syslog sur le client local au port 25224.  Vous pouvez modifier ce port en ajoutant la section suivante pour le fichier de configuration de l’agent OMS situé à **/etc/opt/microsoft/omsagent/conf/omsagent.conf**.  Remplacez 25224 dans l’entrée du **port** avec le numéro de port que vous souhaitez.  Notez que vous devez également modifier le fichier de configuration pour le processus syslog chargé d’envoyer des messages à ce port.

    <source>
      type syslog
      port 25224
      bind 127.0.0.1
      protocol_type udp
      tag oms.syslog
    </source>


## <a name="data-collection"></a>Collecte de données

L’agent de l’OMS est à l’écoute pour les messages Syslog sur le client local au port 25224. Le fichier de configuration pour le démon Syslog transfère les messages de Syslog envoyés à partir de l’application à ce port où ils sont collectés par le journal Analytique.


## <a name="syslog-record-properties"></a>Propriétés d’enregistrement de journal système

Les enregistrements de journal système ont un type de **Syslog** et ont les propriétés dans le tableau suivant.

| Propriété | Description |
|:--|:--|
| Ordinateur | Ordinateur sur lequel l’événement a été collecté à partir de. |
| Installation portuaire | Définit la partie du système qui a généré le message. |
| HostIP | Adresse IP du système n’envoie le message.  |
| Nom d’hôte | Nom du système envoie le message. |
| SeverityLevel | Niveau de gravité de l’événement. |
| SyslogMessage | Texte du message. |
| ProcessID | ID du processus qui a généré le message. |
| EventTime | Date et heure à laquelle l’événement a été généré.



## <a name="log-queries-with-syslog-records"></a>Journal des requêtes avec des enregistrements de journal système

Le tableau suivant fournit des exemples de requêtes de journaux qui extrait des enregistrements du journal système.

| Requête | Description |
|:--|:--|
| Type = Syslog | Toutes les alertes. |
| Type = Syslog SeverityLevel = erreur | Tous les enregistrements de journal système avec la gravité de l’erreur. |
| Type = Syslog & #124 ; count() mesure par ordinateur | Nombre de Syslog des enregistrements par ordinateur. |
| Type = Syslog & #124 ; count() mesure par facilité | Nombre de Syslog des enregistrements par facilité. |

## <a name="next-steps"></a>Étapes suivantes

- Obtenir des informations sur les [recherches de journaux](log-analytics-log-searches.md) analyser les données collectées à partir de sources de données et des solutions. 
- Utilisez les [Champs personnalisés](log-analytics-custom-fields.md) pour analyser les données à partir d’enregistrements de journal système dans les champs individuels.
- [Linux de configurer des agents](log-analytics-linux-agents.md) pour collecter d’autres types de données. 
