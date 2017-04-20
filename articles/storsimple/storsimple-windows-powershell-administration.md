<properties 
   pageTitle="PowerShell pour la gestion de périphérique StorSimple | Microsoft Azure"
   description="Apprenez à utiliser Windows PowerShell pour StorSimple pour gérer le périphérique StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utiliser Windows PowerShell pour StorSimple pour administrer votre périphérique

## <a name="overview"></a>Vue d’ensemble

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour gérer le périphérique Microsoft Azure StorSimple. Comme son nom l’indique, il s’agit d’une interface de ligne de commande basé sur Windows PowerShell, qui est intégrée dans une instance d’exécution limitée. Du point de vue de l’utilisateur au niveau de la ligne de commande, un espace d’exploitation contrainte apparaît sous la forme d’une version limitée de Windows PowerShell. Tout en conservant certaines fonctionnalités de base de Windows PowerShell, cette interface a des cmdlets de dédiées supplémentaires qui s’adressent à la gestion de votre périphérique Microsoft Azure StorSimple. 

Cet article décrit le Windows PowerShell pour les fonctionnalités de StorSimple, y compris la façon dont vous pouvez vous connecter à cette interface et contient des liens vers des procédures pas à pas ou de flux de travail que vous pouvez effectuer à l’aide de cette interface. Les flux de travail comprendre comment inscrire votre appareil, de configurer l’interface réseau sur le périphérique, d’installer des mises à jour nécessitant le périphérique en mode maintenance, modifier l’état du périphérique, et résoudre les problèmes que vous pouvez rencontrer.

Après avoir lu cet article, vous pourrez :

- Se connecter à votre périphérique de StorSimple à l’aide de Windows PowerShell pour StorSimple.

- Administrer votre appareil StorSimple à l’aide de Windows PowerShell pour StorSimple.

