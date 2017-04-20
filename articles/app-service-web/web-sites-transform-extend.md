<properties
    pageTitle="Application web de Service d’application Azure advanced configuration et extensions"
    description="Utilisez les déclarations XML Document Transformation(XDT) pour transformer le fichier ApplicationHost.config dans votre application web de Service d’application Azure et pour ajouter des extensions privées pour activer les actions d’administration personnalisé."
    authors="cephalin"
    writer="cephalin"
    editor="mollybos"
    manager="wpickett"
    services="app-service"
    documentationCenter=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/25/2016"
    ms.author="cephalin"/>

# <a name="azure-app-service-web-app-advanced-config-and-extensions"></a>Application web de Service d’application Azure advanced configuration et extensions

À l’aide de déclarations de [Transformation de documents XML](http://msdn.microsoft.com/library/dd465326.aspx) (XDT), vous pouvez transformer le fichier [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) dans votre application web dans le Service d’application Azure. Vous pouvez également utiliser des déclarations de XDT pour ajouter des extensions privées pour activer les actions d’administration personnalisé web app. Cet article inclut un exemple PHP Manager web application d’extension qui permet la gestion des paramètres PHP via une interface web.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

##<a id="transform"></a>Configuration avancée via ApplicationHost.config
La plate-forme de Service de l’application fournit la souplesse et contrôle pour la configuration d’application web. Bien que le fichier de configuration IIS ApplicationHost.config standard n’est pas disponible pour l’édition directe dans le Service d’application, la plate-forme prend en charge un modèle de transformation ApplicationHost.config déclaratif basé sur XML Document Transformation (XDT).

Pour tirer parti de cette fonctionnalité de transformation, vous créez un fichier ApplicationHost.xdt avec le contenu XDT et les place sous la racine du site (d:\home\site) dans la [Console de Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). Vous devrez peut-être redémarrer l’application Web pour que les modifications prennent effet.

Le applicationHost.xdt suivant montre comment ajouter une nouvelle variable d’environnement personnalisé à une application web qui utilise le point 5.4 de PHP.

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.webServer>
                <fastCgi>
                    <application>
                        <environmentVariables>
                                <environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
                        </environmentVariables>
                    </application>
                </fastCgi>
        </system.webServer>
    </configuration>


Un fichier journal avec les détails et le statut de la transformation est disponible à partir de la racine FTP sous LogFiles\Transform.

Pour des exemples supplémentaires, consultez [https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples).

**Remarque**<br />
Les éléments de la liste des modules sous `system.webServer` ne peut pas être supprimé ou réorganisé, mais des ajouts à la liste sont possibles.


##<a id="extend"></a>Étendre votre application web

###<a id="overview"></a>Vue d’ensemble des extensions d’application web privée

Service d’application prend en charge les extensions d’application web sous la forme d’un point d’extensibilité pour les actions d’administration. En fait, certaines fonctionnalités de plate-forme de Service de l’application sont implémentées en tant qu’extensions préinstallées. Alors que les extensions de la plate-forme préinstallé ne peut pas être modifiées, vous pouvez créer et configurer des extensions privées pour votre application web. Cette fonctionnalité s’appuie également sur les déclarations de XDT. Les étapes clés pour la création d’une extension d’application web privés sont les suivants :

1. Extension de app **contenu**Web : créer une application web pris en charge par le Service de l’application
2. Web application extension **déclaration**: créer un fichier ApplicationHost.xdt
3. App extension **déploiement**Web : placer du contenu dans le dossier SiteExtensions sous`root`

Liens internes de l’application web doivent pointer vers un chemin relatif au chemin de l’application spécifié dans le fichier ApplicationHost.xdt. Toute modification apportée au fichier ApplicationHost.xdt nécessite un recyclage d’application web.

**Remarque**: des informations supplémentaires sur ces éléments clés sont disponibles à l’adresse [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Un exemple détaillé est inclus pour montrer les étapes de la création et l’activation d’une extension d’application web privée. Le code source de l’exemple de gestionnaire de PHP qui suit peut être téléchargé à partir de [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a>Exemple d’extension Web app : le gestionnaire PHP

Le gestionnaire PHP est une extension d’application web qui permet à web application aux administrateurs de facilement afficher et configurer leurs paramètres de PHP à l’aide d’une interface web au lieu de devoir modifier directement les fichiers .ini PHP. Les fichiers de configuration communs pour PHP incluent le fichier php.ini qui se trouve dans Program Files et. fichier user.ini situé dans le dossier racine de votre application web. Dans la mesure où le fichier php.ini n’est pas directement modifiable sur la plate-forme de Service de l’application, l’extension PHP Manager utilise le. fichier user.ini pour appliquer les modifications de paramètre.

####<a id="PHPwebapp"></a>L’application web de gestionnaire de PHP

Voici la page d’accueil du déploiement le gestionnaire PHP :

![TransformSitePHPUI][TransformSitePHPUI]

Comme vous pouvez le voir, une extension d’application web est comme une application web standard, mais avec un fichier de ApplicationHost.xdt supplémentaire placé dans le dossier racine de l’application web (plus de détails sur le fichier ApplicationHost.xdt sont disponibles dans la section suivante de cet article).

L’extension du Gestionnaire de PHP a été créée à l’aide du modèle d’Application de Web Visual Studio ASP.NET MVC 4. La vue suivante à partir de l’Explorateur de solutions affiche la structure de l’extension du Gestionnaire de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

La logique spéciale uniquement nécessaire pour les e/s de fichier est pour indiquer où se trouve le répertoire wwwroot de l’application web. Comme dans l’exemple de code suivant, la variable environnement « HOME » indique le chemin d’accès de la racine de l’application web et le chemin d’accès de wwwroot peut être construite en ajoutant « site\wwwroot » :

    /// <summary>
    /// Gives the location of the .user.ini file, even if one doesn't exist yet
    /// </summary>
    private static string GetUserSettingsFilePath()
    {
            var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
            if (rootPath == null)
            {
                rootPath = System.IO.Path.GetTempPath(); // For testing purposes
            };
            var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
            return userSettingsFile;
    }


Une fois que le chemin d’accès du répertoire, vous pouvez utiliser des opérations d’e/s de fichier normal pour lire et écrire dans des fichiers.

Un point de l’attention avec les extensions d’application web ce qui concerne la gestion des liens internes.  Si vous avez des liens dans les fichiers HTML qui donnent des chemins d’accès absolus pour les liens internes de votre application web, vous devez vérifier que ces liens sont précédées de votre nom d’extension en tant que votre racine. Cela est nécessaire car la racine de votre extension est à présent « /`[your-extension-name]`/ » plutôt que d’être simples « / », donc tout internes des liens doivent être mis à jour en conséquence. Par exemple, supposons que votre code inclut un lien vers les éléments suivants :

`"<a href="/Home/Settings">PHP Settings</a>"`

Lorsque le lien fait partie d’une extension d’application web, le lien doit être sous la forme suivante :

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Vous pouvez contourner cette exigence en soit à l’aide des seuls chemins d’accès relatifs dans votre application web, ou dans le cas d’applications ASP.NET à l’aide de la `@Html.ActionLink` méthode qui crée automatiquement les liens appropriés.

####<a id="XDT"></a>Le fichier applicationHost.xdt

Le code de votre extension d’application web passe sous %HOME%\SiteExtensions\[nom de votre extension]. Nous allons appeler cette racine d’extension.  

Pour enregistrer votre extension d’application web dans le fichier applicationHost.config, vous devez placer un fichier appelé ApplicationHost.xdt dans la racine de l’extension. Le contenu du fichier ApplicationHost.xdt doit être comme suit :

    <?xml version="1.0"?>
    <configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
        <system.applicationHost>
                <sites>
                    <site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
                        <!-- NOTE: Add your extension name in the application paths below -->
                        <application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
                        <application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
                            <virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
                        </application>
                    </site>
                </sites>
        </system.applicationHost>
    </configuration>

Le nom que vous avez sélectionné comme le nom de votre extension doit avoir le même nom que votre dossier de racine d’extension.

Cela a pour effet d’ajouter un nouveau chemin d’accès d’application pour le `system.applicationHost` liste de sites sous le site SCM. Le site SCM est un point de terminaison de site administration avec des droits d’accès spécifiques. L’URL est `https://[your-site-name].scm.azurewebsites.net`.  

    <system.applicationHost>
        ...
        <site name="~1[your-website]" id="1716402716">
                <bindings>
                    <binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
                    <binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
                </bindings>
                <traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
                <detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
                <logFile logSiteId="false" />
                <application path="/" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
                </application>
                <!-- Note the custom changes that go here -->
                <application path="/[your-extension-name]" applicationPool="[your-website]">
                    <virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
                </application>
            </site>
    </sites>
      ...
    </system.applicationHost>

###<a id="deploy"></a>Déploiement d’une extension Web app

Pour installer votre extension d’application web, vous pouvez utiliser FTP pour copier tous les fichiers de votre application web à la `\SiteExtensions\[your-extension-name]` dossier de l’application web sur lequel vous souhaitez installer l’extension.  Veillez à copier le fichier ApplicationHost.xdt à cet emplacement. Redémarrez votre application web pour activer l’extension.

Vous serez en mesure de voir votre extension d’application web à :

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Notez que l’URL ressemble juste l’URL pour votre application web, sauf qu’il utilise le protocole HTTPS et contient « .scm ».

Il est possible de désactiver toutes les extensions (pas préinstallées) privées pour votre application web lors du développement et des enquêtes, en ajoutant les paramètres de l’application avec la clé `WEBSITE_PRIVATE_EXTENSIONS` et une valeur de `0`.

>[AZURE.NOTE] Si vous souhaitez commencer avec le Service d’application Azure avant l’ouverture d’un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web de courte durée starter immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; aucun des engagements.

## <a name="whats-changed"></a>Ce qui a changé
* Pour obtenir un guide pour la modification de sites Web au Service de l’application voir : [Service d’application Azure et son Impact sur les Services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 
