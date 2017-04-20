<properties 
   pageTitle="Configurer le proxy web pour un périphérique StorSimple | Microsoft Azure"
   description="Apprenez à utiliser Windows PowerShell pour StorSimple pour configurer les paramètres de proxy web pour votre périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurer le proxy web pour votre périphérique StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment utiliser Windows PowerShell pour StorSimple pour configurer et afficher les paramètres de proxy web pour votre périphérique StorSimple. Les paramètres de proxy web sont utilisés par le périphérique StorSimple lors de la communication avec le nuage. Un serveur proxy web est utilisé pour ajouter une autre couche de sécurité, filtre, contenu, facilité de bande passante ou même à l’aide avec analytique du cache.

Proxy Web est une configuration facultative de votre périphérique StorSimple. Vous pouvez configurer le proxy web uniquement par le biais de Windows PowerShell pour StorSimple. La configuration est un processus en deux étapes comme suit :

1. Tout d’abord, vous configurez les paramètres de proxy web par le biais de l’Assistant d’installation ou de Windows PowerShell pour les applets de commande StorSimple.

2. Puis, vous activez les paramètres de proxy web configurés par le biais de Windows PowerShell pour les applets de commande StorSimple.

Une fois la configuration du proxy web est terminée, vous pouvez afficher les paramètres de proxy web configurés dans le service Microsoft Azure StorSimple Manager et le Windows PowerShell pour StorSimple. 

Après avoir lu ce didacticiel, vous pourrez :

- Configurer le serveur proxy web à l’aide de l’Assistant Installation et applets de commande
- Activer le proxy web à l’aide des applets de commande
- Afficher les paramètres de proxy web dans Azure portal classique
- Résoudre les erreurs lors de la configuration du proxy web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurer le proxy web par le biais de Windows PowerShell pour StorSimple

Vous utilisez une des opérations suivantes pour configurer les paramètres de proxy web :

- Assistant d’installation vous guide dans les étapes de configuration.

- Applets de commande Windows PowerShell pour StorSimple.

Chacune de ces méthodes est décrite dans les sections suivantes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurer le proxy web via l’Assistant d’installation

Vous pouvez utiliser l’Assistant d’installation vous guide dans les étapes de la configuration du serveur proxy web. Effectuez les étapes suivantes pour configurer le proxy web sur votre appareil.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Pour configurer le proxy web via l’Assistant d’installation

1. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès total** et fournir le **mot de passe administrateur**. Tapez la commande suivante pour démarrer une session de l’Assistant d’installation :

    `Invoke-HcsSetupWizard`

2. Si c’est la première fois que vous avez utilisé l’Assistant d’installation pour l’enregistrement du périphérique, vous devez configurer tous les paramètres réseau requis, jusqu'à ce que vous atteigniez la configuration du proxy web. Si votre périphérique est déjà enregistré, vous pouvez accepter tous les paramètres de configuration réseau jusqu'à ce que vous atteigniez la configuration du proxy web. Dans l’Assistant d’installation, lorsque vous êtes invité à configurer les paramètres de proxy web, tapez **Oui**.

3. Pour l' **URL du Proxy Web**, spécifiez l’adresse IP ou le nom de domaine pleinement qualifié (FQDN) de votre serveur proxy web et le numéro de port TCP que vous souhaitez utiliser lors de la communication avec le nuage de votre périphérique. Utilisez le format suivant :

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    Par défaut, le numéro de port TCP 8080 est spécifié.

