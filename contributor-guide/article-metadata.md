

#<a name="metadata-for-azure-technical-articles"></a>Métadonnées pour Azure articles techniques

Tous les articles techniques Azure contient deux sections de métadonnées - une section de propriétés et une section de balises. La section Propriétés permet à certains automation de site Web et les sélections SEO, tandis que la section balises permet à un grand nombre de rapports de contenu interne. Les deux sections sont requises.

- [Syntaxe]
- [Utilisation de]
- [Les attributs et les valeurs de la section Propriétés]
- [Les attributs et les valeurs de la section de balises]

##<a name="syntax"></a>Syntaxe

La section Propriétés utilise la syntaxe suivante :

    <properties
       pageTitle="Page title that displays in search results and the browser tab | Microsoft Azure"
       description="Article description that will be displayed on landing pages and in most search results"
       services="service-name"
       documentationCenter="dev-center-name"
       authors="GitHub-alias-of-only-one-author"
       manager="manager-alias"
       editor=""
       tags="optional"
       keywords="For use by SEO champs only. Separate terms with commas. Check with your SEO champ before you change content in this article containing these terms."/>

La section balises utilise la syntaxe suivante :

    <tags
       ms.service="required"
       ms.devlang="may be required"
       ms.topic="article"
       ms.tgt_pltfrm="may be required"
       ms.workload="na"
       ms.date="mm/dd/yyyy"
       ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

##<a name="usage"></a>Utilisation de

- Le nom de l’élément et les noms d’attribut respectent la casse.
- Le <properties> section doit être la première ligne de votre fichier.
- Laisser une ligne vide après chaque section de métadonnées et avant le titre de votre page pour vous assurer que le titre de la page est correctement converti en HTML au cours du processus de publication.

## <a name="attributes-and-values-for-the-properties-section"></a>Les attributs et les valeurs de la section Propriétés

![](./media/article-metadata/checkmark-small.png)**pageTitle**: obligatoire ; important pour SEO. Le texte de cet attribut apparaît dans l’onglet du navigateur comme titre dans les résultats de la recherche. 55-60 caractères, espaces compris et y compris l’identificateur de site *| Microsoft Azure* (de type : espace espace de tuyau Microsoft Azure).  Le pageTitle doit être différente de la H1.

![](./media/article-metadata/checkmark-small.png)**Description**: obligatoire ; important pour SEO (pertinence) et la fonctionnalité du site. La description doit être d’au moins 125 caractères longs à 155 caractères maximum, espaces compris. Décrire le rôle de votre contenu afin que les clients savent, il faut choisir dans la liste des résultats de la recherche. La valeur est :

