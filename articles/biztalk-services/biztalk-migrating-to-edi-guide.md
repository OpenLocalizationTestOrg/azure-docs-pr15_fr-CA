<properties   
    pageTitle="Migration des Solutions EDI de BizTalk Server vers le Guide technique des Services BizTalk | Microsoft Azure"
    description="Migrer l’EDI à MABS ; Services BizTalk Microsoft Azure"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migration des Solutions EDI de BizTalk Server à BizTalk Services : Guide technique

Auteur : Tim Wieman et Nitin Mehrotra

Relecteurs : Karthik Bharthy

Écrit à l’aide : mise à jour Microsoft Azure BizTalk Services – février 2014.

## <a name="introduction"></a>Introduction

Échange de données électroniques (EDI) est un des moyens plus courants par les entreprises des données exchange par voie électronique, également appelé en tant que transactions Business-to-Business ou B2B. BizTalk Server a été prise en charge EDI de plus d’une décennie, depuis la première version de BizTalk Server. Avec les Services BizTalk, Microsoft poursuit la prise en charge pour les solutions EDI sur la plate-forme Microsoft Azure. Les transactions B2B sont principalement externes pour une organisation, et, par conséquent, il est plus facile à implémenter si elle a été implémentée sur une plate-forme de nuage. Microsoft Azure fournit cette fonctionnalité via les Services BizTalk.

Bien que certains clients regardez des Services BizTalk en tant que « nouveau » plate-forme de nouvelles solutions EDI, de nombreux clients ont des solutions EDI de BizTalk Server en cours qui souhaitent migrer vers Azure. Comme EDI de BizTalk Services est conçue selon les mêmes entités de clé comme architecture EDI de BizTalk Server (commerciaux des partenaires, des entités, des accords), il est possible de migrer des artefacts de l’EDI de BizTalk Server à BizTalk Services.

