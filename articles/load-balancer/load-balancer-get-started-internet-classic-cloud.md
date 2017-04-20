<properties
   pageTitle="Commencer à créer un Internet face à équilibrage de la charge dans le modèle de déploiement classique à l’aide des services en nuage | Microsoft Azure"
   description="Apprenez à créer un équilibreur de charge dans le modèle de déploiement classiques pour les services en nuage d’ouvert sur Internet"
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
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Démarrer la création d’un équilibreur de charge pour les services en nuage d’ouvert sur Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique. Vous pouvez également [apprendre à créer un équilibreur de charge à l’aide du Gestionnaire de ressources Azure d’ouvert sur Internet](load-balancer-get-started-internet-arm-cli.md).

Services en nuage sont automatiquement configurés avec un équilibreur de charge et peuvent être personnalisés par le biais du modèle de service.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Créer un équilibreur de charge à l’aide du fichier de définition du service

Vous pouvez exploiter l’Azure SDK 2.5 .NET mettre à jour votre service cloud. Paramètres de point de terminaison pour les services en nuage sont effectuées dans le fichier de [définition de service](https://msdn.microsoft.com/library/azure/gg557553.aspx)de .csdef.

L’exemple suivant montre comment un fichier de servicedefinition.csdef pour un déploiement de cloud est configuré :

Vérification de la snipet pour le fichier .csdef généré par un déploiement de cloud, vous pouvez voir le point de terminaison externe configuré pour utiliser les ports HTTP sur le port 10000, 10001, 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Vérifier l’état de santé d’équilibrage de la charge des services en nuage


Voici un exemple d’une sonde de santé :

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

L’équilibreur de charge combine les informations du point de terminaison et les informations de la sonde pour créer une URL sous la forme de http://{DIP de VM}:80/Probe.aspx qui peut être utilisé pour interroger l’état de santé du service.

Le service détecte des sondes périodiques à partir de la même adresse IP. Il s’agit de la demande de sonde de santé provenant de l’hôte du nœud dans lequel l’ordinateur virtuel est en cours d’exécution.
Le service doit répondre avec un code d’état HTTP 200 pour l’équilibrage de la charge de supposer que le service est en bon état. Tout autre code d’état HTTP (par exemple 503) prend directement de la machine virtuelle de rotation.

La définition de sonde contrôle également la fréquence de la sonde. Dans notre cas ci-dessus, l’équilibreur de charge est détection du point de terminaison toutes les 5 secondes. Si aucune réponse positive n’est reçue de 10 secondes (deux intervalles de sonde), la sonde est supposée vers le bas, et l’ordinateur virtuel est retiré de rotation. De même, si le service n’est pas de la rotation et une réponse positive est reçue, le service est replacé à la rotation tout de suite. Si le service fluctue entre corrects et incorrects, l’équilibreur de charge peut décider de retarder la réintroduction du service à rotation jusqu'à ce qu’il a été en bon état pour un certain nombre de sondes.

Vérifiez le schéma de définition de service pour la [sonde de santé](https://msdn.microsoft.com/library/azure/jj151530.aspx) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

[Démarrer la configuration d’un équilibreur de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configurer un mode de distribution d’équilibrage de la charge](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’attente TCP inactifs pour votre équilibreur de charge](load-balancer-tcp-idle-timeout.md)

