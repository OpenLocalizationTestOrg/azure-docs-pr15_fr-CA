<properties 
    pageTitle="Azure RemoteApp la bande passante réseau - directives générales | Microsoft Azure"
    description="Comprendre certaines directives de la bande passante réseau de base pour vos collections de RemoteApp d’Azure et les applications."
    services="remoteapp"
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp la bande passante réseau - général (si vous ne pouvez pas tester vos propres)

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Si vous n’avez pas le temps ou la possibilité d’exécuter les [tests de bande passante réseau](remoteapp-bandwidthtests.md) pour Azure RemoteApp, voici quelques indications relativement génériques qui peuvent vous aider à estimer la bande passante par utilisateur.

Si vous avez un mélange de ces scénarios, nous ne recommande pas quoi que ce soit inférieur (ou égal à) 10 Mo/s en tant que la bande passante minimale pour les applications modernes connectés à Internet dans un environnement à distance. (Bien que, comme indiqué, cela ne garantira pas une meilleure qualité que l’expérience de l’utilisateur moyen.)

## <a name="complex-powerpoint-simple-powerpoint"></a>Complexe PowerPoint, PowerPoint simple

Azure RemoteApp peut mieux sur réseau local de 100 Mo. Dans le profil de réseau 10 Mo/s, lorsque l’instabilité au-dessus de 120 ms est supérieure à 5 %, l’utilisateur verra une expérience moyenne. 1 MB/s, que l’autre est éblouissants - par exemple, dans un diaporama, l’utilisateur peut ne pas afficher des transitions animées à, car les images sont ignorées.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, mixtes PDF, PDF, texte

Profil de réseau 10 Mo/s est proche de LAN dans la plupart des aspects. 1 MB/s fournira une expérience OK, bien qu’il y a peut-être une gigue lorsqu’un utilisateur fait défiler alors qu’il existe des images à l’écran.

## <a name="flash-video-youtube"></a>Vidéo Flash (YouTube)

LAN 100 Mbit/s fournit la meilleure expérience possible, alors que 10 Mo/s est acceptable (c'est-à-dire nous faire face à la fréquence d’images mais instabilité augmente). 1 MB/s, instabilité est très élevée et visible.

## <a name="word-typing-word-remote-input"></a>Word frappe (Word entrée à distance)
Il s’agit d’un scénario d’utilisation de bande passante faible. 256 Ko/s, nous fournissons aussi bien d’une expérience en tant que réseau local.

## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimer](remoteapp-bandwidth.md)

- [RemoteApp Azure - comment la bande passante réseau et la qualité de profiter de travail ensemble ?](remoteapp-bandwidthexperience.md)

- [RemoteApp Azure - tseting l’utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)