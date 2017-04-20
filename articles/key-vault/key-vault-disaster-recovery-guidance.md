<properties
    pageTitle="Que faire en cas d’un Azure service interruption qui affecte le coffre-fort de clé Azure | Microsoft Azure"
    description="Apprenez quoi faire en cas d’une interruption de service Azure qui affecte le coffre-fort de clé Azure."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure de redondance et de disponibilité de clé coffre-fort

Coffre-fort de clé Azure offre plusieurs couches de redondance pour vous assurer que vos clés et les secrets restent disponibles à votre application même si les composants individuels du service échouent.

Le contenu de votre coffre-fort clé est répliqué dans la région et à une zone secondaire au moins de 150 milles immédiatement, mais dans la même région. Cela permet de conserver une durabilité élevée de vos clés et les secrets.

Si les composants individuels au sein du service de chambre forte clé échouent, des composants de substitution dans la zone étape pour répondre à votre requête afin de vous assurer qu’il n’existe aucune dégradation des fonctionnalités. Vous n’avez pas besoin à intervenir pour déclencher cette. Il se produit automatiquement et est transparent pour l’utilisateur.

Dans les rares cas qu’une région entière de Azure n’est pas disponible, les demandes que vous apportez de chambre forte de clé Azure dans cette région sont automatiquement routés (*basculement*) pour une zone secondaire. Lorsque la région principale est à nouveau disponible, les demandes sont acheminées précédent (*bascule*) pour la zone principale. Là encore, il est inutile à aucune action car cela se produit automatiquement.

Il existe quelques inconvénients à connaître :

* En cas de basculement de la région, il peut prendre quelques minutes pour le service à basculer. Demandes qui sont effectuées pendant ce temps peuvent échouer jusqu'à ce que le basculement est terminée.
* Après un basculement, votre coffre-fort clé est en mode lecture seule. Les demandes qui sont pris en charge dans ce mode sont les suivantes :
 * Liste des coffres-forts clés
 * Obtenir les propriétés de clé coffres-forts
 * Liste des secrets
 * Obtenir des secrets
 * Liste des clés
 * Obtenir des clés de (propriétés de)
 * Crypter
 * Décrypter
 * Retour à la ligne
 * Défaire
 * Vérifier
 * Signe
 * Sauvegarde
* Après qu’un basculement sur incident est Échec précédent, tous les types de demandes (y compris les requêtes de lecture *et* d’écriture) sont disponibles.
