<properties
    pageTitle="Service d’application Azure et son impact sur les services Azure existants"
    description="Explique comment le nouveau Service d’application Azure et de ses fonctionnalités impact sur des services existants dans Azure."
    services="app-service"
    documentationCenter=""
    authors="yochay"
    manager="nirma"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="yochayk"/>


# <a name="azure-app-service-and-existing-azure-services"></a>Service d’application Azure et services Azure existants

Cet article décrit les modifications apportées aux services Azure existants dans le cadre de la modification de réunir plusieurs services Azure dans le [Service d’application Azure](https://azure.microsoft.com/services/app-service/), une offre intégrée de nouveau.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="overview"></a>Vue d’ensemble

[Service d’application Azure](https://azure.microsoft.com/services/app-service/) est un service en nuage nouvelle et unique qui permet aux développeurs de créer du web et des applications mobiles pour n’importe quelle plate-forme et n’importe quel périphérique. Application de Service est une solution intégrée conçue pour rationaliser les fonctions de codage répétées, intégrer à l’entreprise et les systèmes de SaaS et automatiser leurs processus métiers tout en respectant vos besoins en matière de sécurité, de fiabilité et d’évolutivité.

Service d’application rassemble suivants Azure services existants - [sites Web](https://azure.microsoft.com/services/websites/), les [Services mobiles](https://azure.microsoft.com/services/mobile-services/)et les [Services Biztalk](https://azure.microsoft.com/services/biztalk-services/) dans un seul service combiné, lors de l’ajout de nouvelles fonctionnalités puissantes.  Service d’application vous permet d’héberger les types d’application suivants :

-   Applications Web
-   Applications mobiles
-   Applications d’API
-   Applications de logique

Le tableau suivant explique comment les services Azure mappage, au Service de l’application et les types d’application disponibles au sein de celui-ci.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Service Azure existant</th>
<th align="left", style="width:10%">Service d’application Azure</th>
<th align="left", style="width:80%">Ce qui a changé</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites Web Azure</td>
<td align="left">Applications Web</td>
<td align="left"><li>Pour les sites Web d’Azure, Service d’application est strictement limitée à la modification du nom des sites Web à des applications Web.
<p><li>Toutes les instances existantes des sites Web sont désormais des applications Web dans le Service d’application.</p>
<p><li>Vous pouvez accéder à vos sites Web existants via le <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portail Azure</a>, où vous trouverez tous vos sites existants dans <em>Les applications Web</em>.</p>
<p><li><em>Web hébergeant le Plan</em> est à présent <em>Plan de Service d’application</em>. Un <em>Plan de Service d’application</em> peut héberger de n’importe quel type d’application de Service d’application, telles que les applications Web, Mobile, logique ou API.</p>
<p><li>Azure Application Service Web Apps est en général la disponibilité.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">En savoir plus sur les applications Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Services mobiles Azure</td>
<td align="left">Applications mobiles</td>
<td align="left"><p><li>Services mobiles continuent d’être disponibles comme un service autonome restent totalement pris en charge.</p>
<p><li>Applications Mobile est un type d’application dans le Service d’application, qui intègre toutes les fonctionnalités de Services mobiles et bien plus encore.</p>
<p><li>Il est facile de <a href="http://go.microsoft.com/fwlink/?LinkID=724279&clcid=0x409">migrer à partir des Services mobiles pour les applications mobiles</a>.</p>
<p><li>Dans le cadre du Service de l’application, applications Mobile obtenir nouvelles fonctionnalités au-delà de Services mobiles, tels que l’intégration avec des locaux et des systèmes de SaaS, emplacements, WebJobs, options d’échelle mieux et plus la zone de transit.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">En savoir plus sur les applications Mobile</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Applications d’API</td>
<td align="left">
<p><li>Applications d’API est un nouveau type d’application dans le Service d’application qui vous permet de facilement générer et utiliser des API dans le nuage.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">En savoir plus sur les applications d’API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Applications de logique</td>
<td align="left">
<p><li>Logique d’applications est un nouveau type d’application dans le Service d’application qui vous permet d’automatiser facilement les processus d’entreprise.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">En savoir plus sur les applications de la logique de</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Services BizTalk Azure</td>
<td align="left">Applications BizTalk API</td>
<td align="left">
<li><p>Les Services BizTalk continuent d’être disponibles comme un service autonome restent totalement pris en charge.</p>
<li><p>Toutes les fonctionnalités des Services BizTalk sont intégrées dans le Service d’application qui permet aux utilisateurs des applications API pour effectuer l’intégration et scénarios d’intégration B2B avec tous les types d’application dans le Service d’application</p>
<li><p>Avec des applications de logique, vous pouvez désormais automatiser des processus d’entreprise à l’aide d’une expérience de conception visuelle pour créer des workflows</p></td>
</tr>
</tbody>
</table>

Pour en savoir plus, visitez la [documentation du Service d’application](https://azure.microsoft.com/documentation/services/app-service/).
