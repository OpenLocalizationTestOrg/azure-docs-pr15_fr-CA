<properties 
    pageTitle="Configurer le codeur Telestream Wirecast pour envoyer un seule vitesse de transmission de flux | Microsoft Azure" 
    description="Cette rubrique indique comment configurer le codeur de live Wirecast pour envoyer un flux unique de vitesse de transmission à canaux AMS qui sont activés pour l ' encodage live. " 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="juliako;cenkdin;anilmur"/>

#<a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Le codeur Wirecast permet d’envoyer un flux direct de vitesse de transmission unique

> [AZURE.SELECTOR]
- [Wirecast](media-services-configure-wirecast-live-encoder.md)
- [Élémentaire Live](media-services-configure-elemental-live-encoder.md)
- [Tricaster](media-services-configure-tricaster-live-encoder.md)
- [FMLE](media-services-configure-fmle-live-encoder.md)

Cette rubrique indique comment configurer le codeur de live [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) pour envoyer un flux unique de vitesse de transmission à canaux AMS qui sont activés pour l ' encodage live.  Pour plus d’informations, consultez [utilisation des canaux qui sont activés pour effectuer l ' encodage Live avec Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

Ce didacticiel explique comment gérer les Azure Media Services (AMS) avec l’outil d’Azure Media Services Explorer (AMSE). Cet outil s’exécute uniquement sur un PC Windows. Si vous êtes sous Mac ou Linux, utilisez le portail Azure pour créer des [canaux](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) et des [programmes](media-services-portal-creating-live-encoder-enabled-channel.md#create-and-manage-a-program).


##<a name="prerequisites"></a>Conditions préalables

- [Créer un compte Azure Media Services](media-services-portal-create-account.md)
- Garantir un point de terminaison de diffusion en continu au moins une unité de transmission en continu alloué. Pour plus d’informations, voir [Gérer les points de terminaison de diffusion en continu dans un compte de Services multimédia](media-services-portal-manage-streaming-endpoints.md)
- Installez la dernière version de l’outil [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
- Lancez l’outil et vous connecter à votre compte de l’AMS.

##<a name="tips"></a>Conseils

- Si possible, utilisez une connexion à internet non configurable.
- Une bonne méthode empirique lors de la détermination des besoins en bande passante consiste à doubler les vitesses de transmission en continu. Alors que ce n’est pas obligatoire, il vous aide à atténuer l’impact de la congestion du réseau.
- Lors de l’utilisation du logiciel codeurs, fermez tous les programmes inutiles.


## <a name="create-a-channel"></a>Création d’un canal

1.  Dans l’outil AMSE, accédez à l’onglet **Live** et cliquez avec le bouton droit dans la zone de canal. Sélectionnez **créer une chaîne...** dans le menu.

![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Spécifiez un nom de canal, le champ description est facultatif. Sous paramètres de canal, sélectionnez **Standard** pour l’option d ' encodage Live, avec le protocole d’entrée la valeur **RTMP**. Vous pouvez laisser tous les autres paramètres est.


Assurez-vous que le **début du nouveau canal maintenant** est sélectionnée.

3. Cliquez sur **créer un canal**.
![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

>[AZURE.NOTE] Le canal peut prendre jusqu'à 20 minutes pour démarrer.

Pendant le démarrage de la chaîne, vous pouvez [configurer le codeur](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

>[AZURE.IMPORTANT] Notez que la facturation démarre dès que le canal est dans un état prêt. Pour plus d’informations, consultez [les États du canal](media-services-manage-live-encoder-enabled-channels.md#states).

##<a id=configure_wirecast_rtmp></a>Configurer le codeur Telestream Wirecast

Dans ce didacticiel, les paramètres de sortie suivants sont utilisés. Le reste de cette section décrit plus en détail les étapes de configuration. 

**Vidéo**:
 
- Codec : H.264 
- Profil : Haute (niveau de 4.0) 
- Débit : 5000 Kbits/s 
- Trame clé : 2 secondes (60 secondes) 
- Fréquence d’image : 30
 
**Audio**:

- Codec : AAC (LC) 
- Vitesse de transmission : 192 Kbits/s 
- Taux d’échantillonnage : 44,1 kHz


###<a name="configuration-steps"></a>Étapes de configuration

1. Ouvrez l’application de Telestream Wirecast sur l’ordinateur qui est utilisé et configuré pour la diffusion en continu de RTMP.
2. Configurer la sortie en accédant à l’onglet **sortie** et en sélectionnant les **Paramètres de sortie...**.
    
    Assurez-vous que la **Destination de sortie** est défini sur **Serveur RTMP**.
3. Cliquez sur **OK**.
4. Dans la page Paramètres, définissez le champ de **Destination** **Azure Media**services.
 
    Le profil d’encodage est présélectionné pour **Azure H.264 720 p 16:9 (1280 x 720)**. Pour personnaliser ces paramètres, sélectionnez l’icône engrenage à droite de la liste déroulante vers le bas, puis choisissez l’option **Prédéfini nouvelle**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)

5. Configurer les paramètres prédéfinis du codeur.

    Nom de la présélection et vérifiez les paramètres recommandés suivants :

    **Vidéo**
    
    - Codeur : MainConcept H.264
    - Images par seconde : 30
    - Vitesse de transmission moyenne : 5000 kbits/s (peut être ajusté en fonction des limitations de réseau)
    - Profil : Main
    - Image clé toutes : 60 images

    **Audio**

    - Vitesse de transmission cible : 192 kbits/s
    - Taux d’échantillonnage : 44,100 kHz
     
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)

6. Cliquez sur **Enregistrer**.

    Le champ de codage a maintenant le profil nouvellement créé disponible pour la sélection. 

    Assurez-vous que le nouveau profil est sélectionné.

7. Obtenir les URL d’entrée du canal affecté à Wirecast **Point de terminaison RTMP**.
    
    Accédez à l’outil AMSE et vérifier l’état d’achèvement de canal. Une fois que l’état a changé depuis le **début** , en cours **d’exécution**, vous pouvez obtenir l’URL d’entrée.
      
    Lorsque le canal est en cours d’exécution, cliquez avec le bouton droit sur le nom du canal, naviguer jusqu'à sensitif sur **Copier l’URL entrée dans le Presse-papiers** et sélectionnez **Les URL d’entrée principale**.  
    
    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)

8. Dans la fenêtre Wirecast **Des paramètres de sortie** , coller ces informations dans le champ **adresse** de la section de la sortie et attribuer un nom de flux de données. 


    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

9. Sélectionnez **OK**.

10. Sur l’écran principal de le **Wirecast** , vérifiez les sources d’entrée vidéo et audio sont prêtes et cliquez sur le **flux de données** dans le coin supérieur gauche.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

>[AZURE.IMPORTANT]Avant de cliquer sur des **flux de données**, vous **devez** vous assurer que le canal est prêt. 
>Assurez-vous également que pour ne pas laisser le canal dans un état prêt sans une contribution d’entrée d’alimentation pendant plus de 15 minutes de >.

##<a name="test-playback"></a>Test de lecture
  
1. Accédez à l’outil AMSE, et cliquez avec le bouton droit sur le canal doit être testée. Dans le menu, placez le pointeur sur la **lecture de l’aperçu** et sélectionnez **avec Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Si le flux de données s’affiche dans le lecteur, le codeur a été correctement configuré pour se connecter au système AMS. 

Si une erreur est reçue, le canal devra être réinitialisé et régler les paramètres de l’encodeur. Consultez la rubrique de [dépannage](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions.  

##<a name="create-a-program"></a>Créer un programme

1. Une fois la lecture du canal est confirmée, créez un programme. Sous l’onglet **Live** dans l’outil AMSE, cliquez avec le bouton droit dans la zone programme et sélectionnez **Créer un nouveau programme**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)

2. Le nom du programme et, si nécessaire, ajuster la **Longueur de la fenêtre Archive** (qui par défaut est 4 heures). Vous pouvez également spécifier un emplacement de stockage ou conservez la valeur par défaut.  
3. Cochez la case **Démarrer le programme maintenant** .
4. Cliquez sur **créer un programme**.  
  
    Remarque : La création d’un programme prend moins de temps que la création du canal.    
 
5. Une fois que le programme est en cours d’exécution, vérifiez la lecture en cliquant avec le bouton droit sur le programme et naviguer vers **l’ou les programmes de lecture** , puis **avec Azure Media Player**.  
6. Une fois confirmée, avec le bouton droit, cliquez sur le programme à nouveau et sélectionnez **Copier l’URL de sortie dans le Presse-papiers** (ou de récupérer ces informations à partir de l’option **paramètres et informations sur le programme** dans le menu). 

Le flux est maintenant prêt à être incorporé dans un lecteur ou distribué à une audience pour direct.  


## <a name="troubleshooting"></a>Résolution des problèmes
 
Consultez la rubrique de [dépannage](media-services-troubleshooting-live-streaming.md) pour obtenir des instructions. 

##<a name="media-services-learning-paths"></a>Cursus Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
