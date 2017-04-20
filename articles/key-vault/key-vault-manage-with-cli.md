<properties
    pageTitle="Gérer le stockage en chambre forte de clé à l’aide de la CLI | Microsoft Azure"
    description="Utilisez ce didacticiel pour automatiser les tâches courantes dans le coffre-fort de la clé à l’aide de l’interface CLI"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Gérer le stockage en chambre forte de clé à l’aide de la CLI #
Coffre-fort de clé Azure est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification en chambre forte de la clé](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction  
Utilisez ce didacticiel pour vous aider à démarrer avec chambre de clé Azure pour créer un conteneur renforcé (un coffre-fort) dans Azure, pour stocker et gérer des clés de chiffrement et des secrets dans Azure. Il vous guide dans le processus d’utilisation d’Interface de ligne de commande multiplateforme Azure pour créer un coffre-fort contenant une clé ou un mot de passe que vous pouvez ensuite utiliser avec une application Azure. Il vous indique ensuite comment une application peut ensuite utiliser cette clé ou le mot de passe.

**Les temps estimé pour terminer :** 20 minutes

>[AZURE.NOTE]  Ce didacticiel n’inclut pas d’instructions sur la façon d’écrire l’application Azure que l’une des étapes contient, qui montre comment autoriser une application à utiliser une clé ou un secret dans la chambre forte de clé.
>
>Actuellement, vous ne pouvez pas configurer Azure clé coffre-fort dans le portail Azure. À la place, utilisez les instructions de l’Interface de ligne de commande multiplateforme. Ou, pour obtenir des instructions PowerShell d’Azure, consultez [ce didacticiel équivalent](key-vault-get-started.md).

Pour plus d’informations sur le coffre-fort de clé d’Azure, consultez [Quel est le coffre-fort de clé Azure ?](key-vault-whatis.md)

## <a name="prerequisites"></a>Conditions préalables
Pour terminer ce didacticiel, vous devez disposer des éléments suivants :

- Un abonnement à Microsoft Azure. Si vous n’en avez pas, vous pouvez vous inscrire pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial).
- L’Interface de ligne de commande version 0.9.1 ou une version ultérieure. Pour installer la dernière version et vous connecter à votre abonnement Azure, consultez [installation et configuration de l’Interface de ligne de commande multiplateforme Azure](../xplat-cli-install.md).
- Une application qui est configurée pour utiliser la clé ou le mot de passe que vous créez dans ce didacticiel. Un exemple d’application est disponible à partir du [Centre de téléchargement Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Pour obtenir des instructions, consultez le fichier Readme qui l’accompagne.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtention d’aide avec Interface de ligne de commande multiplates-formes Azure

Ce didacticiel suppose que vous êtes familiarisé avec l’interface de ligne de commande (Bash, Terminal, invite de commandes)

La barre d’outils : paramètre d’aide ou -h peut être utilisé pour afficher l’aide pour des commandes spécifiques. Vous pouvez également l’aide azure [commande] [options] format peut également être utilisé pour renvoyer les mêmes informations. Par exemple, toutes les commandes suivantes renvoient les mêmes informations :

    azure account set --help

    azure account set -h

    azure help account set

En cas de doute sur les paramètres requis par une commande, consultez l’aide à l’aide de--aide,-h ou azure help [commande].

Vous pouvez également lire les didacticiels suivants pour familiariser avec le Gestionnaire de ressources Azure dans Azure multiplates-formes de ligne de commande Interface :

- [Comment faire pour installer et configurer l’Interface de ligne de commande multiplateforme Azure](../xplat-cli-install.md)
- [À l’aide d’Interface de ligne de commande multiplateforme Azure avec le Gestionnaire de ressources Azure](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Se connecter à vos abonnements

Pour vous connecter à l’aide d’un compte d’organisation, utilisez la commande suivante :

    azure login -u username -p password

ou si vous voulez vous connecter en tapant de façon interactive

    azure login

>[AZURE.NOTE]  La méthode de connexion fonctionne uniquement avec les comptes. Un compte d’organisation est un utilisateur qui est géré par votre organisation et défini dans les clients de Azure Active Directory de votre organisation.


Si vous ne disposez pas actuellement d’un compte d’organisation et que vous utilisez un compte Microsoft pour vous connecter à votre abonnement Azure, vous pouvez facilement en créer un à l’aide de la procédure suivante.

1.  Connexion d’accès à la connexion au [Portail de gestion Azure](https://manage.windowsazure.com/)et cliquez sur Active Directory.
2.  Si aucun répertoire n’existe, sélectionnez Créer votre répertoire et fournir les informations demandées.
3.  Sélectionnez votre répertoire et ajouter un nouvel utilisateur. Ce nouvel utilisateur est un compte d’organisation. Lors de la création de l’utilisateur, il vous êtes fourni avec une adresse de messagerie de l’utilisateur et un mot de passe temporaire. Enregistrez ces informations qu’il est utilisé dans une autre étape.
4.  À partir du portail, sélectionnez Paramètres, puis les administrateurs. Sélectionnez Ajouter et ajouter le nouvel utilisateur en tant qu’un administrateur de collaboration. Ainsi, le compte d’organisation gérer votre abonnement Azure.
5.  Enfin, déconnecter le portail Azure et puis ouvrez une session à l’aide du nouveau compte d’organisation. S’il s’agit de la première connexion à ce compte, vous êtes invité à modifier le mot de passe.

Pour plus d’informations sur l’utilisation d’un compte d’organisation avec Microsoft Azure, consultez [inscription à Microsoft Azure sous la forme d’une organisation](../active-directory/sign-up-organization.md).

Si vous disposez de plusieurs abonnements et que vous souhaitez spécifier un spécifique à utiliser pour la chambre forte de clé Azure, tapez ce qui suit pour afficher les abonnements de votre compte :

    azure account list

Ensuite, pour spécifier l’abonnement à utiliser, tapez :

    azure account set <subscription name>

Pour plus d’informations sur la configuration de l’Interface de ligne de commande Azure multiplates-formes, voir [Comment faire pour installer et configurer d’Interface de ligne de commande multiplateforme Azure](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Passer à l’aide du Gestionnaire de ressources Azure

Le coffre-fort de la clé requiert des Azure le Gestionnaire de ressources, tapez ce qui suit pour passer en mode de Gestionnaire des ressources Azure :

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Créer un nouveau groupe de ressources

Lorsque vous utilisez le Gestionnaire de ressources Azure, toutes les ressources associées sont créés à l’intérieur d’un groupe de ressources. Nous allons créer un nouveau groupe de ressources 'ContosoResourceGroup' pour ce didacticiel.

    azure group create 'ContosoResourceGroup' 'East Asia'

Le premier paramètre est le nom du groupe de ressources et le deuxième paramètre est l’emplacement. Pour l’emplacement, utilisez la commande `azure location list` pour savoir comment spécifier un autre emplacement à celui de cet exemple. Si vous avez besoin de plus d’informations, tapez :`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Le fournisseur de ressources de stockage en chambre forte de clé de Registre
Assurez-vous que ce fournisseur de ressources en chambre forte de la clé est enregistré dans votre abonnement :

`azure provider register Microsoft.KeyVault`

Cette opération ne doit être effectuée qu’une fois par abonnement.


## <a name="create-a-key-vault"></a>Créer un coffre-fort de clé

Utilisez le `azure keyvault create` commande pour créer un coffre-fort de clé. Ce script a trois paramètres obligatoires : un nom de groupe de ressources, un nom de coffre-fort de clé et l’emplacement géographique.

Par exemple, si vous utilisez le nom de la chambre forte de la ContosoKeyVault, le nom du groupe de ressources de ContosoResourceGroup et l’emplacement de l’Asie de l’est, tapez :

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

La sortie de cette commande affiche les propriétés de la chambre forte clée que vous venez de créer. Les deux propriétés les plus importantes sont les suivantes :

- **Nom**: dans l’exemple, cela est ContosoKeyVault. Vous utiliserez ce nom pour d’autres applets de commande de stockage en chambre forte de clé.
- **vaultUri**: l’exemple il s’agit de https://contosokeyvault.vault.azure.net. Les applications qui utilisent votre coffre-fort via son API REST doivent utiliser cet URI.

Votre compte Azure est maintenant autorisé à effectuer des opérations sur ce coffre-fort de clé. Encore, personne d’autre ne l’est.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Ajouter une clé ou un secret dans la chambre forte de clé

Si vous souhaitez en chambre forte de la clé Azure pour créer une clé protégé par le logiciel pour vous, utilisez le `azure key create` de commande et tapez ce qui suit :

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Toutefois, si vous disposez d’une clé existante dans un fichier .pem enregistré en tant que fichier local dans un fichier nommé softkey.pem que vous souhaitez télécharger sur Azure clé coffre-fort, tapez la commande suivante pour importer la clé de la. Fichier PEM, qui protège la clé par le logiciel dans le service de stockage en chambre forte de clé :

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

Vous pouvez maintenant référencer la clé que vous avez créé ou téléchargé dans Azure coffre-fort de clé, à l’aide de son URI. Utilisez **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** pour obtenir la version actuelle et **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** pour obtenir cette version spécifique.

Pour ajouter un secret dans la chambre forte, qui est un mot de passe nommée SQLPassword et qui possède la valeur de Pa$ $w0rd Azure clé coffre-fort, tapez ce qui suit :

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

Vous pouvez maintenant référencer ce mot de passe que vous avez ajoutés à la chambre forte de clé Azure, à l’aide de son URI. Utilisez **https://ContosoVault.vault.azure.net/secrets/SQLPassword** pour obtenir la version actuelle et **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** pour obtenir cette version spécifique.

Examinons la clé ou le mot de passe que vous venez de créer :

- Pour afficher la clé, tapez :`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Pour afficher votre secret, tapez :`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Inscrire une application avec Azure Active Directory

Cette étape devrait généralement être effectuée par un développeur, sur un ordinateur distinct. Il n’est pas spécifique à Azure clé coffre-fort mais est inclus ici par souci d’exhaustivité.


>[AZURE.IMPORTANT] Pour terminer le didacticiel, votre compte, le coffre-fort et l’application que vous inscrivez lors de cette étape doivent tous être dans le même répertoire Azure.

Les applications qui utilisent un coffre-fort clé doivent s’authentifier à l’aide d’un jeton d’Azure Active Directory. Pour ce faire, le propriétaire de l’application doit d’abord inscrire l’application dans leur Azure d’Active Directory. À la fin de l’enregistrement, le propriétaire de l’application obtient les valeurs suivantes :


- Un **ID de l’Application** (également appelé ID de Client) et une **clé d’authentification** (également connu sous le nom, le secret partagé). L’application doit présenter ces deux valeurs pour Azure d’Active Directory, afin d’obtenir un jeton. Comment l’application est configurée pour cela dépend de l’application. Pour l’exemple d’application en chambre forte de la clé, le propriétaire de l’application définit ces valeurs dans le fichier app.config.



Pour enregistrer l’application dans Azure Active Directory :

1. Ouvrez une session sur le portail Azure.
2. Sur la gauche, cliquez sur **Active Directory**, puis sélectionnez le répertoire dans lequel vous allez enregistrer votre application. <br> <br> Remarque : Vous devez sélectionner le même répertoire que celui qui contient l’abonnement Azure avec lequel vous avez créé votre clé coffre-fort. Si vous ne savez pas quel répertoire cela est, cliquez sur **paramètres**, identifier l’abonnement avec lequel vous avez créé votre coffre-fort clé et notez le nom du répertoire s’affiché dans la dernière colonne.

3. Cliquez sur **APPLICATIONS**. Si aucune application n’ont été ajoutées à votre répertoire, cette page affichera uniquement le lien **Ajouter une application** . Cliquez sur le lien, ou vous pouvez également cliquer l' **Ajouter** sur la barre de commandes.
4.  Dans l’Assistant **Ajouter une APPLICATION** , sur le **que voulez-vous faire ?** , cliquez sur **Ajouter une application à mon entreprise se développe**.
5.  Sur la page de **nous dire sur votre application** , spécifiez un nom pour votre application et sélectionnez **WEB APPLICATION et/ou WEB API** (il s’agit de la valeur par défaut). Cliquez sur l’icône suivante.
6.  Sur la page de **Propriétés de l’application** , spécifiez **l’URL de la session** et d’un **URI d’ID APP** pour votre application web. Si votre application ne dispose pas de ces valeurs, vous pouvez les rendre pour cette étape (par exemple, vous pouvez spécifier http://test1.contoso.com pour les deux zones). Peu importe si ces sites existent ; ce qui est important, c’est que l’identificateur URI d’application pour chaque application est différente pour chaque application dans votre répertoire. Le répertoire utilise cette chaîne pour identifier votre application.
7.  Cliquez sur terminé pour enregistrer vos modifications dans l’Assistant.
8.  Dans la page de démarrage rapide, cliquez sur **configurer**.
9.  Faites défiler jusqu'à la section **clés** , sélectionnez la durée et puis cliquez sur **Enregistrer**. La page s’actualise et affiche à présent une valeur de clé. Vous devez configurer votre application avec cette valeur de clé et la valeur de **l’ID de CLIENT** . (Les instructions pour cette configuration sera spécifique à l’application).
10. Copier la valeur d’ID de client à partir de cette page, que vous utiliserez à l’étape suivante pour définir les autorisations sur votre coffre-fort.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorisez l’application à utiliser la clé ou le secret

Pour autoriser l’application à accéder à la clé ou le secret dans la chambre forte, utiliser le `azure keyvault set-policy` commande.

Par exemple, si le nom de votre coffre-fort est ContosoKeyVault et l’application que vous souhaitez autoriser possède un ID de client de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed et que vous souhaitez autoriser l’application à décrypter et signer avec des clés dans votre chambre forte, puis exécutez la commande suivante :

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Si vous exécutez à l’invite de commandes Windows, vous devez remplacer des guillemets simples avec des guillemets doubles et également par échappement les guillemets internes. Par exemple : « [\"décrypter\",\"signe\"] ».

Si vous souhaitez autoriser cette même application à lire des secrets dans votre chambre forte, exécutez la commande suivante :

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Si vous souhaitez utiliser un module de sécurité matériel (HSM) ##

Pour la garantie, vous pouvez importer, ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne jamais laisser la frontière HSM. Les modules HSM sont FIPS 140-2 de niveau 2 est validé. Si cette exigence ne s’applique pas à vous, ignorez cette section et passez à [Supprimer le coffre-fort de clé et les clés associées et les secrets](#delete-the-key-vault-and-associated-keys-and-secrets).

Pour créer ces clés protégées par HSM, vous devez posséder un abonnement de chambre forte qui prend en charge les clés de HSM protégé.

Lorsque vous créez le keyvault, ajoutez le paramètre 'référence' :

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

Vous pouvez ajouter des logiciels protégés par les clés (comme indiqué plus haut) et protégé par HSM ce coffre-fort. Pour créer une clé protégée par HSM, définissez le paramètre de Destination de « HSM » :

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

Vous pouvez utiliser la commande suivante pour importer une clé à partir d’un fichier .pem sur votre ordinateur. Cette commande importe la clé dans les modules HSM dans le service de stockage en chambre forte de clé :

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

La commande suivante importe un « apportez votre propre clé » package de (BYOK). Cela vous permet de générer votre clé dans votre local HSM et le transférer vers HSM dans le service de stockage en chambre forte de clé, sans la clé de laisser la frontière HSM :

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Pour obtenir des instructions plus détaillées sur la façon de générer ce package BYOK, voir [comment utiliser les clés de HSM-Protected avec la chambre forte de clé Azure](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Supprimer le coffre-fort de clé et les clés associées et les secrets

Si vous n’avez plus besoin le coffre-fort de clé et la clé ou le secret qu’il contient, vous pouvez supprimer le coffre-fort de clé à l’aide de la commande de suppression keyvault azure :

    azure keyvault delete --vault-name 'ContosoKeyVault'

Ou bien, vous pouvez supprimer un groupe de ressources Azure entière, ce qui inclut la chambre forte de clé et d’autres ressources que vous avez inclus dans ce groupe :

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Autres commandes de l’Interface de ligne de commande multiplateforme Azure

Autres commandes que cela peut être utile pour la gestion de coffre-fort de clé Azure.

Cette commande répertorie un affichage tabulaire des toutes les clés et les propriétés sélectionnées :

    azure keyvault key list --vault-name 'ContosoKeyVault'

Cette commande affiche une liste complète des propriétés pour la clé spécifiée :

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Cette commande répertorie un affichage tabulaire de tous les noms de secret et de propriétés sélectionnées :

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Voici un exemple de la façon de supprimer une clé spécifique :

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Voici un exemple de la façon de supprimer un secret spécifique :

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Étapes suivantes

Pour les références de programmation, consultez [le coffre-fort de clé Azure guide du développeur](key-vault-developers-guide.md).
