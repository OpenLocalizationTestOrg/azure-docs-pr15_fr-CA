<properties
    pageTitle="Quel est le coffre-fort de clé Azure ? | Microsoft Azure"
    description="Coffre-fort de clé Azure permet de sauvegarde des clés cryptographiques et les secrets utilisés par les services et les applications en nuage. En utilisant Azure clé coffre-fort, les clients peuvent crypter les clés et données confidentielles (telles que l’authentification par clés, stockage compte, clés de cryptage de données. Les fichiers PFX et mots de passe) à l’aide des clés qui sont protégés par les modules de sécurité matérielle (HSM)."
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
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>Quel est le coffre-fort de clé Azure ?

Coffre-fort de clé Azure est disponible dans la plupart des régions. Pour plus d’informations, consultez la [page de tarification en chambre forte de la clé](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduction

Coffre-fort de clé Azure permet de sauvegarde des clés cryptographiques et les secrets utilisés par les services et les applications en nuage. À l’aide de coffre-fort de clé, vous pouvez chiffrer les clés et les données confidentielles (telles que l’authentification par clés, stockage compte, clés de cryptage de données. Les fichiers PFX et mots de passe) à l’aide des clés qui sont protégés par les modules de sécurité matérielle (HSM). Pour la garantie, vous pouvez importer, ou générer des clés dans les modules HSM. Si vous choisissez pour ce faire, vos clés de FIPS 140-2 de niveau 2 validé avec des modules HSM (matériel et microprogramme) de processus de Microsoft.  

Chambre forte de clé rationalise le processus de gestion des clés et vous permet de garder le contrôle des clés qui accèdent et crypter vos données. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes et ensuite les migrer en toute transparence aux clés de la production. Administrateurs de la sécurité peuvent accorder (et révoquer) autorisations sur les clés, le cas échéant.

Utilisez le tableau suivant afin de mieux comprendre comment coffre-fort de clé peut pour répondre aux besoins des développeurs et administrateurs de la sécurité.





| Rôle        | Définition du problème           | Résolu en chambre forte de clé Azure  |
| ------------- |-------------|-----|
| Développeur d’une application Azure      | « Vous voulez écrire une application pour Azure qui utilise des clés pour la signature et le cryptage, mais souhaitée de ces clés est externe à partir de mon application afin que la solution est adaptée à une application qui est distribuée géographiquement. <br/><br/>Je souhaite également ces clés et ces secrets à protéger, sans avoir à écrire le code moi-même. Je souhaite également ces clés et ces secrets pour être simple à utiliser à partir de mes applications, avec des performances optimales. » | √ Clés sont stockés dans un coffre-fort et appelés par URI lorsque cela est nécessaire.<br/><br/> √ Clés sont sauvegardés par Azure, à l’aide d’algorithmes standard, les longueurs de clé et les modules de sécurité matérielle (HSM).<br/><br/> √ Clés sont traités dans les modules HSM qui résident dans les centres de données Azure même que les applications. Cela offre une meilleure fiabilité et une latence réduite que si les clés se trouvent dans un emplacement distinct, comme sur site.|
| Développeur de logiciels en tant que Service (SaaS)      |« Je ne veux la responsabilité ou une responsabilité potentielle pour les clés de clients de mes clients et des secrets. <br/><br/>Je souhaite que les clients qui possède et gère leurs clés de sorte que je peux consacrer ainsi que faire mieux, qui fournit les principales fonctionnalités du logiciel. » | √ Clients peuvent importer leurs propres clés Azure et les gérer. Lorsqu’une application SaaS doit effectuer des opérations de chiffrement à l’aide de clés de leurs clients, clé de coffre-fort effectue ces opérations au nom de l’application. L’application ne voit pas les clés du client.|
| Agent responsable de la sécurité (CSO) | « Je voudrais savoir que nos applications conformes FIPS 140-2 niveau 2 HSM pour une gestion sécurisée des clés. <br/><br/>Je veux que mon entreprise contrôle le cycle de vie de clé et peut contrôler l’utilisation de la clé. <br/><br/>Et bien que nous utilisons plusieurs services Azure et les ressources, gérer les clés à partir d’un emplacement unique dans Azure. »     |√ HSM sont FIPS 140-2 de niveau 2 est validé.<br/><br/>√ Clé coffre-fort est conçue afin que Microsoft ne pas voir ou extraire vos clés.<br/><br/>√ Près de journalisation en temps réel de l’utilisation de la clé.<br/><br/>√ Le coffre-fort fournit une interface unique, coffres-forts combien vous ayez dans Azure, les régions de leur prise en charge et les applications qui utilisent les. |


Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres-forts clés. Bien que coffre-fort de clé avantages aux développeurs et administrateurs de la sécurité, il pourrait être implémentée et géré par l’administrateur de l’organisation qui gère les autres services Azure pour une organisation. Par exemple, l’administrateur devait signer avec un abonnement Azure, créer un coffre-fort pour l’organisation dans laquelle stocker les clés et puis être chargés de tâches opérationnelles, telles que :

+ Créer ou importer une clé ou un secret
+ Retirer ou supprimer une clé ou un secret
+ Autoriser des utilisateurs ou des applications à accéder à la clé coffre-fort, ils peuvent ensuite gérer ou ses clés et les secrets
+ Configurer l’utilisation de la clé (par exemple, signer ou crypter)
+ Surveiller l’utilisation de clé

Cet administrateur ensuite fournir aux développeurs des URI à appeler à partir de leurs applications et fournir leur administrateur de sécurité avec des informations de journalisation de l’utilisation de la clé. 

   ![Vue d’ensemble de chambre forte de clé Azure][1]

Les développeurs peuvent également gérer les clés directement, en utilisant des API. Pour plus d’informations, voir le [guide du développeur de la chambre forte de clé](key-vault-developers-guide.md).

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir un didacticiel démarré lors de l’obtention d’un administrateur, consultez [Mise en route de la chambre forte de clé Azure](key-vault-get-started.md).

Pour plus d’informations sur la journalisation d’utilisation de clé coffre-fort, voir [Enregistrement du coffre-fort Azure clé](key-vault-logging.md).

Pour plus d’informations sur l’utilisation de clés et secrets avec Azure clé coffre-fort, voir [à propos des clés, des Secrets et des certificats](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