- Obtenir de l’aide dans Windows PowerShell pour StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell pour les applets de commande de StorSimple permettent de gérer votre appareil StorSimple à partir d’une console série ou à distance via l’accès distant de Windows PowerShell. Pour plus d’informations sur chacune des applets de commande individuels qui peuvent être utilisées dans cette interface, atteindre la [référence de l’applet de commande de Windows PowerShell pour StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Les applets de commande Azure PowerShell StorSimple sont une autre collection d’applets de commande qui vous permettent d’automatiser StorSimple niveaux de service et tâches de migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell de Azure pour StorSimple, accédez à la [référence d’applet de commande StorSimple d’Azure](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Vous pouvez accéder à la Windows PowerShell pour StorSimple à l’aide d’une des méthodes suivantes :

- [Se connecter à la console série du périphérique StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Se connecter à distance à StorSimple à l’aide de Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Se connecter à Windows PowerShell pour StorSimple via la console série du périphérique

Vous pouvez [Télécharger PuTTY](http://www.putty.org/) ou Terminal Server semblable logiciel d’émulation de se connecter à Windows PowerShell pour StorSimple. Vous devez configurer PuTTY spécifiquement pour accéder au périphérique Microsoft Azure StorSimple. Les rubriques suivantes contiennent les étapes détaillées sur la façon de configurer PuTTy et de se connecter au périphérique. Différentes options de menu de la console série sont également expliquées.

### <a name="putty-settings"></a>Paramètres de mastic

Assurez-vous que vous utilisez les paramètres suivants de mastic pour se connecter à l’interface de Windows PowerShell à partir de la console série.

#### <a name="to-configure-putty"></a>Pour configurer PuTTY

1. Dans la boîte de dialogue mastic **Reconfiguration** , dans le volet **catégorie** , sélectionnez **clavier**.

2. Assurez-vous que les options suivantes sont activées (ce sont les paramètres par défaut lorsque vous démarrez une nouvelle session). 

  	|Élément associé au clavier|Sélectionnez|
  	|---|---|
  	|Touche Retour arrière|Contrôle- ? (127)|
  	|Touches début et fin|Standard|
  	|Clavier et touches de fonction|ÉCHAP [n ~|
  	|État initial des touches du curseur|Normal|
  	|État initial du pavé numérique|Normal|
  	|Activer les fonctionnalités de clavier supplémentaires|Contrôle Alt GR diffère|

    ![Paramètres de mastic pris en charge](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Cliquez sur **Appliquer**.

4. Dans le volet **catégorie** , sélectionnez **traduction**.

5. Dans la zone de liste **du jeu de caractères à distance** , sélectionnez **UTF-8**.

6. Sous **Gestion des caractères de dessin au trait**, sélectionnez les **points de code Unicode d’utiliser dessin de ligne**. L’illustration suivante affiche les sélections de mastic correctes.

    ![UTF Putty paramètres](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Cliquez sur **Appliquer**.


Vous pouvez désormais utiliser PuTTY pour se connecter à la console série du périphérique en procédant comme suit.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>Sur la console série

Lorsque vous accédez à la Windows PowerShell interface de votre périphérique StorSimple de via la console série, un message de bannière s’affiche, suivi des options de menu. 

Le message de bannière contient des informations de périphérique StorSimple base telles que le nom, la version du logiciel installée et modèle état du contrôleur, vous accédez à. L’image suivante montre un exemple d’un message de bannière.

![Message de bannière de série](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Vous pouvez utiliser le message de bannière pour déterminer si le contrôleur que vous êtes connecté est actif ou passif.

L’image suivante montre les différentes options d’espace d’exploitation qui sont disponibles dans le menu de la console série.

![Enregistrer votre périphérique 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Vous pouvez choisir parmi les paramètres suivants :

1. **Ouvrez une session avec accès complet** Cette option vous permet de vous connecter (avec les informations d’identification correctes) à l’instance d’exécution **SSAdminConsole** sur le contrôleur local. (Le contrôleur local est le contrôleur auquel vous accédez actuellement via la console série du périphérique StorSimple.) Cette option peut également être utilisée pour permettre le Support Microsoft pour accéder à espace d’exploitation illimitée (une session de support) pour résoudre les problèmes de périphériques possible. Après avoir utilisé l’option 1 pour ouvrir une session, vous pouvez autoriser l’ingénieur du Support Microsoft accéder aux runspace illimitée en exécutant une applet de commande spécifique. Pour plus d’informations, reportez-vous à [Démarrer une session de prise en charge](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Connectez-vous au contrôleur de pair avec un accès complet** Cette option est identique à l’option 1, à ceci près que vous pouvez vous connecter (avec les informations d’identification correctes) à l’instance d’exécution **SSAdminConsole** sur le contrôleur de l’homologue. Le périphérique StorSimple étant un périphérique haute disponibilité avec deux contrôleurs dans une configuration active-passive, homologue fait référence à l’autre contrôleur dans le périphérique auquel vous accédez par le biais de la console série).
Similaire à l’option 1, cette option peut également être utilisée pour permettre à Microsoft de Support à accès illimité runspace sur un contrôleur de pair.

3. **Se connecter avec un accès limité** Cette option est utilisée pour accéder à interface Windows PowerShell en mode limité. Vous n’êtes pas invité pour les informations d’accès. Cette option se connecte à une instance d’exécution plus restreinte par rapport aux options 1 et 2.  Parmi les tâches qui sont disponibles par le biais de l’option 1 qui **ne peut pas* être effectuée dans cet espace d’exploitation sont :

    - Réinitialiser les paramètres d’usine
    - Modifier le mot de passe
    - Activer ou désactiver l’accès au support
    - Appliquer les mises à jour
    - Installation de correctifs 
                                                

    >[AZURE.NOTE] **Il s’agit de l’option par défaut si vous avez oublié le mot de passe administrateur et ne peut pas se connecter via l’option 1 ou 2.**

4. **Changez la langue** Cette option vous permet de modifier la langue d’affichage dans l’interface de Windows PowerShell. Les langues prises en charge sont l’anglais, japonais, russe, Français, coréen, espagnol, italien, allemand, chinois et portugais (Brésil).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Se connecter à distance à l’aide de Windows PowerShell pour StorSimple de StorSimple

Vous pouvez utiliser l’accès distant de Windows PowerShell pour vous connecter à votre périphérique de StorSimple. Lorsque vous vous connectez de cette manière, vous ne verrez pas un menu. (Vous menu n’apparaît que si vous utilisez la console série sur le périphérique pour se connecter. Connexion à distance pour accéder directement à l’équivalent de « option 1 – accès complet » sur la console série.) Avec l’accès distant de Windows PowerShell, vous vous connectez à une instance d’exécution spécifique. Vous pouvez également spécifier la langue d’affichage. 

La langue d’affichage est indépendante de la langue que vous définissez à l’aide de l’option **Modifier la langue** dans le menu de la console série. PowerShell distant reprendra automatiquement les paramètres régionaux du périphérique que vous vous connectez depuis si none est spécifié.

>[AZURE.NOTE] Si vous travaillez avec les hôtes virtuels Microsoft Azure et StorSimple des périphériques virtuels, vous pouvez utiliser l’accès distant de Windows PowerShell et de l’hôte virtuel pour vous connecter au périphérique virtuel. Si vous avez défini un emplacement de partage sur l’hôte sur lequel enregistrer les informations de la session Windows PowerShell, vous devez être conscient que l’entité de sécurité tout le monde comprend uniquement les utilisateurs authentifiés. Par conséquent, si vous avez configuré le partage pour permettre l’accès par tout le monde et de vous connectez sans spécifier des informations d’identification, l’entité de sécurité anonyme non authentifiée sera utilisée et vous verrez une erreur. Pour résoudre ce problème, sur le partage de l’hôte vous devez activer le compte invité et donnez à l’invité compte un accès total au partage ou vous devez spécifier les informations d’identification valides avec l’applet de commande Windows PowerShell.

Vous pouvez utiliser HTTP ou HTTPS pour vous connecter via l’accès distant de Windows PowerShell. Suivez les instructions dans les didacticiels suivants :

- [Se connecter à distance à l’aide de HTTP](storsimple-remote-connect.md#connect-through-http)
- [Se connecter à distance à l’aide de HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considérations de sécurité de connexion

Lorsque vous décidez comment se connecter à Windows PowerShell pour StorSimple, considérez les points suivants :

- Se connectant directement à la console série du périphérique est sécurisée, mais la connexion à la console série sur les commutateurs de réseau n’est pas. Soyez vigilant par rapport au risque de sécurité lors de la connexion à des périphériques série via des commutateurs de réseau.

- Connexion via une session HTTP peut offrir la plus grande sécurité que la connexion par l’intermédiaire de la console série sur réseau. Bien qu’il ne s’agit pas de la méthode la plus sûre, il est acceptable de réseaux approuvés.

- Connexion via une session HTTPS est le plus sécurisé et l’option recommandée.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrer votre périphérique StorSimple à l’aide de Windows PowerShell pour StorSimple
Le tableau suivant affiche un résumé de toutes les tâches de gestion courantes et des workflows complexes qui peuvent être effectuées dans l’interface Windows PowerShell de votre périphérique StorSimple. Pour plus d’informations sur chaque flux de travail, cliquez sur l’entrée appropriée dans le tableau.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell pour les workflows de StorSimple

|Si vous souhaitez le faire...|Utilisez cette procédure.|
|---|---|
|Inscrire votre appareil|[Configurer et inscrire l’appareil à l’aide de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurer le proxy web</br>Afficher les paramètres de proxy web|[Configurer le proxy web pour votre périphérique StorSimple](storsimple-configure-web-proxy.md)|
|Modifier les paramètres de l’interface réseau 0 données sur votre périphérique|[Modifier l’interface de réseau 0 de données de votre périphérique StorSimple](storsimple-modify-data-0.md)|
|Arrêter un contrôleur </br> Redémarrer ou arrêter à un contrôleur </br> Arrêter un périphérique</br>Réinitialisation du dispositif aux paramètres par défaut|[Gérer les contrôleurs de périphérique](storsimple-manage-device-controller.md)|
|Installer les correctifs et les mises à jour du mode de maintenance|[Mise à jour de votre périphérique](storsimple-update-device.md)|
|Mode de maintenance </br>Quitter le mode maintenance|[Modes du périphérique StorSimple](storsimple-device-modes.md)|
|Créer un package de prise en charge</br>Décrypter et modifier un lot de prise en charge|[Créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md)|
|Démarrer une session de prise en charge</br>|[Démarrer une session de prise en charge de Windows PowerShell pour StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtenir de l’aide dans Windows PowerShell pour StorSimple

Aide de l’applet de commande de Windows PowerShell pour StorSimple, est disponible. Une version à jour de cette aide en ligne est également disponible, qui vous permet de mettre à jour l’aide sur votre système.

Obtenir de l’aide de cette interface est semblable à celui de Windows PowerShell, et fonctionne dans la plupart des applets de commande relatives à l’aide. Vous pouvez trouver de l’aide pour Windows PowerShell en ligne dans la bibliothèque TechNet : [génération de scripts avec Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Voici une brève description des types d’aide pour cette interface de Windows PowerShell, y compris la mise à jour de l’aide.

#### <a name="to-get-help-for-a-cmdlet"></a>Pour obtenir de l’aide pour une applet de commande

- Pour obtenir de l’aide pour une applet de commande ou une fonction, utilisez la commande suivante :`Get-Help <cmdlet-name>`

- Pour obtenir de l’aide en ligne pour toute applet de commande, utilisez l’applet de commande précédente avec le `-Online` paramètre :`Get-Help <cmdlet-name> -Online`

- Pour obtenir une aide complète, vous pouvez utiliser la `–Full` paramètre et pour obtenir des exemples, vous devez utiliser le `–Examples` paramètre.

#### <a name="to-update-help"></a>Mise à jour d’aide

Vous pouvez facilement mettre à jour de l’aide dans l’interface de Windows PowerShell. Effectuez les opérations suivantes pour mettre à jour de l’aide sur votre système.

#### <a name="to-update-cmdlet-help"></a>Mettre à jour l’aide de l’applet de commande

1. Avec l’option **Exécuter en tant qu’administrateur** , démarrez Windows PowerShell.

1. À l’invite de commandes, tapez : `Update-Help`

1. Les fichiers d’aide mis à jour seront installés.

1. Une fois les fichiers d’aide sont installés, tapez : `Get-Help Get-Command`. Ceci affichera une liste des applets de commande pour lequel l’aide est disponible.


>[AZURE.NOTE] Pour obtenir une liste de toutes les cmdlets disponibles dans une instance d’exécution, ouvrez une session l’option de menu correspondante et exécuter la `Get-Command` applet de commande.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes avec votre périphérique StorSimple lors de l’exécution d’un des flux de travail ci-dessus, consultez [Outils de dépannage des déploiements StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

