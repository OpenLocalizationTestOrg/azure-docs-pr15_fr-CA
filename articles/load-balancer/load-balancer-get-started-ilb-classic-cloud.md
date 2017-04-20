<properties
   pageTitle="Créer un équilibreur de charge interne pour les services en nuage dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Commencer à créer un équilibreur de charge interne (classique) pour les services en nuage

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurer l’équilibreur de charge interne pour les services en nuage

Équilibreur de charge interne est pris en charge pour les machines virtuelles et services dans le nuage. Un point de terminaison d’équilibreur de charge interne créé dans un service en nuage qui est à l’extérieur d’un réseau virtuel régionaux est accessible que dans le service en nuage.

La configuration d’équilibrage de la charge interne doit être défini lors de la création du premier déploiement dans le service cloud, comme illustré dans l’exemple ci-dessous.

>[AZURE.IMPORTANT] Un réseau virtuel déjà créé pour le déploiement de cloud est une condition préalable à l’exécution de la procédure ci-dessous. Vous devez le nom de réseau virtuel et le nom de sous-réseau pour créer l’équilibrage de charge interne.

### <a name="step-1"></a>Étape 1

Ouvrez le fichier de configuration de service (.cscfg) pour le déploiement de cloud dans Visual Studio et ajoutez la section suivante pour créer l’équilibrage de charge interne sous le dernier "`</Role>`" article pour la configuration du réseau.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Nous allons ajouter les valeurs pour le fichier de configuration réseau afficher l’aperçu. Dans l’exemple, supposons que vous avez créé un sous-réseau appelé « test_vnet » avec un sous-réseau 10.0.0.0/24 appelé test_subnet et un 10.0.0.4 des adresses IP statiques. L’équilibrage de charge sera nommé testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Pour plus d’informations sur le schéma d’équilibrage de la charge, voir [Ajout d’équilibrage de la charge](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Étape 2


Modifiez le fichier de définition (.csdef) de service pour ajouter des points de terminaison à l’équilibrage de charge interne. Au moment où qu'une instance de rôle est créée, le fichier de définition de service ajoute les instances de rôle à l’équilibrage de charge interne.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Suivant les mêmes valeurs à partir de l’exemple ci-dessus, ajoutons les valeurs dans le fichier de définition de service.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

Le trafic réseau sera à charge équilibrée à l’aide de l’équilibrage de la charge testLB utilise le port 80 pour les requêtes entrantes, envoi à des instances de rôle de travail également sur le port 80.


## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de la charge l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)