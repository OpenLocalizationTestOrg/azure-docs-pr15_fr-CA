<properties
   pageTitle="Que sont les Services d’analyse Azure | Microsoft Azure"
   description="Afficher un récapitulatif complet des Services d’analyse dans Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="what-is-azure-analysis-services"></a>Nouveautés d’Azure Analysis Services ?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Basé sur le moteur analytique éprouvées dans Microsoft SQL Server Analysis Services, Azure Analysis Services fournit des données d’entreprise de modélisation dans le nuage.

> [AZURE.IMPORTANT] Azure Analysis Services est en **mode Aperçu**. Il existe certaines choses qui ne fonctionnent pas encore. Veillez à vérifier les [attentes d’aperçu](#preview-expectations) plus loin dans cet article. Et n’oubliez pas de garder un œil sur notre [blog d’Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) pour obtenir les dernières informations.

## <a name="built-on-sql-server-analysis-services"></a>Basé sur les Services d’analyse SQL Server
Azure Analysis Services est compatible avec le même SQL Server 2016 Analysis Services Enterprise Edition vous connaissez déjà. Azure Analysis Services prend en charge les modèles sous forme de tableau au niveau de compatibilité 1200. DirectQuery, partitions, sécurité de niveau ligne, les relations bidirectionnelles et traductions sont toutes prises en charge.

## <a name="use-the-tools-you-already-know"></a>Utilisez les outils que vous connaissez déjà
![Outils de développement BI](./media/analysis-services-overview/aas-overview-dev-tools.png)

Lors de la création de modèles de données pour Azure Analysis Services, vous utilisez les mêmes outils que pour SQL Server Analysis Services. Créer et déployer des modèles sous forme de tableau à l’aide des dernières versions des [Outils de données SQL Server (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) ou à l’aide de modèles de [Powershell d’Azure](../powershell-install-configure.md) et [d’Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) dans [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Se connecter aux sources de données
Modèles de données déploiement sur serveurs dans Azure prise en charge de connexion aux données sources sur les sites de votre organisation ou dans le nuage. Combiner des données issues à la fois sur site et les sources de données pour une solution Décisionnelle de hybride en nuage.

![Sources de données](./media/analysis-services-overview/aas-overview-data-sources.png)

Votre serveur étant dans le nuage, connexion à des sources de données de nuage est transparente. Lors de la connexion aux sources de données sur site, [passerelle de données sur site](analysis-services-gateway.md) garantit des connexions rapides et sécurisées avec votre serveur Analysis Services dans le nuage.  

 \*Certaines sources de données ne sont pas encore pris en charge dans l’aperçu. Pour plus d’informations, consultez [les attentes aperçu](#preview-expectations) plus loin dans cet article.

## <a name="explore-your-data-from-anywhere"></a>Explorez vos données depuis n’importe où
Se connecter et [d’obtenir des données](analysis-services-connect.md) à partir de vos serveurs à partir de sur anywhere. Azure Analysis Services prend en charge la connexion à partir d’alimentation BI bureau, Excel, des applications personnalisées et outils basés sur le navigateur.

![Visualisations de données](./media/analysis-services-overview/aas-overview-visualization.png)

 \*Alimentation BI incorporé n'est pas encore pris en charge dans l’aperçu.

## <a name="secure"></a>Sécuriser

#### <a name="user-authentication"></a>Authentification de l’utilisateur
Authentification de l’utilisateur pour Azure Analysis services est traitée par [Azure Active Directory (DAS)](../active-directory/active-directory-whatis.md). Lors de la tentative de connexion à une base de données Azure Analysis Services, les utilisateurs utilisent une identité de compte de société disposant d’un accès à la base de données qu’ils tentent d’accéder. Ces utilisateurs doivent appartenir à Azure Active Directory par défaut pour l’abonnement où réside le serveur Azure Analysis Services. [Intégration d’annuaire](https://technet.microsoft.com/library/jj573653.aspx) entre les DAS et sur site Active Directory est un excellent moyen pour obtenir vos locaux aux utilisateurs d’accéder à une base de données Azure Analysis Services, mais il n’est pas obligatoire pour tous les scénarios.

Les utilisateurs se connectent avec le nom utilisateur principal (UPN) de leur compte et leur mot de passe. Lors de la synchronisation avec Active Directory sur site, UPN de l’utilisateur est souvent leur adresse de messagerie d’organisation.

Les autorisations pour la gestion de la ressource de serveur Azure Analysis Services sont traitées en assignant des utilisateurs à des rôles dans votre abonnement Azure. Par défaut, les administrateurs d’abonnement ont des autorisations de propriétaire de la ressource serveur dans Azure. Des utilisateurs supplémentaires peuvent être ajoutés à l’aide du Gestionnaire de ressources Azure.

#### <a name="data-security"></a>Sécurité des données
Azure Analysis Services utilise le stockage Azure Blob pour rendre persistant le stockage et les métadonnées des bases de données Analysis Services. Les fichiers de données dans l’objet Blob sont cryptés à l’aide du cryptage côté serveur (SSE) de Azure Blob. Lorsque vous utilisez le mode de requête directe, seules les métadonnées sont stockées ; les données réelles sont accessible à partir de la source de données au moment de la requête.

#### <a name="on-premises-data-sources"></a>Sources de données sur site
Accès sécurisé aux données résidant sur les sites de votre organisation, il est possible par l’installation et la configuration d’une [passerelle de données sur site](analysis-services-gateway.md). Les passerelles constituent l’accès aux données pour une requête directe et les modes en mémoire. Lorsqu’un modèle d’Azure Analysis Services se connecte à une source de données sur site, une requête est créée avec les informations d’identification cryptées pour la source de données sur site. Le service en nuage gateway analyse la requête et exécute un push de la demande à un Bus de Service d’Azure. La passerelle locale interroge le Bus des services Azure pour les demandes en attente. La passerelle Obtient la requête, décrypte les informations d’identification, puis se connecte à la source de données pour l’exécution. Les résultats sont ensuite envoyés à partir de la source de données, à la passerelle, puis à la base de données Azure Analysis Services.

Azure Analysis Services est régie par les [Termes du contrat de Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et de [Déclaration de confidentialité de Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Pour en savoir plus sur la sécurité d’Azure, consultez le [Centre de confidentialité de Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obtenir de l’aide
Azure Analysis Services est simple à configurer et à gérer. Vous trouverez toutes les informations que vous avez besoin pour créer et gérer un serveur ici. Lorsque vous créez un modèle de données à déployer sur votre serveur, il est très similaire pour la création d’un modèle de données que vous déployez sur un serveur local. Il existe une bibliothèque étendue de concepts, des procédures, des didacticiels et des articles de référence à [Analysis Services sur MSDN](https://msdn.microsoft.com/library/bb522607.aspx).

Nous avons également un certain nombre de vidéos à [Azure Analysis Services sur Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Choses évoluent rapidement. Vous pouvez toujours obtenir les informations les plus récentes sur le [blog de Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Communauté
Analysis Services dispose d’une Communauté VIBRANTE d’utilisateurs. Rejoignez la conversation sur le [forum d’Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Commentaires
Avez des suggestions ou des demandes de fonctionnalités ? Veillez à laisser vos commentaires sur [Les commentaires des Services Azure analyse](https://aka.ms/azureanalysisservicesfeedback).

Avez des suggestions concernant la documentation ? Vous pouvez ajouter des commentaires à l’aide de Disqus à la fin de chaque article.

## <a name="preview-expectations"></a>Attentes de l’aperçu
Azure Analysis Services est en cours d’aperçu. Il existe plusieurs choses que vous devez être conscient de.

##### <a name="server-modes"></a>Modes serveur
Azure Analysis Services prend en charge en mode tabulaire pour les modèles sous forme de tableau au niveau de compatibilité 1200. Mode multidimensionnel et extraction de données et le Pivot de puissance pour mode SharePoint ne sont pas pris en charge.

##### <a name="data-sources"></a>Sources de données
Pour l’aperçu, les sources de données suivantes sont prises en charge dans les modèles tabulaires de 1200 déployés sur un serveur Azure Analysis Services.

| **Nuage** | **Sur site** |
|--------------|------------|
| Base de données SQL | SQL Server |
| Entrepôt de données SQL | POINTS D’ACCÈS |
|      | Oracle |
|       | Teradata |


### <a name="data-source-providers"></a>Fournisseurs de source de données
Modèles de données dans Azure Analysis Services peuvent nécessiter des fournisseurs de données différentes pour se connecter à des sources de données que les modèles de données dans SQL Server Analysis Services. Exigences en matière de fournisseur de données dépendent de la source de données en cours dans le nuage ou sur site et le type de modèle de données ; Requête directe ou en mémoire. Pour plus d’informations, voir [connexions de source de données](analysis-services-datasource.md).


### <a name="client-connections"></a>Connexions client
Alimentation BI incorporé n'est pas encore pris en charge dans l’aperçu.

Classeurs Excel avec des connexions à un serveur Azure Analysis Services et enregistrée sur OneDrive ou SharePoint Online ne sont pas pris en charge.



## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous en savez plus sur Azure Analysis Services, il est temps de commencer. Découvrez comment [créer un serveur](analysis-services-create-server.md) dans Azure et [déployer un modèle sous forme de tableau](analysis-services-deploy.md) à celui-ci.
