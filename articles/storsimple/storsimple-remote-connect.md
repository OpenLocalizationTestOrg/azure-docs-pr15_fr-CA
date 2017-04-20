<properties 
   pageTitle="Se connecter à distance à votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment configurer votre périphérique pour la gestion à distance et comment se connecter à Windows PowerShell pour StorSimple de via HTTP ou HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Se connecter à distance à votre périphérique de StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser l’accès distant de Windows PowerShell pour vous connecter à votre périphérique de StorSimple. Lorsque vous vous connectez de cette manière, vous ne verrez pas un menu. (Vous voyez un menu que si vous utilisez la console série sur le périphérique de connexion). Avec l’accès distant de Windows PowerShell, vous vous connectez à une instance d’exécution spécifique. Vous pouvez également spécifier la langue d’affichage. 

Pour plus d’informations sur l’utilisation de la communication à distance de Windows PowerShell pour gérer le périphérique, accédez à [Windows PowerShell d’utilisation de StorSimple administrer votre périphérique StorSimple](storsimple-windows-powershell-administration.md).

Ce didacticiel explique comment configurer votre périphérique pour la gestion à distance et se connecter à Windows PowerShell pour StorSimple. Vous pouvez utiliser HTTP ou HTTPS pour vous connecter via l’accès distant de Windows PowerShell. Toutefois, lorsque vous décidez comment se connecter à Windows PowerShell pour StorSimple, considérez les points suivants : 

- Se connectant directement à la console série du périphérique est sécurisée, mais la connexion à la console série sur les commutateurs de réseau n’est pas. Soyez vigilant par rapport au risque de sécurité lors de la connexion à la console série du périphérique sur les commutateurs de réseau. 

- Connexion via une session HTTP peut offrir de plus de sécurité que la connexion par l’intermédiaire de la console série sur le réseau. Bien qu’il ne s’agit pas de la méthode la plus sûre, il est acceptable de réseaux approuvés. 

- Connexion via une session HTTPS avec un certificat auto-signé est le plus sécurisé et l’option recommandée.

Vous pouvez vous connecter à distance à l’interface de Windows PowerShell. Toutefois, l’accès distant à votre appareil StorSimple de via l’interface de Windows PowerShell n’est pas activé par défaut. Vous devez tout d’abord activer la gestion à distance sur le périphérique, et ensuite sur le client qui est utilisé pour accéder à votre périphérique.

Les étapes décrites dans cet article ont été exécutées sur un système hôte exécutant Windows Server 2012 R2.

## <a name="connect-through-http"></a>Se connecter via HTTP

Connexion à Windows PowerShell pour StorSimple de via une session HTTP offre davantage de sécurité que la connexion par l’intermédiaire de la console série du périphérique StorSimple. Bien qu’il ne s’agit pas de la méthode la plus sûre, il est acceptable de réseaux approuvés.

Vous pouvez utiliser le portail classique Azure ou la console série pour configurer la gestion à distance. Sélectionnez les procédures suivantes :

