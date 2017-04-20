<properties
   pageTitle="Touche Guide du développeur de coffre-fort | Microsoft Azure"
   description="Les développeurs peuvent utiliser le coffre-fort de clé Azure pour gérer des clés de chiffrement dans l’environnement Microsoft Azure. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Guide du développeur de la chambre forte de clé Azure
À l’aide de la clé de coffre-fort, vous serez en mesure d’accéder de manière sécurisée des informations sensibles dans vos applications telles que :

- Les clés et les secrets seront protégés sans avoir à écrire le code vous-même et vous pourrez facilement les utiliser à partir de vos applications.
- Vous pourrez avoir vos clients propres et gérer leurs propres clés afin de vous concentrer sur les fournissant les principales fonctionnalités du logiciel. De cette manière vos applications ne possédera pas la responsabilité ou une responsabilité potentielle pour les clés des clients de vos clients et des secrets.
- Votre application peut utiliser des clés pour la signature et le cryptage assure la gestion des clés externes à partir de votre application telle que la solution est adaptée à une application qui est distribuée géographiquement.

- Avec la version de septembre 2016 de chambre forte de clé, vos applications peuvent désormais bénéficier de chambre forte de clé des certificats. Pour plus d’informations, consultez **à propos des clés et des certificats, des secrets,** l’article dans la [référence du reste](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Pour plus d’informations sur le coffre-fort de clé Azure, consultez [clé coffre-fort](key-vault-whatis.md).

## <a name="videos"></a>Vidéos
Cette vidéo vous montre comment créer votre propre clé coffre-fort et comment l’utiliser à partir de l’exemple d’application « Hello clé en chambre forte ».

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Liens vers les ressources mentionnées dans la vidéo :
- [PowerShell Azure](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Exemples de Code Azure coffre-fort de clé](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Pour en savoir plus vous pouvez suivre le [Blog de coffre-fort de clé](http://aka.ms/kvblog) et participer au [Forum de chambre forte de clé](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Création et gestion de clé coffres-forts

Avant de travailler avec Azure clé coffre-fort dans votre code, vous pouvez créer et gérer des coffres-forts via reste, Gestionnaire de ressources de modèles, PowerShell ou l’interface CLI, comme décrit dans les articles suivants :

- [Créer et gérer des clés coffres-forts avec le reste](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Créer et gérer des clés coffres-forts avec PowerShell](key-vault-get-started.md)
- [Créer et gérer des clés coffres-forts avec l’interface CLI](key-vault-manage-with-cli.md)
- [Créer un coffre-fort de clé et ajouter un secret via un modèle de gestionnaire de ressources Azure](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Opérations contre les coffres-forts principaux sont authentifiées par le biais de DAS et autorisées via la stratégie d’accès de clé de Vault propre, défini par coffre-fort.

## <a name="coding-with-key-vault"></a>Codage avec clés coffre-fort

Le système de gestion de stockage en chambre forte de clé pour les programmeurs compose de plusieurs interfaces, avec le reste de la Fondation, [Clé de référence de l’API reste en chambre forte](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Vous pouvez, sous réserve d’une autorisation réussie, procédez ainsi :

- Gérer les clés de chiffrement à l’aide de la [Création](https://msdn.microsoft.com/library/azure/dn903634.aspx), [l’importation](https://msdn.microsoft.com/library/azure/dn903626.aspx), [mise à jour](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903611.aspx) et autres opérations

- Gérer les secrets à l’aide de [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [mise à jour](https://msdn.microsoft.com/library/azure/dn986818.aspx), [Supprimer](https://msdn.microsoft.com/library/azure/dn903613.aspx) et autres opérations

- Utiliser des clés de chiffrement avec [signe](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Vérifier](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) et [crypter](https://msdn.microsoft.com/library/azure/dn878060.aspx)/opérations de[décrypter](https://msdn.microsoft.com/library/azure/dn878097.aspx)

Les kits de développement suivantes sont disponibles pour l’utilisation de la clé de stockage en chambre forte :

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentation du Kit de développement .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Documentation du Kit de développement logiciel Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Package du Kit de développement .NET sur Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Package Node.js SDK](https://www.npmjs.com/package/azure-keyvault)|

Pour plus d’informations sur la version 2.x du Kit de développement .NET, consultez les [notes de version](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Exemple de code
Pour obtenir des exemples complets à l’aide de la clé de coffre-fort avec vos applications, consultez :

- Exemple d’application .NET *HelloKeyVault* et un exemple de service web Azure. [Exemples de code clé coffre-fort Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Didacticiel pour vous aider à apprendre comment utiliser Azure clé Vault à partir d’une application web dans Azure. [Utilisez Azure coffre-fort de clé à partir d’une Application Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Articles Comment faire

Les scénarios et les articles suivants fournissent des instructions relatives aux tâches spécifiques pour l’utilisation de la chambre forte de clé Azure :

- [Modifie l’identificateur de clients clés coffre-fort après déplacement d’abonnement](key-vault-subscription-move-fix.md) - lorsque vous déplacez votre abonnement Azure à partir de clients A au client B, vos coffres-forts clés existantes sont inaccessibles les identités (utilisateurs et applications) dans le locataire correctif B. ce à l’aide de ce guide.
- [L’accès de coffre-fort clé derrière le pare-feu](key-vault-access-behind-firewall.md) - pour accéder à une clé vault que votre application cliente de coffre-fort de clé doit être en mesure d’accéder à plusieurs points de terminaison pour différentes fonctionnalités.

- [Comment faire pour générer et clés de Transfer HSM-Protected pour Azure clé Vault](key-vault-hsm-protected-keys.md) - cela vous aidera à planifier, générer et transférer vos propres clés protégés par HSM à utiliser avec Azure clé coffre-fort.
- [Le passage de valeurs sécurisés (par exemple, des mots de passe) au cours du déploiement](../resource-manager-keyvault-parameter.md) - lorsque vous devez passer une valeur sécurisée (par exemple, un mot de passe) en tant que paramètre au cours du déploiement, vous pouvez stocker cette valeur comme un secret dans un coffre-fort de clé Azure et la référence la valeur dans d’autres modèles du Gestionnaire de ressources.
- [L’utilisation de stockage en chambre forte de la clé pour la gestion des clés extensible avec SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - connecteur SQL Server pour la chambre forte clé Azure permet à SQL Server et SQL-dans-un-machine virtuelle d’exploiter le service de chambre forte de clé Azure comme un fournisseur de gestion de clé Extensible (EKM) pour protéger ses clés de chiffrement pour les applications de lien ; Chiffrement transparent des données, le cryptage de sauvegarde et de cryptage au niveau colonne.
- [Comment faire pour déployer des certificats pour les ordinateurs virtuels à partir de la clé de coffre-fort](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - une application en nuage en cours d’exécution sur un ordinateur virtuel sur Azure a besoin d’un certificat. Comment obtenir ce certificat dans cette machine virtuelle aujourd'hui ?
- [Comment le programme d’installation en chambre forte de la clé avec rotation des clés de bout en bout et d’audit](key-vault-key-rotation-log-monitoring.md) - ce parcours dans la procédure de configuration d’audit avec Azure clé de coffre-fort et rotation de la clé.

Pour obtenir des instructions spécifiques sur l’intégration et à l’aide de la clé de coffres-forts avec Azure, voir [exemples de modèles de Ryan Jones Azure le Gestionnaire de ressources de stockage en chambre forte de clé](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Intégré avec clé coffre-fort

Ces articles sont sur des scénarios et des services nous ou intègrent le coffre-fort de la clé.

- [Chiffrement de disque Azure](../security/azure-security-disk-encryption.md) tire parti de la fonctionnalité de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) de secteur standard de Windows et de la fonction [Crypt-DM](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le cryptage de volume pour le système d’exploitation et les disques de données. La solution est intégrée à la chambre forte de clé Azure pour vous aider à contrôler et de gérer les clés de cryptage de disque et les secrets de votre abonnement au coffre-fort clé, tout en s’assurant que toutes les données sur les disques de l’ordinateur virtuel sont cryptées au reste de votre stockage Azure.


## <a name="supporting-libraries"></a>Prise en charge des bibliothèques

- [Bibliothèque principale de Microsoft Azure clé Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fournit `IKey` et `IKeyResolver` des interfaces pour la recherche de clés à partir d’identificateurs en effectuant des opérations avec des clés.

- [Extensions de coffre-fort clé Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fournit des capacités étendues pour Azure clé coffre-fort.

## <a name="other-key-vault-resources"></a>Autres ressources de stockage en chambre forte de clé
- [Blog de la chambre forte de clé](http://aka.ms/kvblog)
- [Forum de clé coffre-fort](http://aka.ms/kvforum)
