<properties
   pageTitle="Sécuriser un cluster Service Fabric | Microsoft Azure"
   description="Décrit les scénarios de sécurité pour un cluster à structure de Service et les différentes technologies utilisées pour implémenter ces scénarios."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Scénarios de sécurité de cluster service Fabric

Un cluster de tissu de Service est une ressource qui vous appartient. Les clusters doivent toujours être sécurisées pour empêcher les utilisateurs non autorisés de se connecter à votre cluster, surtout lorsqu’il a des charges de travail qu’il exécute. Bien qu’il soit possible de créer un cluster non sécurisé, cela permettra donc tout utilisateur anonyme pour se connecter à elle si elle expose les points de terminaison de gestion de l’Internet public. 

Cet article fournit une vue d’ensemble des scénarios de sécurité pour les clusters s’exécutant sur Azure ou autonomes et les différentes technologies utilisées pour implémenter ces scénarios. Les scénarios de sécurité de cluster sont :

- Sécurité de nœud à nœud
- Sécurité de client-à-nœud
- Contrôle d’accès basé sur les rôles (RBAC)

## <a name="node-to-node-security"></a>Sécurité de nœud à nœud
Sécurise les communications entre les ordinateurs virtuels ou les ordinateurs dans le cluster. Cela garantit que seuls les ordinateurs autorisés à rejoindre le cluster peuvent participer à héberger des applications et des services dans le cluster.

![Schéma de communication de nœud à nœud][Node-to-Node]

