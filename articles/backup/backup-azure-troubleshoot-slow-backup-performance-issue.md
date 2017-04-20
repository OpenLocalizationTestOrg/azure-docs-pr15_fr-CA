<properties
   pageTitle="Résoudre les problèmes de sauvegarde lente des fichiers et des dossiers de sauvegarde Azure | Microsoft Azure"
   description="Fournit des conseils de dépannage pour vous aider à diagnostiquer la cause de problèmes de performances de sauvegarde de Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Résoudre les problèmes de sauvegarde lente des fichiers et des dossiers de sauvegarde Azure

Cet article fournit des conseils de dépannage pour vous aider à diagnostiquer la cause du ralentissement des performances de sauvegarde des fichiers et des dossiers lorsque vous utilisez la sauvegarde Azure. Lorsque vous utilisez l’agent de sauvegarde Azure pour sauvegarder des fichiers, la sauvegarde peut durer plus longtemps que prévu. Ce retard peut être provoqué par un ou plusieurs des opérations suivantes :

-   [Il existe des goulots d’étranglement de performances sur l’ordinateur en cours de sauvegarde.](#cause1)
-   [Un autre processus ou un logiciel antivirus interfère avec le processus de sauvegarde d’Azure.](#cause2)
-   [L’agent de sauvegarde est en cours d’exécution sur une machine virtuelle Azure (VM).](#cause3)  
-   [Vous sauvegardez un grand nombre (en millions) des fichiers.](#cause4)

Avant de commencer la résolution des problèmes, nous vous recommandons de télécharger et d’installer [dernier agent de sauvegarde d’Azure](http://aka.ms/azurebackup_agent). Nous apporter des mises à jour fréquentes à l’agent de sauvegarde pour résoudre divers problèmes, ajouter des fonctionnalités et améliorer les performances.

Nous vous recommandons également vivement de consulter le [Forum aux questions du service de sauvegarde d’Azure](backup-azure-backup-faq.md) pour vous assurer que vous n’êtes pas confronté tous les problèmes de configuration courants.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Cause : Les goulots d’étranglement sur l’ordinateur

Les goulots d’étranglement sur l’ordinateur en cours de sauvegarde peuvent entraîner des retards. Par exemple, capacité de l’ordinateur pour lire ou écrire sur disque ou la bande passante disponible pour envoyer des données sur le réseau, peut provoquer des goulots d’étranglement.

Windows fournit un outil appelé [Analyseur de performances](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) pour détecter les goulets d’étranglement.

Voici certains des compteurs de performance et les plages qui peuvent être utiles pour diagnostiquer les goulots d’étranglement pour des sauvegardes optimales.

| Compteur  | État  |
|---|---|
|Disque logique (disque physique)--% inactif   | • 100 % inactif inactivité de 50 % = sain</br>• 49 % inactif inactivité de 20 % = avertissement ou moniteur</br>• 19 % inactif inactivité à 0 % = critique ou spécifications|
|  Disque logique (disque physique)--% moyenne S lecture ou écriture disque |  • ms 0,001 à 0,015 ms = sain</br>• 0,015 ms à 0,025 ms = avertissement ou moniteur</br>• 0.026 ms ou plus = critique ou spécifications|
|  Disque logique (disque physique) : longueur actuelle de file d’attente de disque (pour toutes les instances) | 80 demandes pendant plus de 6 minutes. |
| Mémoire--Non paginée octets|• Inférieure à 60 % du pool consommé = sain<br>• 61 % à 80 % du pool consommé = avertissement ou moniteur</br>• Supérieure à un pool de 80 % consommé = critique ou spécifications|
| Mémoire - octets de réserve paginée |• Inférieure à 60 % du pool consommé = sain</br>• 61 % à 80 % du pool consommé = avertissement ou moniteur</br>• Supérieure à un pool de 80 % consommé = critique ou spécifications|
| Mémoire : mégaoctets disponibles| • 50 % de mémoire libre disponible ou plus = sain</br>• 25 % de mémoire libre disponible = Moniteur</br>• 10 % de mémoire libre disponible = avertissement</br>• Moins de 100 Mo ou de 5 % de mémoire libre disponible = critique ou spécifications|
|Processeur :\%de temps processeur (toutes les instances)|• Inférieure à 60 % de consommation = sain</br>• 61 % à 90 % de consommation = moniteur ou attention</br>• 91 % à 100 % de consommation = critique|


> [AZURE.NOTE] Si vous déterminez que l’infrastructure est la cause du problème, il est recommandé de défragmenter les disques régulièrement pour de meilleures performances.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Cause : Un autre processus ou un logiciel antivirus interfère avec sauvegarde d’Azure

Nous avons vu plusieurs instances où autres processus dans le système Windows ont affecté négativement les performances du processus de l’agent de sauvegarde d’Azure. Par exemple, si vous utilisez à la fois l’agent de sauvegarde d’Azure et un autre programme pour sauvegarder des données, ou si un logiciel antivirus est en cours d’exécution et a un verrou sur les fichiers à sauvegarder, le multiple des verrous sur les fichiers risque de conflit. Dans ce cas, la sauvegarde peut échouer ou l’opération peut prendre plus longtemps que prévu.

La meilleure recommandation dans ce scénario consiste à désactiver de l’autre programme de sauvegarde pour voir si l’heure de sauvegarde pour l’agent de sauvegarde Azure change. En règle générale, il est suffisante pour les empêcher de s’affecter mutuellement de s’assurer que plusieurs opérations de sauvegarde ne s’exécutent pas en même temps.

Pour les programmes antivirus, nous vous recommandons d’exclure les fichiers et dossiers suivants :

- C:\Program Files\Microsoft Azure récupération Services Agent\bin\cbengine.exe en tant que processus
- C:\Program Files\Microsoft l’Agent\ les Services de récupération Azure dossiers
- Emplacement de travail (si vous n’utilisez pas l’emplacement standard)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Cause : L’agent de sauvegarde en cours d’exécution sur une machine virtuelle Azure

Si vous exécutez l’agent de sauvegarde sur une machine virtuelle, les performances seront plus lentes que lorsque vous l’exécutez sur un ordinateur physique. Ce comportement est normal en raison des limitations des Ops ES/s.  Toutefois, vous pouvez optimiser les performances en changeant les lecteurs de données sont sauvegardées sur le stockage Azure Premium. Nous travaillons sur la résolution de ce problème et le correctif sera disponible dans une version future.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Cause : La sauvegarde un grand nombre (en millions) de fichiers

Déplacement d’un volume important de données prendra plus de temps que de déplacer un plus petit volume de données. Dans certains cas, les temps de sauvegarde est lié à la non seulement la taille des données, mais également le nombre de fichiers ou de dossiers. Cela est particulièrement vrai lorsque des millions de petits fichiers (de quelques octets à quelques kilo-octets) sont sauvegardées.

Ce problème se produit parce que pendant que vous êtes la sauvegarde des données et en la déplaçant vers Azure, Azure est catalogage simultanément vos fichiers. Dans certains cas rares, l’opération de catalogue peut prendre plus longtemps que prévu.

Les indicateurs suivants peuvent vous aider à comprendre le goulot d’étranglement et de travail en conséquence dans les étapes suivantes :

- **L’interface utilisateur affiche la progression du transfert de données**. Les données sont toujours en cours de transfert. La bande passante du réseau ou la taille des données peut être à l’origine de retards.

- **L’interface utilisateur n’affiche pas la progression du transfert de données**. Ouvrir les journaux à l’Agent\Temp de Services de récupération de Azure C:\Microsoft et puis recherchez l’entrée FileProvider::EndData dans les journaux. Cette entrée indique que le transfert de données terminé, et l’opération de catalogue qui se passe. Ne pas annuler les travaux de sauvegarde. À la place, attendez un peu plus terminer l’opération de catalogue. Si le problème persiste, contactez [l’assistance technique de Azure](https://portal.azure.com/#create/Microsoft.Support).
