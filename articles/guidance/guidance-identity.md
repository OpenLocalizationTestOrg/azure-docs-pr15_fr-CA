<properties
   pageTitle="Gestion des identités dans Azure | Microsoft Azure"
   description="Décrit et compare les différentes méthodes disponibles pour la gestion des identités dans les systèmes hybrides qui couvrent la limite sur-locaux/nuage avec Azure."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>
<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/26/2016"
   ms.author="telmosampaio"/>
   
# <a name="managing-identity-in-azure"></a>Gestion des identités dans Azure

Dans la plupart des systèmes d’entreprise basés sur Windows, vous allez utiliser Active Directory (AD) afin de fournir des services de gestion identité à vos applications. Active Directory fonctionne correctement dans un environnement sur site, mais lorsque vous étendez votre infrastructure de réseau vers le nuage vous avez quelques décisions importantes à prendre en ce qui concerne la gestion des identités. Vous devez développer vos domaines locaux pour intégrer des ordinateurs virtuels dans le nuage ? Vous devez créer des domaines dans le nuage et si tel est le cas, comment ? Vous devez implémenter votre propre forêt dans le nuage, ou vous devez vous utiliser d’Azure Active Directory (DAS) ?

Cet article décrit des options communes pour relever les défis posés par ce scénario et vous aide à que déterminer quelle solution de mieux répondre à vos besoins en fonction de vos besoins.

## <a name="using-azure-active-directory"></a>Utilisation d’Azure Active Directory

Vous pouvez utiliser des DAS pour créer un domaine Active Directory dans le nuage et le lier à un site de domaine Active Directory. DAS vous permet de configurer de session unique (SSO) pour les utilisateurs exécutant des applications accédées via le nuage.

[! [0]][0]

DAS est un moyen simple d’implémenter un domaine de sécurité dans le nuage. Il est utilisé par de nombreuses applications Microsoft, telles que Microsoft Office 365. 

Avantages de l’utilisation de DAS :

- Il n’est pas nécessaire de maintenir une infrastructure Active Directory dans le nuage. DAS est entièrement géré et maintenu par Microsoft.

- DAS fournit les mêmes informations d’identité qui sont disponible sur site.

- L’authentification peut se produire dans Azure, réduisant la nécessité pour les utilisateurs et les applications externes à contacter le domaine local.

Points à prendre en compte lors de l’utilisation de DAS :

- Services d’identité sont limités aux utilisateurs et aux groupes. Il est impossible d’authentifier les comptes de service et l’ordinateur.

- Vous devez configurer la connectivité avec votre domaine local de conserver le répertoire DAS synchronisé. 

- Vous êtes responsable de la publication d’applications que les utilisateurs peuvent accéder dans le nuage par le biais de DAS.

Pour plus d’informations, lire la [Mise en œuvre d’Azure Active Directory][implementing-aad].

## <a name="using-active-directory-in-the-cloud-joined-to-an-on-premises-forest"></a>À l’aide d’Active Directory dans le nuage rejoint une forêt locale

Vous pouvez héberger les Services d’annuaire d’Active Directory (AD DS) sur site, mais dans un scénario hybride où se trouvent les éléments d’une application dans Azure, il peut être plus efficace pour répliquer cette fonctionnalité et le référentiel d’Active Directory vers le nuage. Cette approche permet de réduire le temps de latence dû à l’envoi de l’authentification et les demandes d’autorisation locale à partir du cloud dans les services AD DS en cours d’exécution sur site. 

[! [1]][1]

Cette approche nécessite que vous créez votre propre domaine dans le nuage et le joignez à la forêt locale. Vous créez des ordinateurs virtuels pour héberger les services de domaine Active Directory.

Avantages de l’utilisation d’un domaine distinct dans le nuage :

- Fournit la possibilité d’authentifier l’utilisateur, service et ordinateur comptes locaux et dans le cloud.

- Permet d’accéder aux mêmes informations d’identité qui sont disponible sur site.

- Il est inutile de gérer une forêt Active Directory distincte ; le domaine dans le nuage peut appartenir à la forêt locale.

- Vous pouvez appliquer la stratégie de groupe défini par les objets d’objet stratégie de groupe local pour le domaine dans le nuage.

Considérations sur l’utilisation d’un domaine distinct dans le nuage :

- Vous devez créer et gérer vos propres serveurs de service d’annuaire Active Directory et le domaine dans le nuage.

- Il peut y avoir une latence de synchronisation entre les serveurs du domaine dans le nuage et des serveurs sur site.

Pour plus d’informations sur la configuration de cette architecture, consultez [Extension de Active Directory Directory Services (ADDS) pour Azure][extending-adds].