Ce document décrit certaines des différences liées à la migration des artefacts de EDI de BizTalk Server à BizTalk Services. Ce document suppose une connaissance pratique de traitement de l’EDI de BizTalk Server et les accords entre partenaires commerciaux. Pour plus d’informations sur l’EDI de BizTalk Server, consultez [Commercial partenaire gestion à l’aide de BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Quelle version d’artefacts de l’EDI de BizTalk Server peut être migrée vers les Services BizTalk ?

Le module EDI de BizTalk Server a été considérablement amélioré pour BizTalk Server 2010, lorsqu’il a été REMODÉLISATION d’inclure les partenaires, les profils et les accords. Les Services BizTalk utilise le même modèle pour organiser les partenaires commerciaux et les divisions de l’entreprise au sein de ces partenaires commerciaux. Par conséquent, la migration des artefacts de l’EDI dans BizTalk Server 2010 et les versions ultérieures aux Services BizTalk, est un processus beaucoup plus simple. Pour effectuer la migration des artefacts EDI associés avec les versions antérieures de BizTalk Server 2010, vous devez tout d’abord mettre à niveau vers BizTalk Server 2010 et puis migrer vos artefacts EDI BizTalk Services.

## <a name="scenariosmessage-flow"></a>Flux de scénarios/Message

Comme avec BizTalk Server, EDI dans BizTalk Services repose sur une solution de gestion des partenaires commerciaux (GPC). La solution de module de plateforme sécurisée présente les composants clés suivants :

- Partenaires commerciaux, qui représentent l’organisation dans une transaction B2B.
- Profils, qui représentent les divisions d’un partenaire commercial.
- Négociation d’accords de partenariat (ou accords), qui représentent l’accord entreprise entre deux partenaires/profils.

L’illustration suivante représente les similitudes, ainsi que les différences, entre une solution EDI de BizTalk Server et d’une solution BizTalk Services EDI :

![][EDImessageflow]

Les principales différences et similarités, un flux de solution EDI dans BizTalk Server et les Services BizTalk sont les suivantes :

- Comme BizTalk Server utilise un pipeline EDIReceive pour recevoir un message EDI et un pipeline EDISend pour envoyer un message EDI, Services BizTalk utilise un pont de réception EDI de recevoir et d’un EDI envoyer de pont pour envoyer des messages EDI. Dans BizTalk Server, les pipelines sont associés à un contrat à l’aide d’envoyer ou de recevoir des ports. Dans les Services BizTalk, l’accord désigne l’envoyer ou recevoir le pont.
- Dans BizTalk Server, une fois que le pipeline EDIReceive traite le message EDI, le message est exportée vers une base de données SQL Server. Le pipeline EdiSend puis récupère le message à partir de la base de données SQL Server, traite et il envoie ensuite au partenaire.

    Dans les Services BizTalk, après réception de l’EDI pont traite le message EDI, il achemine le message vers un processus externe. Le processus externe peut s’exécuter sur Microsoft Azure ou sur site. Le processus externe doit router le message vers le pont d’envoi EDI ; le pont d’envoi n’extrait, par nature, le message. Après avoir traité le message, le pont d’envoi EDI achemine le message vers le partenaire commercial.

Les Services BizTalk fournit une expérience faciles à utiliser la configuration pour créer et déployer un accord B2B entre les partenaires commerciaux sans configuration d’une carte Microsoft Azure Compute instances (rôles Web ou de travail), les bases de données de SQL Azure Microsoft ou des comptes de stockage Microsoft Azure. Des scénarios plus complexes nécessite réunissant des workflows ou tout autre traitement de service « autour des bords » d’un accord partenaire commercial, c'est-à-dire avant ou après le traitement de pont EDI de contrat de partenaire commercial. Dans le détail, les séquences suivantes d’événements se produisent au cours d’un traitement dans les Services BizTalk du message EDI.

1. Un message EDI provenant de partenaire, Fabrikam.  Pour la réception des messages EDI de partenaires commerciaux, les Services BizTalk prend en charge les protocoles de transport comme FTP, SFTP, AS2 et HTTP/S.

2. Le traitement de côté réception d’accord partenaire commercial désassemble le message EDI au format XML.  Vous pouvez router le message EDI désassemblé (en format XML) aux points de terminaison de Bus de Service comme un point de terminaison de Service Bus relais, rubrique Bus de Service, file d’attente de Bus de Service ou un pont de BizTalk Services.

3. Les messages XML désassemblés peuvent ensuite être reçus à partir du point de terminaison pour un traitement personnalisé supplémentaire.  Ces points de terminaison peuvent être traitées par un composant sur site ou à une instance de Microsoft Azure Compute pour poursuivre le traitement du message dans un service Windows Communication Foundation (WCF) ou de flux de travail WF (Windows), par exemple.

4. Le « traitement côté envoi » du partenaire commercial accord puis assemble le message XML en format EDI et l’envoie au partenaire commercial, Contoso.  Pour l’envoi des messages EDI à des partenaires commerciaux, les Services BizTalk prend en charge les mêmes protocoles que ceux utilisés pour la réception des messages EDI.

Ce document plus fournit une aide conceptuelle sur la migration des différents artefacts EDI de BizTalk Server aux Services BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Envoyer/recevoir des Ports à des partenaires commerciaux

Dans BizTalk Server, vous définissez les emplacements de réception et les Ports de réception pour recevoir les messages EDI/XML à partir des partenaires commerciaux, et les Ports d’envoi est configuré pour envoyer des messages EDI/XML à un partenaire commercial. Ensuite, vous vous privez ces ports pour un accord de partenariat commercial à l’aide de la console Administration de BizTalk Server. Dans les Services BizTalk, les emplacements où vous recevez des messages à partir des partenaires commerciaux et où vous envoyez des messages à des partenaires commerciaux sont configurés dans le cadre de l’accord de partenariat commercial lui-même (dans le cadre des paramètres de Transport) dans le portail de Services de BizTalk.  Donc vraiment ne pas le concept de « ports d’envoi » et « recevoir les emplacements », par leur nature, dans les Services BizTalk. Pour plus d’informations, consultez [Création de contrats](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (ponts)

Dans l’EDI de BizTalk Server, les pipelines sont des entités de traitement de message qui peuvent également inclure une logique personnalisée pour des capacités de traitement spécifique, comme requis par l’application. Pour les Services BizTalk, l’équivalent serait un pont EDI. Toutefois dans les Services BizTalk, pour le moment, les ponts EDI sont « fermés ».  En d’autres termes, vous ne pouvez ajouter vos propres activités personnalisées à un pont EDI. Tout traitement personnalisé doit être effectuée en dehors de la passerelle EDI dans votre application, avant ou après le message entre dans le pont configuré dans le cadre de l’accord du partenaire commercial. Les ponts EAI ont la possibilité d’effectuer un traitement personnalisé. Si vous souhaitez un traitement personnalisé, vous pouvez utiliser les ponts EAI avant ou après le traitement du message par le pont EDI. Pour plus d’informations, consultez [comment inclure de Code personnalisé dans les ponts](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Vous pouvez insérer un flux avec code personnalisé et/ou à l’aide du Bus de Service d’échanges les rubriques et les files d’attente avant que l’accord de partenariat commercial reçoit le message, ou une fois l’accord traite le message et le dirige vers un point de terminaison de Bus des services de publication et d’abonnement.

Dans cette rubrique pour le modèle de flux de message, reportez-vous à la section **Flux de scénarios/messages** .

## <a name="agreements"></a>Accords de

Si vous êtes familiarisé avec les accords partenaire BizTalk Server 2010 échanger utilisées pour le traitement de l’EDI, les Services BizTalk négociation d’accords de partenariat sembler très familières. La plupart des paramètres de l’accord est les mêmes et utiliser la même terminologie. Dans certains cas, les paramètres de l’accord sont beaucoup plus simples par rapport aux mêmes paramètres dans BizTalk Server. Microsoft Azure BizTalk Services prend en charge X12, EDIFACT et AS2 de transport.

Microsoft Azure BizTalk Services fournit également un outil de **Migration de données de module de plateforme sécurisée** pour effectuer la migration des partenaires et des accords commerciaux à partir du module du partenaire d’échanges BizTalk Server au portail de Services de BizTalk. L’outil de Migration de données de module de plateforme sécurisée n’est disponible dans le cadre d’un package d’outils, qui peut être téléchargé à partir du [Kit de développement logiciel MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057). Le package inclut également un fichier readme qui fournit des instructions sur l’utilisation de l’outil et informations de dépannage de base pour l’outil.

## <a name="schemas"></a>Schémas

Les Services BizTalk fournit des schémas EDI qui peuvent être utilisés dans des solutions de Services de BizTalk.  En outre, les schémas EDI de BizTalk Server permet également aux Services BizTalk étant le nœud racine du schéma EDI même sur BizTalk Server, ainsi que les Services BizTalk. Ainsi, vous serez en mesure de prendre vos schémas EDI de BizTalk Server et de les utiliser dans les solutions EDI que vous développez à l’aide des Services BizTalk directement. Vous pouvez également télécharger les schémas à partir du [Kit de développement logiciel MABS](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Cartes (transformations)

Les mappages dans BizTalk Server sont appelées des transformations dans les Services BizTalk. Migration de mappages dans BizTalk Server aux Services BizTalk peuvent être une des tâches plus complexes à réaliser (en fonction de la complexité de carte). L’outil de mappage utilisé pour les Services BizTalk est différent du Mappeur BizTalk. Même si le mappeur de recherche essentiellement la même, le format de mappage sous-jacent est différent. Les fonctoids (appelés **Opérations de mappage** dans les Services BizTalk) disponibles pour les utilisateurs sont également différentes.  En effet, vous ne pouvez pas utiliser directement un mappage BizTalk dans les Services BizTalk. En outre, pas tous les fonctoids disponibles dans BizTalk Server sont disponibles en tant qu’opérations de mappage dans les Services BizTalk.

### <a name="new-transform-operations"></a>Des opérations de transformation

La liste des opérations de mappage de transformation disponibles peut sembler assez différente, le Mappeur BizTalk Server, BizTalk Services transforme ont des nouvelles façons d’accomplir les mêmes tâches. Par exemple, transforme les Services BizTalk ont la **Liste opérations** disponibles. Cela n’était pas disponible dans le Mappeur BizTalk.  Les **Opérations de la liste** permettent de créer et de faire fonctionner sur une « liste », où une liste est un ensemble d’éléments (également connu sous le nom « lignes ») et chaque élément peut avoir plusieurs membres (également connu sous le nom « colonnes »).  Vous pouvez trier la liste, sélectionnez les éléments en fonction d’une condition, un etc..

Un autre exemple de nouvelles fonctionnalités de BizTalk Services transforme sont les **Opérations de la boucle**.  Il est difficile de créer des boucles imbriquées dans le Mappeur BizTalk Server.  Ainsi, les opérations de carte de boucle sont ajoutées pour le transforme les Services BizTalk.

Encore un autre exemple est l’opération de mappage d’Expression **If-Then-Else** .  Une opération de type if-then-else était possible dans le Mappeur BizTalk, mais il nécessaire plusieurs fonctoids pour accomplir une tâche apparemment simple.

### <a name="migrating-biztalk-server-maps"></a>Migration de BizTalk Server est mappé.

Microsoft Azure BizTalk Services fournit un outil de migration de BizTalk Server mappe aux transformations des Services BizTalk. Le **BTMMigrationTool** est disponible en tant que partie du package des **Outils** fourni avec le [téléchargement du SDK des Services BizTalk](http://go.microsoft.com/fwlink/p/?LinkId=235057). Pour plus d’informations sur l’outil, consultez [convertir un mappage BizTalk à une transformation des Services BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Vous pouvez également consulter un échantillon par Sandro Pereira, MVP de BizTalk, comment [migrer les mappages de BizTalk Server à des transformations des Services BizTalk](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations

Si vous avez besoin migrer d’orchestration de BizTalk Server pour Microsoft Azure, les orchestrations devra être réécrits, car Microsoft Azure ne prend pas en charge les orchestrations BizTalk Server en cours d’exécution.  Vous pouvez réécrire la fonctionnalité d’orchestration dans un service Windows Workflow Foundation 4.0 (WF4).  Ce serait une réécriture complète, car il n’existe actuellement pas de migration dans les orchestrations BizTalk Server à WF4. Voici quelques ressources pour Windows Workflow :

- [*Comment intégrer un Service de Workflow WCF avec les rubriques et les files d’attente de Bus de Service*](https://msdn.microsoft.com/library/azure/hh709041.aspx) par Paolo Salvatori. 

- [ *Création d’applications avec Windows Workflow Foundation et Azure* session](http://go.microsoft.com/fwlink/p/?LinkId=237314) de la conférence Build 2011.

- [*Centre de développement Windows Workflow Foundation*](http://go.microsoft.com/fwlink/p/?LinkId=237315) sur MSDN.

- [*Documentation de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) sur MSDN.

## <a name="other-considerations"></a>Autres considérations

Voici quelques considérations que vous devez effectuer lors de l’utilisation des Services BizTalk.

### <a name="fallback-agreements"></a>Accords de secours

Traitement de l’EDI de BizTalk Server possède le concept de « Accords de secours ».  Les Services BizTalk effectuent **pas** ont un concept de secours accord jusqu'à présent.  Consultez les rubriques de la documentation BizTalk [Rôle d’accords dans le traitement de l’EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) et de [configuration Global ou propriétés de l’accord de secours](https://msdn.microsoft.com/library/bb245981.aspx) pour plus d’informations sur l’utilisation des accords de secours dans BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Routage vers plusieurs destinations

Ponts de Services de BizTalk, dans son état actuel ne pas prend en charge de routage des messages à plusieurs destinations à l’aide d’une publication-abonnement modèle. Au lieu de cela vous pourriez router les messages à partir d’un pont de Services BizTalk à une rubrique de Bus de Service, lequel peut ensuite établir de plusieurs abonnements à recevoir le message à plus d’un point de terminaison.

## <a name="conclusion"></a>Conclusion

Microsoft Azure BizTalk Services est mis à jour à des jalons réguliers pour ajouter plus de fonctionnalités et de capacités. Avec chaque mise à jour, nous nous réjouissons à la prise en charge des fonctionnalités améliorées pour faciliter la création de solutions de bout en bout à l’aide de BizTalk Services et autres technologies Azure.

## <a name="see-also"></a>Voir aussi

[Développement d’Applications d’entreprise avec Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