- [Le portail classique Azure permet d’activer la gestion à distance via HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utilisez la console série pour activer la gestion à distance via HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Après avoir activé la gestion à distance, procédez comme suit pour préparer le client pour une connexion à distance.

- [Préparer le client pour une connexion à distance](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Le portail classique Azure permet d’activer la gestion à distance via HTTP 

Effectuez les opérations suivantes dans le portail Azure classique pour activer la gestion à distance sur HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Pour activer la gestion à distance via le portail classique Azure

1. Accéder aux **périphériques** > **configurer** pour votre périphérique.

2. Faites défiler jusqu'à la section de **Gestion à distance** .

3. **Oui**, définissez **Activer la gestion à distance** .

4. Vous pouvez maintenant choisir pour se connecter à l’aide de HTTP. (La valeur par défaut est de se connecter via le protocole HTTPS). Assurez-vous que le protocole HTTP est sélectionné.

    >[AZURE.NOTE] Connexion via HTTP est acceptable uniquement sur les réseaux approuvés.

6. Cliquez sur **Enregistrer** en bas de la page.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilisez la console série pour activer la gestion à distance via HTTP

Effectuez les opérations suivantes sur la console série du périphérique pour activer la gestion à distance.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Pour activer la gestion à distance via la console série du périphérique

1. Dans le menu de la console série, sélectionnez l’option 1. Pour plus d’informations sur l’utilisation de la console série sur le périphérique, accédez à [se connecter à Windows PowerShell pour StorSimple de via la console série du périphérique](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. À l’invite, tapez :`Enable-HcsRemoteManagement –AllowHttp`

3. Vous serez averti sur les vulnérabilités de sécurité de l’utilisation de HTTP pour se connecter au périphérique. Lorsque vous y êtes invité, confirmez en tapant **Y**.

4. Vérifiez que HTTP est activé en tapant :`Get-HcsSystem`

5. Vérifiez que le champ **RemoteManagementMode** contient **HttpsAndHttpEnabled**. L’illustration suivante montre ces paramètres dans PuTTY.

     ![Série HTTPS et HTTP activée](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Préparer le client pour une connexion à distance

Effectuez les opérations suivantes sur le client pour activer la gestion à distance.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Pour préparer le client connexion à distance

1. Démarrer une session Windows PowerShell en tant qu’administrateur.

2. Tapez la commande suivante pour ajouter l’adresse IP du périphérique StorSimple à la liste des hôtes approuvés du client : 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Remplacez <*device_ip*> avec l’adresse IP de votre périphérique ; par exemple : 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Tapez la commande suivante pour enregistrer les informations d’identification de périphérique dans une variable : 

     *$cred = get-Credential*

4. Dans la boîte de dialogue qui s’affiche :

    1. Entrez le nom d’utilisateur au format : *device_ip\SSAdmin*.
    2. Tapez le mot de passe administrateur périphérique a été définie lorsque le périphérique a été configuré avec l’Assistant d’installation. Le mot de passe par défaut est le *mot de passe1*.

7. Démarrez une session Windows PowerShell sur le périphérique en tapant cette commande :

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Pour créer une session de Windows PowerShell pour une utilisation avec le périphérique virtuel StorSimple, ajouter la `–Port` paramètre et indiquez le port public que vous avez configurés dans l’accès distant pour l’Appliance virtuelle de StorSimple.

     À ce stade, vous devez disposer d’une session de Windows PowerShell à distance active sur le périphérique.

    ![Remoting de PowerShell à l’aide de HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Se connecter via HTTPS

Connexion à Windows PowerShell pour StorSimple de via une session HTTPS est la méthode la plus sûre et recommandée de connexion à distance à votre périphérique Microsoft Azure StorSimple. Les procédures suivantes expliquent comment configurer les ordinateurs client et de la console séries afin que vous pouvez utiliser HTTPS pour vous connecter à Windows PowerShell pour StorSimple.

Vous pouvez utiliser le portail classique Azure ou la console série pour configurer la gestion à distance. Sélectionnez les procédures suivantes :

- [Le portail classique Azure permet d’activer la gestion à distance via HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utilisez la console série pour activer la gestion à distance via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Après avoir activé la gestion à distance, utilisez les procédures suivantes pour préparer l’hôte pour une gestion à distance et de connecter le périphérique à partir de l’hôte distant.

- [Préparation de l’hôte pour la gestion à distance](#prepare-the-host-for-remote-management)

- [Connecter le périphérique à partir de l’hôte distant](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Le portail classique Azure permet d’activer la gestion à distance via HTTPS

Effectuez les opérations suivantes dans le portail Azure classique pour activer la gestion à distance via HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Pour activer la gestion à distance via le protocole HTTPS à partir du portail classique Azure

1. Accéder aux **périphériques** > **configurer** pour votre périphérique.

2. Faites défiler jusqu'à la section de **Gestion à distance** .

3. **Oui**, définissez **Activer la gestion à distance** .

4. Vous pouvez maintenant choisir pour se connecter à l’aide de HTTPS. (La valeur par défaut est de se connecter via le protocole HTTPS). Assurez-vous que le protocole HTTPS est sélectionné. 

5. Cliquez sur **Télécharger le certificat de gestion à distance**. Spécifiez un emplacement pour enregistrer ce fichier. Vous devez installer ce certificat sur l’ordinateur client ou l’hôte que vous utiliserez pour vous connecter au périphérique.

6. Cliquez sur **Enregistrer** en bas de la page.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilisez la console série pour activer la gestion à distance via HTTPS

Effectuez les opérations suivantes sur la console série du périphérique pour activer la gestion à distance.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Pour activer la gestion à distance via la console série du périphérique

1. Dans le menu de la console série, sélectionnez l’option 1. Pour plus d’informations sur l’utilisation de la console série sur le périphérique, accédez à [se connecter à Windows PowerShell pour StorSimple de via la console série du périphérique](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. À l’invite, tapez : 

     `Enable-HcsRemoteManagement`

    Cela doit activer HTTPS sur votre périphérique.

3. Vérifiez que le protocole HTTPS a été activé en tapant : 

     `Get-HcsSystem`

    Assurez-vous que le champ **RemoteManagementMode** affiche **HttpsEnabled**. L’illustration suivante montre ces paramètres dans PuTTY.

     ![Série HTTPS activé](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. À partir de la sortie de `Get-HcsSystem`, copiez le numéro de série du périphérique et l’enregistrer pour une utilisation ultérieure.

    >[AZURE.NOTE] Le numéro de série correspond au nom CN du certificat.

5. Obtenir un certificat de gestion à distance en tapant : 
 
     `Get-HcsRemoteManagementCert`

    Un certificat semblable à la suivante s’affiche.

    ![Obtenir un certificat de gestion à distance](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copier les informations dans le certificat de **---début certificat---** **---fin certificat---** dans un éditeur de texte tel que le bloc-notes et enregistrez-le sous la forme d’un fichier .cer. (Vous allez copier ce fichier à votre hôte distant lors de la préparation de l’hôte).

    >[AZURE.NOTE] Pour générer un nouveau certificat, utilisez la `Set-HcsRemoteManagementCert` applet de commande.

### <a name="prepare-the-host-for-remote-management"></a>Préparation de l’hôte pour la gestion à distance

Pour préparer l’ordinateur hôte pour une connexion à distance qui utilise une session HTTPS, procédez comme suit :

- [Importer le fichier .cer dans le magasin racine de l’hôte distant ou le client](#to-import-the-certificate-on-the-remote-host).

- [Ajouter les numéros de série de périphérique au fichier hosts sur l’hôte distant](#to-add-device-serial-numbers-to-the-remote-host).

Chacune de ces procédures est décrite ci-dessous.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Pour importer le certificat sur l’hôte distant

1. Cliquez sur le fichier .cer et sélectionnez **installer le certificat**. Cela démarre l’Assistant Importation de certificat.

    ![Assistant Importation de certificat 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Pour l' **emplacement de stockage**, sélectionnez **Ordinateur Local**, puis cliquez sur **suivant**.

3. Sélectionnez **placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir**. Naviguer dans le magasin racine de l’hôte distant, puis cliquez sur **suivant**.

    ![Assistant Importation de certificat 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Cliquez sur **Terminer**. Un message vous indiquant que l’importation a réussi s’affiche.

    ![Assistant Importation de certificat 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Pour ajouter des numéros de série de périphérique à l’hôte distant

1. Démarrer le bloc-notes en tant qu’administrateur et ouvrez le fichier hosts situé à \Windows\System32\Drivers\etc.

2. Ajoutez les trois entrées suivantes à votre fichier hosts : **adresse IP de données 0**, **adresse IP fixe du contrôleur 0**et **adresse IP fixe de contrôleur 1**.

3. Entrez le numéro de série du périphérique que vous avez enregistré précédemment. Mapper sur l’adresse IP comme indiqué dans l’image suivante. Pour le contrôleur 0 et 1 de contrôleur, ajoutez **Controller0** et **: Contrôleur1** à la fin du numéro de série (nom CN).

    ![Ajout de nom CN au fichier hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Enregistrez le fichier d’hôtes.

### <a name="connect-to-the-device-from-the-remote-host"></a>Connecter le périphérique à partir de l’hôte distant

Utiliser Windows PowerShell et SSL pour entrer une session SSAdmin sur votre périphérique à partir d’un client ou un hôte distant. La session SSAdmin correspond à l’option 1 du menu de la [console série](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) du périphérique.

Effectuez la procédure suivante sur l’ordinateur à partir duquel vous voulez établir la connexion à distance de Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Pour entrer une session SSAdmin sur le périphérique à l’aide de Windows PowerShell et SSL

1. Démarrer une session Windows PowerShell en tant qu’administrateur.

2. Ajoutez l’adresse IP du périphérique pour les hôtes approuvés du client en tapant :

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Où <*device_ip*> est l’adresse IP de votre périphérique ; par exemple : 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Créez de nouvelles informations d’identification en tapant : 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Où <*adresse IP de l’équipement cible*> est l’adresse IP de données 0 pour votre périphérique ; par exemple, **10.126.173.90** comme indiqué dans l’image précédente du fichier hosts. Vous devez également fournir le mot de passe administrateur pour votre périphérique.

4. Créez une session en tapant :

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Pour le paramètre - ComputerName dans l’applet de commande, indiquez le <*numéro de série de l’équipement cible*>. Ce numéro de série a été mappé à l’adresse IP de données 0 dans le fichier hosts sur l’hôte distant ; par exemple, **SHX0991003G44MT** comme indiqué dans l’image suivante.

5. Type : 

     `Enter-PSSession $session`

6. Vous devrez attendre quelques minutes, et puis vous serez connecté à votre appareil via HTTPS via SSL. Vous verrez un message qui indique que vous êtes connecté à votre périphérique.

    ![Remoting de PowerShell à l’aide de HTTPS et SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur [l’utilisation de Windows PowerShell pour administrer votre périphérique StorSimple](storsimple-windows-powershell-administration.md).

- En savoir plus sur [le service de gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
