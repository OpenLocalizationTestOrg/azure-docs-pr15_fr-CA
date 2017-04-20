<properties
    pageTitle="Extraction des données publiques dans Azure événement concentrateurs | Microsoft Azure"
    description="Vue d’ensemble des événements importer à partir du web, exemple"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>Extraction des données publiques dans Azure événement concentrateurs

Dans les scénarios Internet des objets (IoT) classiques, vous avez les périphériques que vous pouvez programmer pour envoyer des données vers Azure, soit à un concentrateur d’événements Azure ou un concentrateur IoT. Les deux les concentrateurs sont des points d’entrée dans Azure pour stocker, d’analyse et de visualisation avec une multitude d’outils mis à disposition sur Microsoft Azure. Toutefois, les deux exigent que vous poussez des données pour les mises en forme en tant que JSON et sécurisé de manière spécifique. Cela soulève la question suivante. Que faire si vous souhaitez importer les données de sources publiques ou privées où les données sont exposées sous la forme d’un service web ou l’alimentation quelconque, mais que vous ne disposez pas de la possibilité de modifier la façon dont les données sont publiées ? Pensez à la météo et le trafic ou cotations boursières - vous ne pouvez pas savoir NOAA, ou WSDOT ou NASDAQ pour configurer une diffusion sur votre concentrateur d’événements. Pour résoudre ce problème, nous avons écrit et un petit nuage exemple que vous pouvez modifier et déployer qui va extraire les données de certaines de ces sources et l’envoyer à votre concentrateur d’événements source ouverte. À partir de là, vous pouvez faire ce que vous voulez, sous réserve, bien entendu, les termes de la licence du producteur. Vous pouvez trouver l’application [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Notez que le code de cet exemple affiche uniquement comment extraire des flux de données web typique et comment écrire à un concentrateur d’événements Azure. Ce n’est pas censé être une application de production et aucune tentative n’a été effectuée afin d’être adapté à une utilisation dans un tel environnement, il est strictfly un soi-même, exemple destinés aux développeurs. En outre, l’existence de cet exemple n’est pas équivalent à une recommandation que vous devez **Extraire** les données dans Azure plutôt que de **push** il. Vous devez examiner la sécurité, de performances, de fonctionnalités et facteurs de coût avant de s’installer sur une architecture de bout en bout.

## <a name="application-structure"></a>Structure d’application

L’application est écrite en C# et la [description de l’exemple](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contient toutes les informations dont vous avez besoin de modifier, de générer et de publier l’application. Les sections suivantes fournissent une vue d’ensemble de l’application.

Nous allons commencer en partant du principe que vous avez accès à un flux de données. Par exemple, vous pouvez souhaiter extraire les données de trafic à partir de l’état de Washington ministère des transports ou les données météorologiques de NOAA, pour afficher des rapports personnalisés ou à les combiner avec d’autres données dans votre application. Vous devez également avoir configuré à un concentrateur d’événements Azure et connaître la connexion chaîne pour y accéder.

Au démarrage de la solution GenericWebToEH, il lit un fichier de configuration (App.config) pour obtenir un certain nombre de choses :

1. L’URL, ou une liste d’URL pour le site de publication des données. Dans l’idéal, il s’agit d’un site qui publie des données au format JSON, telles que celles référencées par WSDOT [ici](http://www.wsdot.wa.gov/Traffic/api/). 
2. Informations d’identification de l’URL, si nécessaire. De nombreuses sources publiques n’ont pas besoin d’informations d’identification, ou vous pouvez placer les informations d’identification dans la chaîne d’URL. D’autres nécessitent que vous fournissez séparément. (Notez que vous pouvez spécifier seulement un seul jeu d’informations d’identification dans cette application, afin qu’il fonctionne uniquement si vous ne spécifiez qu’une seule URL, pas une liste d’URL).
3. La chaîne de connexion et le nom du concentrateur d’événements dans cet espace de noms de concentrateurs de l’événement auquel vous fera les données. Vous pouvez trouver ces informations dans le portail Azure.
4. Un intervalle de la veille, en millisecondes, pour l’intervalle entre l’interrogation du site de données publiques. Définition de ce paramètre nécessite la réflexion. Si vous interrogez trop rarement, vous risquez de manquer des données ; d’autre part, si vous interrogez trop fréquemment, vous pouvez obtenir un grand nombre de données répétitives, ou pire encore, vous pouvez être bloquée comme un bot tombaient. Considérez la fréquence de mise à jour de la source de données - données météo ou le trafic peuvent être actualisés toutes les 15 minutes, mais peut-être après quelques secondes, selon où les obtenir les cotations boursières. 
5. Un indicateur pour indiquer si les données entrant en tant que JSON ou XML à l’application. Dans la mesure où vous devez déplacer les données vers un concentrateur d’événements, l’application possède un module afin de convertir le XML en JSON avant de l’envoyer.

Après avoir lu le fichier de configuration, l’application s’exécute en boucle - l’accès au site web public, en convertissant les données si nécessaire, écrire à votre concentrateur d’événements et d’attendre l’intervalle de veille avant d’effectuer tout à nouveau. En particulier :

  * Le site Web public de lecture. Pour la réception de données prêt à transmettre l’instance de classe de RawXMLWithHeaderToJsonReader est utilisé à partir de Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Il lit le flux source dans la méthode GetData et puis fractionne en morceaux plus petits (c'est-à-dire des enregistrements) à l’aide de GetXmlFromOriginalText. 
  Cette méthode lit le XML et bien formé JSON ou JSON de tableau. Traitement a démarré à l’aide de la configuration de MergeToXML d’App.config (par défaut = vide).
  * Les données de réception et d’envoi sont implémentées sous la forme d’une boucle dans la méthode Process() dans Program.cs. 
  Après réception des résultats de la sortie GetData(), place de la méthode valeurs séparées par vers le concentrateur d’événements.

## <a name="next-steps"></a>Étapes suivantes

Pour déployer la solution, cloner ou télécharger l’application [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , modifiez le fichier App.config, créer et finalement le publier. Une fois que vous avez publié l’application, vous pouvez voir qu’il exécute dans Azure portal classique sous les Services en nuage, et vous pouvez modifier certains paramètres de configuration (par exemple, la cible de concentrateur de l’événement et l’intervalle de veille) dans l’onglet **configurer** .

Voir plus d’échantillons de concentrateurs d’événement dans la [Galerie d’exemples d’Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) et sur [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).