4. Choisissez le type d’authentification **NTLM**, **Basic**ou **Aucun**. Base est l’authentification la moins sécurisée de la configuration du serveur proxy. NT LAN Manager (NTLM) est un protocole d’authentification hautement sécurisé et complexe qui utilise un système de messagerie à trois voies (parfois quatre si une intégrité supplémentaire n’est requise) pour authentifier un utilisateur. L’authentification par défaut est NTLM. Pour plus d’informations, consultez [base](http://hc.apache.org/httpclient-3.x/authentication.html) et [l’authentification NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **Dans le service Gestionnaire de StorSimple, les graphiques d’analyse de périphérique ne fonctionnent pas lorsque la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour le périphérique. Pour les plans de surveillance fonctionne, vous devrez vous assurer que l’authentification est définie sur aucun.**

5. Si vous utilisez l’authentification, fournir un **Nom d’utilisateur du Proxy Web** et un **Mot de passe du Proxy Web**. Vous devez également confirmer le mot de passe.

    ![Configurer le Proxy Web sur périphérique1 de StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Si vous enregistrez votre périphérique pour la première fois, passez à l’enregistrement. Si votre périphérique a déjà été enregistré, l’Assistant va être arrêté. Les paramètres configurés seront enregistrées.

Proxy Web est désormais également activé. Vous pouvez ignorer l’étape [Activer le proxy web](#enable-web-proxy) et accéder directement à [Afficher les paramètres de proxy web dans Azure portal classique](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurer les proxy web par le biais de Windows PowerShell pour les applets de commande StorSimple

Un autre moyen pour configurer les paramètres de proxy web est via le Windows PowerShell pour les applets de commande StorSimple. Effectuez les étapes suivantes pour configurer le proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Pour configurer le proxy web via des applets de commande

1. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès complet**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur**. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Fournir et confirmer le mot de passe lorsque vous y êtes invité, comme illustré ci-dessous.

    ![Configurer le Proxy Web sur StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Le proxy web est désormais configuré et doit être activé.

## <a name="enable-web-proxy"></a>Activer le proxy web

Proxy Web est désactivé par défaut. Après avoir configuré les paramètres de proxy web sur le périphérique StorSimple, vous devez utiliser le Windows PowerShell pour StorSimple pour activer les paramètres de proxy web.

> [AZURE.NOTE] **Cette étape ne seront pas nécessaire si l’Assistant d’installation vous permet de configurer le proxy web. Proxy Web est automatiquement activé par défaut après une session de l’Assistant d’installation.**

Dans Windows PowerShell pour StorSimple activer le proxy web sur votre périphérique, effectuez les opérations suivantes :

#### <a name="to-enable-web-proxy"></a>Pour activer le proxy web

1. Dans le menu de la console série, choisissez l’option 1, **Connectez-vous à l’aide d’un accès complet**. Lorsque vous y êtes invité, fournissez le **mot de passe administrateur**. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Enable-HcsWebProxy`

    Vous avez maintenant activé la configuration du proxy web sur le périphérique StorSimple.

    ![Configurer le Proxy Web sur StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Afficher les paramètres de proxy web dans Azure portal classique

Les paramètres de proxy web sont configurés par le biais de l’interface Windows PowerShell et ne peuvent pas être modifiées dans le portail classique. Vous pouvez, cependant, permet d’afficher ces paramètres configurés dans le portail classique. Effectuez les opérations suivantes pour afficher les proxy web.

#### <a name="to-view-web-proxy-settings"></a>Pour afficher les paramètres de proxy web
1. Accédez à **StorSimple Manager service > périphériques**. Sélectionnez et cliquez sur un périphérique et passez à **configurer**.
1. Faites défiler vers le bas sur la page de **configuration** pour la section de **paramètres de proxy Web** . Vous pouvez afficher les paramètres de proxy web configurés sur votre appareil StorSimple comme indiqué ci-dessous.

    ![Proxy Web de vue dans le portail de gestion](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Erreurs lors de la configuration du proxy web

Si les paramètres de proxy web ont été configurés de manière incorrecte, des messages d’erreur seront affichera à l’utilisateur de Windows PowerShell pour StorSimple. Le tableau suivant décrit certains de ces messages d’erreur, leurs causes probables et les actions recommandées.

|N° de série.|Erreur HRESULT Code|Causes possibles|Action recommandée|
|:---|:---|:---|:---|
|1.|0 x 80070001|Commande est exécutée à partir du contrôleur passif et qu’il n’est pas en mesure de communiquer avec le contrôleur actif.|Exécutez la commande sur le contrôleur actif. Pour exécuter la commande à partir du contrôleur passif, vous devrez corriger la connectivité à partir de passif au contrôleur active. Vous devez engager le Support Microsoft si cette connexion est interrompue.|
|2.|0x800710dd - l’identificateur d’opération n’est pas valide|Les paramètres de proxy ne sont pas pris en charge sur le périphérique virtuel de StorSimple.|Les paramètres de proxy ne sont pas pris en charge sur le périphérique virtuel de StorSimple. Il seulement peuvent être configurés que sur un périphérique physique StorSimple.|
|3.|0 x 80070057 - paramètre non valide|L’un des paramètres fournis pour les paramètres de proxy n’est pas valide.|L’URI n’est pas fournie dans le format correct. Utilisez le format suivant :`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706BA - serveur RPC non disponible|La cause est une des opérations suivantes :</br></br>Cluster n’est pas disponible.</br></br>DataPath service n’est pas en cours d’exécution.</br></br>La commande est exécutée à partir de contrôleur passif et qu’il n’est pas en mesure de communiquer avec le contrôleur actif.|Veuillez engager de Support Microsoft afin de garantir que le cluster est disponible et datapath service est en cours d’exécution.</br></br>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous devrez vous assurer que le contrôleur passif peut communiquer avec le contrôleur actif. Vous devez engager le Support Microsoft si cette connexion est interrompue.|
|5.|0x800706be - échoué de l’appel RPC|Cluster est arrêté.|Veuillez engager de Support Microsoft afin de garantir que le cluster est disponible.|
|6.|0x8007138f - ressources de cluster introuvable|Ressource de cluster service de plate-forme n’est pas trouvée. Cela peut se produire lors de l’installation n’était pas correcte.|Vous devrez peut-être effectuer une fabrique de réinitialisation sur votre périphérique. Vous devrez peut-être créer une ressource de la plate-forme. Veuillez contacter le Support Microsoft pour les étapes suivantes.|
|7.|0x8007138c - pas en ligne la ressource de cluster|Ressources de cluster de plate-forme ou datapath ne sont pas en ligne.|Veuillez contacter le Support Microsoft pour vous assurer que la ressource de service datapath et plate-forme sont en ligne.|

> [AZURE.NOTE] 
> 
> -  La liste des messages d’erreur n’est pas exhaustive. 
> - Erreurs liées aux paramètres de proxy web n’apparaîtra pas dans le portail Azure classique dans votre service de gestionnaire de StorSimple. S’il existe un problème avec le proxy web une fois la configuration terminée, l’état du périphérique indiquera alors **hors connexion** dans le portail classique. |

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes lors du déploiement de votre appareil ou de configuration des paramètres de proxy web, reportez-vous à [Dépannage de votre déploiement de périphérique StorSimple](storsimple-troubleshoot-deployment.md).

- Pour savoir comment utiliser le service Gestionnaire de StorSimple, accédez à [utiliser le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
