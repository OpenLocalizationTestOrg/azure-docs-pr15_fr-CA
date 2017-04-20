<properties
    pageTitle="Intégrer un service cloud avec Azure CDN | Microsoft Azure"
    description="Un didacticiel qui vous explique comment déployer un service en nuage qui sert le contenu à partir d’un point de terminaison Azure CDN intégré"
    services="cdn, cloud-services"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor="tysonn"/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="intro"></a>Intégrer un service cloud avec Azure CDN

Un service en nuage peut être intégré avec Azure CDN, desservant un contenu à partir de l’emplacement du service nuage. Cette approche vous offre les avantages suivants :

- Facilement déployer et mettre à jour des images, des scripts et des feuilles de style dans les répertoires de projet de votre service cloud
- Facilement mettre à niveau les packages NuGet dans votre service cloud, tels que jQuery ou versions d’amorçage
- Gérer votre application Web et votre tout contenu servi CDN depuis la même interface de Visual Studio
- Flux de travail unifié de déploiement pour votre application Web et votre contenu servi CDN
- Intégration ASP.NET regroupement et minimisation avec Azure CDN

## <a name="what-you-will-learn"></a>Vous apprendrez ##

Dans ce didacticiel, vous allez apprendre comment :

-   [Intégrer un point de terminaison Azure CDN avec votre service cloud et de servir du contenu statique dans vos pages Web à partir d’Azure CDN](#deploy)
-   [Configurer les paramètres de cache pour le contenu statique dans votre service cloud](#caching)
-   [Fournir du contenu à partir des actions de contrôleur via Azure CDN](#controller)
-   [Servir et combinés réduite contenu grâce à Azure CDN tout en préservant le script de débogage dans Visual Studio](#bundling)
-   [Configurer des secours vos scripts et les CSS lorsque votre Azure CDN est en mode hors connexion](#fallback)

## <a name="what-you-will-build"></a>Vous allez générer ##

Vous déployez un rôle Web de service cloud à l’aide de la valeur par défaut du modèle ASP.NET MVC, ajoutez du code pour servir du contenu depuis un CDN Azure intégré, par exemple une image, les résultats d’action de contrôleur et les fichiers JavaScript et CSS par défaut et également écrire du code pour configurer le mécanisme de secours pour les offres groupées pris en charge dans le cas où le CDN est hors connexion.

## <a name="what-you-will-need"></a>Ce que vous devez ##

Ce didacticiel présente les conditions préalables suivantes :

-   Un actif [compte Microsoft Azure](/account/)
-   Visual Studio 2015 avec [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [AZURE.NOTE] Vous avez besoin d’un compte Azure pour compléter ce didacticiel :
> + Vous pouvez [Ouvrir un compte Azure gratuitement](/pricing/free-trial/) , vous obtenez des crédits vous permet d’essayer les services Azure payés et même après leur utilisation jusqu'à vous pouvez conserver le compte et utilisation libre des services Azure, tels que les sites Web.
> + Vous pouvez [activer des avantages pour les abonnés MSDN](/pricing/member-offers/msdn-benefits-details/) - MSDN votre abonnement fournit les crédits chaque mois que vous pouvez utiliser pour des services Azure payés.

<a name="deploy"></a>
## <a name="deploy-a-cloud-service"></a>Déployer un service cloud ##

Dans cette section, vous déployez le modèle d’application ASP.NET MVC dans Visual Studio 2015 par défaut à un rôle de Web service cloud et puis vous l’intégrez avec un nouveau point de terminaison CDN. Suivez les instructions ci-dessous :

1. Dans Visual Studio 2015, créer un nouveau service en nuage Azure à partir de la barre de menus à partir de **fichier > Nouveau > projet > nuage > Azure Cloud Service**. Donnez-lui un nom, puis cliquez sur **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Sélectionnez le **Rôle de Web ASP.NET** , cliquez sur le **>** bouton. Cliquez sur OK.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. **MVC** et cliquez sur **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Maintenant, publier ce rôle Web à un service cloud Azure. Cliquez sur le projet de service cloud et sélectionnez **Publier**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Si vous n’êtes pas encore inscrit dans Microsoft Azure, cliquez sur la liste déroulante **Ajouter un compte...** , puis cliquez sur l’élément de menu **Ajouter un compte** .

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Dans la page de connexion, connectez-vous avec le compte de Microsoft que vous permet d’activer votre compte Azure.
7. Une fois que vous êtes connecté, cliquez sur **suivant**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. En supposant que vous n’avez pas créé un compte de service ou de stockage cloud, Visual Studio vous permet de créer à la fois. Dans la boîte de dialogue **créer un Service Cloud et compte** , tapez le nom de service de votre choix et sélectionnez la région souhaitée. Ensuite, cliquez sur **créer**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Dans la page Paramètres de publication, vérifiez la configuration et cliquez sur **Publier**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

    >[AZURE.NOTE] Le processus de publication des services en nuage prend beaucoup de temps. L’activer le déploiement Web pour toutes les option de rôles peut rendre le débogage de votre service cloud beaucoup plus rapide grâce à des mises à jour rapides (mais temporaire) à vos rôles Web. Pour plus d’informations sur cette option, consultez la [publication d’un Service Cloud à l’aide d’outils Azure](http://msdn.microsoft.com/library/ff683672.aspx).

    Lorsque le **Journal d’activité de Microsoft Azure** indique que l’état de publication est **terminé**, vous allez créer un point de terminaison CDN qui est intégré à ce service en nuage.

    >[AZURE.WARNING] Si, après la publication, le service en nuage déployé affiche un écran d’erreur, il est probable, car vous avez déployé le service en nuage est à l’aide d’un [système d’exploitation qui n’inclut pas .NET 4.5.2 invité](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Vous pouvez contourner ce problème en [déployant .NET 4.5.2 sous la forme d’une tâche de démarrage](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="create-a-new-cdn-profile"></a>Créer un nouveau profil CDN

Un profil CDN est une collection de points de terminaison CDN.  Chaque profil contient un ou plusieurs points de terminaison CDN.  Vous souhaiterez peut-être plusieurs profils permettent d’organiser vos points de terminaison CDN par domaine internet, application web ou d’autres critères.

> [AZURE.TIP] Si vous avez déjà un profil CDN que vous souhaitez utiliser pour ce didacticiel, passez à [Création d’un nouveau point de terminaison CDN](#create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Créer un nouveau point de terminaison CDN

**Pour créer un nouveau point de terminaison CDN pour votre compte de stockage**

1. Dans le [Portail de gestion Azure](https://portal.azure.com), accédez à votre profil CDN.  Vous pouvez avoir épinglé il au tableau de bord à l’étape précédente.  Si vous ne vous le trouverez en cliquant sur **Parcourir**, puis **les profils CDN**, sur le profil que vous souhaitez ajouter votre point de terminaison.

    La lame de profil CDN s’affiche.

    ![Profil CDN][cdn-profile-settings]

2. Cliquez sur le bouton **Ajouter un point de terminaison** .

    ![Ajouter bouton de point de terminaison][cdn-new-endpoint-button]

    La blade **d’Ajouter un point de terminaison** s’affiche.

    ![Ajouter des lames de point de terminaison][cdn-add-endpoint]

3. Entrez un **nom** pour ce point de terminaison CDN.  Ce nom servira à accéder à vos ressources mises en cache au niveau du domaine `<EndpointName>.azureedge.net`.

4. Dans la liste déroulante **type d’origine** , sélectionnez *service de Cloud*.  

5. Dans la liste déroulante **nom d’hôte d’origine** , sélectionnez votre service cloud.

6. Laissez les valeurs par défaut pour le **port de protocole/origine**, **en-tête hôte d’origine**et **chemin d’accès d’origine**.  Vous devez spécifier au moins un protocole (HTTP ou HTTPS).

7. Cliquez sur le bouton **Ajouter** pour créer le nouveau point de terminaison.

8. Une fois le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour le profil. La vue liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine.

    ![Point de terminaison CDN][cdn-endpoint-success]

    > [AZURE.NOTE] Le point de terminaison pas seront immédiatement disponible pour utilisation.  Il peut prendre jusqu'à 90 minutes pour l’enregistrement de se propager à travers le réseau CDN. Les utilisateurs qui tentent d’utiliser le nom de domaine CDN immédiatement receviez code d’état 404, jusqu'à ce que le contenu est disponible via le CDN.

## <a name="test-the-cdn-endpoint"></a>Tester le point de terminaison CDN

Lorsque l’état de publication est **terminé**, ouvrez une fenêtre de navigateur et accédez à * *http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Dans mon programme d’installation, cette URL est la suivante :

    http://camservice.azureedge.net/Content/bootstrap.css

Qui correspond à l’URL d’origine suivante au point de terminaison CDN :

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Lorsque vous naviguez vers * *http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, en fonction de votre navigateur, vous serez invité à télécharger ou à ouvrir le bootstrap.css provenant de votre application Web publiée.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Vous pouvez accéder de la même manière à n’importe quelle URL accessible publiquement à * *http://*&lt;ServiceName% >*.cloudapp.net/**, directement à partir de votre point de terminaison CDN. Par exemple :

-   Un fichier .js dans le chemin d’accès /Script
-   Tout fichier de contenu à partir de la/Content chemin d’accès
-   Toute action et de contrôleur
-   Si la chaîne de requête est votre point de terminaison CDN, une URL avec des chaînes de requête

En fait, avec la configuration ci-dessus, vous pouvez héberger le service cloud entière à partir * *http://*&lt;cdnName >*.azureedge.net/**. Si j’accède à **http://camservice.azureedge.net/ **, obtenir le résultat de l’action d’accueil/Index.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Cela ne signifie pas, toutefois, il est toujours une bonne idée (ou généralement une bonne idée) pour répondre à un service en nuage ensemble grâce à Azure CDN. Les restrictions sont les suivantes :

-   Cette approche nécessite d’être publique, car Azure CDN ne peut pas servir de n’importe quel contenu privé pour l’instant sur l’ensemble de votre site.
-   Si le point de terminaison CDN se déconnecte pour une raison quelconque, si maintenance programmée ou erreur de l’utilisateur, votre service cloud entier est mis hors connexion, sauf si les clients peuvent être redirigés vers l’URL d’origine * *http://*&lt;ServiceName% >*.cloudapp.net/**.
-   Même avec les paramètres personnalisés de Cache-Control (voir [configurer les options pour les fichiers statiques dans votre service cloud la mise en cache](#caching)), un point de terminaison CDN n’améliore pas les performances du contenu de hautement dynamiques. Si vous avez essayé de charger la page d’accueil à partir de votre point de terminaison CDN comme indiqué ci-dessus, notez qu’il a pris au moins 5 secondes pour charger la page d’accueil par défaut la première fois, ce qui est une page simple. Imaginez ce qui se passerait à l’expérience du client si cette page contient du contenu dynamique qui doit de mettre à jour toutes les minutes. Héberger un contenu dynamique à partir d’un point de terminaison CDN nécessite l’expiration de cache court, qui se traduit par des échecs de cache fréquentes au point de terminaison CDN. Cela nuit aux performances de votre service cloud et nuit à l’utilité d’un CDN.

L’alternative consiste à déterminer le contenu à servir d’Azure CDN sur une base au cas par cas dans votre service cloud. À cette fin, vous avez déjà vu comment accéder à des fichiers de contenu à partir de l’extrémité CDN. Je vous montrerai comment prendre en charge une action de contrôleur spécifique via le point de terminaison CDN de [servir du contenu à partir des actions de contrôleur via Azure CDN](#controller).

<a name="caching"></a>
## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configurer les options de mise en cache de fichiers statiques dans votre service cloud ##

Grâce à l’intégration d’Azure CDN dans votre service cloud, vous pouvez spécifier comment vous souhaitez que le contenu statique à mettre en cache dans le point de terminaison CDN. Pour ce faire, ouvrez le *fichier Web.config* de votre projet de rôle Web (par exemple, WebRole1) et ajouter une `<staticContent>` élément à `<system.webServer>`. Le code XML ci-dessous configure le cache pour expirer dans les 3 jours.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Une fois cela fait, tous les fichiers statiques dans votre service cloud observera la même règle dans votre cache CDN. Pour un contrôle plus granulaire des paramètres du cache, ajoutez un fichier *Web.config* dans un dossier et vos paramètres. Par exemple, ajouter un fichier *Web.config* dans le dossier *\Content* et remplacer le contenu par le code XML suivant :

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Avec ce paramètre, tous les fichiers statiques à partir du dossier *\Content* mise en cache pendant 15 jours.

Pour plus d’informations sur la configuration de la `<clientCache>` élément, reportez-vous à la section [Cache Client &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

De [servir du contenu à partir des actions de contrôleur via Azure CDN](#controller), je vais également vous montrer à comment vous pouvez configurer les paramètres de cache de résultats d’action de contrôleur dans le cache CDN.

<a name="controller"></a>
## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Fournir du contenu à partir des actions de contrôleur via Azure CDN ##

Lorsque vous intégrez un rôle de Web service cloud avec Azure CDN, il est relativement facile de servir du contenu à partir des actions de contrôleur via l’Azure CDN. Autre que d’héberger votre service cloud directement par l’intermédiaire d’Azure CDN (illustré ci-dessus), [Maarten Balliauw](https://twitter.com/maartenballiauw) vous montre comment procéder avec un contrôleur de MemeGenerator [latence de réduction sur le web avec l’Azure CDN](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)vous amuser. Je sera simplement le reproduire ici.

Supposons que dans votre service cloud que vous souhaitez générer les memes basé sur une image de Chuck Norris jeune (photo par [Alan lumière](http://www.flickr.com/photos/alan-light/218493788/)) comme suit :

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Vous avez un simple `Index` action qui autorise les clients à spécifier les superlatifs dans l’image, puis génère le meme une fois qu’ils publient à l’action. Puisqu’il s’agit de Chuck Norris, vous vous attendez à cette page pour devenir globalement l’outil très apprécié. Il s’agit d’un bon exemple de service contenu partiel dynamique avec Azure CDN.

Suivez les étapes ci-dessus pour configurer cette action de contrôleur :

1. Dans le dossier *\Controllers* , créez un nouveau fichier .cs appelé *MemeGeneratorController.cs* et remplacer le contenu par le code suivant. Veillez à remplacer la partie en surbrillance avec votre nom CDN.  

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

                public ActionResult Index()
                {
                    return View();
                }

                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }

                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }

                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }

                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }

                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }

                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }

                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);

                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }

                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }

2. Avec le bouton droit dans la valeur par défaut `Index()` action et sélectionnez **Ajouter une vue**.

    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Acceptez les paramètres ci-dessous, puis cliquez sur **Ajouter**.

    ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Ouvrez la nouvelle *Views\MemeGenerator\Index.cshtml* et remplacer le contenu par le HTML simple suivant pour soumettre les superlatifs :

        <h2>Meme Generator</h2>

        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>

5. Publiez de nouveau le service en nuage et accédez à * *http://*&lt;ServiceName% >*.cloudapp.net/MemeGenerator/Index** dans votre navigateur.

Lorsque vous envoyez les valeurs de formulaire à `/MemeGenerator/Index`, le `Index_Post` méthode d’action renvoie un lien vers le `Show` méthode d’action avec l’identificateur d’entrée respectif. Lorsque vous cliquez sur le lien, vous atteignez le code suivant :  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Si votre débogueur local est attaché, vous obtiendrez l’expérience de débogage régulières avec une redirection locale. Si elle est en cours d’exécution dans le service cloud, puis il redirige vers :

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Qui correspond à l’URL d’origine suivante à votre point de terminaison du Canada :

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Vous pouvez ensuite utiliser le `OutputCacheAttribute` d’attribut sur le `Generate` méthode pour spécifier comment le résultat de l’action doit être mis en cache, qui respecte la Azure CDN. Le code ci-dessous spécifie une expiration du cache de 1 heure (3 600 secondes).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

De même, vous pouvez servir des contenus à partir de n’importe quelle action de contrôleur dans votre service cloud via votre Azure CDN, avec l’option de mise en cache de votre choix.

Dans la section suivante, je vous montrerai comment prendre en charge les scripts fournis et réduites et CSS grâce à Azure CDN.

<a name="bundling"></a>
## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Intégration ASP.NET regroupement et minimisation avec Azure CDN ##

Les feuilles de style CSS et les scripts changent peu souvent et constituent des candidates pour le cache d’Azure CDN. Desservant la totalité du rôle Web via votre Azure CDN est le moyen le plus simple pour intégrer le regroupement et minimisation avec Azure CDN. Cependant, que vous souhaiterez pas cela, je vous montrerai pour cela tout en conservant l’expérience de programme souhaité de ASP.NET regroupement et minimisation, telles que :

-   Expérience du mode débogage très
-   Déploiement rationalisé
-   Mises à jour immédiates aux clients des mises à niveau de version de script/CSS
-   Mécanisme de secours en cas d’échec de votre point de terminaison CDN
-   Réduire la modification de code

Dans le projet **WebRole1** que vous avez créé dans [intégrer un point de terminaison Azure CDN avec votre Azure site Web et servir du contenu statique dans vos pages Web à partir d’Azure CDN](#deploy), ouvrez *App_Start\BundleConfig.cs* et examinez les `bundles.Add()` les appels de méthode.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

La première `bundles.Add()` instruction ajoute une offre groupée de script dans le répertoire virtuel `~/bundles/jquery`. Ouvrez ensuite *Views\Shared\_Layout.cshtml* pour voir comment la balise offre groupée de script est rendue. Vous serez en mesure de trouver la ligne suivante de code Razor :

    @Scripts.Render("~/bundles/jquery")

Lorsque ce code Razor est exécuté dans le rôle Azure Web, il restituera un `<script>` balise pour l’offre groupée du script semblable au suivant :

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

Toutefois, lorsqu’il est exécuté dans Visual Studio en tapant `F5`, il restituera individuellement chaque fichier de script du lot (dans ce cas, un seul script fichier est dans le lot) :

    <script src="/Scripts/jquery-1.10.2.js"></script>

Cela vous permet de déboguer le code JavaScript dans votre environnement de développement tout en réduisant les connexions client simultanées (regroupement) et amélioration du fichier téléchargent des performances (réduction) dans la production. Il s’agit d’une fonctionnalité intéressante à conserver avec intégration d’Azure CDN. En outre, étant donné que l’offre groupée rendue contient déjà une chaîne de version généré automatiquement, vous souhaitez répliquer cette fonctionnalité afin que chaque fois que vous mettez à jour votre version de jQuery via NuGet, elle peut être mise à jour côté client dès que possible.

Suivez les étapes ci-dessous pour le regroupement des ASP.NET d’intégration et minimisation avec votre point de terminaison CDN.

1. Dans *App_Start\BundleConfig.cs*, modifiez les `bundles.Add()` les méthodes à utiliser un autre [constructeur de l’offre groupée](http://msdn.microsoft.com/library/jj646464.aspx), qui spécifie une adresse CDN. Pour ce faire, remplacez la `RegisterBundles` définition de méthode par le code suivant :  

        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Veillez à remplacer `<yourCDNName>` avec le nom de votre Azure CDN.

    Clairement, vous définissez des `bundles.UseCdn = true` et ajouté une URL CDN soigneusement chaque lot. Par exemple, le premier constructeur dans le code :

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    est le même que :

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

    Ce constructeur indique le groupage d’ASP.NET et de minimisation à rendre les fichiers de script individuels lors du débogage localement, mais utiliser l’adresse CDN spécifiée pour accéder au script en question. Notez, cependant, deux caractéristiques importantes avec cette URL CDN élaborée avec soin :

    -   L’origine de cette URL CDN est `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, qui est en fait le répertoire virtuel du fichier de script dans votre service cloud.
    -   Dans la mesure où vous utilisez le constructeur CDN, la balise de script CDN pour l’offre groupée ne contient plus la chaîne de version généré automatiquement dans l’URL rendue. Vous devez générer manuellement une chaîne de version unique chaque fois que l’ensemble du script est modifié pour forcer une absence de cache à votre Azure CDN. Dans le même temps, cette chaîne de version unique doit rester constante par le biais de la durée de vie du déploiement pour maximiser les correspondances dans le cache à votre Azure CDN après le déploiement de l’offre groupée.
    -   La chaîne de requête v = extrait de < W.X.Y.Z > à partir de *Properties\AssemblyInfo.cs* dans votre projet de rôle Web. Vous pouvez avoir un flux de travail de déploiement qui inclut chaque fois que vous publiez vers Azure, l’incrémentation de la version de l’assembly. Ou bien, vous pouvez simplement modifier *Properties\AssemblyInfo.cs* dans votre projet pour incrémenter automatiquement la chaîne de version à chaque fois que vous générez, en utilisant le caractère générique ' *'. Par exemple :

            [assembly: AssemblyVersion("1.0.0.*")]

        Une autre stratégie pour rationaliser la génération d’une chaîne unique pour la durée de vie d’un déploiement fonctionnera également ici.

3. Republier le service en nuage et accéder à la page d’accueil.

4. Permet d’afficher le code HTML de la page. Vous devez être en mesure de voir l’URL CDN rendu, avec une chaîne de version unique chaque fois que vous publiez de nouveau les modifications à votre service cloud. Par exemple :  

        ...

        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>

        ...

        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>

        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>

        ...

5. Dans Visual Studio, déboguer le service en nuage dans Visual Studio en tapant `F5`.,

6. Permet d’afficher le code HTML de la page. Vous verrez toujours chaque fichier de script restitué individuellement et avoir un débogage cohérente peut rencontrer dans Visual Studio.  

        ...

            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>

            <script src="/Scripts/modernizr-2.6.2.js"></script>

        ...

            <script src="/Scripts/jquery-1.10.2.js"></script>

            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>

        ...   

<a name="fallback"></a>
## <a name="fallback-mechanism-for-cdn-urls"></a>Mécanisme de secours pour les URL du CDN ##

Lorsque votre point de terminaison Azure CDN échoue pour une raison quelconque, vous souhaitez que votre page Web assez intelligent accéder à votre serveur Web d’origine comme option de secours de chargement du JavaScript ou amorçage. Il est assez sérieuse perte d’images sur votre site Web en raison de l’indisponibilité CDN, mais beaucoup plus important de perdre la fonctionnalité essentielle fournie par les scripts et les feuilles de style.

La classe [offre groupée](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contient une propriété appelée [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) qui vous permet de configurer le mécanisme de secours de l’échec CDN. Pour utiliser cette propriété, procédez comme suit :

1. Dans votre projet de rôle Web, ouvrez *App_Start\BundleConfig.cs*, où vous avez ajouté une URL CDN dans chaque [constructeur de faisceau](http://msdn.microsoft.com/library/jj646464.aspx)et apportez les modifications suivantes en surbrillance pour ajouter le mécanisme de secours aux groupes par défaut :  

        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                "~/Scripts/bootstrap.js",
                                "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Lors de la `CdnFallbackExpression` est pas null, script est injecté dans le code HTML pour tester si l’offre est chargé correctement et, dans le cas contraire, accéder à l’offre groupée directement à partir du serveur Web d’origine. Cette propriété doit être définie sur une expression JavaScript qui teste si le bundle CDN respectif est chargé correctement. L’expression permettant de tester chaque botte diffère en fonction du contenu. Pour les offres groupées par défaut ci-dessus :

    -   `window.jquery`est défini dans .js jquery-{version}
    -   `$.validator`est défini dans jquery.validate.js
    -   `window.Modernizr`est défini dans modernizer-{version} .js
    -   `$.fn.modal`est défini dans bootstrap.js

    Vous avez peut-être remarqué que je n’avez pas défini de CdnFallbackExpression pour la `~/Cointent/css` offre groupée. C’est parce que, actuellement, il existe un [bogue dans System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) qui injecte un `<script>` balises de la CSS de secours au lieu du texte attendu `<link>` balises.

    Toutefois, il existe un bon [Style offre groupée secours](https://github.com/EmberConsultingGroup/StyleBundleFallback) offerts par [Les Consulting Group](https://github.com/EmberConsultingGroup).

2. Pour utiliser la solution de contournement pour CSS, créer un nouveau fichier .cs dans le dossier *App_Start* de votre projet rôle Web appelé *StyleBundleExtensions.cs*et de remplacer son contenu par le [code à partir de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Dans *App_Start\StyleFundleExtensions.cs*, renommez l’espace de noms pour le nom de votre rôle de serveur Web (par exemple, **WebRole1**).

3. Revenez à `App_Start\BundleConfig.cs` et de modifier la dernière `bundles.Add` instruction par le code en surbrillance suivant :  

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));

    Cette nouvelle méthode d’extension utilise la même idée pour injecter un script dans le code HTML pour vérifier le DOM pour le nom de classe correspondant, nom de la règle, de valeur définie dans l’offre groupée CSS et se situe sur le serveur Web d’origine en cas d’échec trouver la correspondance de la règle.

4. Publiez de nouveau le service en nuage et accéder à la page d’accueil.

5. Permet d’afficher le code HTML de la page. Vous devriez trouver des scripts injectées semblable à la suivante :    

        ...

        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>

        ...

            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>

        ...


    Notez que script injecté pour l’offre groupée CSS contient toujours le remnant errant de la `CdnFallbackExpression` propriété dans la ligne :

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mais depuis la première partie de la || expression retournera toujours la valeur true (dans la ligne directement au-dessus de), la fonction document.Write () ne s’exécutera jamais.

## <a name="more-information"></a>Plus d’informations ##
- [Vue d’ensemble du réseau Azure de diffusion de contenu (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [À l’aide d’Azure CDN](cdn-create-new-endpoint.md)
- [ASP.NET regroupement et minimisation](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
