<properties
    pageTitle="Forum aux questions sur bases de données MySql de ClearDB avec le Service d’application Azure | Microsoft Azure"
    description="Réponses aux questions courantes sur l’utilisation de bases de données MySQL de ClearDB avec le Service d’application Azure."
    documentationCenter="php"
    services=""
    authors="sunbuild"
    manager="yochayk"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="sumuth"/>

# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Forum aux questions sur bases de données MySql de ClearDB avec le Service d’application Azure

Ce forum aux questions répond aux questions courantes sur l’utilisation et l’achat de bases de données ClearDB MySQL pour Azure Web Apps.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quelles options ai-je pour MySQL sur Azure ?

Vous disposez de plusieurs options :

* [ClearDB partagés base de données](/marketplace/partners/cleardb/databases/)

* [Clusters ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)

* [Cluster de MySQL en cours d’exécution sur un ordinateur virtuel d’Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Instance unique de MySQL en cours d’exécution sur un ordinateur virtuel d’Azure](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB est un service d’hébergement de MySQL et gère l’infrastructure de MySQL pour vous. Lorsque vous exécutez votre propre base de données ou de MySQL cluster sur une Machine virtuelle de Azure, vous devez configurer le serveur MySQL et le maintenir à jour avec les correctifs.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Ai-je besoin d’une carte de crédit pour l’application Web + modèle MySQL sur le marché d’Azure ?

Cela dépend du type d’abonnement que vous utilisez. Voici certains types d’abonnement couramment utilisées :

* [Retenues](/offers/ms-azr-0003p/): nécessite une carte de crédit, et lorsque vous achetez une base de données MySQL payé, votre carte de crédit est débitée.

* [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/): comprend les crédits pour l’utiliser avec Microsoft Azure services mais ne permet pas de fournisseur de ressources de tiers. Pour acheter des services tiers ou une base de données MySQL payé, que vous devez utiliser une carte de crédit activé l’abonnement. Pour les applications Web, vous pouvez créer une base de données libre MySQL de ClearDB.

* [Abonnement MSDN](/pricing/member-offers/msdn-benefits/) et **MSDN Dev Test de paie que vous allez**: semblable à la version d’évaluation gratuite, un abonnement MSDN vous oblige à disposer d’une carte de crédit pour acheter une solution de MySQL payante à partir de ClearDB.

* [Accord entreprise (EA)](/pricing/enterprise-agreement/): les clients EA sont facturés par rapport à leur EA chaque trimestre pour tous leurs achats (tiers) de Azure Marketplace sur une facture distincte et consolidée. Vous êtes facturé en dehors de l’engagement monétaire des achats marketplace. Veuillez noter que, à ce stade, Azure banque n’est pas disponible pour les clients inscrits en Azerbaïdjan, Croatie, Norvège et Porto Rico. 

*  [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): vous pouvez créer uniquement des bases de données ClearDB gratuit pour les applications Web. Il n’y a aucune limite sur le nombre de bases de données libre MySQL de ClearDB que vous pouvez créer. Notez que les bases de données libres ne doivent ne pas être utilisés pour les applications web de production, que ce service a été conçu uniquement pour la version d’évaluation.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Pourquoi m’a facturé des 3,50 USD pour une application Web + MySQL à partir du marché Azure ?

L’option de base de données par défaut est Titan, ce qui est de 3,50 USD. Nous n’affichons pas le coût lors de la création de la base de données, et vous pouvez acheter par erreur d’une base de données que vous ne souhaitiez. Nous essayons de trouver un moyen d’améliorer l’expérience, mais en attendant vous devez archiver tous vos niveaux de prix sélectionnés pour l’application web et la base de données avant de cliquer sur **créer** et démarrer le déploiement des ressources.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>J’exécute MySQL sur votre propre machine virtuelle Azure. Puis-je connecter mon application web Azure à ma base de données ?

Oui. Vous pouvez connecter votre application web à votre base de données tant que votre Azure VM est autorisé à accéder à distance à votre application web. Pour plus d’informations, consultez [Installation de MySQL sur une machine virtuelle](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Dans lequel des pays sont les clusters de ClearDB prime MySQL pris en charge ?

[ClearDB prime MySQL clusters](/marketplace/partners/cleardb-clusters/cluster/) sont disponibles dans toutes les régions Azure dans le monde entier à l’exception de l’Inde, l’Australie, Brésil Sud et en Chine.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Est-il possible de créer un nouveau cluster avant de créer une base de données avec la solution de cluster ClearDB prime ?

Non, la création de clusters de ClearDB vides n’est pas pris en charge. Le portail Azure vous permet de créer des bases de données dans un cluster, capable de crée un nouveau cluster en même temps.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Sera j’averti si j’essaie de supprimer une base de données ClearDB MySQL qui est en cours d’utilisation par un de mes applications ?

Non, Azure vous avertira pas si vous supprimez un fournisseur marketplace dont dépend votre application.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Les régions de la créer dans les bases de données de la ClearDB ?

Azure Marketplace n’est pas disponible pour les clients inscrits en Azerbaïdjan, de Croatie, de Norvège ou de Porto Rico. ClearDB n’est pas disponible dans ces régions.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Quel niveau de tarification dois-je choisir pour une application web de production et de la base de données ?

Utilisez Basic ou un niveau de prix plus élevé pour les applications Web. Pour ClearDB, nous vous recommandons de plan un Saturne ou Jupiter. Passez en revue les fonctionnalités et les limitations de chaque niveau de tarification pour les [Applications Web](/pricing/details/app-service/) et de [bases de données MySQL de ClearDB](/marketplace/partners/cleardb/databases/) à choisir celle qui répond à vos besoins.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Comment passer ma base de données ClearDB d’un plan à l’autre ?

Dans [Azure portal](https://portal.azure.com), vous pouvez adapter une base d’hébergement partagé ClearDB. Lisez cet [article](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) pour en savoir plus. Nous avons actuellement ne prend en charge mise à niveau des clusters ClearDB prime dans le portail Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Je ne vois pas ma base de données ClearDB dans Azure portal ?

Si nous créons la base de données ClearDB à l’aide de gestionnaire de ressources Azure ou [Nouveau portail d’Azure](https://portal.azure.com), il ne sera pas visible dans l' [Ancien portail Azure](https://manage.windowsazure.com). Pour contourner cela consiste à lier manuellement de votre base de données à l’application web. De la même façon si créer des ClearDB de base de données dans l' [ancien portail](https://manage.windowsazure.com) , vous ne serez pas en mesure de voir votre base de données dans le [Nouveau portail d’Azure](https://portal.azure.com). Il n’existe aucune solution de contournement pour le scénario de cette dernière.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Qui peut me pour la prise en charge lorsque ma base de données est hors service ?

Problèmes liés à contact [ClearDB prise en charge](https://www.cleardb.com/developers/help/support) pour les bases de données. Préparez-vous à fournir les informations sur votre abonnement Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Puis-je créer des utilisateurs supplémentaires pour ma solution de cluster de base de données ClearDB MySQL ? 

N° Vous ne pouvez pas créer des utilisateurs supplémentaires, mais vous pouvez créer des bases de données supplémentaires sur votre cluster de base de données ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Série Basic/Pro être des bases de données peut mis à niveau sur place similaire aux plans planétaires aujourd'hui sur le portail de ClearDB ?

Oui, série de base bases de données peuvent être mis à niveau sur place (60 base par le biais de base 500). Série Pro peut être mis à niveau la place (125 Pro et Pro 1000), à l’exception de 60 Pro. Nous ne supportent pas la mise à niveau de base de données de 60 Pro actuellement. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Lorsque je migre mes ressources à partir d’un abonnement à un autre, est ma base de données ClearDB MySQL obtenir également migré ? 

Lorsque vous effectuez la migration de la ressource sur les abonnements, certaines [restrictions](./app-service-web/app-service-move-resources.md) s’appliquent. Une base de données ClearDB MySQL est un service de tiers et par conséquent ne sont-elles pas migrée pendant la migration d’abonnement Azure. Si vous ne gérez pas la migration de votre base de données MySQL avant la migration des ressources Azure, vos bases de données ClearDB MySQL peuvent être désactivés. Migrer manuellement vos bases de données tout d’abord, puis exécutez migration d’abonnement Azure pour votre application web. 

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Puis-je transférer une base de données ClearDB d’un abonnement de carte de crédit à un abonnement EA ?

ClearDB bases de données utilisent la carte de crédit associée avec les abonnements existants. Pour utiliser un abonnement EA, vous avez besoin migrer vos données vers une nouvelle base de données :

* Achat d’une nouvelle base de données ClearDB avec votre abonnement EA.
* Migrer vos données vers votre nouvelle base de données.
* Mettre à jour votre application pour utiliser la nouvelle base de données.
* Supprimer votre ancienne base de données ClearDB.

Lorsque vous créez une nouvelle application web avec MySQL (ClearDB) ou créez une base de données MySQL (ClearDB), l’abonnement que vous choisissez détermine la façon dont vous allez payer pour le service. Avec un abonnement EA, nous ne bloque pas les marchés des services tiers comme ClearDB dans le portail Azure. Les abonnements EA sont facturés en dehors de l’engagement monétaire et sont facturés et trimestriels en retard. Les clients EA devra définir un mode de paiement, tel qu’une carte de crédit pour payer pour tous les services tiers marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Où puis-je voir les frais de ClearDB des ressources dans un abonnement EA ?

Pour les clients EA Direct, frais d’Azure Marketplace sont visibles dans Enterprise Portal. Notez que tous les achats de marché et la consommation sont facturés en dehors de l’engagement monétaire et facturation trimestrielle et en retard. Les clients EA de payer directement aux fournisseurs de service tiers et peuvent le faire en activant le mode de paiement est une carte de crédit avec leur compte EA.

Les clients EA indirects peuvent rechercher leurs abonnements Azure Marketplace sur la page **Gérer les abonnements** d’Enterprise Portal, mais la tarification est masquée. Les clients doivent contacter leurs prestataires de services linguistiques pour plus d’informations sur les frais de marché.

Accès à Azure Marketplace pour des services tiers peuvent être géré par les administrateurs d’inscription EA Azure. Ils peuvent désactiver ou réactiver accès à 3e partie achats à partir de la banque dans Gestion des comptes et des abonnements sous la section comptes dans Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Qui dois-je contacter pour toute question relative à ma facture pour les services ClearDB dans mon abonnement EA ?

Contactez le [Support Enterprise](http://aka.ms/AzureEntSupport) en ce qui concerne la facturation sous leur inscription EA. L’équipe de Support de portail EA répondront à votre question ou aider à résoudre votre problème.

 



## <a name="more-information"></a>Plus d’informations

[Forum aux questions sur Azure Marketplace](/marketplace/faq/)
