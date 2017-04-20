<properties 
    pageTitle="Guide de dépannage pour la diffusion en continu live | Microsoft Azure" 
    description="Cette rubrique fournit des suggestions sur la façon de résoudre les problèmes de diffusion en continu live." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2016"  
    ms.author="juliako"/>

#<a name="troubleshooting-guide-for-live-streaming"></a>Guide de dépannage pour la diffusion en continu live

Cette rubrique fournit des suggestions sur la façon de résoudre certains problèmes de diffusion en continu live.

## <a name="issues-related-to-on-premises-encoders"></a>Problèmes liés à des codeurs de locaux 

Cette section fournit des suggestions sur la façon de résoudre les problèmes liés à des codeurs sur place qui sont configurés pour envoyer un flux unique de vitesse de transmission sur les canaux AMS qui sont activés pour l ' encodage live.

###<a name="problem-would-like-to-see-logs"></a>Problème : Souhaiterait voir journaux 

- **Problème potentiel**: ne peut pas rechercher les journaux de codeur peut aider au débogage des problèmes.
    
    - **Telestream Wirecast**: vous pouvez généralement trouver les journaux dans C:\Users\{nom d’utilisateur} \AppData\Roaming\Wirecast\ 
    - **Live élémentaire**: vous trouverez a des liens vers les journaux sur le portail de gestion. Cliquez sur **statistiques**, puis les **journaux**. Sur la page **Fichiers de journal** , vous verrez une liste des journaux pour tous les éléments de l’événement en direct ; Sélectionnez celui correspondant à la session en cours. 
    - **Flash Media Encoder Live**: vous pouvez rechercher le **Répertoire journal...** en accédant à l’onglet **Journal de codage** .
    
###<a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problème : Il n’y a aucune option pour sortir un flux progressif

- **Problème potentiel**: le codeur en cours d’utilisation ne désentrelacer automatiquement. 

    **Étapes de dépannage**: recherchez une option de désentrelacement dans l’interface du codeur. Une fois désentrelacement est activé, vérifiez à nouveau les paramètres de sortie progressive. 
 
###<a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problème : Essayez plusieurs paramètres de sortie de codeur et il est toujours Impossible de se connecter. 

- **Problème potentiel**: canal de codage Azure n’a pas été réinitialisée correctement. 

    **Étapes de dépannage**: Assurez-vous que le codeur est n’est plus en exécutant un push de l’AMS, arrêter et réinitialiser le canal. Une fois en cours d’exécution, essayez de connecter votre codeur avec les nouveaux paramètres. Si cela ne résout toujours pas le problème, essayez de créer un nouveau canal entièrement, parfois les canaux peuvent être endommagés après que plusieurs tentatives infructueuses.  

- **Problème potentiel**: taille de groupe d’images l’ou les paramètres de l’image clé ne sont pas optimales. 

    **Étapes de dépannage**: intervalle de taille ou image-clé de groupe d’images recommandé est de 2 secondes. Certains codeurs de calculent ce paramètre en nombre d’images, tandis que d’autres utilisent des secondes. Par exemple : lors d’une sortie de 30 i/s, la taille du groupe d’images serait 60 images, ce qui équivaut à 2 secondes.  
     
- **Problème potentiel**: ports fermés sont le blocage du flux. 

    **Étapes de dépannage**: lors de la diffusion en continu via le protocole RTMP, vérifiez les paramètres de pare-feu ou proxy pour confirmer que les ports de sortie 1935 et 1936 sont ouverts. Lorsque vous utilisez le protocole RTP en continu, vérifiez que le port de sortie 2010 est ouvert. 


###<a name="problem-when-configuring-the-encoder-to-stream-with-the-rtp-protocol-there-is-no-place-to-enter-a-host-name"></a>Problème : Lors de la configuration de l’encodeur de flux avec le protocole RTP, il n’est pas de place pour entrer un nom d’hôte. 

- **Problème potentiel**: codeurs de nombreux RTP ne permettent pas de noms d’hôte et une adresse IP devront être acquis.  

    **Étapes de dépannage**: pour trouver l’adresse IP, ouvrez une invite de commande sur n’importe quel ordinateur. Pour ce faire, dans Windows, ouvrez le Lanceur d’exécution (WIN + R) et tapez « cmd » pour l’ouvrir.  

    Une fois l’invite de commandes ouverte, tapez « Ping [nom d’hôte de l’AMS] ». 

    Le nom d’hôte peut être dérivé en omettant le numéro de port de l’Azure acquisition URL, comme illustré dans l’exemple suivant : 

    RTP://Test2-amstest009.RTP.Channel.mediaservices.Windows.NET:2010 / 

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle10.png)

###<a name="problem-unable-to-playback-the-published-stream"></a>Problème : Impossible de lecture de flux de données publié.
 
- **Problème potentiel**: il n’existe aucun point de terminaison de diffusion en continu en cours d’exécution, ou il n’y a aucune diffusion unités (échelle) allouées. 

    **Étapes de dépannage**: accédez à l’onglet « Point de terminaison en continu » dans l’outil AMSE et confirmez il y a un point de terminaison de diffusion en continu avec une unité de transmission en continu. 
    


>[AZURE.NOTE] Si après avoir appliqué les étapes de dépannage que vous ne pouvez pas toujours avec succès le flux, envoyer un ticket de support via le portail Azure.

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
