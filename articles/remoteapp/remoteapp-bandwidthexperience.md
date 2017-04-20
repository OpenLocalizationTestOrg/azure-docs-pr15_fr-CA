<properties 
    pageTitle="RemoteApp Azure - comment la bande passante réseau et la qualité de profiter de travail ensemble ? | Microsoft Azure"
    description="Découvrez l’impact de la bande passante réseau dans Azure RemoteApp la qualité de l’utilisateur de votre expérience."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>RemoteApp Azure - comment la bande passante réseau et la qualité de profiter de travail ensemble ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Lorsque vous regardez la [bande passante globale](remoteapp-bandwidth.md) requise pour Azure RemoteApp, gardez à l’esprit les facteurs suivants : ils sont tous partie d’un système dynamique qui a une incidence sur l’expérience utilisateur globale. 

- **Bande passante réseau disponible et les conditions de réseau actuelles** - un jeu de paramètres (gigue, perte de temps de latence,) sur le même réseau à un moment donné peut influer sur l’application de diffusion en continu, c'est-à-dire un confort réduits. La bande passante disponible de votre réseau est une fonction de congestion, perte aléatoire, la latence, car tous ces paramètres affectent le mécanisme de contrôle de congestion, qui à son tour contrôle la vitesse de transmission pour éviter les collisions.  Par exemple, un réseau avec perte de données ou un réseau à latence élevée va améliorer l’expérience utilisateur incorrecte même sur un réseau avec une bande passante de 1 000 Mo. La perte et la latence varient en fonction du nombre d’utilisateurs qui se trouvent sur le même réseau et de ce que font les utilisateurs (par exemple, le visionnage de vidéos, de téléchargement ou de transfert de fichiers volumineux, l’impression).
- **Scénario d’utilisation** - l’expérience dépend de ce que font les utilisateurs en tant qu’individus et en tant que groupe sur le même réseau. Par exemple, la lecture d’une diapositive, une seule image à mettre à jour ; Si l’utilisateur fait défiler le contenu d’un document de texte skims, ils ont besoin d’un plus grand nombre de trames d’être mis à jour par seconde. La communication en arrière pour le serveur dans ce scénario finira par consommer davantage de bande passante réseau. Pensez également à un exemple extrême : plusieurs utilisateurs sont le visionnage de vidéos haute définition (comme la résolution de 4 Ko), contenant des appels de conférence HD, les jeux vidéo 3D ou fonctionne sur les systèmes de CAO. Tous ces éléments permettent même d’un réseau à bande passante élevée vraiment pratiquement inutilisable.
- **Résolution de l’écran et le nombre d’écrans** - plus de bande passante réseau est nécessaire pour la mise à jour complète des écrans plus gros que les petits écrans. La technologie sous-jacente ne fait du bon travail d’encodage et de transmettre uniquement les zones des écrans qui ont été mis à jour, mais de temps, l’écran entier doit être mis à jour. Lorsque l’utilisateur a un écran de résolution supérieur (résolution de 4K par exemple), cette mise à jour nécessite plus de bande passante à un écran avec une résolution inférieure (par exemple, 1024x768px). Cette même logique s’applique si vous utilisez plus d’un écran pour la redirection. La bande passante doit augmenter avec le nombre d’écrans.
- **Redirection du Presse-papiers et dispositif** - il s’agit d’un problème de pas très évident, mais dans de nombreux cas si un utilisateur stocke un grand bloc de données dans le Presse-papiers, il prend un peu de temps pour obtenir ces informations à transférer à partir du client Bureau à distance sur le serveur. L’expérience en aval peut être affecté par l’expérience d’envoyer le contenu du Presse-papiers en amont. En va de même pour la redirection de périphérique - si un scanneur ou une webcam produit beaucoup de données qui doivent être envoyé en amont vers le serveur, ou si une imprimante doit recevoir un document volumineux, stockage local doit être disponible pour une application s’exécutant dans le nuage pour copier un fichier volumineux, les utilisateurs peuvent remarquer de trames ignorées ou temporairement vidéo de « figé », car les données requises pour la redirection de périphérique augmente la bande passante réseau doit. 

Lorsque vous évaluez vos besoins en bande passante réseau, veillez à prendre en compte tous ces facteurs fonctionne comme un système.

Maintenant, revenez à l' [article de la bande passante réseau principal](remoteapp-bandwidth.md)ou passer au test de votre [bande passante réseau](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimer](remoteapp-bandwidth.md)

- [RemoteApp Azure - test de l’utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)

- [Azure RemoteApp la bande passante réseau - général (si vous ne pouvez pas tester vos propres)](remoteapp-bandwidthguidelines.md)