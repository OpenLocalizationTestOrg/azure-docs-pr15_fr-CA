<properties
    pageTitle="Résolution des problèmes de stockage Azure fichier | Microsoft Azure"
    description="Résolution des problèmes de stockage Azure fichier"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Résolution des problèmes de stockage Azure fichier

Cet article répertorie les problèmes courants liés au stockage de fichiers de Microsoft Azure lorsque vous vous connectez à partir de clients Windows et Linux. Il fournit également les causes possibles et les résolutions de ces problèmes.

**Problèmes généraux (qui se produit dans les clients Windows et Linux)**

- [Erreur de quota lorsque vous tentez d’ouvrir un fichier](#quotaerror)

- [Ralentissement des performances lorsque vous accédez à partir de Windows ou Linux à stockage Azure](#slowboth)

**Problèmes du client Windows**

- [Ralentissement des performances lorsque vous accédez à stockage Azure 8.1 de Windows ou de Windows Server 2012 R2](#windowsslow)

- [Erreur 53 tentative de montage d’un partage de fichier Azure](#error53)

- [Utilisation de nette a réussi, mais je ne vois pas le fichier Azure partager monté dans l’Explorateur Windows](#netuse)

- [Mon compte de stockage contient « / » et la net use Échec de la commande](#slashfails)

- [Mon service, d’application ne peut pas accéder à lecteur de fichiers d’Azure monté.](#accessfiledrive)

- [Recommandations supplémentaires pour optimiser les performances](#additional)

**Problèmes du client Linux**

- [Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le cryptage » pour télécharger/copier les fichiers aux fichiers d’Azure](#encryption)

- [Partage d’erreur de « L’hôte est hors service » sur un fichier existant, ou l’interpréteur de commandes se bloque lors de l’exécution des commandes de la liste sur le point de montage](#errorhold)

- [Erreur de montage de 115 lors de la tentative de montage Azure des fichiers sur la VM Linux](#error15)

- [VM Linux connaît des retards aléatoires dans des commandes telles que « ls »](#delayproblem)

## <a name="general-problems"></a>Problèmes généraux
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Erreur de quota lorsque vous tentez d’ouvrir un fichier

Dans Windows, vous recevez des messages d’erreur semblables aux suivants :

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Quota est insuffisant pour traiter cette commande.**

**La valeur de handle non valide GetLastError : 53**

Sous Linux, vous recevez des messages d’erreur semblables aux suivants :

**<filename>[autorisation refusée]**

**Dépassé du quota de disque**

#### <a name="cause"></a>Cause

Le problème se produit car vous avez atteint la limite supérieure de handles ouverts simultanées autorisées pour un fichier.

#### <a name="solution"></a>Solution

Réduire le nombre de descripteurs d’ouvrir simultanés en fermant certaines poignées et recommencez. Pour plus d’informations, consultez [Microsoft Azure Storage Performance et l’évolutivité de liste de contrôle](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Ralentissement des performances lorsque vous accédez à stockage de fichiers à partir de Windows ou Linux

- Si vous n’avez pas une exigence de taille d’e/s minimale spécifique, nous vous recommandons d’utiliser 1 Mo comme taille d’e/s pour des performances optimales.

- Si vous connaissez la taille finale du fichier vous étendez avec écritures et que votre logiciel ne dispose pas des problèmes de compatibilité lorsque la queue n’ont pas encore étée sur le fichier contenant les zéros non significatifs, puis définissez la taille du fichier à l’avance au lieu de chaque écriture est une écriture d’extension.

## <a name="windows-client-problems"></a>Problèmes du client Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Ralentissement des performances lors de l’accès au stockage de fichier à partir de 8.1 de Windows ou de Windows Server 2012 R2

Pour les clients qui exécutent Windows 8.1 ou Windows Server 2012 R2, assurez-vous que le correctif [KB3114025](https://support.microsoft.com/kb/3114025) est installé. Ce correctif améliore la création et fermer le handle de performances.

Vous pouvez exécuter le script suivant pour vérifier si le correctif a été installé sur :

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Si le correctif est installé, la sortie suivante s’affiche :

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0 x 1**

> [AZURE.NOTE]  Les images de Windows Server 2012 R2 dans Azure Marketplace ont le correctif logiciel KB3114025 installé par défaut à partir de décembre 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Recommandations supplémentaires pour optimiser les performances

Ne jamais créer ou ouvrir un fichier d’e/s mises en cache qui demande l’accès en écriture, mais pas l’accès en lecture. Autrement dit, lorsque vous appelez **CreateFile()**, ne spécifiez jamais seulement **GENERIC_WRITE**, mais toujours spécifier **GENERIC_READ | GENERIC_WRITE**. Une poignée en écriture seule ne peut pas mettre en cache localement, écritures de petite taille même le handle ouvert uniquement pour le fichier. Cela impose une baisse de performances sur les écritures de petite taille. Notez que le mode « a » à CRT **fopen()** ouvre un handle en écriture seule.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>« Erreur 53 » lorsque vous essayez de monter ou démonter un partage de fichier Azure

Ce problème peut être dû aux conditions suivantes :

#### <a name="cause-1"></a>Cause 1

« L’erreur système 53 s’est produite. L’accès est refusé. » Pour des raisons de sécurité, les connexions à des partages de fichiers d’Azure sont bloquées si le canal de communication n’est pas chiffré et que la tentative de connexion n’est pas effectuée du même centre de données sur lequel résident les partages de fichiers d’Azure. Cryptage de canal de communication n’est fourni que si le client de l’utilisateur du système d’exploitation ne prend pas en charge le cryptage de SMB. Cela est indiqué par un « l’erreur système 53 s’est produite. L’accès est refusé » message d’erreur lorsqu’un utilisateur essaie de monter un partage de fichiers en local ou à partir d’un autre centre de données. Windows 8, Windows Server 2012 et les versions ultérieures de chaque demande de négociation incluant SMB 3.0, qui prend en charge le cryptage.

#### <a name="solution-for-cause-1"></a>Solution pour la 1ère Cause

Se connecter à partir d’un client qui répond aux exigences de Windows 8, Windows Server 2012 ou une version ultérieure, ou qui se connectent à partir d’un ordinateur virtuel qui se trouve sur le même centre de données en tant que le compte de stockage Azure qui est utilisé pour le partage de fichiers d’Azure.

#### <a name="cause-2"></a>Cause 2

« L’erreur système 53 » lors du montage d’un partage de fichier Azure peut se produire si le Port 445 les communications sortantes vers le centre de données Azure fichiers sont bloquée. Cliquez [ici](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) pour voir un résumé de fournisseurs de services Internet que d’autoriser ou d’interdire l’accès à partir du port 445.

Bloquent le port Comcast et certaines organisations informatiques. Pour comprendre si c’est la raison pour laquelle le message « L’erreur système 53 », vous pouvez utiliser Portqry pour interroger le point de terminaison TCP:445. Si le point de terminaison TCP:445 est affichée comme filtrés, le port TCP est bloqué. Voici un exemple de requête :

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Si le TCP 445 bloqué par une règle de chemin d’accès réseau, vous verrez la sortie suivante :

**Le port TCP 445 (service microsoft-ds) : filtré**

Pour plus d’informations sur l’utilisation de Portqry, reportez-vous à la section [Description de l’utilitaire de ligne de commande Portqry.exe](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Solution de la Cause 2

Fonctionne avec votre service informatique pour ouvrir le Port 445 de sortantes vers les [plages IP d’Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Cause 3

« L’erreur système 53 » peut également être reçue si NTLMv1 communication est activée sur le client. Avec NTLMv1 activé crée un client moins sécurisé. Par conséquent, les communications seront bloquées pour les fichiers d’Azure. Pour vérifier s’il s’agit de la cause de l’erreur, vérifiez que la sous-clé de Registre suivante est définie sur une valeur de 3 :

HKLM\System\CurrentControlSet\Control\LSA > LmCompatibilityLevel.

Pour plus d’informations, voir la rubrique [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) sur TechNet.

#### <a name="solution-for-cause-3"></a>Solution pour la 3ème Cause

Pour résoudre ce problème, rétablissez la valeur de LmCompatibilityLevel dans la clé de Registre HKLM\SYSTEM\CurrentControlSet\Control\Lsa la valeur par défaut de 3.

Fichiers Azure prend en charge uniquement l’authentification NTLMv2. Assurez-vous que la stratégie de groupe est appliquée aux clients. Cela empêchera cette erreur ne se produise. Il est également considéré comme une meilleure pratique de sécurité. Pour plus d’informations, consultez [Comment faire pour configurer des clients pour utiliser NTLMv2 à l’aide de la stratégie de groupe](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

Le paramètre recommandé est **Envoyer les réponses NTLMv2 uniquement**. Cela correspond à une valeur de Registre de 3. Les clients utilisent uniquement l’authentification NTLMv2, et ils utilisent la sécurité de session NTLMv2 si le serveur la prend en charge. Les contrôleurs de domaine acceptent les authentifications LM, NTLM et NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Utilisation de nette a réussi mais ne voyez pas le fichier Azure partager monté dans l’Explorateur Windows

#### <a name="cause"></a>Cause

Par défaut, l’Explorateur Windows ne s’exécute pas en tant qu’administrateur. Si vous exécutez **Utilisation nette** à partir d’une invite de commandes administrateur, vous mappez le lecteur réseau « en tant qu’administrateur ». Étant donné que les lecteurs mappés sont centré sur l’utilisateur, le compte d’utilisateur qui est connecté n’affiche pas les lecteurs si ils sont montés sous un autre compte d’utilisateur.

#### <a name="solution"></a>Solution

Montez le partage à partir d’une ligne de commande non administrateur. Vous pouvez également suivre [cette rubrique de TechNet](https://technet.microsoft.com/library/ee844140.aspx) pour configurer la valeur de Registre **EnableLinkedConnections** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Mon compte de stockage contient « / » et la net use Échec de la commande

#### <a name="cause"></a>Cause

Lorsque la commande **net use** est exécutée dans l’invite de commandes (cmd.exe), il est analysé en ajoutant « / » comme une option de ligne de commande. Cela provoque l’échec du mappage du lecteur.

#### <a name="solution"></a>Solution

Vous pouvez utiliser une des étapes suivantes pour contourner le problème :

• Utilisez la commande PowerShell suivante :

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

À partir d’un fichier de commandes pour cela en tant que

`Echo new-smbMapping ... | powershell -command –`

• Placer des guillemets doubles autour de la clé pour contourner ce problème, sauf si « / » est le premier caractère. Si c’est le cas, utilisez le mode interactif et entrez votre mot de passe séparément ou régénérer vos clés pour obtenir une clé qui ne commence pas par le caractère de barre oblique (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Mon service, d’application ne peut pas accéder à lecteur de fichiers d’Azure monté

#### <a name="cause"></a>Cause

Les lecteurs sont montés par utilisateur. Si votre application ou votre service s’exécute sous un autre compte d’utilisateur, les utilisateurs ne voient le lecteur.

#### <a name="solution"></a>Solution

Monter le lecteur à partir du même compte d’utilisateur sous lequel l’application est. Cela peut être effectué à l’aide d’outils tels que psexec.

Vous pouvez également créer un utilisateur qui possède les mêmes privilèges que le compte de service ou un système de réseau et puis exécutez **cmdkey** et l' **utilisation de nette** sous ce compte. Le nom d’utilisateur doit être le nom de compte de stockage et de mot de passe doit être la clé de compte de stockage. Une autre option **net** Use consiste à passer le nom du compte de stockage et de la clé dans les paramètres de nom et de mot de passe utilisateur de la commande **net use** .

Après avoir suivi ces instructions, vous pouvez recevoir le message d’erreur suivant : « l’erreur système 1312 s’est produite. Une ouverture de session spécifiée n’existe pas. Il peut déjà avoir été arrêté » lorsque vous exécutez l' **Utilisation nette** pour le compte de service réseau ou système. Dans ce cas, assurez-vous que le nom d’utilisateur qui est passé à **l’utilisation de nette** inclut des informations sur le domaine (par exemple : « [nom du compte de stockage]. file.core.windows .net »).

## <a name="linux-client-problems"></a>Problèmes du client Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erreur « Vous copiez un fichier vers une destination qui ne prend pas en charge le cryptage »

#### <a name="cause"></a>Cause

Les fichiers chiffrés de BitLocker peuvent être copiées vers Azure fichiers. Toutefois, le stockage de fichiers ne gère pas EFS de NTFS. Par conséquent, vous utilisez probablement l’EFS dans ce cas. Si vous avez des fichiers cryptés par EFS, une opération de copie pour le stockage de fichiers peut échouer, à moins que la commande copy décrypte un fichier copié.

#### <a name="workaround"></a>Solution de contournement

Pour copier un fichier dans le stockage de fichiers, vous devez tout d’abord le décrypter. Pour cela, à l’aide d’une des méthodes suivantes :

• Utilisez **/d de copie**.

• Définissez la clé de Registre suivante :

- Chemin d’accès = HKLM\Software\Policies\Microsoft\Windows\System
- Type de valeur = DWORD
- Nom = CopyFileAllowDecryptedRemoteDestination
- Valeur = 1

Toutefois, notez que la définition de la clé de Registre affecte toutes les opérations de copie à des partages réseau.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Partage d’erreur de « L’hôte est hors service » sur un fichier existant, ou l’interpréteur de commandes se bloque lorsque vous exécutez des commandes de la liste sur le point de montage

#### <a name="cause"></a>Cause

Cette erreur se produit sur le client Linux, lorsque le client a été inactif pendant une longue période de temps. Lorsque cette erreur se produit, le client se déconnecte, et de l’expiration de la connexion du client.

#### <a name="solution"></a>Solution

Ce problème est maintenant résolu dans le noyau Linux en tant que partie du [jeu de modifications](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), backport en attente dans la distribution Linux.

Fonctionnement de résoudre ce problème, maintenir la connexion et évitez d’entrer dans un état d’inactivité, conserver un fichier dans le partage de fichiers de Azure que vous écrivez régulièrement. Il doit s’agir d’une opération d’écriture, telles que la date de création/modification de réécriture dans le fichier. Sinon, vous pouvez obtenir des résultats mis en cache et l’opération ne peut pas déclencher la connexion.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>« Monter erreur 115 » lorsque vous essayez de monter les fichiers Azure sur la VM Linux

#### <a name="cause"></a>Cause

Distributions Linux ne pas encore prennent en charge la fonctionnalité de chiffrement SMB 3.0. Dans certaines distributions, utilisateur peut recevoir un message d’erreur « 115 » s’ils tentent d’Azure de monter les fichiers à l’aide de SMB 3.0 grâce à une fonctionnalité manquante.

#### <a name="solution"></a>Solution

Si le client Linux SMB utilisé ne supporte pas le cryptage, Azure de monter les fichiers à l’aide de 2.1 SMB d’une VM Linux sur le même centre de données du compte de stockage de fichier.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>VM Linux connaît des retards aléatoires dans des commandes telles que « ls »

#### <a name="cause"></a>Cause

Cela peut se produire lorsque la commande mount n’inclut pas l’option **serverino** . Sans **serverino**, la commande ls exécute un **stat** sur chaque fichier.

#### <a name="solution"></a>Solution

Vérifiez le **serverino** dans l’entrée « / etc/fstab » :

azureuser.file.Core.Windows.NET/WMS/comer sur/home/sampledir type cifs (rw, nodev, relatime, vers = 2.1, s = ntlmssp, cache = nom d’utilisateur strict, = xxx, domaine = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Si l’option **serverino** n’est pas présente, démontez et monter à nouveau les fichiers Azure grâce à l’option **serverino** est sélectionnée.

## <a name="learn-more"></a>Pour en savoir plus

- [Mise en route de stockage Azure sous Windows](storage-dotnet-how-to-use-files.md)

- [Mise en route de stockage Azure sous Linux](storage-how-to-use-files-linux.md)
