<properties
    pageTitle="Mise en route de la chambre forte de clé Azure | Microsoft Azure"
    description="Utilisez ce didacticiel pour vous aider à démarrer avec chambre de clé Azure pour créer un conteneur de sécurité renforcé dans Azure, pour stocker et gérer des clés de chiffrement et des secrets dans Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Mise en route de la chambre forte de clé Azure #
Coffre-fort de clé Azure est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification en chambre forte de la clé](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à démarrer avec chambre de clé Azure pour créer un conteneur renforcé (un coffre-fort) dans Azure, pour stocker et gérer des clés de chiffrement et des secrets dans Azure. Il vous guide dans le processus d’utilisation de PowerShell d’Azure pour créer un coffre-fort contenant une clé ou un mot de passe que vous pouvez ensuite utiliser avec une application Azure. Il vous montre ensuite comment une application peut utiliser cette clé ou le mot de passe.

**Les temps estimé pour terminer :** 20 minutes

>[AZURE.NOTE]  Ce didacticiel n’inclut pas d’instructions sur la façon d’écrire l’application Azure que l’une des étapes comprend, à savoir comment autoriser une application à utiliser une clé ou un secret dans la chambre forte de clé.
>
>Ce didacticiel utilise Azure PowerShell. Pour les instructions de l’Interface de ligne de commande de plusieurs plates-formes, consultez [ce didacticiel équivalent](key-vault-manage-with-cli.md).

Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables

Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Un abonnement à Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
- PowerShell Azure, **une version minimale de 1.1.0**. Pour installer PowerShell d’Azure et l’associer à votre abonnement Azure, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md). Si vous avez déjà installé les Azure PowerShell et que vous ne connaissez pas la version, à partir de la console PowerShell d’Azure, tapez `(Get-Module azure -ListAvailable).Version`. Lorsque vous avez Azure PowerShell version 0.9.1 via la 0.9.8 installée, vous pouvez toujours utiliser ce didacticiel avec des modifications mineures. Par exemple, vous devez utiliser le `Switch-AzureMode AzureResourceManager` commande et certaines commandes Azure clé coffre-fort ont changé. Pour obtenir une liste des applets de commande en chambre forte de la clé pour les versions 0.9.1 à 0.9.8, consultez [Azure clé coffre-fort applets de commande](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Une application qui est configurée pour utiliser la clé ou le mot de passe que vous créez dans ce didacticiel. Un exemple d’application est disponible à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Pour obtenir des instructions, consultez le fichier Readme qui l’accompagne.


Ce didacticiel est conçu pour les débutants de PowerShell d’Azure, mais elle suppose que vous comprenez les concepts de base, tels que les modules, les applets de commande et les sessions. Pour plus d’informations, consultez [mise en route avec Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Pour obtenir une aide détaillée sur une applet de commande que vous voyez dans ce didacticiel, utilisez l’applet de commande **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Par exemple, pour obtenir de l’aide pour l’applet de commande de **Connexion-AzureRmAccount** , tapez :

    Get-Help Login-AzureRmAccount -Detailed

Vous pouvez également lire les didacticiels suivants pour familiariser avec le Gestionnaire de ressources Azure dans Azure PowerShell :

- [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md)
- [À l’aide de PowerShell Azure avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Se connecter à vos abonnements ##

Démarrer une session PowerShell d’Azure et vous connecter à votre compte Azure avec la commande suivante :  

    Login-AzureRmAccount 

Notez que si vous utilisez une instance spécifique de l’utilisation d’Azure, par exemple, les administrations d’Azure,-paramètre environnement avec cette commande. Par exemple :`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Dans la fenêtre contextuelle, entrez votre nom d’utilisateur de compte Azure et d’un mot de passe. PowerShell Azure Obtient tous les abonnements qui sont associés à ce compte et par défaut, utilise la première.

Si vous disposez de plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser pour la chambre forte de clé Azure, tapez ce qui suit pour afficher les abonnements de votre compte :

    Get-AzureRmSubscription

Ensuite, pour spécifier l’abonnement à utiliser, tapez :

    Set-AzureRmContext -SubscriptionId <subscription ID>

Pour plus d’informations sur la configuration d’Azure PowerShell, voir [Comment faire pour installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Créer un nouveau groupe de ressources ##

Lorsque vous utilisez le Gestionnaire de ressources Azure, toutes les ressources associées sont créés à l’intérieur d’un groupe de ressources. Nous allons créer un nouveau groupe de ressource nommé **ContosoResourceGroup** pour ce didacticiel :

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Créer un coffre-fort de clé ##

L’applet de commande [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) permet de créer un coffre-fort de clé. Cette applet de commande a trois paramètres obligatoires : un **nom de groupe de ressources**, un **nom de coffre-fort de clé**et l' **emplacement géographique**.

Par exemple, si vous utilisez le nom de la chambre forte de la **ContosoKeyVault**, le nom du groupe de ressources de **ContosoResourceGroup**et l’emplacement de l' **Asie de l’est**, tapez :

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

La sortie de cette applet de commande affiche les propriétés de la chambre forte clée que vous venez de créer. Les deux propriétés les plus importantes sont les suivantes :

- **Nom de coffre-fort**: dans l’exemple, il s’agit de **ContosoKeyVault**. Vous utiliserez ce nom pour d’autres applets de commande de stockage en chambre forte de clé.
- **URI du coffre-fort**: dans l’exemple, il s’agit de https://contosokeyvault.vault.azure.net/. Les applications qui utilisent votre coffre-fort via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer des opérations sur ce coffre-fort de clé. Encore, personne d’autre ne l’est.

>[AZURE.NOTE]  Si vous voyez l’erreur **l’abonnement n’est pas enregistré pour utiliser l’espace de noms « Microsoft.KeyVault »** lorsque vous essayez de créer votre clé coffre-fort, exécutez `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` , puis réexécutez la commande New-AzureRmKeyVault. Pour plus d’informations, consultez [Register-AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Ajouter une clé ou un secret dans la chambre forte de clé ##

Si vous souhaitez que le coffre-fort de clé Azure pour créer une clé protégé par le logiciel pour vous, utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) et tapez ce qui suit :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Toutefois, si vous disposez d’une clé existante logiciel protégé dans un. Fichier PFX à votre lecteur C:\ dans un fichier nommé softkey.pfx que vous souhaitez télécharger sur Azure coffre-fort de clé, tapez la commande suivante pour définir la variable **securepfxpwd** un mot de passe de **123** pour le. Fichiers PFX :

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Puis tapez la commande suivante pour importer la clé à partir de la. Fichier PFX, qui protège la clé par le logiciel dans le service de stockage en chambre forte de clé :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Vous pouvez maintenant référencer cette clé que vous avez créé ou téléchargé dans Azure coffre-fort de clé, à l’aide de son URI. Utilisez **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** pour obtenir la version actuelle et **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** pour obtenir cette version spécifique.  

Pour afficher l’URI de cette clé, tapez :

    $Key.key.kid

Pour ajouter un secret dans la chambre forte, qui est un mot de passe nommée SQLPassword et a la valeur Pa$ $w0rd Azure clé coffre-fort, convertissez d’abord la valeur de Pa$ $w0rd à une chaîne sécurisée en tapant ce qui suit :

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Ensuite, tapez le texte suivant :

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Vous pouvez maintenant référencer ce mot de passe que vous avez ajoutés à la chambre forte de clé Azure, à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/SQLPassword** pour obtenir la version actuelle et **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** pour obtenir cette version spécifique.

Pour afficher l’URI de ce mot de passe, tapez :

    $secret.Id

Examinons la clé ou le mot de passe que vous venez de créer :

- Pour afficher la clé, tapez :`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Pour afficher votre secret, tapez :`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Désormais, votre coffre-fort de clé et de la clé ou le secret est prêt pour les applications à utiliser. Vous devez autoriser les applications à utiliser.  

## <a id="register"></a>Inscrire une application avec Azure Active Directory ##

Cette étape devrait généralement être effectuée par un développeur, sur un ordinateur distinct. Il n’est pas spécifique à la chambre forte de clé Azure, mais il est inclus ici par souci d’exhaustivité.


>[AZURE.IMPORTANT] Pour terminer le didacticiel, votre compte, le coffre-fort et l’application que vous inscrivez lors de cette étape doivent tous être dans le même répertoire Azure.

Les applications qui utilisent un coffre-fort clé doivent s’authentifier à l’aide d’un jeton d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans leur Azure d’Active Directory. À la fin de l’enregistrement, le propriétaire de l’application obtient les valeurs suivantes :


- Un **ID de l’Application** (également appelé ID de Client) et une **clé d’authentification** (également connu sous le nom, le secret partagé). L’application doit présenter ces deux valeurs pour Azure d’Active Directory, afin d’obtenir un jeton. Comment l’application est configurée pour cela dépend de l’application. Pour l’exemple d’application en chambre forte de la clé, le propriétaire de l’application définit ces valeurs dans le fichier app.config.

Pour enregistrer l’application dans Azure Active Directory :

1. Connectez-vous au portail Azure classique.
2. Sur la gauche, cliquez sur **Active Directory**, puis sélectionnez le répertoire dans lequel vous allez enregistrer votre application. <br> <br> **Remarque :** Vous devez sélectionner le même répertoire que celui qui contient l’abonnement Azure avec lequel vous avez créé votre clé coffre-fort. Si vous ne savez pas quel répertoire cela est, cliquez sur **paramètres**, identifier l’abonnement avec lequel vous avez créé votre coffre-fort clé et notez le nom du répertoire s’affiché dans la dernière colonne.

3. Cliquez sur **APPLICATIONS**. Si aucune application n’ont été ajoutées à votre répertoire, cette page affiche uniquement le lien **Ajouter une application** . Cliquez sur le lien, ou vous pouvez également cliquer sur la commande **Ajouter** sur la barre de commandes.
4.  Dans l’Assistant **Ajouter une APPLICATION** , sur le **que voulez-vous faire ?** , cliquez sur **Ajouter une application à mon entreprise se développe**.
5.  Sur la page de **nous dire sur votre application** , spécifiez un nom pour votre application et sélectionnez **WEB APPLICATION et/ou WEB API** (il s’agit de la valeur par défaut). Cliquez sur l’icône **suivante** .
6.  Sur la page de **Propriétés de l’application** , spécifiez **l’URL de la session** et d’un **URI d’ID APP** pour votre application web. Si votre application ne dispose pas de ces valeurs, vous pouvez les rendre pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com pour les deux zones). Peu importe si ces sites existent. Ce qui est important, c’est que l’identificateur URI d’application pour chaque application est différente pour chaque application dans votre répertoire. Le répertoire utilise cette chaîne pour identifier votre application.
7.  Cliquez sur **Terminer** pour enregistrer vos modifications dans l’Assistant.
8.  Dans la page de **Démarrage rapide** , cliquez sur **configurer**.
9.  Faites défiler jusqu'à la section **clés** , sélectionnez la durée et puis cliquez sur **Enregistrer**. La page s’actualise et affiche à présent une valeur de clé. Vous devez configurer votre application avec cette valeur de clé et la valeur de **l’ID de CLIENT** . (Les instructions pour cette configuration sont spécifiques à l’application).
10. Copier la valeur d’ID de client à partir de cette page, que vous utiliserez à l’étape suivante pour définir les autorisations sur votre coffre-fort.

## <a id="authorize"></a>Autorisez l’application à utiliser la clé ou le secret ##

Pour autoriser l’application à accéder à la clé ou le secret dans la chambre forte, utilisez l’applet de commande  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Par exemple, si le nom de votre coffre-fort est **ContosoKeyVault** et l’application que vous souhaitez autoriser possède un ID de client de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, et que vous souhaitez autoriser l’application à décrypter et signer avec des clés dans votre chambre forte, exécutez la commande suivante :


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Si vous souhaitez autoriser cette même application à lire des secrets dans votre chambre forte, exécutez la commande suivante :


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM) ##

Pour la garantie, vous pouvez importer, ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne jamais laisser la frontière HSM. Les modules HSM sont FIPS 140-2 de niveau 2 est validé. Si cette exigence ne s’applique pas à vous, ignorez cette section et passez à [Supprimer le coffre-fort de clé et les clés associées et les secrets](#delete).

Pour créer ces clés protégées par HSM, vous devez utiliser le [niveau de service Premium de coffre-fort Azure clé pour prendre en charge les clés de HSM protégé](https://azure.microsoft.com/pricing/free-trial/). En outre, notez que cette fonctionnalité n’est pas disponible pour la Chine d’Azure.


Lorsque vous créez le coffre-fort clé, ajouter le paramètre **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Vous pouvez ajouter des logiciels protégés par les clés (comme indiqué plus haut) et protégé par HSM ce coffre-fort de clé. Pour créer une clé protégée par HSM, définissez la **-Destination** paramètre « HSM » :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un. Fichier PFX sur votre ordinateur. Cette commande importe la clé dans les modules HSM dans le service de stockage en chambre forte de clé :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


La commande suivante importe un « apportez votre propre clé » package de (BYOK). Ce scénario vous permet de générer votre clé dans votre local HSM et le transférer vers HSM dans le service de stockage en chambre forte de clé, sans la clé de laisser la frontière HSM :

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Pour obtenir des instructions plus détaillées sur la façon de générer ce package BYOK, reportez-vous à la section [comment générer et transfert des clés protégées par HSM pour Azure clé coffre-fort](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Supprimer le coffre-fort de clé et les clés associées et les secrets ##

Si vous n’avez plus besoin le coffre-fort de clé et la clé ou le secret qu’il contient, vous pouvez supprimer le coffre-fort de clé à l’aide de l’applet de commande [Remove-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Ou bien, vous pouvez supprimer un groupe de ressources Azure entière, ce qui inclut la chambre forte de clé et d’autres ressources que vous avez inclus dans ce groupe :

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Autres applets de commande PowerShell Azure ##

Autres commandes qui peut être utile pour la gestion de coffre-fort de clé Azure :

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Cette commande Obtient un affichage sous forme de tableau de toutes les clés et les propriétés sélectionnées.
- `$Keys[0]`: Cette commande affiche une liste complète des propriétés pour la clé spécifiée
- `Get-AzureKeyVaultSecret`: Cette commande affiche un affichage tabulaire de tous les noms de secret et de propriétés sélectionnées.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Exemple comment faire pour supprimer une clé spécifique.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Exemple comment supprimer un secret spécifique.


## <a id="next"></a>Étapes suivantes ##

Pour un didacticiel de suivi qui utilise Azure clé coffre-fort dans une application web, consultez [Utilisation Azure clé Vault à partir d’une Application Web](key-vault-use-from-web-application.md).

Pour voir comment votre coffre-fort clé est utilisé, voir [Enregistrement d’Azure clé coffre-fort](key-vault-logging.md).

Pour obtenir une liste des applets de commande PowerShell de Azure dernière pour Azure clé coffre-fort, consultez [Azure clé coffre-fort applets de commande](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Pour les références de programmation, consultez [le coffre-fort de clé Azure guide du développeur](key-vault-developers-guide.md).
