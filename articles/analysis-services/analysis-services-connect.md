<properties
   pageTitle="Obtenir des données d’Azure Analysis Services | Microsoft Azure"
   description="Apprenez à vous connecter à et obtenir des données à partir d’un serveur Analysis Services dans Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="get-data-from-azure-analysis-services"></a>Obtenir des données d’Azure Analysis Services
Une fois que vous avez créé un serveur dans Azure et déployé un modèle sous forme de tableau, les utilisateurs de votre organisation sont prêts à vous connecter et commencer l’exploration de données.

Azure Analysis Services prend en charge les connexions clientes à l’aide de [mise à jour des modèles d’objet](#client-libraries); TOM, AMO, Adomd.Net ou MSOLAP, pour vous connecter via xmla sur le serveur. Par exemple, BI de puissance, puissance BI bureau, Excel ou n’importe quelle application client tiers prenant en charge les modèles d’objet.

## <a name="server-name"></a>Nom du serveur
Lorsque vous créez un serveur Analysis Services dans Azure, vous spécifiez un nom unique et la région où le serveur doit être créé. Lorsque vous spécifiez le nom du serveur dans une connexion, le schéma d’affectation de noms de serveur est :
```
<protocol>://<region>/<servername>
```
 Dans le cas où le protocole est la chaîne **asazure**, la région est l’Uri de la région où le serveur a été créé (par exemple, pour les États-Unis Ouest, westus.asazure.windows.net) et nom_serveur est le nom de votre serveur unique au sein de la région.

## <a name="get-the-server-name"></a>Obtenir le nom du serveur
Avant de vous connecter, vous devez obtenir le nom du serveur. Dans **Azure portal** > serveur > **vue d’ensemble** > **nom de serveur**, copiez le nom de l’ensemble du serveur. Si d’autres utilisateurs de votre organisation sont connectent à ce serveur trop, vous voudrez partager ce nom de serveur avec eux. Lorsque vous spécifiez un nom de serveur, le chemin d’accès complet doit être utilisé.

![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connect-in-power-bi-desktop"></a>Se connecter dans le bureau de BI de puissance

> [AZURE.NOTE] Cette fonctionnalité est aperçu.

1. Dans le [Bureau de BI d’alimentation](https://powerbi.microsoft.com/desktop/), cliquez sur **Obtenir les données** > **bases de données** > **Azure Analysis Services**.

2. Dans **serveur**, collez le nom du serveur à partir du Presse-papiers.

3. Dans la **base de données**, si vous connaissez le nom de la base de données du modèle tabulaire ou vous souhaitez vous connecter à un point de vue, collez-la ici. Dans le cas contraire, vous pouvez laisser ce champ vide. Vous pouvez sélectionner une base de données ou d’un point de vue sur l’écran suivant.

4. Laissez l’option par défaut de **connexion live** sélectionné, puis appuyez sur **se connecter**. Si vous êtes invité à entrer un compte, entrez le compte de votre organisation.

5. Dans **Navigator**, développez le serveur, puis sélectionnez le modèle ou la vue que vous souhaitez vous connecter, puis cliquez sur **se connecter**. Un simple clic sur un modèle ou un point de vue affiche tous les objets de cette vue.


## <a name="connect-in-power-bi"></a>Connexion d’alimentation BI
1. Créer un fichier de puissance BI bureau qui dispose d’une connexion active à votre modèle sur votre serveur.

2. [Alimentation BI](https://powerbi.microsoft.com), cliquez sur **Obtenir les données** > **fichiers**. Recherchez et sélectionnez votre fichier.


## <a name="connect-in-excel"></a>Se connecter dans Excel
Connexion au serveur Azure Analysis Services dans Excel est pris en charge à l’aide d’obtenir les données dans Excel 2016 ou une requête d’alimentation dans les versions antérieures. [Fournisseur MSOLAP.7](https://aka.ms/msolap) est requis. Connexion à l’aide de l’Assistant Importation de la Table dans le tableau croisé dynamique de l’alimentation n’est pas pris en charge.

1. Dans Excel, 2016, sur le ruban de **données** , cliquez sur **Données externes** > **à partir d’autres Sources de** > **à partir d’Analysis Services**.

2. Dans l’Assistant connexion de données, dans la zone **nom du serveur**, collez le nom du serveur à partir du Presse-papiers. Puis, d' **informations d’identification d’ouverture de session**, sélectionnez **utiliser le nom d’utilisateur et le mot de passe suivants**et puis tapez le nom d’utilisateur d’organisation, par exemple nancy@adventureworks.com, et le mot de passe.

    ![Se connecter dans l’ouverture de session Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)

4. Dans, **Sélectionnez la base de données et de Table**, sélectionnez la base de données et un modèle ou un point de vue, puis cliquez sur **Terminer**.

    ![Se connecter dans Sélectionnez un modèle Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Chaîne de connexion
Lors de la connexion à l’aide du modèle d’objet sous forme de tableau de Azure Analysis Services, utilisez les formats de chaîne de connexion suivante :

###### <a name="integrated-azure-active-directory-authentication"></a>Authentification intégrée de Azure Active Directory
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
L’authentification intégrée à chercher le cache d’informations d’identification Azure Active Directory s’il est disponible. Si ce n’est pas le cas, la fenêtre de connexion Azure s’affiche.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Authentification Active Directory Azure avec nom d’utilisateur et le mot de passe
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Bibliothèques clientes
Lors de la connexion à partir d’Excel ou d’autres interfaces telles que TOM, AsCmd, ADOMD.NET à Azure Analysis Services, vous devrez peut-être installer les dernières bibliothèques de client fournisseur. Obtenir la dernière version :  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>



## <a name="next-steps"></a>Étapes suivantes
[Gérer votre serveur](analysis-services-manage.md)
