<properties
   pageTitle="Définition et gestion de l’état | Microsoft Azure"
   description="Comment faire pour définir et gérer l’état du service dans le Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="service-state"></a>État du service
**État de service** fait référence aux données que le service a besoin pour fonctionner. Il inclut les variables le service lit et écrit effectuer le travail et les structures de données.

Imaginez un service de calculatrice simple, par exemple. Ce service prend deux nombres et retourne leur somme. Il s’agit d’un service purement sans état qui ne comporte aucune donnée associée.

Examinons à présent la calculatrice même, mais en plus de la somme de l’informatique, il a également une méthode pour retourner la somme dernière qu’il a calculée. Ce service est maintenant avec état, il contient un certain état qu’elle écrit à (lorsqu’il calcule une somme de nouveau) et le lit (lorsqu’il renvoie la somme calculée dernier).

Dans Azure Fabric du Service, le premier service est appelé service sans état. Le second service est appelé service dynamique.

## <a name="storing-service-state"></a>Stockage de l’état du service
État peut être externalisé ou être hébergé avec le code qui manipule l’état. Externalisation de l’état est généralement effectuée à l’aide d’une banque ou une base de données externe. Dans notre exemple de calculatrice, cela peut être une base de données SQL dans laquelle le résultat est stocké dans une table. Chaque demande pour calculer la somme effectue une mise à jour sur cette ligne.

État peut également être installé conjointement avec le code qui manipule ce code. Avec état services de Fabric de Service sont générés à l’aide de ce modèle. TISSU de service fournit l’infrastructure pour s’assurer que cet état est hautement disponible et la tolérance de pannes en cas de panne.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concepts de Service Fabric, consultez les rubriques suivantes :

- [Disponibilité des services de Fabric de Service](service-fabric-availability-services.md)

- [Évolutivité des services de Fabric de Service](service-fabric-concepts-scalability.md)

- [Partitionnement des services de Fabric de Service](service-fabric-concepts-partitioning.md)
