<properties
   pageTitle="Chiffrer les disques sur une VM Linux | Microsoft Azure"
   description="Comment chiffrer des disques sur une VM Linux à l’aide de la CLI d’Azure et le modèle de déploiement du Gestionnaire de ressources"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Chiffrer les disques sur une VM Linux à l’aide de la CLI d’Azure
Pour la sécurité d’une machine virtuelle () et de la conformité, les disques virtuels dans Azure peuvent être cryptés au repos. Les disques sont chiffrés à l’aide de clés de chiffrement sont sécurisées dans un coffre-fort de clé Azure. Vous pouvez contrôler ces clés de chiffrement et que vous pouvez auditer leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une VM Linux à l’aide de la CLI d’Azure et le modèle de déploiement du Gestionnaire de ressources.


## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin effectuer rapidement les tâches, les détails suivants de la section de la base de commandes pour crypter les disques virtuels sur votre machine virtuelle. Informations plus détaillées et contexte pour chaque étape vous pouvez trouver le reste du document, en [commençant ici](#overview-of-disk-encryption).

Vous devez la [Dernière Azure CLI](../xplat-cli-install.md) installé et connecté en utilisant le mode du Gestionnaire de ressources comme suit :

```
azure config mode arm
```

Dans les exemples suivants, remplacer les noms de paramètre d’exemple par vos propres valeurs. Incluent des exemples de noms de paramètre `myResourceGroup`, `myKeyVault`, et `myVM`.

