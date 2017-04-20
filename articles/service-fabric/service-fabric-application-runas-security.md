<properties
   pageTitle="Présentation des applications de Service Fabric et les stratégies de sécurité de service | Microsoft Azure"
   description="Une vue d’ensemble de l’exécution d’une application de Service Fabric sous système et des comptes de sécurité locaux, y compris le point SetupEntry où une application doit exécuter certaines actions privilégiées avant le début"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurer des stratégies de sécurité pour votre application
Azure Fabric de Service permet de sécuriser les applications en cours d’exécution dans le cluster sous différents comptes d’utilisateurs. TISSU de service sécurise également les ressources utilisées par les applications au moment de déploiement sous le compte d’utilisateur, tels que des fichiers, des répertoires et des certificats. Ainsi, les applications en cours d’exécution, même dans un environnement partagé hébergé, sécurisé à partir d’un autre. 

Par défaut, les applications de Fabric du Service s’exécutent sous le compte du processus de Fabric.exe sous lequel s’exécute. Service de Fabric offre également la possibilité d’exécuter des applications sous un compte d’utilisateur local ou le compte système local, spécifié dans le manifeste de l’application. Types de compte de système local pris en charge pour sont **LocalUser**, **service réseau**, **service local**et **LocalSystem**.

 Lorsque vous exécutez le Service Fabric sur Windows Server dans votre centre de données à l’aide du programme d’installation autonome, vous pouvez utiliser des comptes de domaine Active Directory (AD).

Groupes d’utilisateurs peuvent être définis et créés afin qu’un ou plusieurs utilisateurs peuvent être ajoutés à chaque groupe à être gérés ensemble. Cela est utile lorsqu’il existe plusieurs utilisateurs service différents points d’entrée et ils doivent disposer de certains privilèges communs qui sont disponibles au niveau du groupe.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurer la stratégie pour le service SetupEntryPoint

Comme décrit dans le [modèle d’application](service-fabric-application-model.md) de la **SetupEntryPoint** est un point d’entrée privilégié qui s’exécute avec les mêmes informations d’identification en tant que Service de Fabric (généralement le compte *NetworkService* ) avant tout autre point d’entrée. Le fichier exécutable spécifié par le **point d’entrée** est généralement l’hôte de service longue, ayant une entrée le programme d’installation distinct pointez évite d’avoir à exécuter l’exécutable hôte de service avec des privilèges élevés pour de longues périodes. Le fichier exécutable spécifié par le **point d’entrée** est exécuté après que **SetupEntryPoint** a été fermé avec succès. Le processus résultant est surveillé et redémarré, commençant à nouveau par **SetupEntryPoint**, s’il s’arrête ou se bloque de jamais.

Voici un exemple de manifeste de service simple qui affiche le SetupEntryPoint et le point d’entrée principal pour le service.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurer la stratégie à l’aide d’un compte local

Après avoir configuré le service pour que le point une entrée de configuration, vous pouvez modifier les autorisations de sécurité, il s’exécute sous le manifeste d’application. L’exemple suivant montre comment configurer le service pour s’exécuter sous des privilèges de compte d’utilisateur administrateur.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Tout d’abord, créez une section **entités** avec un nom d’utilisateur, par exemple SetupAdminUser. Cela indique que l’utilisateur est un membre du groupe administrateurs système.

Ensuite, dans la section **ServiceManifestImport** , vous devez configurer une stratégie pour appliquer cette entité de sécurité à **SetupEntryPoint**. Cette option indique à Service Fabric que lorsque le fichier **MySetup.bat** est exécuté, il convient de RunAs avec des privilèges d’administrateur. Étant donné que vous avez *pas* appliquer une stratégie au point d’entrée principal, le code dans **MyServiceHost.exe** s’exécute sous le compte **NetworkService** de système. C’est le compte par défaut que tous les points d’entrée de service sont exécutés en tant que.

Nous allons maintenant ajouter le fichier MySetup.bat dans le projet Visual Studio pour tester les privilèges d’administrateur. Dans Visual Studio, avec le bouton droit sur le projet de service et ajoutez un nouveau fichier appelé MySetup.bat.

Ensuite, assurez-vous que le fichier MySetup.bat est inclus dans le package de service. Par défaut, il n’est pas. Sélectionnez le fichier avec le bouton droit pour afficher le menu contextuel et choisissez **Propriétés**. Dans la boîte de dialogue Propriétés, vérifiez que l’option **Copier dans le répertoire de sortie** est défini sur **Copier si plus récent**. Voir l’écran suivante capture.

![Visual Studio CopyToOutput pour le fichier de commandes SetupEntryPoint][image1]

