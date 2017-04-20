<properties
   pageTitle="Tissu Azure Service sous Linux | Microsoft Azure"
   description="Clusters de service Fabric prennent en charge Linux et Java, qui signifie que vous pourrez déployer et les applications de Fabric du Service hôte écrites en Java et C# sous Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>TISSU de services sous Linux

L’aperçu du tissu de Service sous Linux vous permet de générer, de déployer et de gérer des applications hautement disponibles et évolutives sur Linux comme vous le feriez sur Windows. Les infrastructures de Service Fabric (des Services fiables et fiable des acteurs) sont disponibles dans Java sous Linux en plus C# (.NET de base).  Vous pouvez également créer [les services exécutables invité](service-fabric-deploy-existing-app.md) avec n’importe quel langage ou d’un framework. En outre, l’aperçu prend également en charge des conteneurs Docker orchestrant ces opérations. Conteneurs de docker peuvent exécuter invité exécutables ou des services de Fabric de Service natifs, qui utilisent les infrastructures de Service Fabric.

Services sur Linux est conceptuellement équivalent au Fabric de Service sur Windows (à l’exception des caractéristiques du système d’exploitation et prise en charge du langage de programmation). Par conséquent, la plupart de nos [documentation existante](http://aka.ms/servicefabricdocs) s’applique pour vous aider à vous familiariser avec cette technologie.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Prise en charge des systèmes d’exploitation et des langages de programmation

L’aperçu limité prend en charge la création de clusters de développement d’une case en plus des clusters comportant plusieurs ordinateurs dans Azure exécutant Ubuntu serveur 16.04. L’aperçu prend en charge les acteurs fiable et les infrastructures des Services fiables sans état dans Java et C# en plus des fichiers exécutables d’invité et d’organiser des conteneurs de Docker.  

>[AZURE.NOTE] Collections fiables ne sont pas supportées encore sous Linux. Support des clusters seuls ne sont pas pris en charge-une seule zone et Azure Linux des clusters comportant plusieurs ordinateurs sont pris en charge dans l’aperçu.

## <a name="supported-tooling"></a>Outils pris en charge

L’aperçu prend en charge l’interaction avec le cluster via l’interface CLI d’Azure. Pour les développeurs Java, intégration avec Eclipse et Yeoman est fournie avec Eclipse sur Linux et sur OSX pris en charge. L’intégration de OSX utilise une VM Linux dans les coulisses via vagrant. Pour les développeurs C#, intégration avec Yeoman est fournie pour générer des modèles de l’application.

## <a name="next-steps"></a>Étapes suivantes


1. Familiarisez-vous avec les infrastructures de programmation [Fiable d’acteurs](service-fabric-reliable-actors-introduction.md) et de [Services fiables](service-fabric-reliable-services-introduction.md) .

2. [Préparez votre environnement de développement sous Linux](service-fabric-get-started-linux.md)

3. [Préparez votre environnement de développement sur OSX](service-fabric-get-started-mac.md)

4. [Créer votre première application Service Fabric Java sous Linux](service-fabric-create-your-first-linux-application-with-java.md)
