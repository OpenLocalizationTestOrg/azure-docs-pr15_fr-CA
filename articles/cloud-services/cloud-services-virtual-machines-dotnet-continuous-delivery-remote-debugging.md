<properties
    pageTitle="Activer le débogage distant avec livraison continue | Microsoft Azure"
    description="Découvrez comment activer le débogage distant lors de l’utilisation de livraison continue à déployer vers Azure"
    services="cloud-services"
    documentationCenter=".net"
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>Activer le débogage distant lors de l’utilisation de livraison continue de publier vers Azure

Vous pouvez activer le débogage distant dans Azure, pour les services en nuage ou de machines virtuelles, lorsque vous utilisez la [livraison continue](cloud-services-dotnet-continuous-delivery.md) de publier sur Azure en suivant ces étapes.

## <a name="enabling-remote-debugging-for-cloud-services"></a>L’activation du débogage à distance des services en nuage

1. Sur l’agent de build, configurer l’environnement initial pour Azure comme indiqué dans [La génération de ligne de commande pour Azure](http://msdn.microsoft.com/library/hh535755.aspx).
2. Étant donné que l’exécution du débogage distant (msvsmon.exe) est requise pour le package, installez les [Outils à distance pour Visual Studio 2015](http://www.microsoft.com/en-us/download/details.aspx?id=48155) (ou les [Outils à distance pour Microsoft Visual Studio 2013 mise à jour 5](https://www.microsoft.com/en-us/download/details.aspx?id=48156) si vous utilisez Visual Studio 2013). Comme alternative, vous pouvez copier les fichiers binaires de débogage à distance à partir d’un système qui a installé de Visual Studio.
3. Créer un certificat, comme décrit dans [Vue d’ensemble des certificats pour les Services en nuage Azure](cloud-services-certs-create.md). Conserver les .pfx et l’empreinte de certificat du RDP et télécharger le certificat au service de cloud cible.
4. Utilisez les options suivantes dans la ligne de commande MSBuild pour générer et empaqueter avec débogage distant activé. (Remplacer les chemins d’accès réels de vos fichiers système et de projet pour les éléments entre parenthèses l’angle).

        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"

    `VSX64RemoteDebuggerPath`est le chemin d’accès au dossier contenant msvsmon.exe dans les outils à distance pour Visual Studio.
    `RemoteDebuggerConnectorVersion`est la version Azure SDK dans votre service cloud. Il doit également correspondre à la version installée avec Visual Studio.

5. Publier sur le service en nuage cible en utilisant le fichier de package et .cscfg généré à l’étape précédente.
6. Importer le certificat (fichier .pfx) sur l’ordinateur qui a Visual Studio avec Azure SDK pour .NET est installé. Veillez à importer dans le `CurrentUser\My` magasin de certificats, sinon y attacher le débogueur dans Visual Studio échoue.

## <a name="enabling-remote-debugging-for-virtual-machines"></a>L’activation du débogage à distance des machines virtuelles

1. Créer un ordinateur virtuel Azure. Reportez-vous à la section [créer un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [créer et gérer des Machines virtuelles Azure dans Visual Studio](../virtual-machines/virtual-machines-windows-classic-manage-visual-studio.md).
2. Sur la [page du portail classique d’Azure](http://go.microsoft.com/fwlink/p/?LinkID=269851), afficher le tableau de bord de la machine virtuelle pour voir de la machine virtuelle **Empreinte de certificat du RDP**. Cette valeur est utilisée pour le `ServerThumbprint` valeur dans la configuration de l’extension.
3. Créer un certificat de client, comme décrit dans [Vue d’ensemble des certificats pour les Services en nuage Azure](cloud-services-certs-create.md) (laissez le .pfx et l’empreinte de certificat du RDP).
4. Installer Powershell d’Azure (version 0.7.4 ou ultérieure) comme indiqué dans la [procédure pour installer et configurer Azure PowerShell](../powershell-install-configure.md).
5. Exécutez le script suivant pour activer l’extension RemoteDebug. Remplacez les chemins d’accès et les données personnelles de votre choix, par exemple votre nom d’abonnement, le nom du service et l’empreinte numérique.

    >[AZURE.NOTE] Ce script est configuré pour Visual Studio 2015. Si vous utilisez Visual Studio 2013, modifier la `$referenceName` et `$extensionName` affectations ci-dessous pour utiliser `RemoteDebugVS2013` (au lieu de `RemoteDebugVS2015`).

    <pre>
   Ajouter-AzureAccount

    Sélectionnez-AzureSubscription « Mon abonnement Microsoft »

    $vm = « mytestvm1 » de get-AzureVM - ServiceName-nom « mytestvm1 »

    $endpoints = @( ,@{Name="RDConnVS2013"; PublicPort = 30400 ; PrivatePort = 30398} ,@{Name="RDFwdrVS2013"; PublicPort = 31400 ; PrivatePort = 31398})  

    foreach ($endpoint dans $endpoints) {Add-AzureEndpoint - VM $vm-Name $endpoint. Nom - protocole tcp PublicPort - $endpoint. PublicPort - Port_local $endpoint. PrivatePort}

    $referenceName = « Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015 » $publisher = « Microsoft.VisualStudio.WindowsAzure.RemoteDebug » $extensionName = « RemoteDebugVS2015 » $version = « 1.* » $publicConfiguration = «<PublicConfig>< Connector.Enabled > true < /Connector.Enabled ><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>»

    $vm | Ensemble-AzureVMExtension `
    -ReferenceName $referenceName ` 
    -Publisher $publisher `
    -ExtensionName $extensionName ` 
    -Version $version ' PublicConfiguration - $publicConfiguration

    foreach ($extension dans $vm. MACHINE VIRTUELLE. ResourceExtensionReferences) {si (($extension. Nom de la référence - eq $referenceName) `
    -and ($extension.Publisher -eq $publisher) ` 
    - et ($extension. Nom du $extensionName - eq) '- et ($extension. Version - eq $version)) {$extension. ResourceExtensionParameterValues [0]. Clé = « config.txt » saut}}

    $vm | Mise à jour-AzureVM </pre>

6. Importer le certificat (.pfx) sur l’ordinateur qui a Visual Studio avec Azure SDK pour .NET est installé.