Ouvrez le fichier MySetup.bat et ajoutez les commandes suivantes :

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Ensuite, générer et déployer la solution vers un cluster de développement local.  Une fois que le service a démarré, comme illustré dans l’Explorateur de tissu de Service, vous pouvez voir que le MySetup.bat a réussi de deux façons. Ouvrez une invite de commande PowerShell et tapez :

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Ensuite, notez le nom du nœud où le service a été déployé et lancé dans l’Explorateur de tissu de Service, par exemple, nœud 2. Ensuite, naviguez vers le dossier de travail d’instance application pour rechercher le fichier out.txt qui indique la valeur de **TestVariable**. Pour l’exemple si ce service a été déployé au nœud 2, puis vous pouvez accéder à ce chemin d’accès pour le **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurer la stratégie à l’aide de comptes système local
Il est souvent préférable d’exécuter le script de démarrage à l’aide d’un compte système local plutôt que sur un compte Administrateurs indiqué le précédant. Exécute le RunAs stratégie en tant qu’administrateurs en général ne fonctionne pas bien dans la mesure où les ordinateurs ont accès aux contrôles utilisateur (UAC) activée par défaut. Dans ce cas, **est recommandé d’exécuter la SetupEntryPoint en tant que LocalSystem au lieu d’un utilisateur local est ajouté au groupe Administrateurs**. L’exemple suivant montre la définition de la SetupEntryPoint à exécuter en tant que LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Lancer des commandes PowerShell à partir d’un SetupEntryPoint
Pour exécuter PowerShell à partir du point **SetupEntryPoint** , vous pouvez exécuter **PowerShell.exe** dans un fichier de commandes qui pointe vers un fichier de PowerShell. Tout d’abord, ajoutez un fichier PowerShell pour le projet de service, par exemple **MySetup.ps1**. N’oubliez pas de définir la propriété *Copier si plus récent* afin que le fichier est également inclus dans le package de service. L’exemple suivant montre un exemple de fichier de traitement par lots pour lancer un fichier PowerShell appelé MySetup.ps1, qui définit une variable d’environnement appelée **TestVariable**.


MySetup.bat pour lancer le fichier PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Dans le fichier de PowerShell, ajoutez la commande suivante pour définir une variable d’environnement système :

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Remarque :** Par défaut lors de l’exécution du fichier batch il examine le dossier de l’application appelé **fonctionne** pour les fichiers. Dans ce cas, lorsque MySetup.bat exécute nous souhaitons afin de rechercher la MySetup.ps1 dans le même dossier, qui est le dossier de **package du code** d’application. Pour modifier ce dossier, définissez le dossier de travail, comme indiqué dans le code suivant.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>À l’aide de la redirection de la console pour le débogage local
Il est parfois utile de voir la sortie de console à partir de l’exécution d’un script pour le débogage. Pour ce faire, vous pouvez définir une stratégie de redirection de console, qui écrit la sortie dans un fichier. Le fichier de sortie est écrite dans le dossier de l’application appelé **journal** sur le nœud où l’application est déployée et exécutée (voir où le trouver dans l’exemple précédent).

**Remarque : jamais** utiliser la stratégie de redirection de la console dans une application déployée en production, dans la mesure où ceci peut influer sur le basculement de l’application. **Uniquement** à utiliser pour le développement local et le débogage.  

L’exemple suivant montre la définition de la redirection de la console avec une valeur de FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Si vous modifiez maintenant le fichier MySetup.ps1 pour écrire une commande **Echo** , il écrit dans le fichier de sortie pour le débogage.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Une fois que vous avez débogué votre script, supprimez immédiatement cette stratégie de redirection de console**

## <a name="configure-policy-for-service-code-packages"></a>Configurer la stratégie pour les packages de code de service 
Dans les étapes précédentes, vous avez appris à appliquer la stratégie de RunAs à SetupEntryPoint. Nous allons un peu plus loin dans la procédure de création des différentes entités de sécurité qui peuvent être appliquées en tant que stratégies de service.

