<properties 
pageTitle="Communication des rôles dans les Services en nuage | Microsoft Azure" 
description="Instances de rôle dans les Services en nuage peuvent avoir points de terminaison (http, https, tcp, udp) définis pour eux qui communiquent avec l’extérieur, ou entre les autres instances de rôle." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Autoriser la communication pour des instances de rôle dans azure

Rôles de service cloud communiquent via des connexions internes et externes. Connexions externes sont appelées des **points de terminaison d’entrée** pendant que des connexions internes sont appelées des **points de terminaison internes**. Cette rubrique décrit la procédure pour modifier la [définition de service](cloud-services-model-and-package.md#csdef) pour créer des points de terminaison.


## <a name="input-endpoint"></a>Point de terminaison d’entrée
Le point de terminaison d’entrée est utilisé lorsque vous souhaitez exposer un port à l’extérieur. Vous spécifiez le type de protocole et le port du point de terminaison qui s’applique ensuite pour les deux ports externes et internes pour le point de terminaison. Si vous le souhaitez, vous pouvez spécifier un autre port interne du point de terminaison avec l’attribut [Port_local](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

Le point de terminaison d’entrée peut utiliser les protocoles suivants : **http, https, tcp et udp**.

Pour créer un point de terminaison d’entrée, ajoutez l’élément enfant de **InputEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un travailleur.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Point de terminaison d’entrée instance
Instance de points de terminaison d’entrée est semblables à l’entrée des points de terminaison mais vous permet de mapper des ports orienté public spécifiques pour chaque instance de rôle individuel à l’aide du réacheminement de port de l’équilibreur de charge. Vous pouvez spécifier un seul port accessible au public, ou une plage de ports.

Le point de terminaison d’entrée instance ne pouvez utiliser **tcp** ou **udp** comme protocole.

Pour créer un point de terminaison instance d’entrée, ajoutez l’élément enfant de **InstanceInputEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un travailleur.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Point de terminaison interne
Les points de terminaison internes sont disponibles pour la communication de l’instance de l’instance. Le port est facultatif, et en cas d’omission, un port dynamique est affecté au point de terminaison. Une plage de ports peut être utilisée. Il existe une limite de cinq points de terminaison internes par rôle.

Le point de terminaison interne peut utiliser les protocoles suivants : **http, tcp, udp, tout**.

Pour créer un point de terminaison d’entrée interne, ajoutez l’élément enfant de **InternalEndpoint** à l’élément de **points de terminaison** du rôle d’un site ou un travailleur.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

Vous pouvez également utiliser une plage de ports.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Rôles Worker et rôles Web

Il existe une différence mineure avec les points de terminaison lorsque vous travaillez avec rôles web et worker. Le rôle web doit avoir au moins un point de terminaison d’entrée unique à l’aide du protocole **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>À l’aide du Kit de développement .NET pour accéder à un point de terminaison
La bibliothèque managée Azure fournit des méthodes pour les instances de rôles communiquer au moment de l’exécution. À partir du code s’exécutant dans une instance de rôle, vous pouvez récupérer des informations sur l’existence d’autres instances de rôles et de leurs points de terminaison, ainsi que des informations sur l’instance de rôle actuel.

> [AZURE.NOTE] Vous ne pouvez récupérer que des informations sur les instances de rôles qui sont en cours d’exécution dans votre service cloud et définissent au moins un point de terminaison interne. Impossible d’obtenir des données sur les instances de rôle en cours d’exécution dans un autre service.

Vous pouvez utiliser la propriété [Instances](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) pour récupérer des instances d’un rôle. Tout d’abord utiliser la [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) pour renvoyer une référence à l’instance de rôle actuel et ensuite utiliser la propriété [Role](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) pour renvoyer une référence au rôle lui-même.

Lorsque vous vous connectez à une instance du rôle par programme via le Kit de développement .NET, il est relativement facile d’accéder aux informations de point de terminaison. Par exemple, une fois que vous êtes déjà connecté à un environnement de rôle spécifique, vous pouvez obtenir le port d’un point de terminaison spécifique avec ce code :

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

La propriété **Instances** retourne une collection d’objets de **RoleInstance** . Cette collection contient toujours l’instance en cours. Si le rôle ne définit pas un point de terminaison interne, la collection contient l’instance en cours, mais aucune autre instance. Le nombre d’instances de rôles dans la collection est toujours égal à 1 dans le cas où aucun point de terminaison interne n’est défini pour le rôle. Si le rôle définit un point de terminaison interne, ses instances sont détectables au moment de l’exécution et le nombre d’instances de la collection correspond au nombre d’instances spécifiées pour le rôle dans le fichier de configuration de service.

> [AZURE.NOTE] La bibliothèque managée Azure ne fournit pas un moyen de déterminer l’état de santé d’autres instances de rôle, mais vous pouvez implémenter ces examens de santé vous-même si votre service a besoin de cette fonctionnalité. Vous pouvez utiliser [Les Diagnostics Azure](cloud-services-dotnet-diagnostics.md) pour obtenir des informations sur l’exécution des instances de rôles.

Pour déterminer le numéro de port pour un point de terminaison interne sur une instance de rôle, vous pouvez utiliser la propriété [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) pour renvoyer un objet de dictionnaire qui contient les noms de point de terminaison et leurs ports et d’adresses IP correspondantes. La propriété [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) renvoie l’adresse IP et un port pour un point de terminaison spécifié. La propriété **PublicIPEndpoint** retourne le port de l’équilibrage de la charge d’un point de terminaison. Portion de l’adresse IP de la propriété **PublicIPEndpoint** n’est pas utilisée.

Voici un exemple qui itère au sein des instances de rôles.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Voici un exemple d’un rôle de collaborateur qui obtient le point de terminaison exposé par le biais de la définition du service et commence à écouter les connexions.

> [AZURE.WARNING] Ce code ne fonctionne que pour un service déployé. Lors de l’exécution dans l’émulateur de calcul Azure, les éléments de configuration de service qui créent des points de terminaison port direct (éléments**InstanceInputEndpoint** ) sont ignorés.

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Règles de trafic du réseau pour contrôler la communication entre les rôles
Après avoir défini des points de terminaison internes, vous pouvez ajouter des règles de trafic du réseau (basés sur les points de terminaison que vous avez créé) pour contrôler comment les instances de rôles peuvent communiquer entre eux. Le diagramme suivant illustre quelques scénarios courants permettant de contrôler la communication entre les rôles :

![Scénarios de règles de trafic de réseau] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Scénarios de règles de trafic de réseau")

L’exemple de code suivant montre des définitions de rôle pour les rôles présentés dans le diagramme précédent. Chaque définition de rôle inclut au moins un point de terminaison interne défini :

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Restriction de la communication entre les rôles peut se produire avec des points de terminaison internes des deux fixe et automatiquement les ports assignés.

Par défaut, une fois défini, un point de terminaison interne communication peut être transmis à partir de n’importe quel rôle au point de terminaison interne d’un rôle sans aucune restriction. Pour limiter la communication, vous devez ajouter un élément de **NetworkTrafficRules** à l’élément **ServiceDefinition** dans le fichier de définition de service.

### <a name="scenario-1"></a>Scénario 1
Autoriser uniquement le trafic réseau à partir de **WebRole1** à **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scénario 2
Autorise uniquement le trafic réseau à partir de **WebRole1** à **WorkerRole1** et **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scénario 3
Autorise uniquement le trafic réseau à partir de **WebRole1** à **WorkerRole1**et **WorkerRole1** à **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scénario 4
Autorise uniquement le trafic réseau à partir de **WebRole1** à **WorkerRole1**, **WebRole1** à **WorkerRole2**et **WorkerRole1** à **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Une référence de schéma XML pour les éléments ci-dessus, vous pouvez trouver [ici](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Étapes suivantes
Apprenez-en plus sur le [modèle](cloud-services-model-and-package.md)de Service en nuage.