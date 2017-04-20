<properties
    pageTitle="Introduction de pile clé coffre-fort Azure | Microsoft Azure"
    description="Découvrez comment Azure pile clé coffre-fort gère les clés et les secrets"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduction à la clé coffre-fort dans la pile d’Azure #

## <a name="before-you-start"></a>Avant de commencer

Cet article suppose que les éléments suivants :

- Le lecteur a accès à un abonnement qui a Azure clé coffre-fort est activé.
- Le Kit de développement de PowerShell Azure est configuré et disponible.
- Pour la version de protocole TP2, toutes les opérations clients peuvent être effectuées uniquement à partir de PowerShell.

## <a name="key-vault-basics"></a>Notions de base en chambre forte de la clé

Stockage en chambre forte de la clé dans la pile d’Azure protège les clés de chiffrement et utilisent des secrets que les applications et les services en nuage. À l’aide de coffre-fort de clé, vous pouvez chiffrer les clés et les données confidentielles (telles que des clés d’authentification, clés de compte de stockage, les clés de cryptage de données, les fichiers .pfx et mots de passe).

Chambre forte de clé rationalise le processus de gestion des clés et vous permet de garder le contrôle des clés qui accèdent et crypter vos données. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes et ensuite les migrer en toute transparence aux clés de la production. Administrateurs de la sécurité peuvent accorder (et révoquer) autorisations sur les clés, le cas échéant.

Toute personne disposant d’un abonnement Azure pile peut créer et utiliser des coffres-forts clés. Bien que la clé de coffre-fort avantages aux développeurs et administrateurs de la sécurité, il peut être implémentée et géré par un administrateur qui gère les autres services de pile d’Azure pour une organisation. Par exemple, cet administrateur peut se connecter avec un abonnement Azure pile, créer un coffre-fort pour l’organisation dans lequel vous souhaitez stocker les clés et être responsable de ces tâches opérationnelles :

- Créer ou importer une clé ou un secret
- Retirer ou supprimer une clé ou un secret
- Autoriser des utilisateurs ou des applications à accéder à la clé coffre-fort, ils peuvent ensuite gérer ou ses clés et les secrets
- Configurer l’utilisation de la clé (par exemple, signer ou crypter)

Cet administrateur peut ensuite fournir aux développeurs des URI à appeler à partir de leurs applications et fournissez un administrateur de sécurité avec des informations de journalisation de l’utilisation de la clé.

Les développeurs peuvent également gérer les clés directement, en utilisant des API. Pour plus d’informations, consultez le guide du développeur de la chambre forte de clé.

## <a name="scenarios"></a>Scénarios

Le tableau suivant décrit certains des scénarios où clé coffre-fort peut aider à répondre aux besoins des développeurs et administrateurs de la sécurité :


### <a name="developer-for-an-azure-stack-application"></a>Développeur d’une application Azure pile

**Problème**: vous voulez écrire une application pour pile Azure qui utilise des clés pour la signature et le cryptage mais je souhaite qu’ils soient externes à partir de mon application afin que la solution est adaptée à une application qui est distribuée géographiquement.

**Instruction**: clés sont stockés dans un coffre-fort et appelés par URI lorsque cela est nécessaire.


### <a name="developer-for-software-as-a-service-saas"></a>Développeur de logiciels en tant que service (SaaS)

**Problème :** Je ne veux pas la responsabilité ou une responsabilité potentielle pour les clés de clients de mes clients et des secrets.

**Instruction :** Les clients peuvent importer leurs propres clés de pile d’Azure et les gérer. Je souhaite que les clients qui possède et gère leurs clés de sorte que je peux consacrer ainsi que faire mieux, qui fournit les principales fonctionnalités du logiciel.


### <a name="chief-security-officer-cso"></a>Sécurité du directeur (responsable de la sécurité)

**Problème :** Je veux que mon entreprise contrôle le cycle de vie de clé et peut contrôler l’utilisation de la clé.

**Instruction** Clé de stockage en chambre forte est conçue afin que Microsoft ne pas voir ou extraire vos clés.  Lorsqu’une application a besoin effectuer des opérations de chiffrement à l’aide de clés de clients, clé de coffre-fort pour cela au nom de l’application. L’application ne voit pas les clés du client.  Bien que nous utilisons plusieurs ressources et services de la pile d’Azure, je veux gérer les clés à partir d’un seul emplacement dans la pile d’Azure. Le coffre-fort fournit une interface unique, coffres-forts combien vous ayez dans la pile d’Azure, les régions de leur prise en charge et les applications qui utilisent les.

## <a name="next-steps"></a>Étapes suivantes

[Mise en route de la chambre forte de clé](azure-stack-kv-getting-started.md)