## <a name="using-active-directory-with-a-separate-forest"></a>À l’aide d’Active Directory avec une forêt distincte

Une organisation qui exécute Active Directory (AD) sur site peut être une forêt comprenant de nombreux domaines différents. Vous pouvez utiliser des domaines pour assurer l’isolation entre les domaines fonctionnels qui doivent être maintenus séparés, peut-être pour des raisons de sécurité, mais vous pouvez partager des informations entre des domaines en établissant des relations d’approbation.

[! [2]][2]

Une organisation qui utilise des domaines séparés peut tirer parti d’Azure en déplaçant un ou plusieurs de ces domaines dans une forêt distincte dans le nuage. Vous pouvez également, une organisation peut souhaiter garder toutes les ressources de cloud logiquement distinctes des ces détenues sur place et stocker des informations sur les ressources du cloud dans leur propre répertoire, en tant que partie d’une forêt également contenue dans le nuage.

Avantages de l’utilisation d’une forêt distincte dans le nuage :

- Vous pouvez implémenter les identités sur site et séparer les identités Azure uniquement.

- Il est inutile de répliquer à partir du site sur forêt Active Directory d’Azure, réduisant les effets de latence du réseau.

Considérations relatives à :

- L’authentification des identités en local dans le nuage effectue réseau supplémentaire *sauts* dans les locaux sur les serveurs Active Directory.

- Vous devez implémenter vos propres serveurs AD DS et la forêt dans le nuage et établir les relations d’approbation appropriée entre les forêts.

Le document de [Création d’une forêt de ressources des Services de répertoire Active Directory (ADDS) dans Azure] [ adds-forest-in-azure] explique comment implémenter cette approche plus en détail.

## <a name="using-active-directory-federation-services-adfs-with-azure"></a>À l’aide d’Active Directory Federation Services (ADFS) d’Azure

ADFS peut s’exécuter sur place, mais dans un scénario hybride où les applications se trouvent dans Azure qu’il peut être plus efficace d’implémenter cette fonctionnalité dans le nuage, comme illustré ci-dessous.

[! [3]][3]

Cette architecture est particulièrement utile pour :

- Solutions utilisant l’autorisation fédérée à exposer les applications web aux organisations partenaires.

- Systèmes qui prennent en charge les accès à partir de navigateurs web s’exécutant en dehors du pare-feu de votre entreprise.

- Systèmes qui permettent aux utilisateurs d’accéder aux applications web en vous connectant à partir de périphériques externes autorisés, tels que des ordinateurs distants, ordinateurs portables et autres périphériques mobiles. 

Avantages de l’utilisation d’ADFS avec Azure :

- Vous pouvez tirer parti des applications prenant en charge les revendications.

- Il offre la possibilité d’approuver des partenaires externes pour l’authentification.

- Il assure la compatibilité avec un grand ensemble de protocoles d’authentification.

Considérations sur l’utilisation d’ADFS avec Azure :

- Il vous oblige à implémenter vos propres serveurs Proxy de l’Application Web ADFS, ADFS et ajoute dans le nuage.

- Cette architecture peut être complexe à configurer.

Pour plus d’informations, lisez [l’Implémentation d’Active Directory Federation Services (ADFS) dans Azure][adfs-in-azure].

## <a name="next-steps"></a>Étapes suivantes

Les ressources ci-dessous vous indiquent comment implémenter les architectures décrites dans cet article.

- [Implémentation d’Azure Active Directory][implementing-aad]
- [Extension des Services d’annuaire Active Directory (ADDS) pour Azure][extending-adds]
- [Création d’une forêt de ressources des Services de répertoire Active Directory (ADDS) dans Azure][adds-forest-in-azure]
- [Mise en œuvre d’Active Directory Federation Services (ADFS) dans Azure][adfs-in-azure]

<!-- Links -->
[0]: ./media/guidance-identity/figure1.png "Architecture d’identité cloud à l’aide d’Azure Active Directory"
[1]: ./media/guidance-identity/figure2.png "Architecture de réseau hybride avec Active Directory sécurisée"
[2]: ./media/guidance-identity/figure3.png "Architecture de réseau hybride avec les domaines Active Directory et des forêts distinctes sécurisée"
[3]: ./media/guidance-identity/figure4.png "Architecture de réseau hybride avec ADFS sécurisée"
[implementing-aad]: ./guidance-identity-aad.md
[extending-adds]: ./guidance-identity-adds-extend-domain.md
[adds-forest-in-azure]: ./guidance-identity-adds-resource-forest.md
[adfs-in-azure]: ./guidance-identity-adfs.md