<properties
   pageTitle="Mise en route avec un échantillon"
   description="Puissance incorporées de BI, SDK permet d’ajouter des rapports interactifs de puissance BI dans votre application décisionnelle"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-power-bi-embedded-sample"></a>Mise en route avec alimentation BI incorporé, exemple

Avec **Microsoft Power BI incorporé**, vous pouvez intégrer des rapports BI d’alimentation directement dans vos applications web ou mobiles. Dans cet article, nous vous présenterons l’exemple démarré get **Incorporées de BI d’alimentation** .

Avant de poursuivre, vous souhaiterez sans doute enregistrer les ressources suivantes. Elle vous aide à vous lors de l’intégration des rapports BI de puissance trop dans l’exemple d’application et de vos applications.

 -  [Exemple d’application web du tableau de bord](http://go.microsoft.com/fwlink/?LinkId=761493)
 -  [Référence de l’API incorporées de BI d’alimentation](https://msdn.microsoft.com/library/mt711493.aspx)
 -  [Alimentation BI incorporé .NET SDK](http://go.microsoft.com/fwlink/?LinkId=746472) (disponible via NuGet)



> [AZURE.NOTE] Avant que vous pouvez configurer et exécuter l’exemple démarré get incorporées de BI d’alimentation, vous devez créer au moins une **Collection de l’espace de travail** dans votre abonnement Azure. Pour savoir comment créer une **Collection de l’espace de travail** dans le portail Azure consultez [Mise en route avec alimentation BI incorporé](power-bi-embedded-get-started.md).

## <a name="configure-the-sample-app"></a>Configuration de l’exemple d’application

Nous allons étudier la configuration de votre environnement de développement Visual Studio pour accéder aux composants nécessaires à l’exécution de l’exemple d’application.

1. Téléchargez et décompressez l’échantillon [Power BI Embedded - intégrer un rapport dans une application web](http://go.microsoft.com/fwlink/?LinkId=761493) sur GitHub.

2. Dans Visual Studio, ouvrez **PowerBI-embedded.sln** . Vous devrez peut-être exécuter la commande de **Mise à jour de Package** dans la Console du Gestionnaire de package NuGET afin de mettre à jour les modules utilisés dans cette solution.

3. Générez la solution.

4. Exécutez l’application console de **ProvisionSample** . Dans l’exemple d’application console, vous fournir un espace de travail et importez d’un fichier PBIX.

5. Pour mettre en service un nouvel **espace de travail**, sélectionnez l’option 5, **fourniture d’un espace de travail dans une collection d’espace de travail existant**.

    ![](media\powerbi-embedded-get-started-sample\console-option-5.png)

6. Entrez votre nom de la **Collection de l’espace de travail** et la **Touche d’accès rapide**. Vous pouvez obtenir ces éléments dans le **Portail Azure**. Pour en savoir plus sur l’obtention de la **Touche d’accès rapide**, voir [Touches d’accès rapide vue d’alimentation BI API](power-bi-embedded-get-started-sample.md#view-access-keys) dans mise en route de Microsoft d’alimentation BI incorporé.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

7. Copier et enregistrer **l’ID de l’espace de travail** nouvellement créé à utiliser plus loin dans cet article. Après la création de l' **ID de l’espace de travail** , vous trouverez le **Portail Azure**.

    ![](media\powerbi-embedded-get-started-sample\workspace-id.png)

8. Pour importer un fichier PBIX dans votre **espace de travail**, sélectionnez l’option **6. Importer un fichier dans un espace de travail de bureau de PBIX**. Si vous n’avez pas un fichier PBIX pratique, vous pouvez télécharger le [Retail analyse exemple PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547).

9. Si vous y êtes invité, entrez un nom convivial pour votre **groupe de données**.

Vous devez voir une réponse comme :

````
Checking import state... Publishing
Checking import state... Succeeded
```

> [AZURE.NOTE] If your PBIX file contains any direct query connections, run option 7 to update the connection strings.

At this point, you have a Power BI PBIX report imported into your **Workspace**. Now, let's look at how to run the **Power BI Embedded** get started sample web app.

## Run the sample web app

The web app sample is a sample dashboard that renders reports imported into your **Workspace**. Here's how to configure the web app sample.

1. In the **PowerBI-embedded** Visual Studio solution, right click the **EmbedSample** web application, and choose **Set as StartUp project**.
2. In **web.config**, in the **EmbedSample** web application, edit the **appSettings**: **AccessKey**, **WorkspaceCollection** name, and **WorkspaceId**.

    ```
    <appSettings>
        <add key="powerbi:AccessKey" value="" />
        <add key="powerbi:ApiUrl" value="https://api.powerbi.com" />
        <add key="powerbi:WorkspaceCollection" value="" />
        <add key="powerbi:WorkspaceId" value="" />
    </appSettings>
    ```
3. Run the **EmbedSample** web application.

Once you run the **EmbedSample** web application, the left navigation panel should contain a **Reports** menu. To view the report you imported, expand **Reports**, and click a report. If you imported the [Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547), the sample web app would look like this:

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-sample-left-nav.png)

After you click a report, the **EmbedSample** web application should look something this:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)


## Explore the sample code
The **Microsoft Power BI Embedded** sample is an example dashboard web app that shows you how to integrate **Power BI** reports into your app. It uses a Model-View-Controller (MVC) design pattern to demonstrate best practices. This section highlights parts of the sample code that you can explore within the **PowerBI-embedded** web app solution. The Model-View-Controller (MVC) pattern separates the modeling of the domain, the presentation, and the actions based on user input into three separate classes: Model, View, and Control. To learn more about MVC, see [Learn About ASP.NET](http://www.asp.net/mvc).

The **Microsoft Power BI Embedded** sample code is separated as follows. Each section includes the file name in the PowerBI-embedded.sln solution so that you can easily find the code in the sample.

> [AZURE.NOTE] This section is a summary of the sample code that shows how the code was written. To view the complete sample, please load the PowerBI-embedded.sln solution in Visual Studio.

### Model
The sample has a **ReportsViewModel** and **ReportViewModel**.

**ReportsViewModel.cs**: Represents Power BI Reports.

    public class ReportsViewModel
    {
        public List<Report> Reports { get; set; }
    }

**ReportViewModel.cs**: Represents a Power BI Report.

    public classReportViewModel
    {
        public IReport Report { get; set; }

        public string AccessToken { get; set; }
    }

### Connection string
The connection string must be in the following format:

```
Data Source=tcp:MyServer.database.windows.net,1433;Initial Catalog=MyDatabase
```

Using common server and database attributes will fail. For example: Server=tcp:MyServer.database.windows.net,1433;Database=MyDatabase,

### View
The **View** manages the display of Power BI **Reports** and a Power BI **Report**.

**Reports.cshtml**: Iterate over **Model.Reports** to create an **ActionLink**. The **ActionLink** is composed as follows:

|Part|Description
|---|---
|Title| Name of the Report.
|QueryString| A link to the Report ID.

    <div id="reports-nav" class="panel-collapse collapse">
        <div class="panel-body">
            <ul class="nav navbar-nav">
                @foreach (var report in Model.Reports)
                {
                    var reportClass = Request.QueryString["reportId"] == report.Id ? "active" : "";
                    <li class="@reportClass">
                        @Html.ActionLink(report.Name, "Report", new { reportId = report.Id })
                    </li>
                }
            </ul>
        </div>
    </div>

Report.cshtml: Set the **Model.AccessToken**, and the Lambda expression for **PowerBIReportFor**.

    @model ReportViewModel

    ...

    <div class="side-body padding-top">
        @Html.PowerBIAccessToken(Model.AccessToken)
        @Html.PowerBIReportFor(m => m.Report, new { style = "height:85vh" })
    </div>

### Controller

**DashboardController.cs**: Creates a PowerBIClient passing an **app token**. A JSON Web Token (JWT) is generated from the **Signing Key** to get the **Credentials**. The **Credentials** are used to create an instance of **PowerBIClient**. Once you have an instance of **PowerBIClient**, you can call GetReports() and GetReportsAsync().

CreatePowerBIClient()

    private IPowerBIClient CreatePowerBIClient()
    {
        var credentials = new TokenCredentials(accessKey, "AppKey");
        var client = new PowerBIClient(credentials)
        {
            BaseUri = new Uri(apiUrl)
        };

        return client;
    }

ActionResult Reports()

    public ActionResult Reports()
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = client.Reports.GetReports(this.workspaceCollection, this.workspaceId);

            var viewModel = new ReportsViewModel
            {
                Reports = reportsResponse.Value.ToList()
            };

            return PartialView(viewModel);
        }
    }


Task<ActionResult> Report(string reportId)

    public async Task<ActionResult> Report(string reportId)
    {
        using (var client = this.CreatePowerBIClient())
        {
            var reportsResponse = await client.Reports.GetReportsAsync(this.workspaceCollection, this.workspaceId);
            var report = reportsResponse.Value.FirstOrDefault(r => r.Id == reportId);
            var embedToken = PowerBIToken.CreateReportEmbedToken(this.workspaceCollection, this.workspaceId, report.Id);

            var viewModel = new ReportViewModel
            {
                Report = report,
                AccessToken = embedToken.Generate(this.accessKey)
            };

            return View(viewModel);
        }
    }

### Integrate a report into your app

Once you have a **Report**, you use an **IFrame** to embed the Power BI **Report**. Here is a code snippet from  powerbi.js in the **Microsoft Power BI Embedded** sample.

![](media\powerbi-embedded-get-started-sample\power-bi-embedded-iframe-code.png)


## Filter reports embedded in your application

You can filter an embedded report using a URL syntax. To do this, you add a **$filter** query string parameter with an **eq** operator to your iFrame src url with the filter specified. Here is the filter query syntax:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-...-9673-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

> [AZURE.NOTE] {tableName/fieldName} cannot include spaces or special characters. The {fieldValue} accepts a single categorical value.  


## See also

- [Common Microsoft Power BI Embedded scenarios](power-bi-embedded-scenarios.md)
- [Authenticating and authorizing in Power BI Embedded](power-bi-embedded-app-token-flow.md)
