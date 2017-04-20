<properties 
    pageTitle="Comment rediriger les périphériques USB dans Azure RemoteApp ? | Microsoft Azure" 
    description="Apprenez à utiliser la redirection des périphériques USB dans Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Comment rediriger les périphériques USB dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

La redirection des périphériques permet aux utilisateurs d’utiliser les périphériques USB reliés à leur ordinateur ou d’un Tablet PC avec les applications RemoteApp de Azure. Par exemple, si vous avez partagé Skype par le biais de RemoteApp d’Azure, vos utilisateurs doivent pouvoir utiliser leurs appareils de périphérique.

Avant de poursuivre, assurez-vous de que lire les informations de redirection USB dans [à l’aide de la redirection dans Azure RemoteApp](remoteapp-redirection.md). Cependant, le recommandé nusbdevicestoredirect:s : * ne fonctionne pas pour les caméras web USB et peut ne pas fonctionner pour certaines imprimantes USB ou les périphériques multifonctions de USB. De conception et pour des raisons de sécurité, l’administrateur Azure RemoteApp doit activer la redirection par classe de périphérique GUID ou par ID d’instance de périphérique avant que les utilisateurs peuvent utiliser ces périphériques.

Bien que cet article traite de la redirection de caméra web, vous pouvez utiliser une approche similaire pour rediriger des imprimantes USB et autres périphériques multifonctions USB qui ne sont pas redirigés par la **nusbdevicestoredirect:s :*** commande.

## <a name="redirection-options-for-usb-devices"></a>Options de redirection des périphériques USB
RemoteApp Azure utilise des mécanismes très similaires pour la redirection des périphériques USB en tant que ceux qui sont disponibles pour les Services Bureau à distance. La technologie vous permet de choisir la méthode de redirection correcte pour un périphérique donné, pour obtenir le meilleur de haut niveau et un périphérique USB de RemoteFX redirection à l’aide de la **usbdevicestoredirect:s :** commande. Il existe quatre éléments à cette commande :

| Ordre de traitement | Paramètre           | Description                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Sélectionne tous les périphériques qui ne sont pas prises en compte par la redirection de haut niveau. Remarque : par défaut, * ne fonctionne pas pour les webcams USB.  |
|                  | {GUID de classe périphérique} | Sélectionne tous les périphériques qui correspondent à la classe d’installation de périphérique spécifié.                                                           |
|                  | USB\InstanceID      | Sélectionne un périphérique USB spécifié pour l’ID d’instance donné.                                                                  |
| 2                | -USB\Instance ID    | Supprime les paramètres de redirection pour le périphérique spécifié.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Redirection d’un périphérique USB à l’aide de la classe de périphérique GUID
Il existe deux façons de trouver la classe de périphérique GUID qui peut être utilisé pour la redirection. 

La première option consiste à utiliser la [Définies Device installation de Classes disponibles pour les fournisseurs](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Sélectionnez la classe qui correspond le mieux à l’équipement relié à l’ordinateur local. Pour les appareils photo numériques, cela peut être une classe de périphérique d’image ou de la classe de périphérique de Capture vidéo. Vous devrez effectuer des tests avec les classes de périphériques pour rechercher la GUID qui fonctionne avec le périphérique USB connecté localement (dans notre cas, la caméra web) de la classe correcte.

Une meilleure méthode, ou la deuxième option consiste à procédez comme suit pour trouver le GUID de la classe spécifique de périphérique :

1. Ouvrez le Gestionnaire de périphériques, recherchez le périphérique qui est redirigé et droit et puis ouvrez les propriétés.
![Ouvrez le Gestionnaire de périphériques](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Sous l’onglet **Détails** , cliquez sur la propriété **Guid de la classe**. La valeur qui s’affiche est le GUID de la classe pour ce type de périphérique.
![Propriétés de la caméra](./media/remoteapp-usbredir/ra-classguid.png)
3. La valeur Guid de classe permet de rediriger les périphériques qui lui correspondent.

Par exemple :

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Vous pouvez combiner plusieurs redirections de périphérique dans l’applet de commande même. Par exemple : pour rediriger le stockage local et une webcam USB, applet de commande se présente comme suit :

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Lorsque vous configurez la redirection de périphérique par le GUID de la classe, tous les périphériques qui correspondent à ce GUID de classe dans la collection spécifiée sont redirigés. Par exemple, s’il y a plusieurs ordinateurs sur le réseau local qui ont les mêmes webcams USB, vous pouvez exécuter une applet de commande unique pour rediriger toutes les caméras web.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Redirection d’un périphérique USB à l’aide de l’ID d’instance de périphérique

Si vous souhaitez un contrôle plus précis et que vous souhaitez contrôler la redirection par périphérique, vous pouvez utiliser le paramètre de redirection de **USB\InstanceID** .

La partie la plus difficile de cette méthode consiste à trouver l’ID d’instance périphérique USB. Vous devez accéder à l’ordinateur et le périphérique USB. Ensuite, procédez comme suit :

1. Activer la redirection de périphérique dans la Session Bureau à distance, comme décrit dans [comment utiliser mes périphériques et mes ressources dans une session Bureau à distance ?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Ouvrez une connexion Bureau à distance, puis cliquez sur **Afficher les Options**.
3. Cliquez sur **Enregistrer sous** pour enregistrer les paramètres de connexion actuels dans un fichier RDP.  
    ![Enregistrer les paramètres sous la forme d’un fichier RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Choisissez un nom de fichier et un emplacement, par exemple « MyConnection.rdp » et « PC\Documents ce » et enregistrez le fichier.
5. Ouvrez le fichier MyConnection.rdp à l’aide d’un éditeur de texte et rechercher l’ID d’instance du périphérique que vous voulez rediriger.

Maintenant, utiliser l’ID d’instance dans l’applet de commande suivante :

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Aidez-nous à vous aider 
Saviez-vous qu’en plus de cet article d’évaluation et faire des commentaires vers le bas ci-dessous, vous pouvez modifier l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose de déroutant juste ? Faites défiler et cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux arriveront à nous pour révision, et puis, une fois que nous avons approuver les, vous verrez vos modifications et les améliorations ici.