Tout d’abord activer le fournisseur Azure clé coffre-fort dans votre abonnement Azure et créer un groupe de ressources. L’exemple suivant crée un nom de groupe de ressources `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Créer un coffre-fort de clé Azure. L’exemple suivant crée un coffre-fort de clé nommé `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Créer une clé de chiffrement dans votre chambre forte de clé et l’activer pour le cryptage de disque. L’exemple suivant crée une clé nommée `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Créer un point de terminaison à l’aide d’Azure Active Directory pour gérer l’authentification et l’échange de clés de chiffrement de clé de coffre-fort. La `--home-page` et `--identifier-uris` n’avez pas besoin des adresses routables réel. Pour le niveau de sécurité plus élevé, les secrets du client doivent être utilisés au lieu de mots de passe. L’infrastructure du langage commun Azure ne peut pas générer actuellement les secrets du client. Secrets du client ne peuvent pas être générés dans le portail Azure. L’exemple suivant crée un point de terminaison Azure Active Directory nommé `myAADApp` et utilise un mot de passe de `myPassword`. Spécifiez votre propre mot de passe comme suit :

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Remarque le `applicationId` indiquée dans la sortie de la commande précédente. Cet ID d’application est utilisé dans les étapes suivantes :

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Ajouter un disque de données à un ordinateur virtuel existant. L’exemple suivant ajoute un disque de données à un ordinateur virtuel nommé `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Examinez les détails de votre coffre-fort de clé et de la clé que vous avez créé. Vous devez le N° de clé de stockage en chambre forte, URI et la clé URL dans la dernière étape. L’exemple suivant passe en revue les détails pour un coffre-fort de clé nommé `myKeyVault` et la clé nommée `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crypter vos disques comme suit, saisir vos propres noms de paramètre dans l’ensemble :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

L’infrastructure du langage commun Azure ne fournit pas des erreurs détaillées au cours du processus de cryptage. Pour plus d’informations, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. La commande précédente a de nombreuses variables, et vous n’obtiendrez pas beaucoup indique pourquoi le processus échoue, un exemple de commande complète se présente comme suit :

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Enfin, examinez l’état de chiffrement à nouveau pour confirmer que vos disques virtuels ont maintenant été cryptés. L’exemple suivant vérifie l’état d’un ordinateur virtuel nommé `myVM` dans les `myResourceGroup` groupe de ressources :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Vue d’ensemble du chiffrement des disques
Disques virtuels sur des ordinateurs virtuels de Linux sont cryptés inactives à l’aide de [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Il n’y a aucun frais pour le chiffrement des disques virtuels dans Azure. Clés de chiffrement sont stockées dans le coffre-fort de clé Azure à l’aide du logiciel de protection, ou vous pouvez importer, ou générer vos clés dans les Modules de sécurité matérielle (HSM) certifiés FIPS normes de niveau 2 de 140-2. Vous gardez le contrôle de ces clés de chiffrement et que vous pouvez auditer leur utilisation. Ces clés de chiffrement sont utilisés pour chiffrer et déchiffrer des disques virtuels connectés à votre ordinateur virtuel. Un point de terminaison Azure Active Directory fournit un mécanisme sécurisé pour la délivrance de ces clés de chiffrement que les ordinateurs virtuels sont sous tension et hors tension.

Le processus de cryptage un ordinateur virtuel est le suivant :

1. Créer une clé de chiffrement dans un coffre-fort de clé Azure.
2. Configurer la clé de chiffrement pour être utilisable pour le chiffrement des disques.
3. Pour lire la clé de chiffrement dans le coffre-fort de clé Azure, créer un point de terminaison à l’aide d’Azure Active Directory avec les autorisations appropriées.
4. Exécutez la commande pour crypter vos disques virtuels, en spécifiant le point de terminaison Azure Active Directory et de la clé de chiffrement approprié à utiliser.
5. Le point de terminaison Azure Active Directory demande la clé de chiffrement requise Azure clé coffre-fort.
6. Les disques virtuels sont cryptés à l’aide de la clé de chiffrement fournie.


## <a name="supporting-services-and-encryption-process"></a>Prise en charge des services et des processus de cryptage
Le cryptage de disque repose sur les composants supplémentaires suivants :

- **Azure clé Vault** - utilisé pour sauvegarder les clés de chiffrement et les secrets utilisés pour le processus de cryptage et de décryptage de disque. 
  - S’il en existe, vous pouvez utiliser un coffre-fort de clé Azure existant. Vous n’êtes pas obligé de dédier un coffre-fort de clé pour le cryptage des disques.
  - Pour séparer les limites administratives et la visibilité de clé, vous pouvez créer un coffre-fort de clé dédié.
- **Azure Active Directory** - gère l’échange sécurisé de clés de chiffrement requis et de l’authentification pour les actions demandées. 
  - Vous pouvez généralement utiliser une instance Azure Active Directory existante pour héberger votre application. 
  - L’application est plus un point de terminaison pour les services en chambre forte de la clé et de la Machine virtuelle demander et obtenir émis les clés de cryptage appropriées. Vous développez une application qui s’intègre à Active Directory de Azure pas.


## <a name="requirements-and-limitations"></a>Configurations requises et restrictions
Scénarios pris en charge et les exigences pour le chiffrement de disque :

- Le suivant serveur Linux SKU - Ubuntu, CentOS, SUSE et SUSE Linux Enterprise Server (SLES) et Red Hat Enterprise Linux.
- Toutes les ressources (comme clé de stockage en chambre forte, compte de stockage et VM) doivent être dans la même région d’Azure et d’abonnement.
- Standard A, D, DS, G, GS séries et machines virtuelles.

Cryptage du disque dur n’est pas pris en charge dans les scénarios suivants :

- Ordinateurs virtuels de couche base.
- Machines virtuelles créées avec le modèle de déploiement standard.
- Désactivation du chiffrement de disque du système d’exploitation sur les ordinateurs virtuels de Linux.
- Mettre à jour les clés de chiffrement sur une VM Linux déjà cryptés.


## <a name="create-the-azure-key-vault-and-keys"></a>Créez le coffre-fort de clé Azure et les clés
Pour effectuer le reste de ce guide, vous devez la [Dernière Azure CLI](../xplat-cli-install.md) installé et connecté en utilisant le mode du Gestionnaire de ressources comme suit :

```
azure config mode arm
```

Dans les exemples de la commande, remplacez tous les paramètres de l’exemple avec votre propre nom, l’emplacement et les valeurs de clé. Les exemples suivants utilisent une convention de `myResourceGroup`, `myKeyVault`, `myAADApp`, etc..

La première étape consiste à créer un coffre-fort de clé Azure pour stocker vos clés de cryptage. Coffre-fort de clé Azure peut stocker des clés, des secrets ou des mots de passe qui vous permettent de les implémenter en toute sécurité dans vos applications et services. Pour le cryptage de disque virtuel, vous utilisez en chambre forte de la clé pour stocker une clé de chiffrement utilisée pour chiffrer ou déchiffrer vos disques virtuels. 

Activer le fournisseur Azure clé coffre-fort dans votre abonnement Azure, puis créer un groupe de ressources. L’exemple suivant crée un groupe de ressources appelé `myResourceGroup` dans les `WestUS` emplacement :

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Le coffre-fort de clé Azure contenant les clés de chiffrement et les ressources de compute associés tels que le stockage et la machine virtuelle elle-même doit résider dans la même région. L’exemple suivant crée une clé de stockage en chambre forte Azure nommé `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Vous pouvez stocker des clés de chiffrement à l’aide de logiciels ou protection du modèle de sécurité matériel (HSM). À l’aide d’un module HSM nécessite une clé de stockage en chambre forte de la prime. Il existe un coût supplémentaire à la création d’une prime en chambre forte de la clé au lieu de coffre-fort clé standard qui stocke les clés de logiciel protégé. Pour créer une clé de stockage en chambre forte de la prime, dans l’étape précédente, ajoutez `--sku Premium` à la commande. L’exemple suivant utilise des clés de logiciel protégé dans la mesure où nous avons créé un coffre-fort clé standard. 

