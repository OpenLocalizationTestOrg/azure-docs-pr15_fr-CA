
<properties 
    pageTitle="Utilisation de la bande passante réseau Azure RemoteApp d’estimer | Microsoft Azure"
    description="Obtenir des informations sur les besoins en bande passante pour vos collections de RemoteApp d’Azure et les applications."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Utilisation de la bande passante réseau Azure RemoteApp d’estimer 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

RemoteApp Azure utilise le protocole RDP (Remote Desktop) pour communiquer entre les applications s’exécutant dans le nuage Azure et vos utilisateurs. Cet article fournit quelques principes de base que vous pouvez utiliser pour estimer que l’utilisation du réseau et potentiellement évaluer l’utilisation de la bande passante réseau par utilisateur d’Azure RemoteApp.

Estimation de la bande passante par utilisateur est très complexe et requiert l’exécution de plusieurs applications simultanément dans les scénarios de multitâche où les applications peuvent avoir un impact sur les performances en fonction de leur demande de bande passante du réseau. Même du type de client de bureau à distance (par exemple, le client Mac et HTML5 client) peut entraîner des résultats différents de la bande passante. Pour vous aider à réaliser ces complications, nous risquerions de scénarios d’utilisation dans plusieurs des catégories courantes pour répliquer des scénarios réels. (Dans le cas où la réalité est, bien sûr, un mélange de catégories et diffère de l’utilisateur.)

Avant d’aller plus - Notez que nous supposons que RDP fournit une bonne à excellente expérience pour la plupart des scénarios d’utilisation sur des réseaux à latence au-dessous de la bande passante et les 120 ms plus de 5 Mo - Ceci est basé sur la capacité de RDP pour ajuster dynamiquement à l’aide de la bande passante réseau disponible et la bande passante estimée de l’application a besoin. Cet article va au-delà de celles « la plupart des scénarios d’utilisation » pour rechercher sur le bord, où scénarios commencent à dérouler et l’expérience utilisateur commence à se dégrader.

Extraire les articles suivants pour plus d’informations, y compris les facteurs à prendre en compte, les recommandations de base, et ce que nous avons contenait pas dans nos estimations.

- [Comment la bande passante réseau et la qualité de profiter de travail ensemble ?](remoteapp-bandwidthexperience.md)
- [Test de votre utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)
- [Si vous n’avez pas le temps ou la possibilité de tester les instructions rapides](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>Ce que nous avons pas incluons ?

Lorsque vous examinez les tests proposées et nos recommandations globales (et il est vrai que génériques), n’oubliez pas qu’il y a plusieurs facteurs qui nous n’a pas tenu compte. Par exemple, les complications d’expérience utilisateur fournies par la nature asymétrique de téléchargement et téléchargement la bande passante. La nature asymétrique de la plupart des réseaux Wi-Fi aura en outre un impact sur les performances et la perception d’expérience utilisateur. Pour les scénarios interactives le trafic en aval peut-être être hiérarchisé inférieur à situés en amont, qui peut augmenter le nombre de trames perdues de vidéo ou audio et par conséquent avoir un impact sur la perception de l’utilisateur de l’expérience de diffusion en continu. Vous pouvez exécuter vos propres expériences pour voir ce qui est bon pour votre réseau et de cas d’utilisation spécifique.

Bien que nous abordons la redirection de périphérique, il n’a pas eu en considération l’impact de la bande passante du trafic réseau causé par des périphériques connectés, tels que le stockage, les imprimantes, les scanneurs, les caméras web et les autres périphériques USB. Généralement, l’effet de ces périphériques pics temporairement les besoins en bande passante et disparaît lorsque la tâche est terminée. Mais si fait fréquemment, que la demande de la bande passante peut être nettement plus sensible.

Nous ne discutons pas comment un utilisateur peut affecter les autres utilisateurs au sein du même réseau. Par exemple, un utilisateur consomme vidéo de 4 Ko sur un réseau 100 Mbits/s susceptibles de dégrader considérablement autres utilisateurs sur ce même réseau que vous essayez d’effectuer la même tâche. Malheureusement, il obtient progressivement plus difficile à déterminer l’impact de l’utilisation simultanée de donner une recommandation commune ou complète sur la façon dont le système effectue à l’agrégat. Tout ce que nous pouvons dire est que la technologie de protocole sous-jacente rendra la meilleure utilisation de la bande passante réseau disponible, mais elle a ses limites.