Clusters s’exécutant sur des clusters Azure ou autonome fonctionnant sous Windows peuvent utiliser [Des certificats de sécurité](https://msdn.microsoft.com/library/ff649801.aspx) ou de [Sécurité Windows](https://msdn.microsoft.com/library/ff649396.aspx) pour les ordinateurs Windows Server.
### <a name="node-to-node-certificate-security"></a>Sécurité de certificat de nœud à nœud
TISSU de service utilise les certificats de serveur X.509 que vous spécifiez dans le cadre des configurations de nœud de type lorsque vous créez un cluster. Une vue d’ensemble rapide de ces certificats et comment vous pouvez acquérir ou créer leur est fourni à la fin de cet article.

Certificats de sécurité est configuré lors de la création du cluster, soit par le biais du portail Azure, le Gestionnaire de ressources Azure modèles ou un modèle JSON autonome. Vous pouvez spécifier un certificat principal et un certificat secondaire facultatif qui est utilisé pour les renouvellements de certificats. Les certificats principaux et secondaires que vous spécifiez doivent être différents de celui du client d’administration et les certificats de client en lecture seule que vous spécifiez pour [la sécurité Client-à-nœud](#client-to-node-security).

Pour Azure lire à [configurer un cluster à l’aide d’un modèle de gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md) pour savoir comment configurer des certificats de sécurité dans un cluster.

Pour autonome Windows Server lire [sécuriser un cluster autonome sous Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Sécurité windows de nœud à nœud
Pour autonome Windows Server lire [sécuriser un cluster autonome sous Windows à l’aide de la sécurité de Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Sécurité de client-à-nœud
Authentifie les clients et sécurise les communications entre un client et des nœuds dans le cluster. Ce type de sécurité authentifie et sécurise les communications client, ce qui garantit que seuls les utilisateurs autorisés peuvent accéder au cluster et les applications déployées sur le cluster. Les clients sont identifiés de manière unique par le biais de leurs informations d’identification de sécurité de Windows ou de leurs informations d’identification de sécurité de certificat.

![Schéma de communication client-à-noeud][Client-to-Node]

Clusters s’exécutant sur des clusters Azure ou autonome fonctionnant sous Windows peuvent utiliser [Des certificats de sécurité](https://msdn.microsoft.com/library/ff649801.aspx) ou de [Sécurité de Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Client-à-nœud certificats de sécurité
 Sécurité de certificat de client-à-nœud est configurée lors de la création du cluster par le biais du portail Azure, modèles du Gestionnaire de ressources ou un modèle JSON autonome en spécifiant un certificat de client administrateur et/ou d’un certificat client de l’utilisateur.  L’administrateur client et utilisateur certificats clients que vous spécifiez doivent être différents que vous spécifiez pour la [sécurité de nœud à nœud](#node-to-node-security)certificats primaires et secondaires.

Clients qui se connectent au cluster en utilisant le certificat d’administrateur ont un accès complet aux fonctions de gestion.  Clients qui se connectent au cluster en utilisant le certificat de client utilisateur en lecture seule ont uniquement un accès en lecture aux fonctions de gestion. En d’autres termes, ces certificats sont utilisés pour le contrôle de l’accès de bases de rôle (RBAC) décrit plus loin dans cet article.

Pour Azure lire à [configurer un cluster à l’aide d’un modèle de gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md) pour savoir comment configurer des certificats de sécurité dans un cluster.

Pour autonome Windows Server lire [sécuriser un cluster autonome sous Windows à l’aide de certificats X.509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Sécurité d’Azure Active Directory (DAS) client-à-nœud sur Azure
Clusters de serveurs sur Azure peuvent également sécuriser l’accès aux points de terminaison de gestion à l’aide d’Azure Active Directory (DAS). Pour plus d’informations sur la création d’artefacts DAS nécessaires, afin de les remplir pendant la création du cluster et comment vous connecter à des clusters par la suite, consultez [définir un cluster en utilisant un modèle de gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md) .

## <a name="security-recommendations"></a>Recommandations de sécurité
Pour les clusters Azure, il est recommandé que vous utilisez la sécurité de DAS pour authentifier les clients et les certificats de sécurité du nœud-nœud.

Pour autonome Windows Server clusters, il est recommandé d’utiliser la sécurité de Windows avec le groupe les comptes gérés (GMA) si vous avez Windows Server 2012 R2 et Active Directory. Sinon, toujours utiliser la sécurité Windows avec des comptes Windows.

## <a name="role-based-access-control-rbac"></a>Contrôle d’accès basé sur les rôles (RBAC)
Contrôle d’accès permet à l’administrateur de cluster limiter l’accès à certaines opérations de cluster pour différents groupes d’utilisateurs, le renforcement de la sécurité du cluster. Deux types de contrôle d’accès différents sont prises en charge pour les clients qui se connectent à un cluster : rôle administrateur et rôle de l’utilisateur.

Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctions de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctions de gestion (par exemple, les fonctions de requête) et la capacité à résoudre des applications et des services.

Spécifier les rôles de client administrateur et d’utilisateur au moment de la création d’un cluster en fournissant des identités séparées (certificats, DAS etc.) pour chacun. Pour plus d’informations sur les paramètres de contrôle d’accès par défaut et comment modifier les paramètres par défaut, consultez [contrôle d’accès pour les clients de Service Fabric basée sur les rôles](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Les certificats X.509 et Service de Fabric
Les certificats numériques X.509 sont utilisés pour authentifier les clients et les serveurs et de crypter et de signer numériquement les messages. Pour plus d’informations sur ces certificats, accédez à [l’utilisation des certificats](http://msdn.microsoft.com/library/ms731899.aspx).

Quelques éléments importants à prendre en compte :

- Certificats utilisés dans les charges de travail en cours d’exécution de clusters doivent être créés à l’aide d’un service de certificat Windows Server correctement configuré ou obtenus à partir d’une [Autorité de certification (CA)](https://en.wikipedia.org/wiki/Certificate_authority)approuvé.
- N’utilisez jamais les temporaire ou certificats de test de production qui sont créés avec des outils tels que MakeCert.exe.
- Vous pouvez utiliser un certificat auto-signé, mais devez uniquement faire pour les clusters de test et pas dans la production.

### <a name="server-x509-certificates"></a>Certificats de serveur X.509

Les certificats de serveur ont la tâche principale de l’authentification d’un serveur (nœud) à des clients ou l’authentification d’un serveur (nœud) à un serveur (nœud). Un des contrôles lorsqu’un nœud ou un client s’authentifie un nœud initiales est de vérifier la valeur du nom commun dans le champ objet. Soit ce nom commun ou l’un des autres noms de sujet du certificat doit être présent dans la liste des noms de communes autorisées.

L’article suivant explique comment générer des certificats avec les noms d’objet alternatifs (SAN) : [Comment faire pour ajouter un autre nom du sujet à un certificat LDAP sécurisé](http://support.microsoft.com/kb/931351).

Le champ d’objet peut contenir plusieurs valeurs, chacun précédé d’une initialisation pour indiquer le type de valeur. En règle générale, l’initialisation est « CN » nom commun ; par exemple, « CN = www.contoso.com ». Il est également possible pour le champ d’objet vide. Si le champ facultatif autre nom du sujet est rempli, il doit contenir à la fois le nom commun du certificat et une entrée par l’autre nom du sujet. Elles sont entrées sous forme de valeurs de nom DNS.

La valeur du champ rôles prévus du certificat doit inclure une valeur appropriée, par exemple « Authentification serveur » ou « Authentification du Client ».

### <a name="client-x509-certificates"></a>Certificats de client X.509

Les certificats clients ne sont pas en général émis par une autorité de certification tierce. Au lieu de cela, le magasin personnel de l’emplacement actuel de l’utilisateur contient généralement les certificats clients par une autorité racine, avec pour objectif de « Authentification du Client ». Le client peut utiliser un tel certificat lorsque l’authentification mutuelle est requise.

>[AZURE.NOTE] Toutes les opérations de gestion sur un cluster Service Fabric nécessitent des certificats de serveur. Les certificats clients ne peut pas être utilisés pour la gestion.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Étapes suivantes

Cet article fournit des informations conceptuelles sur la sécurité du cluster. Ensuite, [créez un cluster dans Azure à l’aide d’un modèle de gestionnaire de ressources](service-fabric-cluster-creation-via-arm.md) ou via le [portail Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
