<properties
    pageTitle="Nouveautés dans les images du modèle Azure RemoteApp ? | Microsoft Azure"
    description="Obtenir des informations sur les images de modèle inclus dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Nouveautés dans les images du modèle Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lecture de l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus de détails.

Votre abonnement Azure RemoteApp comprend trois images du modèle :


- Windows Server 2012
- Microsoft Office 365 ProPlus (abonnement à Office 365 requis)
- Microsoft Office 2013 Professionnel Plus (version d’évaluation uniquement)

> [AZURE.IMPORTANT]Votre abonnement Azure RemoteApp vous accorde le qu'accès au logiciel dans les images, à l’exception d’Office 365 ProPlus, qui requiert un abonnement distinct et Office 2013, qui ne peut pas être utilisée en production. Cela signifie que vous pouvez partager des programmes ou des applications sur les images du modèle avec les utilisateurs. Par exemple, si vous créez une collection qui utilise l’image de Windows Server 2012 R2, vous pouvez publier System Center Endpoint Protection accessible par le biais de RemoteApp aux utilisateurs.
>
> Vérifiez les [informations de licence de RemoteApp](remoteapp-licensing.md) pour plus d’informations. Et les informations de licence [à l’aide d’Office avec Azure RemoteApp](remoteapp-o365.md) pour le bureau.

Lisez la suite pour plus d’informations sur le contenu de chaque image.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 (« l’image vanille »)
Cette image est basée sur le système d’exploitation de Microsoft Windows Server 2012 R2 Datacenter et a les rôles et les fonctionnalités installées pour satisfaire les besoins pour les images du modèle Azure RemoteApp suivantes :


- .NET Framework 4.5, 3.5.1, 3,5
- Expérience du bureau
- Services d’encre et l’écriture manuscrite
- Media Foundation
- Hôte de Session Bureau à distance
- Windows PowerShell 4.0
- Windows PowerShell ISE
- La prise en charge WoW64

Cette image comporte également les applications suivantes :

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Lecteur Windows Media


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (inscription requise)
Office 365 est l’application la plus demandée, nous avons créé une image « personnalisée » pour travailler avec.

Cette image est une extension de l’image de vanille et comprend les éléments suivants de Microsoft Office 365 ProPlus installés ainsi que les composants décrits dans l’image de Windows Server 2012 R2 :


- Accès
- Excel
- Lync
- OneNote
- OneDrive pour les entreprises (Notez que l’agent de synchronisation n’est pas pris en charge pour une utilisation avec Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Outils de vérification linguistique de Microsoft Office

L’image inclut également Pro de Visio et Project Pro.

Et les applications suivantes, ainsi que :

- SQL Native client
- Pilote ODBC
- Client SQL Server Data Mining
- MasterDataServices client
- Microsoft Publisher
- PowerQuery
- PowerMap


Toutes les fonctionnalités des applications Office 365 ProPlus sont disponible uniquement pour les utilisateurs qui disposent d’un plan d’Office 365 ProPlus. Pour plus d’informations sur l’Office 365, programmes d’abonnement voir [plans de service d’Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Avez encore des questions ? Vérifiez les informations de [Office 365 + RemoteApp](remoteapp-o365.md) . Consultez également l’article nouveau, [l’utilisation de votre abonnement à Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md).

Notez que vous devez acquérir la licence Office 365 ProPlus Pro de Visio et Project Pro séparément, ils ont chacun leur propre licence.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professionnel Plus (version d’évaluation uniquement)
Au cours de la période d’essai gratuite, vous pouvez tester le service avec l’image Office 2013.

Cette image est une extension de l’image de vanille et comprend les éléments suivants de Microsoft Office 2013 Professionnel Plus installés ainsi que les composants décrits dans l’image de Windows Server 2012 R2 :


- Accès
- Excel
- Lync
- OneNote
- OneDrive pour les entreprises (Notez que l’agent de synchronisation n’est pas pris en charge pour une utilisation avec Azure RemoteApp)
- Outlook
- PowerPoint
- Projet
- Visio
- Word
- Outils de vérification linguistique de Microsoft Office

> [AZURE.IMPORTANT]**Informations légales :** Cette image n’inclut pas une licence de Microsoft Office et *ne peut pas être utilisé pour la production*. L’image est destinée uniquement à des fins d’évaluation Office 2013 Professionnel Plus. Si vous souhaitez utiliser des applications Office dans Azure RemoteApp pour la production, vous devez utiliser l’image d’Office 365 ProPlus. Pour plus d’informations sur la licence d’Office, reportez-vous [à l’aide d’Office 365 avec Azure RemoteApp](remoteapp-o365.md)
