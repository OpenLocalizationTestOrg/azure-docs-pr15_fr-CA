<properties
   pageTitle="Prise en charge de SSMS pour AMF de publicité Azure avec SQL de base de données et SQL Data Warehouse | Microsoft Azure"
   description="Utilisez Multi-prises en charge de l’authentification avec SSMS pour la base de données SQL et l’entrepôt de données SQL."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Prise en charge SSMS pour AMF de publicité Azure avec SQL de base de données et SQL Data Warehouse

Base de données de SQL Azure et de magasin de données SQL Azure prennent désormais en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *L’authentification Active Directory universel*. L’authentification Active Directory universel est un flux de travail interactive qui prend en charge de l' *Authentification à plusieurs facteurs Azure* (AMF). Azure AMF contribue à garantir l’accès aux données et aux applications tout en répondant à la demande de l’utilisateur pour un processus de connexion simple. Il fournit une authentification renforcée grâce à une gamme d’options de vérification facile, appel téléphonique, message texte, cartes à puce avec un code pin, ou la notification de l’application mobile, permettant aux utilisateurs de choisir la méthode qu’ils préfèrent. Pour une description de plusieurs facteurs d’authentification, consultez [Authentification à plusieurs facteurs](../multi-factor-authentication/multi-factor-authentication.md).

SSMS prend désormais en charge :

- AMF interactive avec AD Azure avec le potentiel pour la validation de boîte de dialogue contextuelle.
- Non-mot de passe Active Directory et l’authentification intégrée à Active Directory des méthodes interactives qui peuvent être utilisés dans de nombreuses applications différentes (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes ne résultent jamais dans les boîtes de dialogue qui s’affiche.

Lorsque le compte d’utilisateur est configuré pour AMF du flux de travail d’authentification interactive nécessite une intervention utilisateur supplémentaires par l’intermédiaire de boîtes de dialogue indépendantes, utilisation de la carte à puce, etc.. Lorsque le compte d’utilisateur est configuré de l’AMF, l’utilisateur doit sélectionner authentification universel d’Azure pour vous connecter. Si le compte d’utilisateur ne requiert pas l’AMF, l’utilisateur peut toujours utiliser les deux autres options Azure l’authentification Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universels de la base de données SQL et SQL Data Warehouse

- SSMS est le seul outil actuellement activé pour AMF par le biais de l’authentification Active Directory universel.
- Un seul compte Azure Active Directory peut se connecter pour une instance de SSMS à l’aide de l’authentification universel. Pour vous connecter sous un autre compte AD Azure, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universels Active Directory, vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, authentification intégrée à Active Directory ou l’authentification SQL Server).
- SSMS prend en charge l’authentification Active Directory universel pour la visualisation de l’Explorateur d’objets, d’éditeur de requêtes et de banque de la requête.
- Ni DacFx, ni le Concepteur de schéma prend en charge l’authentification universel.
- Les comptes MSA ne sont pas pris en charge pour l’authentification Active Directory universel.
- L’authentification Active Directory universel n’est pas pris en charge dans SSMS pour les utilisateurs qui sont importés dans Active Directory à partir d’autres annuaires Active Directory Azure en cours. Ces utilisateurs ne sont pas pris en charge, car il requiert un ID de client valider les comptes, et il n’y a pas de mécanisme de prévoir que.
- Il n’existe aucune configuration logicielle supplémentaire requise pour l’authentification Active Directory universel à ceci près que vous devez utiliser une version prise en charge de SSMS.

## <a name="configuration-steps"></a>Étapes de configuration

Mise en œuvre d’une authentification multifactorielle nécessite quatre étapes de base.

1. **Configurer Active Directory Azure** – pour plus d’informations, voir [intégration des identités avec Azure Active Directory sur site](../active-directory/active-directory-aadconnect.md), [Ajouter votre propre nom de domaine pour Active Directory Azure](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure maintenant prend en charge la fédération avec Active Directory de Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administration de votre annuaire AD Azure](https://msdn.microsoft.com/library/azure/hh967611.aspx)et [Gérer Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurer l’AMF** – pour obtenir des instructions détaillées, reportez-vous à la section [Configuration de l’authentification multifactorielle Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Configurer la base de données SQL ou SQL Data Warehouse pour l’authentification AD Azure** – pour obtenir des instructions pas à pas, consultez [connexion de base de données SQL ou SQL données entrepôt par à l’aide d’Azure l’authentification Active Directory](sql-database-aad-authentication.md).

4. **Téléchargement de SSMS** – sur l’ordinateur client, téléchargez le dernier SSMS (au moins août 2016), à partir de [SQL Server Management Studio télécharger (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connexion à l’aide de l’authentification universel avec SSMS

Les étapes suivantes montrent comment se connecter à la base de données de SQL ou SQL Data Warehouse à l’aide la plus récente SSMS.

1. Pour vous connecter à l’aide d’une authentification universelle, dans la boîte de dialogue **se connecter au serveur** , sélectionnez **L’authentification Active Directory universel**.
![se connecter 1mfa-universel][1]

2. Comme d’habitude pour la base de données SQL et SQL Data Warehouse, vous devez cliquez sur les **Options** et spécifier la base de données dans la boîte de dialogue **Options** . Puis cliquez sur **se connecter**.
3. Lorsque la boîte de dialogue **se connecter à votre compte** s’affiche, fournir le compte et le mot de passe de votre identité Azure Active Directory.
![2mfa-connexion à][2]

    > [AZURE.NOTE] Pour une authentification universelle avec un compte qui ne requiert pas de l’AMF, vous vous connectez à ce stade. Pour les utilisateurs exigeant AMF, passez aux étapes suivantes.
 
4. Deux boîtes de dialogue d’installation AMF peuvent apparaître. Cette une fois opération dépend de l’administrateur de l’AMF définition et par conséquent peut être facultative. Pour un domaine AMF activé cette étape est parfois prédéfinie (par exemple, le domaine, aux utilisateurs d’utiliser une carte à puce et un code confidentiel).  
![3mfa-le programme d’installation][3]

5. Le deuxième possible une fois la boîte de dialogue vous permet de sélectionner les détails de votre méthode d’authentification. Les options possibles sont configurées par votre administrateur.
![-Vérifiez les 4mfa-1][4]
 
6. Azure Active Directory vous envoie les informations de confirmation. Lorsque vous recevez le code de vérification, entrez-le dans la zone **code de vérification d’entrée** , puis cliquez sur **se connecter**.
![-Vérifiez les 5mfa-2][5]

Lorsque la vérification est terminée, SSMS connecte normalement en supposant les informations d’identification valides et accès au pare-feu.

##<a name="next-steps"></a>Étapes suivantes  

D’autres accorder l’accès à votre base de données : [SQL, base de données d’authentification et d’autorisation : autoriser l’accès au](sql-database-manage-logins.md)  
Assurez-vous que d’autres utilisateurs peuvent se connecter via le pare-feu : [configurer une règle de pare-feu au niveau du serveur de base de données de SQL Azure via le portail Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