### <a name="create-local-user-groups"></a>Créer des groupes d’utilisateurs locaux
Groupes d’utilisateurs peuvent être définis et créés qui autorise un ou plusieurs utilisateurs à ajouter à un groupe. Ceci est particulièrement utile si plusieurs utilisateurs service différents points d’entrée et que ces derniers doivent disposer de certains privilèges communs qui sont disponibles au niveau du groupe. L’exemple suivant montre un groupe local appelé **LocalAdminGroup** avec des privilèges d’administrateur. Deux utilisateurs, Customer1 et Customer2, deviennent membres de ce groupe local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Créer des utilisateurs locaux
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l’application. Lorsqu’un type de compte **LocalUser** est spécifié dans la section entités du manifeste d’application, Service Fabric crée des comptes d’utilisateurs locaux sur les ordinateurs où l’application est déployée. Par défaut, ces comptes ne disposent pas des mêmes noms que ceux spécifiés dans le manifeste d’application (par exemple, Customer3 dans l’exemple suivant). Au lieu de cela, ils sont générés dynamiquement et ont des mots de passe aléatoires.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Affecter des stratégies pour les packages de code de service
La section **RunAsPolicy** pour un **ServiceManifestImport** Spécifie le compte à partir de la section d’entités de sécurité qui doit être utilisé pour exécuter un package de code. Il associe également des packages de code à partir du manifeste de service avec des comptes d’utilisateur dans la section entités. Vous pouvez le spécifier pour la configuration ou les points d’entrée principale, ou vous pouvez spécifier `All` pour l’appliquer à la fois. L’exemple suivant montre les différentes stratégies appliquées :

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si **EntryPointType** n’est pas spécifié, la valeur par défaut est la valeur EntryPointType = « Main ». Si vous spécifiez **SetupEntryPoint** est particulièrement utile lorsque vous souhaitez effectuer certains paramètres des privilèges élevés sous un compte système. Le code de service peut exécuter sous un compte de privilège inférieur.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Appliquer une stratégie par défaut à tous les modules de code de service
La section **DefaultRunAsPolicy** est utilisée pour spécifier un compte d’utilisateur par défaut pour tous les packages de code qui n’ont pas un spécifique **RunAsPolicy** définie. Si vous avez besoin de la plupart des packages de code spécifiées dans le manifeste de service utilisé par une application pour s’exécuter sous le même utilisateur, l’application peut définir simplement une stratégie de RunAs par défaut avec ce compte d’utilisateur. L’exemple suivant spécifie que si un package de code ne possède pas une **RunAsPolicy** est spécifié, le package de code doit s’exécuter sous le **MyDefaultAccount** spécifié dans la section entités.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>À l’aide d’un utilisateur ou un groupe de domaine Active Directory
Pour tissu de Service installé sur un serveur Windows avec le programme d’installation autonome, peut exécuter le service sous les informations d’identification pour un compte de groupe ou un utilisateur Active Directory (AD). Remarque : Il est Active Directory sur site au sein de votre domaine et non avec Azure Active Directory (DAS). À l’aide d’un utilisateur de domaine ou un groupe, vous pouvez alors accéder autres ressources dans le domaine (par exemple, les partages de fichiers) qui les autorisations ont été accordées.

L’exemple suivant affiche un utilisateur d’AD appelé *TestUser* avec leur mot de passe de domaine crypté à l’aide d’un certificat appelé *MonCert*. Vous pouvez utiliser la `Invoke-ServiceFabricEncryptText` commande Powershell pour créer le texte de chiffrement secrète. Consultez cet article [Gestion des secrets dans des applications de Service Fabric](service-fabric-application-secret-management.md) pour plus de détails. La clé privée du certificat pour décrypter le mot de passe doit être déployée sur la machine locale dans une méthode out-of-band (dans Azure c’est via le Gestionnaire de ressources). Puis, lorsque le Service Fabric déploie le package de service sur l’ordinateur, il est en mesure de décrypter le secret et avec le nom de l’utilisateur, s’authentifier avec AD pour s’exécuter sous les informations d’identification.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Affecter des SecurityAccessPolicy pour les points de terminaison HTTP et HTTPS
Si vous appliquez une stratégie RunAs à un service, et manifeste de service déclare les ressources du point de terminaison avec le protocole HTTP, vous devez spécifier un **SecurityAccessPolicy** pour vous assurer que les ports affectés à ces points de terminaison sont correctement répertorié pour le compte d’utilisateur RunAs du service s’exécute sous contrôle d’accès. Dans le cas contraire, **http.sys** n’a pas accès au service, et vous obtenez des défaillances avec des appels à partir du client. L’exemple suivant applique le compte Customer3 à un point de terminaison appelé **ServiceEndpointName**, en lui attribuant des droits d’accès complets.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Pour le point de terminaison HTTPS, vous devez également indiquer le nom du certificat à renvoyer au client. Pour cela, à l’aide **EndpointBindingPolicy**, avec le certificat défini dans une section de certificats dans le manifeste d’application.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Un exemple de manifeste d’application complète
Le manifeste d’application suivant montre que la plupart des paramètres différents :

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* [Comprendre le modèle d’application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déploiement d’une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
