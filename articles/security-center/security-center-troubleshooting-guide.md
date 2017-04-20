<properties
   pageTitle="Centre de sécurité Azure Guide de dépannage | Microsoft Azure"
   description="Ce document vous aide à résoudre les problèmes dans le centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-troubleshooting-guide"></a>Guide de dépannage du centre de sécurité Azure
Ce guide est pour les professionnels de l’informatique (IT), des analystes de la sécurité des informations et des administrateurs de nuage dont les organisations utilisent le centre de sécurité Azure et doivent résoudre que problèmes de centre de sécurité.

## <a name="troubleshooting-guide"></a>Guide de dépannage
Ce guide explique comment résoudre les problèmes de centre de sécurité. La plupart de l’incident dans le centre de sécurité aura lieu en examinant en premier les enregistrements de [Journal d’Audit](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) pour le composant qui a échoué. Les journaux d’audit, vous pouvez déterminer :

- Les opérations qui ont eu lieu
- Qui a initié l’opération
- Lorsque l’opération s’est produite
- L’état de l’opération
- Les valeurs d’autres propriétés qui peuvent vous aider à l’opération de recherche

Le journal d’audit contient toutes les opérations d’écriture (PUT, POST, DELETE) effectuées sur vos ressources, mais il n’inclut pas les opérations de lecture (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Résolution des problèmes d’installation de l’agent de surveillance dans Windows

L’agent de surveillance du centre de sécurité est utilisé pour effectuer la collecte de données. Après la collecte des données est activée et que l’agent est correctement installé sur l’ordinateur cible, ces processus doivent être en exécution :

- ASMAgentLauncher.exe - Azure, l’Agent de surveillance 
- ASMMonitoringAgent.exe - extension de contrôle de la sécurité Azure
- ASMSoftwareScanner.exe – Gestionnaire de numérisation Azure

L’extension du contrôle de la sécurité Azure recherche les différentes configuration de sécurité pertinentes et collecte des journaux de sécurité de la machine virtuelle. Le Gestionnaire d’analyse sert un analyseur de correctifs.

Si l’installation est effectuée avec succès, vous devez voir une entrée similaire à celui ci-dessous dans les journaux d’Audit pour la machine virtuelle de la cible :

![Journaux d’audit](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Vous pouvez également obtenir plus d’informations sur le processus d’installation en consultant les journaux de l’agent, situés à *%systemdrive%\windowsazure\logs* (exemple : C:\WindowsAzure\Logs).

> [AZURE.NOTE] Si l’Agent de centre de sécurité Azure ne fonctionne pas correctement, vous devez redémarrer la machine virtuelle de la cible dans la mesure où il n’y a aucune commande pour arrêter et démarrer l’agent.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Résolution des problèmes d’installation de l’agent de surveillance sous Linux
Lors de la résolution des problèmes d’installation de l’Agent de l’ordinateur virtuel dans un système Linux, vous devez vous assurer que l’extension a été téléchargée dans/var/lib/waagent /. Vous pouvez exécuter la commande suivante pour vérifier si elle a été installée :

`cat /var/log/waagent.log` 

Les autres fichiers journaux que vous pouvez consulter pour objectif de dépannage sont les suivantes : 

- /var/log/mdsd.Err
- / var/log/azure /

Sur un système qui fonctionne, vous devriez voir une connexion au processus mdsd sur TCP 29130. C’est le journal système communique avec le processus mdsd. Vous pouvez valider ce comportement en exécutant la commande ci-dessous :

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Contacter le Support Microsoft

Certains problèmes peuvent être identifiés en utilisant les indications fournies dans cet article, d’autres que vous pouvez également trouver documentées sur le [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)du centre de sécurité publique. Toutefois si vous avez besoin de davantage de dépannage, vous pouvez ouvrir une nouvelle demande de support à l’aide du portail Azure comme indiqué ci-dessous : 

![Prise en charge de Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment configurer les stratégies de sécurité dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, consultez les rubriques suivantes :

- [Guide des opérations et la planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) : Apprenez à planifier et à comprendre les considérations de conception à adopter le centre de sécurité Azure.
- [Sécurité contrôle d’état dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment surveiller la santé de vos ressources Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Solutions de contrôle partenaire avec le centre de sécurité Azure](security-center-partner-solutions.md) : Apprenez à surveiller l’état de santé de vos solutions de partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : trouver des billets de blog sur la sécurité Azure et de conformité
