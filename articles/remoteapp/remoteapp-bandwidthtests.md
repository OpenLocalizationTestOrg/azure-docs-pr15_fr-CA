<properties 
    pageTitle="RemoteApp Azure - test de l’utilisation de la bande passante réseau avec quelques scénarios courants | Microsoft Azure"
    description="Découvrez qu’en est-il des scénarios d’utilisation communs qui peuvent vous aider à déterminer le rôle de vos besoins de bande passante réseau pour Azure RemoteApp."
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
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>RemoteApp Azure - test de l’utilisation de la bande passante réseau avec quelques scénarios courants

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Comme expliqué dans [l’utilisation de la bande passante du réseau estimation Azure RemoteApp](remoteapp-bandwidth.md), la meilleure façon de comprendre l’impact de Azure RemoteApp à votre réseau à l’exécution de l’utilisation de certains tests. Exécuter ces tests pour une période donnée et mesurer la bande passante nécessaire pour chaque scénario. Si vous avez la possibilité, vous pouvez également mesurer le paquets réseau et la perte de gigue de réseaux pour comprendre les modèles de réseau qui seront créées dans votre environnement spécifique.

    
Lors de l’évaluation de l’utilisation de la bande passante, n’oubliez pas que l’utilisation varie entre différents utilisateurs au sein de votre société. Par exemple, les writers et les lecteurs de texte consomment généralement moins de bande passante que les utilisateurs qui travaillent avec de la vidéo. Pour de meilleurs résultats, étudiez vos besoins utilisateur et créer un mélange des scénarios suivants qui correspond le mieux les utilisateurs de votre société. N’oubliez pas de [consulter les facteurs d’expérience d’utilisation de la bande passante d’impact et de l’utilisateur](remoteapp-bandwidthexperience.md) - qui vous aideront à identifier les tests idéales.

Lisez tout d’abord sur les tests, choisissez votre combinaison et avant de les exécuter. Vous pouvez utiliser le tableau suivant pour faciliter le suivi des performances.

>[AZURE.NOTE] Si vous ne pouvez pas faire vos propres tests de réseau, ou si vous n’avez pas le temps de le faire, consultez nos [estimations/recommandations de la bande passante réseau de base](remoteapp-bandwidthguidelines.md). Votre kilométrage peut varier, cependant, donc si vous *pouvez* exécuter vos propres tests, vous devez.


## <a name="the-usage-tests"></a>Les tests d’utilisation
Chacun de ces tests pour la durée d’exécution d’et tester différentes fonctions/fonctionnalités qui consomment la bande passante du réseau. N’oubliez pas de choisir la combinaison de tests qui correspond le mieux à vos utilisateurs de la société.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>PowerPoint de l’exécutif et complexes - exécution de 900-1000 secondes

Un utilisateur présente entre les diapositives de haute fidélité de 45 à 50 à l’aide de Microsoft Office PowerPoint en mode plein écran. Les diapositives doivent contenir des images, des transitions (avec animations) et des arrière-plans avec dégradé de couleurs qui sont typiques de votre société. L’utilisateur doit passer au moins 20 secondes sur chaque diapositive.
    
Ce scénario crée rafales du trafic, lorsque les transitions d’une diapositive à la diapositive suivante de la présentation.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint simple - exécuter ~ 620 secondes

Un utilisateur présente un fichier PowerPoint simple avec environ 30 diapositives en mode plein écran à l’aide de Microsoft Office PowerPoint. Les diapositives sont plus beaucoup de texte que dans le scénario de PowerPoint de l’exécutif et complexes et ont plus simple des arrière-plans et des images (diagrammes de noirs). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - exécuter ~ 250 secondes

Un utilisateur navigue sur le web à l’aide d’Internet Explorer. L’utilisateur consulte et fait défiler un mélange de texte, des images naturelles et des schémas. Les pages web stockées sur le disque local du serveur hôte de Session Bureau à distance (hôte de Session Bureau à distance) sous la forme d’un. Fichier MHT. L’utilisateur fait défiler à l’aide de PG. préc, PG. suiv, haut et bas clés, avec des intervalles différents pour chaque type/clé de défilement :
    
    - Bas - 250 touches très 500 ms
    - Pg - 36 touches toutes les 1 000 ms
    - Bas - 75 touches toutes les 100 ms
    - Pg - 20 touches toutes les 500 ms
    - Haut - 120 touches toutes les 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Document PDF - simple - exécuter ~ 610 secondes
Un utilisateur lit et recherche dans un document PDF de diverses manières à l’aide de Adobe Acrobat Reader. Le document doit comporter des tables, des graphiques simples et plusieurs polices de texte. Le document est long à 35-40 pages. L’utilisateur fait défiler vers l’arrière à deux des taux différents et transmet, en quatre tailles différentes de zoom (Ajuster à la page, ajuster à la largeur, 100 % et une autre de votre choix). Le zoom permet de s’assurer que le texte (police) effectue le rendu de différentes tailles. Le défilement est à l’aide de la PG. préc, PG. suiv, haut et bas clés, avec des intervalles différents pour chaque défilement.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Exécution - mixte - ~ 320 secondes du document PDF
Un utilisateur lit et recherche dans un document PDF de diverses manières à l’aide de Adobe Acrobat Reader. Le document consiste des images de haute qualité (y compris les photographies), des tables, des graphiques simples et plusieurs polices de texte. L’utilisateur fait défiler vers l’arrière à deux des taux différents et transmet, en quatre tailles différentes de zoom (Ajuster à la page, ajuster à la largeur, 100 % et une autre de votre choix). Le zoom permet de s’assurer que le texte (police) effectue le rendu de différentes tailles. Le défilement est à l’aide de la PG. préc, PG. suiv, haut et bas clés, avec des intervalles différents pour chaque défilement.

