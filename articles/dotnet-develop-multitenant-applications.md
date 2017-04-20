<properties
    pageTitle="Modèle d’Application Web de mutualisée | Microsoft Azure"
    description="Rechercher des présentations architecturales et des modèles de conception qui décrivent comment implémenter une application web de mutualisée dans Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Applications mutualisées dans Azure

Une application partagée est une ressource partagée qui permet aux utilisateurs distincts, ou « utilisateurs » afficher l’application comme si elle était leur propre. Un scénario typique qui se prête à une application partagée est un dans lequel tous les utilisateurs de l’application souhaiterez personnaliser l’expérience utilisateur, mais sinon ont les mêmes exigences métier de base. Office 365, Outlook.com et visualstudio.com sont des exemples de grandes applications mutualisées.

Du point de vue d’un fournisseur d’application, les avantages de l’architecture multilocataire surtout de gains d’efficacité opérationnelles et de coûts. Une version de votre application peut répondre aux besoins de nombreux locataires/client, ce qui permet la consolidation du système de tâches d’administration telles que la surveillance, réglage des performances, maintenance des logiciels et des sauvegardes de données.

Celui-ci fournit une liste des objectifs plus importants et les exigences du point de vue d’un fournisseur.

- **Provisionnement**: vous devez être en mesure de fournir les locataires pour l’application de la nouvelles.  Pour les applications mutualisées avec un grand nombre de clients, il est généralement nécessaire automatiser ce processus en activant l’attribution en libre-service.
- **La facilité de maintenance**: vous devez être en mesure de mettre à niveau l’application et effectuer d’autres tâches de maintenance alors que différents utilisateurs l’utilisent.
- **Analyse**: vous devez être en mesure de surveiller l’application à tout moment pour identifier les problèmes et les résoudre. Ceci inclut l’analyse comment chaque client est à l’aide de l’application.

Une application partagée correctement implémentée offre les avantages suivants aux utilisateurs.

- **Isolement**: les activités de clients individuels n’affectent pas l’utilisation de l’application par les autres locataires. Locataires ne peut pas accéder à d’autres données. Il s’affiche au locataire comme s’ils avaient un usage exclusif de l’application.
- **Disponibilité**: les locataires souhaitez que l’application peuvent être constamment disponibles, peut-être avec des garanties définies dans l’accord SLA. Là encore, les activités des autres locataires ne devraient pas affecter la disponibilité de l’application.
- **Évolutivité**: l’application s’adapte pour répondre à la demande de clients individuels. La présence et les actions des autres locataires ne devraient pas affecter les performances de l’application.
- **Coûts**: les coûts sont inférieurs à une application mono-utilisateur dédiée en cours d’exécution, car l’architecture mutualisée permet le partage des ressources.
- **Possibilités de personnalisation**. La possibilité de personnaliser l’application pour un client individuel de différentes manières comme ajout ou suppression de fonctionnalités, modification des couleurs et les logos ou même ajouter leur propre code ou un script.

En résumé, bien qu’il existe de nombreuses considérations que vous devez prendre en compte pour fournir un service hautement évolutif, il existe également un certain nombre des objectifs et des besoins qui sont communs à de nombreuses applications mutualisées. Certaines ne sont peut-être pas pertinents dans des scénarios spécifiques, et de l’importance de ses objectifs individuels et des exigences diffèrent dans chaque scénario. En tant que fournisseur de l’application partagé, vous disposez également d’objectifs et les exigences telles que des locataires, objectifs et exigences, rentabilité, facturation, plusieurs niveaux de service, provisionnement, surveillance de la facilité de maintenance et l’automatisation de la réunion.