Pour les deux modèles de la protection, la plateforme Azure doit disposer d’un accès pour demander les clés de chiffrement au démarrage de la machine virtuelle pour décrypter les disques virtuels. Créer une clé de cryptage dans le coffre-fort de la clé, puis de l’activer pour une utilisation avec le cryptage de disque virtuel. L’exemple suivant crée une clé nommée `myKey` , puis il permet pour le cryptage de disque :

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Créer l’application Azure Active Directory
Lorsque les disques virtuels sont chiffrées ou déchiffrées, un point de terminaison vous permet de gérer l’authentification et l’échange de clés de chiffrement de clé de coffre-fort. Ce point de terminaison, une application Azure Active Directory, permet la plateforme Azure demander les clés de chiffrement appropriés au nom de la machine virtuelle. Une instance d’Azure Active Directory par défaut est disponible dans votre abonnement, bien que de nombreuses organisations ont dédié de répertoires d’Azure Active Directory.

Comme vous ne créez pas une application Azure Active Directory complet, la `--home-page` et `--identifier-uris` dans l’exemple suivant, les paramètres n’avez pas besoin des adresses routables réel. L’exemple suivant spécifie également un mot de passe secret plutôt que de génération de clés à partir du portail Azure. En ce moment, la génération de clés ne peut être effectuée à partir de la CLI d’Azure. 

Créez votre application Azure Active Directory. L’exemple suivant crée une application nommée `myAADApp` et utilise un mot de passe de `myPassword`. Spécifiez votre propre mot de passe comme suit :

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Prenez note de la `applicationId` qui est renvoyée dans la sortie de la commande précédente. Cet ID d’application est utilisé dans certaines des étapes suivantes. Ensuite, créez un nom principal de service (SPN) afin que l’application est accessible au sein de votre environnement. Pour crypter ou décrypter des disques virtuels avec succès, les autorisations sur la clé de chiffrement stockée dans le coffre-fort de la clé doivent indiquer l’application Azure Active Directory pour lire les clés. 

Créer le SPN et définir les autorisations appropriées comme suit :

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Ajout d’un disque virtuel et examiner l’état de chiffrement
Pour chiffrer certains disques virtuels, vous permet d’ajouter un disque à un ordinateur virtuel existant. Ajouter un disque de données de 5 Go pour un ordinateur virtuel existant comme suit :

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Les disques virtuels ne sont pas actuellement chiffrés. Vérifier l’état de cryptage actuel de votre ordinateur virtuel comme suit :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Chiffrer des disques virtuels
Pour crypter les disques virtuels maintenant, vous réunir tous les composants précédents :

1. Spécifiez l’application Azure Active Directory et le mot de passe.
2. Spécifiez le coffre-fort de la clé pour stocker les métadonnées de vos disques chiffrés.
3. Spécifier les clés de chiffrement à utiliser pour le cryptage proprement dit et le déchiffrement.
4. Spécifiez si vous souhaitez crypter le disque du système d’exploitation, les disques de données ou tous.

Vous permet de passer en revue les détails de votre coffre-fort de clé Azure et la clé que vous avez créé, comme vous devez l’ID Vault clé, URI et puis clé URL dans la dernière étape :

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crypter vos disques virtuels à l’aide de la sortie de la `azure keyvault show` et `azure keyvault key show` les commandes comme suit :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Comme la commande précédente a de nombreuses variables, l’exemple suivant est la commande de référence :

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

L’infrastructure du langage commun Azure ne fournit pas des erreurs détaillées au cours du processus de cryptage. Pour plus d’informations, consultez `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` sur la machine virtuelle que vous chiffrez.

Enfin, vous permet de consulter l’état de cryptage à nouveau pour confirmer que vos disques virtuels ont maintenant été cryptés :

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>L’ajout de disques de données supplémentaires
Une fois que vous avez chiffré vos disques de données, vous pouvez ajouter ultérieurement des disques virtuels de votre machine virtuelle et également de les crypter. Lorsque vous exécutez le `azure vm enable-disk-encryption` de commande, l’incrémentation de la version de séquence à l’aide de la `--sequence-version` paramètre. Ce paramètre de version de séquence vous permet d’effectuer des opérations répétées sur l’ordinateur virtuel même.

Par exemple, vous permet d’ajouter un deuxième disque virtuel de votre machine virtuelle, comme suit :

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Réexécutez la commande pour crypter les disques virtuels, ce temps ajouter le `--sequence-version` paramètre et l’incrémentation de la valeur à partir de notre première exécution comme suit :

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur la gestion de coffre-fort de clé Azure, y compris la suppression de clés de chiffrement et de coffres-forts, voir [Gérer les Vault de clé à l’aide de CLI](../key-vault/key-vault-manage-with-cli.md).
- Pour plus d’informations sur le cryptage de disque, par exemple la préparation d’une VM personnalisée cryptée à télécharger vers Azure, consultez [Cryptage de disque d’Azure](../security/azure-security-disk-encryption.md).