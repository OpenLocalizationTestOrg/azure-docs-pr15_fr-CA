<properties
   pageTitle="Sécurité de niveau ligne avec alimentation BI incorporé"
   description="Pour plus d’informations sur la sécurité de niveau ligne avec alimentation BI incorporé"
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

# <a name="row-level-security-with-power-bi-embedded"></a>Sécurité de niveau ligne avec alimentation BI incorporé

Sécurité de niveau ligne (RLS) peut servir à restreindre l’accès à des données particulières dans un état ou d’un groupe de données, permettant à plusieurs utilisateurs différents à utiliser le même rapport lors de toutes les données de différents voir. Alimentation BI incorporé prend désormais en charge les groupes de données configurés avec RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Pour tirer parti de RLS, il est important de que comprendre les trois principaux concepts ; Utilisateurs, rôles et règles. Examinons plus en détail chaque :

**Les utilisateurs** , ce sont les utilisateurs finaux réels affichage de rapports. Dans incorporées de BI d’alimentation, les utilisateurs sont identifiés par la propriété de nom d’utilisateur dans un jeton d’App.

**Rôles** – les utilisateurs appartiennent à des rôles. Un rôle est un conteneur pour les règles et peut être nommé « Directeur commercial » ou « Commercial ». Dans incorporées de BI d’alimentation, les utilisateurs sont identifiés par la propriété de rôles dans un jeton d’App.

**Règles** : rôles des règles, et ces règles sont les filtres réels qui vont être appliqués aux données. Cela peut être aussi simple que « pays = France » ou quelque chose de plus dynamique.

### <a name="example"></a>Exemple

Pour le reste de cet article, nous vous fournirons un exemple de création RLS et qui consomme puis au sein d’une application embedded. Notre exemple utilise le fichier PBIX [d’Échantillon d’analyse commerciale](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Notre exemple d’analyse de vente au détail affiche les ventes pour toutes les banques dans une chaîne de distribution particulier. Sans RLS, quel que soit le district manager se connecte et affiche le rapport, ils verront les mêmes données. Direction a déterminé chaque responsable de district doit s’afficher que les ventes pour les banques qu’ils gèrent, et pour ce faire, nous pouvons utiliser RLS.

RLS a été créé dans le bureau de BI d’alimentation. Lorsque le rapport et le groupe de données sont ouverts, nous pouvons passer à un affichage des tâches pour voir le schéma :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Voici quelques éléments à noter avec ce schéma :

-   Toutes les mesures, comme **Total des ventes**, sont stockées dans la table de faits **Sales** .
-   Il existe quatre tables de dimension connexes supplémentaires : **article**, **magasin**et **heure** **District**.
-   Les flèches sur les lignes de relation indiquent de quelle façon les filtres peuvent être transmis à partir d’une table à l’autre. Par exemple, si un filtre est placé sur le **temps [Date]**, dans le schéma actuel il serait filtrer uniquement les valeurs de la table **Sales** . Aucuns autres tables ne seraient affectés par ce filtre puisque toutes les flèches sur les lignes de relation de point pour la table sales et pas immédiatement.
-   La table **District** indique qui est le responsable pour chaque secteur :

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Selon ce schéma, si nous appliquer un filtre à la colonne **Responsable de District** de la table de District, et si ce filtre correspond à l’utilisateur d’affichage du rapport, ce filtre filtre également vers le bas les tables **Store** et **Sales** pour afficher uniquement les données pour ce secteur particulier manager.

Voici comment :

1.  Dans l’onglet de la modélisation, cliquez sur **Gérer les rôles**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Créer un rôle appelé **Gestionnaire**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  Dans la table de **District** , entrez l’expression suivante de DAX : **[responsable de District] = USERNAME()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Pour vous assurer que les règles fonctionnent, sur l’onglet **de modélisation** , cliquez sur **Afficher en tant que rôles**et puis entrez les informations suivantes :  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Les États affichent maintenant des données comme si vous avez connecté en tant **qu’Andrew Ma**.

Appliquer le filtre, filtre la façon que nous l’avons fait ici, vers le bas tous les enregistrements dans les tables de **District**, **banque**et **ventes** . Toutefois, en raison de la direction de filtre sur les relations entre les **ventes** et **l’heure**, les tables **Sales** et **article**et **élément** et **l’heure** ne seront pas filtrés vers le bas.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Qui peut être OK pour cette condition, cependant, si nous ne voulons pas aux gestionnaires d’éléments pour lesquels ils ne disposent d’aucune vente, nous pouvons activer bidirectionnelle entre pour filtrer la relation et le filtre de sécurité dans les deux directions de flux. Pour ce faire, vous pouvez modifier la relation entre les **ventes** et d' **élément**, comme suit :

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Désormais, les filtres peuvent également se dérouler à partir de la table Sales à la table des **articles** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Remarque** Si vous utilisez le mode DirectQuery pour vos données, vous devez activer le filtrage de cross bidirectionnelle en activant ces deux options :

1.  **Fichier** -> **les Options et paramètres** -> **Fonctionnalités** -> **Activer entre le filtrage dans les deux directions pour DirectQuery**.
2.  **Fichier** -> **les Options et paramètres** -> **DirectQuery** -> **permettent une mesure sans restriction en mode de DirectQuery**.


Pour en savoir plus sur le filtrage de cross bidirectionnel, téléchargez le livre blanc de [bidirectionnelle entre filtrage dans SQL Server Analysis Services 2016 et alimentation BI bureau](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Tout le travail qui doit être effectué dans le bureau de BI d’alimentation se, mais il y a une plus petite tâche qui doit être fait pour rendre la RLS règles que nous avons défini le travail de puissance BI Embedded. Les utilisateurs sont authentifiés et autorisés par votre application et les jetons de l’application sont utilisés pour accorder l’accès à un rapport incorporées de BI d’alimentation spécifique. Incorporées de BI d’alimentation ne dispose pas d’informations spécifiques sur les utilisateurs auxquels l’utilisateur est. RLS à travailler, vous devez passer un contexte supplémentaire dans le cadre de votre jeton d’application :
-   **nom d’utilisateur** (facultatif) – utilisée avec RLS il s’agit d’une chaîne qui peut être utilisée pour aider à identifier l’utilisateur lors de l’application des règles RLS. Reportez-vous à la section à l’aide de la ligne niveau sécurité avec BI de puissance incorporée
-   **rôles** : une chaîne contenant les rôles à sélectionner lors de l’application des règles de sécurité de niveau ligne. Si vous passez plusieurs rôles, il doivent être passés sous la forme d’un tableau de chaînes.

Si la propriété username est présente, vous devez aussi passer au moins une valeur dans les rôles.

Le jeton de toute l’application ressemblera à ceci :

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Maintenant, avec tous les éléments ensemble, lorsqu’un utilisateur se connecte à notre application pour afficher cet état, ils seulement pourront voir les données qu’ils sont autorisés à afficher, tel que défini par notre sécurité de niveau ligne.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Voir aussi
[Sécurité de niveau ligne (RLS) avec alimentation](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
