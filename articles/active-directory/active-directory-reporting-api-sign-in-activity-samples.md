<properties
    pageTitle="Exemples de rapport API d’activité de connexion Active Directory Azure | Microsoft Azure"
    description="La mise en route avec l’API de création de rapports de répertoire Azure Active"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Exemples de rapport API d’activité de connexion Active Directory Azure

Cette rubrique fait partie d’un ensemble de rubriques sur Azure Active Directory API de création de rapports.  
Azure reporting d’Active Directory vous fournit une API qui vous permet d’accéder aux données d’activité de connexion à l’aide de code ou les outils associés.  
La portée de cette rubrique est de vous fournir des exemples de code pour l' **activité de connexion API**.

Voir :

- [Les journaux d’audit](active-directory-reporting-azure-portal.md#audit-logs) pour plus d’informations
- Pour plus d’informations sur l’API de création de rapports, [mise en route avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md) .

Vous avez des questions, des problèmes ou des commentaires, veuillez contacter [DAS Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="prerequisites"></a>Conditions préalables
Avant de pouvoir utiliser les exemples de cette rubrique, vous devez suivre les [conditions requises pour accéder à l’annonce Azure reporting API](active-directory-reporting-api-prerequisites.md).  


##<a name="powershell-script"></a>Script PowerShell

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>L’exécution du script
Une fois que vous terminez de modifier le script, l’exécuter et de Vérifiez que les données attendues de l’Audit enregistre le rapport est renvoyé.

Le script renvoie la sortie de l’état de connexion au format JSON. Il crée également un `SigninActivities.json` le fichier avec le même résultat. Vous pouvez expérimenter en modifiant le script pour renvoyer des données à partir d’autres rapports et commentez les formats de sortie que vous n’avez pas besoin.



## <a name="next-steps"></a>Étapes suivantes

- Voulez-vous personnaliser les exemples de cette rubrique ? Extraire l' [activité ouverture de session Active Directory de Azure référence sur l’API](active-directory-reporting-api-sign-in-activity-reference.md). 

- Si vous souhaitez voir un aperçu complet de l’utilisation d’Azure Active Directory API de création de rapports, reportez-vous à la section [mise en route avec Active Directory Azure reporting API](active-directory-reporting-api-getting-started.md).

- Si vous souhaitez en savoir plus sur les rapports d’Azure Active Directory, consultez le [Guide Azure Active Directory Reporting](active-directory-reporting-guide.md).  