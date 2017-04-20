<properties
   pageTitle="Sécuriser un cluster s’exécutant sur Windows à l’aide de la sécurité Windows | Microsoft Azure"
   description="Apprenez à configurer la sécurité de nœud à nœud et client-à-nœud sur un cluster autonome fonctionnant sous Windows à l’aide de la sécurité de Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Sécuriser un cluster autonome sous Windows à l’aide de la sécurité de Windows

Pour empêcher tout accès non autorisé à un cluster de Service Fabric que doit sécuriser, surtout lorsqu’il a des charges de travail qu’il exécute. Cet article explique comment configurer la sécurité de nœud à nœud et client-à-nœud à l’aide de la sécurité Windows dans le fichier *ClusterConfig.JSON* et correspond à l’étape de sécurité de configurer de [créer un cluster autonome fonctionnant sous Windows](service-fabric-cluster-creation-for-windows-server.md). Pour plus d’informations sur le Service Fabric utilise la sécurité Windows, consultez [les scénarios de sécurité de Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Vous devez envisager votre sélection de sécurité pour la sécurité de nœud à nœud avec précaution, car il n’existe aucune mise à niveau de cluster à partir de choix d’une sécurité à l’autre. Modification de la sélection de sécurité nécessite une reconstruction complète de cluster.

## <a name="configure-windows-security"></a>Configurer la sécurité de Windows
L’exemple de fichier de configuration *ClusterConfig.Windows.JSON* téléchargé avec le [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. ZIP](http://go.microsoft.com/fwlink/?LinkId=730690) package de cluster autonome contient un modèle de configuration de la sécurité de Windows.  Sécurité de Windows est configurée dans la section **Propriétés** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Paramètre de configuration**|**Description**|
|-----------------------|--------------------------|
|ClusterCredentialType|Sécurité de Windows est activée en définissant le paramètre **ClusterCredentialType** pour *Windows*.|
|ServerCredentialType|Sécurité de Windows pour les clients est activée en définissant le paramètre **ServerCredentialType** pour *Windows*. Cela indique que les clients du cluster et le cluster lui-même, sont en cours d’exécution au sein d’un domaine Active Directory.|
|WindowsIdentities|Contient les identités du client et du cluster.|
|ClusterIdentity|Configure la sécurité du nœud-nœud. Une liste séparée par des virgules de groupe géré des comptes de service ou des noms de machine.|
|ClientIdentities|Configure la sécurité du client-à-nœud. Tableau de comptes d’utilisateur client.|
|Identité|L’identité du client, un utilisateur de domaine.|
|IsAdmin|La valeur True indique que l’utilisateur du domaine a l’accès administrateur client, false pour l’accès client utilisateur.|

[Nœud de sécurité du noeud](service-fabric-cluster-security.md#node-to-node-security) est configuré par le paramètre à l’aide de **ClusterIdentity**. Afin de créer des relations d’approbation entre les nœuds, qu’ils doivent être informés de l’autre. Pour ce faire de deux façons différentes : spécifier Managed Service compte de groupe qui inclut tous les nœuds du cluster ou l’identité de nœud de domaine de tous les nœuds dans le cluster. Nous recommandons vivement l’utilisation de l’approche du [Groupe Managed Service compte (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , en particulier pour les clusters de grande taille (plus de 10 nœuds) ou pour les clusters qui sont susceptibles d’augmenter ou de réduire.
Cette approche permet aux nœuds être ajoutés ou supprimés de la gMSA, sans avoir à modifier le manifeste de cluster. Cette approche ne nécessite pas la création d’un groupe de domaine pour lequel les administrateurs de cluster ont reçu des droits d’accès pour ajouter et supprimer des membres. Pour plus d’informations, consultez [Mise en route avec le groupe les comptes de Service](http://technet.microsoft.com/library/jj128431.aspx).

[Sécurité du nœud client](service-fabric-cluster-security.md#client-to-node-security) est configuré à l’aide de **ClientIdentities**. Pour établir une approbation entre un client et le cluster, vous devez configurer le cluster pour savoir à quel client identités auxquelles il est digne de confiance. Pour ce faire de deux façons différentes : spécifiez le groupe utilisateurs de domaine peuvent se connecter ou spécifier des utilisateurs du domaine du nœud qui peuvent se connecter. TISSU de service prend en charge deux types de contrôle d’accès différents pour les clients qui sont connectés à un cluster de Service Fabric : administrateur et utilisateur. Contrôle d’accès permet à l’administrateur de cluster limiter l’accès à certains types d’opérations de cluster pour différents groupes d’utilisateurs, le renforcement de la sécurité du cluster.  Les administrateurs ont un accès complet aux fonctions de gestion (y compris les fonctions de lecture/écriture). Les utilisateurs, par défaut, ont uniquement un accès en lecture aux fonctions de gestion (par exemple, les fonctions de requête) et la capacité à résoudre des applications et des services.

La section suivante **sécurité** configure la sécurité de Windows et spécifie que les ordinateurs dans *ServiceFabric/clusterA.contoso.com* font partie du cluster et que *CONTOSO\usera* dispose d’un accès administrateur client :

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Étapes suivantes

Après la configuration de la sécurité de Windows dans le fichier *ClusterConfig.JSON* , reprendre le processus de création de cluster dans [créer un cluster autonome fonctionnant sous Windows](service-fabric-cluster-creation-for-windows-server.md).

Pour plus d’informations sur la sécurité de nœud à nœud, la sécurité du client-à-nœud et contrôle d’accès basé sur les rôles, voir [les scénarios de sécurité de Cluster](service-fabric-cluster-security.md).

Voir [se connecter à un cluster sécurisé](service-fabric-connect-to-secure-cluster.md) pour obtenir des exemples de connexion à l’aide de PowerShell ou FabricClient.