- Ce texte peut être affiché en tant que la description ou le paragraphe abstrait dans les résultats de recherche sur Google.
- Ce texte est affiché dans [les résultats de l’index l’article](https://azure.microsoft.com/documentation/articles/).

![](./media/article-metadata/checkmark-small.png)**services**: requis pour les articles qui traitent avec un service. Cette valeur est ofter appelée la « service ligne-bloc ». Liste de tous les services applicables, séparés par des virgules. Le premier service que vous avez indiquée déterminera les arborescences de navigation de navigation pour la page et la navigation de gauche s’affiche avec la page.

Dans les articles qui spécifient une valeur de services et une valeur documentationCenter, la valeur de services déterminera le plan du site. Valeurs supplémentaires qui vous liste apparaîtra sous forme de balises dans l’article publié. Valeurs :

- Active directory
- Active-directory-b2c
- Active-directory-ds
- application-service\api
- API-gestion
- service de l’application
- application-servic\mobile
- application-service\web
- application-service\logic
- passerelle d’application
- Aperçu de l’application
- Automation
- portail d’Azure
- Gestionnaire de ressources Azure
- pile d’Azure
- sauvegarde
- traitement par lots
- meilleures pratiques
- services BizTalk
- mémoire cache
- Paie Canada-Fichier
- services de cloud
- catalogue de données
- données en usine
- données-lac-analytique
- magasin de données lac
- devtest-lab
- DNS
- documentdb
- expressroute
- événement-hubs.
- hdinsight
- IOT-hub
- chambre forte de la clé
- équilibrage de la charge
- Poste-formation
- Marketplace
- services multimédia
- contrat de Mobile
- les services mobiles
- multi-factor-authentification
- notification-hubs.
- perspectives opérationnelles
- suite de gestion des opérations
- powerapps
- Gestionnaire de récupération
- redis-cache
- RemoteApp
- gestion des droits
- Planificateur
- recherche
- Centre de sécurité
- bus des services
- service-fabric
- site-récupération
- bases de données SQL
- entrepôt de données SQL
- génération de rapports de SQL
- stockage
- banque d’informations
- storsimple
- flux-analytique
- Gestionnaire de trafic
- machines virtuelles
- réseau virtuel
- Visual studio-en ligne
- passerelle VPN
- sites Web

![](./media/article-metadata/checkmark-small.png)**documentationCenter**: requis pour les articles orientée dev meilleures proposés par un centre de développement. Spécifiez le centre de développement unique ou d’une langue qui s’applique à l’article. La valeur que vous répertoriez déterminera les arborescences de navigation de navigation pour la page. Dans les articles qui spécifient une valeur de services et une valeur documentationCenter, la valeur de services déterminera le plan du site. Valeurs :

- **.NET**
- **nodejs**
- **Java**
- **PHP**
- **Python**
- **Ruby**
- **Mobile**: déconseillée. Remplacez par une plate-forme mobile spécifique.
- **IOS**: vérification de cette nouvelle valeur.
- **Android**: vérification de cette nouvelle valeur.
- **Windows**: vérification de cette nouvelle valeur.
- **xamarin**: vérification de cette nouvelle valeur.

![](./media/article-metadata/checkmark-small.png)**auteurs**: requis, une seule valeur. Le compte GitHub pour l’auteur principal ou les PME de l’article de la liste. Cet attribut lecteurs de la signature sur l’article publié. Répertorie uniquement l’une, malgré le nom au pluriel de l’attribut.

![](./media/article-metadata/checkmark-small.png)**Manager**: requis si vous êtes un collaborateur de Microsoft. Liste les alias de messagerie du Gestionnaire de publication contenu pour la zone de technologie. Si vous êtes un collaborateur de la Communauté, incluez l’attribut mais laissez vide afin que nous puissions le remplir.

![](./media/article-metadata/checkmark-small.png)**éditeur**: non utilisé. Ne l’utilisez pas à d’autres fins.

![](./media/article-metadata/checkmark-small.png)**balises**: facultatif. Inclure uniquement si vous souhaitez activer un lien sous le plan du site de l’article à la page d’index de l’article (http://azure.microsoft.com/documentation/articles/) à la liste prefiltered des articles qui correspondent à une des valeurs d’approuvé. Ces valeurs sont destinés à fournir un moyen de regrouper des contenus lorsque le regroupement de contenu n’est pas spécifique au service. Ces balises permettent également d’étiquetage qui indique à l’environnement technologique l’article s’applique à. Cette valeur **ne** prend en charge les balises de forme libre ou de hashtags ; les balises doivent être activées sur le site. Vous pouvez fournir plusieurs valeurs de balises à un article, séparés par des virgules. Les valeurs approuvés sont :

  - architecture
  - Gestionnaire de ressources Azure
  - gestion des services Azure
  - facturation
  - MySQL

![](./media/article-metadata/checkmark-small.png)**mots clés**: facultatif. Pour une utilisation par uniquement les champs SEO. Séparez les termes par des virgules. **Vérifiez auprès de votre champ SEO avant de modifier ou de supprimer le contenu dans cet article contenant ces termes.** Cet attribut enregistre le champ SEO a ciblé et effectue le suivi afin d’améliorer le rang de la recherche de mots-clés. Les mots clés ne sont pas rendent dans la page HTML publiée. Validation ne requiert pas cet attribut.

## <a name="attributes-and-values-for-the-tags-section"></a>Les attributs et les valeurs de la section de balises

![](./media/article-metadata/checkmark-small.png)**MS.service**: obligatoire. Spécifie le service Azure, outil ou fonctionnalité auquel s’applique l’article. Une seule valeur par page.

 Si une page s’applique à plusieurs services, sélectionnez le service auquel il plus directement s’applique ; par exemple, un article qui utilise une application hébergée sur des sites web pour démontrer les fonctionnalités de Bus de Service doit avoir la valeur du **bus des services** , plutôt que **des sites web**. Si une page s’applique également aux services multiples, choisissez **plusieurs**. Si une page ne s’applique pas à tous les services (il s’agira rare), choisissez **NA**.

 - **Active directory**
 - **Active-directory-b2c**
 - **Active-directory-ds**
 - **API-gestion**
 - **service de l’application**: s’applique uniquement à des informations conceptuelles générales sur le Service de l’application
 - **api du service d’application**
 - **logique du service d’application**
 - **app-service-mobile**
 - **app-service-web**
 - **Aperçu de l’application**
 - **passerelle d’application**
 - **Automation**
 - **Gestionnaire de ressources Azure**
 - **sécurité Azure**
 - **pile d’Azure**
 - **sauvegarde**
 - **traitement par lots**
 - **meilleures pratiques**
 - **services BizTalk**
 - **facturation**
 - **mémoire cache**
 - **Paie Canada-Fichier**
 - **services de cloud**
 - **catalogue de données**
 - **magasin de données lac**
 - **données-lac-analytique**
 - **devtest-lab**
 - **expressroute**
 - **hdinsight**
 - **Internet des objets**
 - **IOT-hub**
 - **chambre forte de la clé**
 - **Poste-formation**
 - **marché**: Articles sur le marché Azure
 - **services multimédia**
 - **contrat de Mobile**
 - **les services mobiles**
 - **multi-factor-authentification**
 - **plusieurs**: la page s’applique également aux services multiples
 - **NA**: la page ne s’applique pas à tous les services (rares)
 - **notification-hubs.**
 - **perspectives opérationnelles**
 - **powerapps**
 - **Gestionnaire de récupération**
 - **redis-cache**
 - **RemoteApp**
 - **gestion des droits**
 - **Planificateur**
 - **Centre de sécurité**
 - **bus des services**
 - **service-fabric**
 - **site de restauration**: anciennement-services de récupération
 - **bases de données SQL**
 - **entrepôt de données SQL**
 - **génération de rapports de SQL**
 - **stockage**
 - **stocker**: Articles sur les services disponibles par le biais de la banque d’Azure
 - **storsimple**
 - **Gestionnaire de trafic**
 - **machines virtuelles**
 - **réseau virtuel**
 - **Visual studio-en ligne**
 - **passerelle VPN**
 - **sites Web**

![](./media/article-metadata/checkmark-small.png)**MS.DevLang**: obligatoire. Spécifie le langage de programmation auquel s’applique l’article. Valeur unique par page.

 Si une page s’applique également aux deux langages de programmation, choisissez **plusieurs**. Si une page est avant tout conceptuelle et son contenu est généralement applicable à plusieurs langages de programmation, choisissez **plusieurs**. Si une page n’est pas ciblée sur les développeurs et l’applicabilité de langage de programmation n’est pas pertinente, choisissez **NA**. **Api rest** permet d’identifier les rubriques de référence API REST.

 - **cpp**
 - **dotnet**
 - **Java**
 - **JavaScript**
 - **plusieurs**: la page s’applique également à plusieurs langages de programmation.
 - **no**: ne vise pas les développeurs de la page et n’est pas spécifique à des langages de programmation.
 - **nodejs**
 - **objectif-c**
 - **PHP**
 - **Python**
 - **api de REST**
 - **Ruby**


![](./media/article-metadata/checkmark-small.png)**MS.Topic**: obligatoire. Caractéristiques de l’objet de type de la rubrique. La plupart des nouvelles pages créées par les collaborateurs sera l’article ou la référence.

 - **article**: une rubrique conceptuelle, didacticiel, guide de fonctionnalités ou autre article sans référence

 - **page de campagne**: Azure.com uniquement.  Une page qui est spécialement conçue comme une page de lancement de campagnes de marketing externes et n’est pas inclus dans le cadre du site principal IA.  Ne doit pas être utilisée pour les articles de la documentation ou doc régulière pages de lancement.  Exemples :, azure.microsoft.com/develop/net/aspnet/ ; Azure.Microsoft.com/Develop/mobile/IOS/

 - **dev-centre-page d’accueil**: Azure.com uniquement.  Un développement, par exemple, page d’accueil du centre/développement/net /

 - **Get-démarré-article**: affecter à des articles qui sont disponibles dans la section mise en route ou de la vue d’ensemble de la navigation de gauche pour un service.

 - **héros-article**: un didacticiel « héros » qui est conçu pour fournir une introduction à un service ou une fonctionnalité qui obtient les visiteurs démarrés à l’aide du service de rapidement et lecteurs des abonnements d’essai gratuite et les activations de MSDN. Affecter cette valeur uniquement pour les articles en haut de la page de lancement de documentation pour votre service.

 - **page d’accueil**: page d’accueil de documentation de niveau supérieur. Nous avons deux : azure.microsoft.com/documentation/ et msdn.microsoft.com/library/azure/

 - **page d’index**: pages de langues, des services ou des fonctionnalités de programmation de lancement de Second niveau. Ces sont répartis entre les Azure.com et de la bibliothèque et sont utilisés comme points d’entrée pour des informations plus spécifiques, de portée limitées. Exemples : http://azure.microsoft.com/develop/mobile/resources-wp8/, http://msdn.microsoft.com/library/azure/jj673460.aspx, http://msdn.microsoft.com/library/azure/hh689864.aspx

 - **page infographic**: Azure.com uniquement. Une page qui propose une infographic consultable ou une affiche, par exemple http://azure.microsoft.com/documentation/infographics/windows-azure/

 - **référence**: page de référence d’une API (y compris les API REST) ou de la page de référence d’applet de commande PowerShell

 - **service-page d’accueil**: Azure.com uniquement.  Une page d’accueil d’un service doc, par exemple, /documentation/services/virtual-machines /

 - **site-section-page d’accueil**: Azure.com uniquement. Une « page d’accueil » pour un type particulier de contenu sur les azure.com exemples : http://azure.microsoft.com/documentation/infographics/, http://azure.microsoft.com/documentation/scripts/, http://azure.microsoft.com/documentation/videos/home/, http://azure.microsoft.com/downloads/

 - **page de la vidéo**: Azure.com uniquement.  Une page qui propose une vidéo, par exemple http://azure.microsoft.com/documentation/videos/azure-webjobs-hosting-testing-net/

![](./media/article-metadata/checkmark-small.png)**MS.tgt_pltfrm**: obligatoire. Spécifie la plateforme cible, par exemple Windows, Linux, Windows Phone, iOS, Android ou plates-formes de cache spécial. Une seule valeur par page. Cette valeur sera **NA** pour la plupart des rubriques à l’exception de mobile et des ordinateurs virtuels.

 - **rôle de cache**
 - **cache-multiples**
 - **cache-redis**
 - **cache de service**
 - **cache-partagé**
 - **interface de ligne de commande**
 - **Ibiza**: le contenu qui utilise le portail de Ibiza. Utilisez cette option uniquement dans les cas où la fonctionnalité examinée est disponible sur le portail de Ibiza et le portail actuel.
 - **Mobile-android**: Azure.com maintenant uniquement
 - **Mobile-html**: Azure.com maintenant uniquement
 - **ios-mobile**: Azure.com maintenant uniquement
 - **Mobile-kindle**: Azure.com maintenant uniquement
 - **Mobile-multiples**
 - **Mobile-nokia-x**: Azure.com maintenant uniquement
 - **Mobile-phonegap**: Azure.com maintenant uniquement
 - **Mobile-sencha**: Azure.com maintenant uniquement
 - **windows mobile**: Azure.com uniquement maintenant ; Universel de Windows
 - **windows-téléphone mobile**
 - **magasin de windows mobile**
 - **Mobile-xamarin**: Azure.com uniquement maintenant ; Xamarin toutes les plates-formes
 - **Mobile-xamarin-android**: Azure.com maintenant uniquement
 - **Mobile-xamarin-e/s**: Azure.com maintenant uniquement
 - **plusieurs**: la page s’applique également à plusieurs plates-formes
 - **NA**: un spécificateur de plate-forme n’est pas applicable pour cette page
 - **PowerShell**
 - **machine virtuelle linux**
 - **mémoire virtuelle-multiples**
 - **machine virtuelle windows**
 - **mémoire virtuelle-windows-sharepoint**
 - **machine virtuelle windows sql server**
 - **VS-mise en route**: identifie le groupe VS mise en route de la page. Balise de 1/12/14.
 - **VS-simulation-est devenu**: identifie la page VS mise en route ce qui est arrivé. Balise de 1/12/14.

![](./media/article-metadata/checkmark-small.png)**MS.Workload**: obligatoire. Spécifie la charge de travail Azure correspondant à la page. Une seule valeur par article.

**mettre à jour le 15/6/8** La valeur de ms.workload est mappée par un xls, pas la valeur dans le fichier .md. La valeur de ms.workload est toujours nécessaire pour la validation jusqu'à ce que la fonction peut être mis à jour. Ce travail est maintenant planifié.  Veuillez utiliser **« NC »** comme valeur pour l’instant.

![](./media/article-metadata/checkmark-small.png)**MS.date**: obligatoire. Spécifie la date de que la dernière vérification de l’article de pertinence, la précision, captures d’écran correct et des liens de travail. Entrez la date dans le format jj/mm/aaaa. Cette date s’affiche également dans l’article publié en tant que la date de la dernière mise à jour.

![](./media/article-metadata/checkmark-small.png)**MS.Author**: obligatoire. Spécifie les auteurs de la rubrique. Des rapports internes (par exemple, fraîcheur) utilisent cette valeur pour associer les auteurs du droit à l’article. Pour spécifier plusieurs valeurs vous devez les séparer par des points-virgules. Alias de Microsoft, ou adresses de messagerie complètes sont acceptables. La longueur peut être pas dépasse 200 caractères.


###<a name="contributors-guide-links"></a>Liens du Guide des collaborateurs

- [Article de présentation](./../README.md)
- [Index des articles sur les instructions](./contributor-guide-index.md)


<!--Anchors-->
[Syntaxe]: #syntax
[Utilisation de]: #usage
[Les attributs et les valeurs de la section Propriétés]: #attributes-and-values-for-the-properties-section
[Les attributs et les valeurs de la section de balises]: #attributes-and-values-for-the-tags-section