### <a name="flash-video-playback---run-for-180-seconds"></a>Lecture de la vidéo Flash - exécuter ~ 180 secondes
Un utilisateur affiche une vidéo encodée Adobe Flash incorporé dans une page web. La page web est stocké sur le disque dur local du serveur hôte de Session Bureau à distance. Lecture de la vidéo dans Internet Explorer par un plug-in de lecteur incorporé.

Ce scénario émule les utilisateurs à afficher des pages web enrichies contenu contenant multimédia. La plupart des données doit bo via VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word tapant à distance - exécuter ~ 1800 secondes
Un utilisateur tape un document via une session RDP. Combinaisons de touches sont envoyées à partir du client par le biais de la session RDP à un document dans Microsoft Word en cours d’exécution dans la session à distance. La vitesse de frappe est un caractère chaque 250 ms (7050 caractères au total). 

Il s’agit d’un des scénarios plus courants pour un travailleur de connaissances. Ce scénario teste la réactivité d’un utilisateur entrant dans un processeur de travail moderne. Ce scénario est sensible au même des modifications mineures dans l’utilisation de la bande passante.

## <a name="tracking-the-test-results"></a>Les résultats du test de suivi

Le tableau suivant vous permet d’évaluer les scénarios dans votre environnement. Les données fournies ci-dessous sont uniquement à titre d’illustration, il peut être très différent de ce que vous observez. 

Pour plus de simplicité, nous supposons que tous les scénarios sont testés à l’aide de la même résolution d’écran de 1920 x 1080 pixels et variation de transport TCP sur un réseau avec une latence (retard) ci-dessous 200 ms et du réseau dans la marque de 120 + ms d’environ 1 %.

À propos de la table :
- **Expérience de moyenne** contient la bande passante du réseau, où la productivité des utilisateurs n’est pas considérablement affectée, mais n’exclut pas les problèmes de vidéo ou audio occasionnels. Le système est en mesure de récupérer rapidement en tirant parti de la logique dynamique. La tentative d’estimations de la bande passante de réseau afin de garantir la qualité de l’expérience utilisateur.
 - **Problèmes de Noticeable (point d’arrêt)** contient la bande passante du réseau, où les utilisateurs peuvent remarquer des problèmes importants dans leur expérience et leur productivité a un impact sur des périodes de temps mesurable. À ce stade, les algorithmes RDP sont mal et ne peut pas garantir la qualité de l’utilisateur de l’expérience en raison de la bande passante réseau insuffisante.
 - **Recommandé** contient la bande passante recommandée pour une expérience de bonne ou excellente. Il est généralement une étape supérieure à la valeur dans la colonne correspondante de **l’expérience de moyenne** .
 - **Notes** contiennent des observations et commentaires.
 
| Test                  | Expérience moyenne | Problèmes importants (point d’arrêt) | Bande passante du réseau recommandés | Notes                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| Exécutif et complexes PPT | 10 Mo/s             | 1 MB/s                           | > 10 Mo/s, préféré de 100 Mo/s    | 1 MB/s, de nombreuses animations sont perdues                                   |
| PPT simple            | 5 Mo/s              | 256 Ko/s                         | 10 Mo/s                        | Chargement les diapositives à 256 Ko/s avec retard                   |
| Internet Explorer     | 10 Mo/s             | 1 MB/s                           | > 10 Mo/s, préféré de 100 Mo/s    | 1 MB/s vidéos web sont floues et instable, rapide défilement présente des problèmes |
| PDF simple            | 1 MB/s              | 256 Ko/s                         | 5 Mo/s                         | Il faut du temps pour charger la page à 256 Ko/s                       |
| PDF mixte             | 1 MB/s             | 256 Ko/s                         | 5 Mo/s                         | À 256 Ko/s la page prend beaucoup de temps à charger    |
| Lecture de la vidéo Flash  | 10 Mo/s             | 1 MB/s                           | > 10 Mo/s, préféré de 100 Mo/s    | 1 MB/s, la vidéo est granuleuse et certaines images sont supprimées.           |
| Word de saisie à distance    | 256 Ko/s            | 128 Kbits/s                         | 1 MB/s                         | 256 Ko/s utilisateur peut remarquer la durée entre les séquences de touches             |

Pour évaluer la bande passante par utilisateur, créez une combinaison des scénarios ci-dessus et la proportion correspondante de la bande passante réseau requise. Choisissez le numéro le plus élevé nécessaire pour vos scénarios. Dans la mesure où les utilisateurs utilisent pratiquement jamais le seul système, envisagez certaines réserves pour les utilisateurs qui travaillent simultanément sur le même réseau.
     
## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimer](remoteapp-bandwidth.md)

- [RemoteApp Azure - comment la bande passante réseau et la qualité de profiter de travail ensemble ?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp la bande passante réseau - général (si vous ne pouvez pas tester vos propres)](remoteapp-bandwidthguidelines.md)