Pour plus d’informations sur les considérations de conception supplémentaires d’une application partagée, consultez [hébergement mutualisée d’une Application dans Azure][]. Pour plus d’informations sur les modèles d’architecture de données commune d’applications de base de données mutualisées software as a service (SaaS), reportez-vous à la section [Modèles de Design pour les Applications SaaS mutualisée avec la base de données de SQL Azure](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure fournit de nombreuses fonctionnalités qui vous permettent de résoudre les problèmes principaux rencontrés lors de la conception d’un système partagé.

**Isolation**

- Site Web de segment les locataires par des en-têtes d’hôte avec ou sans communication SSL
- Site Web du segment des locataires par les paramètres de la requête
- Services Web dans des rôles Worker
    - Rôles du travailleur. traitent généralement les données sur le serveur principal d’une application.
    - Rôles Web qui agissent en tant que le site Web frontal pour les applications.

**Stockage**

Gestion des données telles que les services de base de données de SQL Azure ou stockage Azure tels que le service de la Table qui fournit des services pour le stockage de grandes quantités de données non structurées et le service d’objet Blob qui fournit des services pour stocker de grandes quantités de texte non structuré ou des données binaires telles que vidéo, audio et images.

- Protection des données pouvant être partagée dans le cas de la base de données SQL par-locataire les connexions SQL Server.
- Sur des Tables d’Azure Application ressources en spécifiant une stratégie d’accès au conteneur, vous pouvez ajuster les autorisations sans avoir à émettre de nouvelles URL pour les ressources protégées avec les signatures d’accès partagé.
- Files d’attente Azure pour les files d’attente Azure de ressources d’Application sont généralement utilisés pour le traitement de lecteur pour le compte des locataires, mais peuvent également être utilisées pour distribuer le travail nécessaire à la mise en service ou de gestion.
- Files d’attente de Bus de service pour les ressources de l’Application qui exécute un push de travail vers un service, vous pouvez utiliser une seule file d’attente à où chaque expéditeur de clients dispose uniquement d’autorisations (telle que dérivée de créances émis par ACS) pour pousser à cette file d’attente, alors que seuls les destinataires à partir du service ont autorisé à extraire à partir de la file d’attente les données provenant des différents utilisateurs.


**Services de sécurité et de connexion**

- Bus des services Azure, une infrastructure de messagerie qui se trouve entre applications leur permettant d’échanger des messages d’une façon faiblement couplée pour échelle améliorée et la résilience.

**Services de mise en réseau**

Azure fournit plusieurs services réseau qui prennent en charge de l’authentification et améliorent la gestion de vos applications hébergées. Ces services sont les suivants :

- Azure permet de réseau virtuel vous configurez et gérer des réseaux privés virtuels (VPN) dans Azure ainsi que liez en toute sécurité avec sur site infrastructure informatique.
- Gestionnaire de trafic de réseau virtuel vous permet de charger équilibre le trafic entrant entre plusieurs services Azure hébergés si ils exécutent dans le même centre de données ou sur des centres de données différents dans le monde entier.
- Azure Active Directory (AD Azure) est un service qui fournit des fonctionnalités de contrôle de gestion et d’accès identité pour vos applications de cloud moderne, basée sur REST. À l’aide d’Active Directory Azure pour les ressources d’Application Azure annonce fournit un moyen simple d’authentification et d’autorisation des utilisateurs à accéder à vos applications et services web, tout en autorisant les fonctionnalités d’authentification et d’autorisation pour être factorisée à partir de votre code.
- Bus des services Azure fournit une messagerie sécurisée et distribués de la fonctionnalité de flux de données pour des applications hybrides, telles que les communications entre Azure hébergés les applications et les applications sur site et les services, sans nécessiter d’infrastructures de pare-feu et de sécurité complexes. À l’aide du relais de Bus de Service pour les ressources de l’Application aux services qui sont exposés comme points de terminaison peut-être appartenir au locataire (par exemple, hébergé à l’extérieur du système et notamment sur site), ou ils peuvent être des services configurés spécifiquement pour le locataire (car les données sensibles, spécifiques au locataire se déplacent entre eux).



**Provisionnement des ressources**

Azure fournit un certain nombre de façons locataires de la nouvelle disposition de l’application. Pour les applications mutualisées avec un grand nombre de clients, il est généralement nécessaire automatiser ce processus en activant l’attribution en libre-service.

- Rôles de travail permettent de mettre en service et la fourniture par le locataire ressources (par exemple, lorsqu’un nouveau locataire des signes ou annule), collecter des mesures de contrôle utiliser et gérer l’échelle suivant une certaine planification ou en réponse à la traversée des seuils d’indicateurs de performance clés. Ce même rôle peut également être utilisé pour diffuser les mises à jour et mises à niveau à la solution.
- BLOB Azure peut être utilisé pour fournir le calcul ou préalablement initialisé Packages, images de disque dur virtuel et d’autres ressources du service des ressources de stockage aux locataires de nouveau tout en fournissant des stratégies d’accès au conteneur à protéger le calcul.
- Options pour le provisionnement des ressources de base de données de SQL pour un client sont les suivantes :

    -   DDL dans les scripts ou incorporés comme ressources dans des assemblys
    -   SQL Server 2008 R2 DAC les Packages déployés par programme.
    -   Copie à partir d’une base de données de référence principales
    -   À l’aide de la base de données d’importation et d’exportation pour la fourniture de nouvelles bases de données à partir d’un fichier.



<!--links-->

[Hébergement d’une Application partagée sur Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
