<properties
   pageTitle="Polymorphisme dans le cadre des acteurs fiable | Microsoft Azure"
   description="Créer des hiérarchies de types dans le cadre des acteurs fiable pour réutiliser les fonctionnalités et les définitions de l’API et les interfaces de .NET."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/07/2016"
   ms.author="seanmck"/>

# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorphisme dans le cadre des acteurs fiable

Le framework d’acteurs fiable permet vous permet de générer des acteurs à l’aide de la plupart des mêmes techniques que celles que vous utiliseriez dans la conception orientée objet. Une de ces techniques est le polymorphisme, qui permet aux types et interfaces pour hériter de plus généralisée des parents. L’héritage dans le cadre des acteurs fiable suit généralement le modèle .NET avec quelques contraintes supplémentaires.

## <a name="interfaces"></a>Interfaces

Le framework des acteurs fiables vous impose de définir au moins une interface à implémenter par votre type d’acteur. Cette interface est utilisée pour générer une classe proxy qui peut être utilisée par les clients pour communiquer avec votre acteurs. Interfaces peuvent hériter d’autres interfaces, tant que chaque interface qui est implémentée par un type d’acteur et tous ses parents finalement dériver à partir de IActor. IActor est l’interface de base défini par la plate-forme pour les acteurs. Ainsi, l’exemple classique de polymorphisme à l’aide de formes peut ressembler à ceci :

![Hiérarchie de l’interface pour les acteurs de la forme][shapes-interface-hierarchy]


## <a name="types"></a>Types de

Vous pouvez également créer une hiérarchie de types d’acteur, qui sont dérivées de la classe de base acteur qui est fournie par la plate-forme. Dans le cas de formes, vous pouvez avoir une base de `Shape` type :

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```

Subtypes de `Shape` pouvez substituer des méthodes à partir de la base.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```

Remarque Les `ActorService` les attributs sur le type d’acteur. Cet attribut indique à l’infrastructure d’acteur fiable qu’il doit automatiquement créer un service d’hébergement des acteurs de ce type. Dans certains cas, vous souhaiterez peut-être créer un type de base qui est exclusivement destiné à partageant la fonctionnalité avec les sous-types et ne sera jamais utilisé pour instancier des acteurs concrètes. Dans ce cas, vous devez utiliser le `abstract` mot clé pour indiquer que vous n’allez jamais créer un acteur en fonction de ce type.


## <a name="next-steps"></a>Étapes suivantes

- Voir [comment l’infrastructure fiable des acteurs s’appuie sur la plate-forme de Service de Fabric](service-fabric-reliable-actors-platform.md) pour fournir une fiabilité, d’évolutivité et de cohérence.
- Obtenir des informations sur le [cycle de vie acteur](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
