<properties
    pageTitle="L’utilisation de la bibliothèque de Client Mobile Apps Android"
    description="Comment utiliser le client Android SDK pour les applications mobiles Azure."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>


# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>L’utilisation de la bibliothèque cliente Android pour les applications mobiles

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Ce guide vous montre comment utiliser le client Android SDK pour applications Mobile pour mettre en œuvre des scénarios courants, tels que :

- Interrogation de données (insertion, mise à jour et suppression).
- Authentification.
- Gestion des erreurs.
- Personnalisation du client. 

Il effectue également un approfondi dans le code client commun utilisé dans des applications plus mobiles.

Ce guide se concentre sur le Kit de développement logiciel Android côté client.  Pour plus d’informations sur les kits de développement côté serveur pour les applications mobiles, voir [travail avec le serveur principal .NET SDK] [ 10] ou [l’utilisation du back-end Node.js SDK][11].

## <a name="reference-documentation"></a>Documentation de référence

Vous pouvez trouver la [référence de l’API de Javadocs] [ 12] de la bibliothèque client Android sur GitHub.

## <a name="supported-platforms"></a>Plates-formes prises en charge

Le SDK de Android applications Azure Mobile prend en charge les niveaux d’API 19 à 24 (KitKat via la commande).  

L’authentification de « serveur-flux » utilise un mode d’affichage Web pour l’interface utilisateur présentée. Si le périphérique n’est pas en mesure de présenter un UI WebView, autres méthodes d’authentification sont nécessaires en dehors de la portée du produit.  Ce SDK n’est pas approprié pour le type d’espion ou de la même façon sur des périphériques restreints.

## <a name="setup-and-prerequisites"></a>Le programme d’installation et conditions requises

Utiliser le didacticiel de [démarrage rapide d’applications Mobile](app-service-mobile-android-get-started.md) .  Cette tâche garantit que toutes les conditions préalables pour le développement d’applications Mobile de Azure ont été respectées.  Le démarrage rapide vous permet également de configurer votre compte et créer votre premier back-end d’application Mobile.

Si vous décidez de ne pas utiliser le didacticiel de démarrage rapide, effectuez les tâches suivantes :

- [créer un back-end de l’application Mobile] [ 13] à utiliser avec votre application d’Android.
- Dans un Studio Android, [mettre à jour les fichiers de build Gradle](#gradle-build).
- [Activer les autorisations internet](#enable-internet).

###<a name="gradle-build"></a>Mettre à jour le fichier de génération Gradle

Modifier les deux fichiers de **build.gradle** :

1. Ajoutez ce code dans le fichier de **build.gradle** de niveau de *projet* à l’intérieur de la balise *buildscript* :

        buildscript {
            repositories {
                jcenter()
            }
        }

2. Ajoutez ce code dans le fichier de **build.gradle** de niveau de *Module app* à l’intérieur de la balise de *dépendances* :

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    Actuellement, la version la plus récente est 3.1.0. Les versions prises en charge sont répertoriées [ici][14].

###<a name="enable-internet"></a>Activer les autorisations internet

Pour accéder à Azure, votre application doit avoir l’autorisation d’INTERNET. Si ce n’est pas déjà fait, ajoutez la ligne de code suivante à votre fichier **AndroidManifest.xml** :

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>L’immersion de notions de base

Cette section décrit la partie du code dans l’application de démarrage rapide qui se rapporte à l’utilisation d’applications de Mobile Azure.  

###<a name="data-object"></a>Définir des classes de données client

Pour accéder aux données des tables de SQL Azure, définir des classes de données client qui correspondent aux tables dans le back-end de l’application Mobile. Exemples de cette rubrique supposent une table nommée **ToDoItem**, qui contient les colonnes suivantes :

- ID
- texte
- Terminer

Le correspondant de type objet côté client :

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

Le code réside dans un fichier appelé **ToDoItem.java**.

Si votre table SQL Azure contient plus de colonnes, vous ajoutez les champs correspondants à cette classe.  Par exemple, si le DTO (objet de transfert de données) avait une colonne de priorité entier, puis vous pouvez ajouter ce champ, ainsi que de ses méthodes d’accesseur Get et Set :

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }
    
    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

Pour savoir comment créer des tables supplémentaires dans votre système principal d’applications Mobile, consultez [Comment : définir un contrôleur de tableau] [ 15] (back-end de .NET) ou [définir des Tables à l’aide d’un schéma dynamique] [ 16] (Node.js backend). Pour un serveur principal Node.js, vous pouvez également utiliser le paramètre de **tables faciles** dans [Azure portal].

###<a name="create-client"></a>Comment : créer le contexte client

Ce code crée l’objet **MobileServiceClient** qui est utilisée pour accéder à votre back-end de l’application Mobile. Le code est inséré le `onCreate` méthode de la classe de **l’activité** spécifiée dans *AndroidManifest.xml* sous la forme d’une action de **MAIN** et le **Lanceur de** catégorie. Dans le code de démarrage rapide, il se passe dans le fichier **ToDoActivity.java** .

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

Dans ce code, remplacez `MobileAppUrl` par l’URL de votre back-end de l’application Mobile, qui se trouve dans le [portail Azure] dans la lame pour votre back-end de l’application Mobile. Pour cette ligne de code à compiler, vous devez également ajouter l’instruction **import** suivante :

    import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>Comment : créer une table de référence

Le moyen le plus simple d’interroger ou de modifier des données dans le serveur principal est en utilisant le *type de modèle de programmation*, puisque Java est un langage fortement typé. Ce modèle fournit la sérialisation JSON transparente et la désérialisation à l’aide de la [gson] [ 3] bibliothèque lors de l’envoi des données entre les tables et les objets de client dans le système principal SQL d’Azure.

Pour accéder à une table, créez d’abord un [MobileServiceTable] [ 8] objet en appelant la **getTable** méthode sur le [MobileServiceClient][9].  Cette méthode possède deux surcharges :

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

Dans le code suivant, **mClient** est une référence à l’objet MobileServiceClient.  La première surcharge est utilisée lorsque le nom de classe et le nom de la table sont les mêmes, et est celui utilisé dans le démarrage rapide :

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

La deuxième surcharge est utilisée lorsque le nom de table est différent du nom de classe : le premier paramètre est le nom de la table.

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>Comment : lier des données à l’interface utilisateur

Liaison de données implique trois composants :

- La source de données
- La mise en page d’écran
- La carte qui relie les deux.

Dans notre exemple, nous renvoyer les données de la table de SQL Azure applications Mobile **ToDoItem** dans un tableau. Cette activité est un modèle courant pour les applications de données.  Requêtes de base de données retournent souvent une collection de lignes que le client obtient dans une liste ou un tableau. Dans cet exemple, le tableau est la source de données.

Le code spécifie une mise en page d’écran qui définit la vue de données qui s’affiche sur le périphérique.  Les deux sont liés avec un adaptateur, qui, dans ce code, est une extension de la **ArrayAdapter&lt;ToDoItem&gt; ** classe.

#### <a name="layout"></a>Comment : définir la mise en page

La disposition est définie par plusieurs extraits de code XML. Étant donné une mise en page existante, le code suivant représente le **ListView** nous souhaitez remplir avec les données de notre serveur.

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

Dans le code précédent, l’attribut *listitem* Spécifie l’id de la disposition d’une ligne individuelle dans la liste. Ce code spécifie une case à cocher et son texte associé et est instancié une seule fois pour chaque élément de la liste. Cette mise en page n’affiche pas le champ **id** , et une mise en page plus complexe spécifiez des champs supplémentaires dans l’affichage. Ce code se trouve dans le fichier **row_list_to_do.xml** .

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>Comment : définir la carte

Dans la mesure où la source de données de notre vue est un tableau de **ToDoItem**, nous sous-classe notre carte à partir d’un **ArrayAdapter&lt;ToDoItem&gt; ** classe. Cette sous-classe produit un affichage pour chaque **ToDoItem** à l’aide de la mise en forme de **row_list_to_do** .

Dans notre code, nous définissons la classe suivante qui est une extension de la **ArrayAdapter&lt;E&gt; ** classe :

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

Substituez la méthode **getView** de cartes. Par exemple :

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

Nous créons une instance de cette classe dans notre activité comme suit :

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

Le deuxième paramètre au constructeur ToDoItemAdapter est une référence à la mise en page. Nous pouvons maintenant instancier la **ListView** et affecter la carte à **ListView**.

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>La structure de l’API

Opérations de table d’applications Mobile et les appels d’API personnalisés sont asynchrones. Utilisez les objets [futur] et [AsyncTask] pour les méthodes asynchrones impliquant des requêtes, des insertions, des mises à jour et des suppressions. L’utilisation de perspectives permet d’effectuer plusieurs opérations sur un thread d’arrière-plan sans avoir à gérer plusieurs rappels imbriqués.

Consultez le fichier **ToDoActivity.java** dans le projet de démarrage rapide Android à partir du [portail Azure] pour obtenir un exemple.

#### <a name="use-adapter"></a>Comment : utiliser la carte

Vous êtes maintenant prêt à utiliser la liaison de données. Le code suivant montre comment obtenir les éléments de la table et remplit la carte locale avec les articles retournés.

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

Appelez la carte chaque fois que vous modifiez la table **ToDoItem** . Dans la mesure où les modifications sont effectuées sur une base par un enregistrement, vous gérez une ligne unique au lieu d’une collection. Lorsque vous insérez un élément, appelez la méthode **add** sur la carte ; lors de la suppression, appelez la méthode **remove** .

##<a name="querying"></a>Comment : interroger des données à partir de votre back-end de l’application Mobile

Cette section décrit comment émettre des requêtes sur le serveur principal d’application Mobile, qui inclut les tâches suivantes :

- [Retourner tous les éléments]
- [Filtrer les données retournées]
- [Tri de données renvoyée]
- [Renvoyer les données dans les pages]
- [Sélectionner des colonnes spécifiques]
- [Concaténer des méthodes de requête](#chaining)

### <a name="showAll"></a>Comment : retourner tous les éléments d’une Table

La requête suivante renvoie tous les éléments dans la table **ToDoItem** .

    List<ToDoItem> results = mToDoTable.execute().get();

La variable de *résultats* renvoie le jeu de résultats de la requête sous forme de liste.

### <a name="filtering"></a>Comment : filtrer les données retournées

L’exécution de la requête suivante retourne tous les éléments de la table **ToDoItem** où **complète** est égale à **false**.

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** est la référence à la table de service mobile que nous avons créée précédemment.

Définissez un filtre à l’aide de l’appel de la méthode **où** la référence de table. La méthode **where** est suivie d’un **champ** de la méthode suivie par une méthode qui spécifie le prédicat logique. Méthodes de prédicat possibles incluent **eq** (égalité), **ne** (différent de), **gt** (supérieur à), **ge** (supérieur ou égal à), **lt** (inférieur à), **le** (inférieur ou égal à). Ces méthodes vous permettent de comparer des champs nombre et chaîne à des valeurs spécifiques.

Vous pouvez filtrer les dates. Les méthodes suivantes vous permettent de comparer le champ de date complète ou des parties de la date : **année**, **mois**, **jour**, **heure**, **minute**et **seconde**. L’exemple suivant ajoute un filtre pour les articles dont la *date d’échéance* correspond à 2013.

    mToDoTable.where().year("due").eq(2013).execute().get();

Les méthodes suivantes prennent en charge des filtres complexes sur les champs de type chaîne : **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **Remplacer**, **toLower**, **toUpper**, **trim**et **longueur**. L’exemple suivant filtre les lignes de la table où la colonne de *texte* commence par « PRI0 ».

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

Les méthodes d’opérateur suivants sont pris en charge sur les champs de numéro : **Ajouter**, **sub**, **mul**, **div**, **mod**, **sol**, un **plafond**et **arrondir**. L’exemple suivant filtre les lignes de la table dans laquelle la **durée** est un nombre pair.

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

Vous pouvez combiner des prédicats avec ces méthodes logiques : **et**, **ou** et **pas**. L’exemple suivant combine deux des exemples précédents.

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

Regrouper et imbriquer des opérateurs logiques :

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

Pour une discussion plus détaillée et des exemples de filtrage, consultez [découverte de la richesse du modèle requête client Android](http://hashtagfail.com/post/46493261719/mobile-services-android-querying).

### <a name="sorting"></a>Comment : trier les données retournées

Le code suivant renvoie que tous les éléments d’un tableau de **ToDoItems** trié par ordre croissant en fonction du champ de *texte* . *mToDoTable* est la référence à la table principale que vous avez créé précédemment :

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

Le premier paramètre de la méthode **orderBy** est une chaîne correspondant au nom du champ sur lequel effectuer le tri. Le deuxième paramètre utilise l’énumération **QueryOrder** pour spécifier s’il faut trier dans l’ordre croissant ou décroissant.  Si vous filtrez à l’aide de la méthode ***where*** , la méthode ***where*** doit être appelée avant la méthode ***orderBy*** .

### <a name="paging"></a>Comment : retourner des données dans les pages

Le premier exemple montre comment sélectionner les cinq éléments principaux d’une table. La requête renvoie les éléments d’un tableau de **ToDoItems**. **mToDoTable** est la référence à la table principale que vous avez créé précédemment :

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


Voici une requête qui ignore les cinq premiers articles, puis retourne les cinq suivantes :

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>Comment : sélectionner des colonnes spécifiques

Le code suivant montre comment retourner tous les éléments d’un tableau de **ToDoItems**, mais n’affiche que les champs **texte** et **complète** . **mToDoTable** est la référence à la table principale que nous avons créée précédemment.

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

Les paramètres de la fonction select sont les noms de chaîne des colonnes de la table que vous souhaitez renvoyer.

La méthode **select** doit suivre les méthodes telles que **where** et **orderBy**. Il peut être suivi par **tout comme**les méthodes de pagination.

### <a name="chaining"></a>Comment : concaténer des méthodes de requête

Les méthodes utilisées dans l’interrogation de tables de back-end peuvent être concaténées. Chaînage des méthodes de requête vous permet de sélectionner des colonnes spécifiques de lignes filtrées sont triées et paginées. Vous pouvez créer des filtres logiques complexes.
Chaque méthode de la requête retourne un objet de la requête. Pour mettre fin à la série de méthodes et exécuter la requête, appelez la méthode **execute** . Par exemple :

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

Les méthodes de requête chaînée doivent être classées comme suit :

1. Méthodes de filtrage (**où**).
2. Méthodes de tri (**orderBy**).
3. Méthodes de sélection (**Sélectionner**).
4. méthodes (**Ignorer** et **haut**) de pagination.

##<a name="inserting"></a>Comment : insérer des données dans le système principal

Instancier une instance de la classe *ToDoItem* et définissez ses propriétés.

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

Puis utilisez **insert()** pour insérer un objet :

    ToDoItem entity = mToDoTable.insert(item).get();

Les correspondances d’entité renvoyé les données insérées dans la table principale, inclus le code et toutes les autres valeurs définies sur le serveur principal.

Tables d’applications Mobile requièrent une colonne de clé primaire intitulée **id**. Par défaut, cette colonne est une chaîne. La valeur par défaut de la colonne ID est un GUID.  Vous pouvez fournir d’autres valeurs uniques, comme les adresses de messagerie ou de noms d’utilisateurs. Lorsqu’une valeur d’ID de chaîne n’est pas fournie pour un enregistrement inséré, le back-end génère un nouveau GUID.

Valeurs d’ID de chaîne offrent les avantages suivants :

+ ID peuvent être générés sans effectuer un aller-retour à la base de données.
+ Les enregistrements sont plus faciles de fusionner à partir de différentes tables ou bases de données.
+ Valeurs d’ID sont mieux intégrant avec une logique de l’application.

Valeurs d’ID de chaîne sont **requis** pour la prise en charge de la synchronisation hors connexion.

##<a name="updating"></a>Comment : mettre à jour les données dans une application mobile

Pour mettre à jour les données d’une table, passer le nouvel objet à la méthode **update()** .

    mToDoTable.update(item).get();

Dans cet exemple, *élément* est une référence à une ligne dans la table *ToDoItem* , qui a eu des modifications apportées.
La ligne avec le même **id** est mis à jour.

##<a name="deleting"></a>Comment : supprimer des données dans une application mobile

Le code suivant montre comment supprimer les données d’une table en spécifiant l’objet de données.

    mToDoTable.delete(item);

Vous pouvez également supprimer un élément en spécifiant le champ **id** de la ligne à supprimer.

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
    mToDoTable.delete(myRowId);

##<a name="lookup"></a>Comment : rechercher un élément spécifique

Rechercher un élément avec un champ spécifique de **code** avec la méthode **lookUp()** :

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

##<a name="untyped"></a>Comment : travailler avec des données non typées

Le modèle de programmation non typé vous donne un contrôle préci sur la sérialisation JSON.  Il existe quelques scénarios courants où vous pouvez souhaiter utiliser un modèle de programmation non typé. Par exemple, si votre table principale contient beaucoup de colonnes, et vous ne devez faire référence à un sous-ensemble de colonnes.  Le modèle typé vous impose de définir les colonnes de toutes les applications mobiles de la table dans votre classe de données.  

La plupart des appels API d’accès aux données est similaire aux appels de programmation typées. La principale différence est que dans le modèle non typé vous appelez des méthodes sur l’objet de la **MobileServiceJsonTable** , au lieu de l’objet **MobileServiceTable** .

### <a name="json_instance"></a>Comment : créer une instance d’une table non typée

Semblable au modèle typé, vous démarrez en obtenant une référence de table, mais dans ce cas, il est un objet **MobileServicesJsonTable** . Obtenir la référence en appelant la **getTable** méthode sur une instance du client :

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

Une fois que vous avez créé une instance de l' **MobileServiceJsonTable**, il a pratiquement de la même API disponible comme avec le modèle de programmation typé. Dans certains cas, les méthodes prennent un paramètre non typé au lieu d’un paramètre typé.

### <a name="json_insert"></a>Comment : insérer dans une table non typée

Le code suivant montre comment effectuer une opération d’insertion. La première étape consiste à créer un [JsonObject][1], qui fait partie de la [gson] [ 3] bibliothèque.

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

Puis, utilisez **insert()** pour insérer l’objet non typé dans la table.

    mJsonToDoTable.insert(jsonItem).get();

Si vous avez besoin obtenir l’ID de l’objet inséré, utilisez la méthode **getAsJsonPrimitive()** .

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>Comment : supprimer une table non typée

Le code suivant montre comment supprimer une instance, dans ce cas, la même instance d’un **JsonObject** qui a été créé dans l’exemple précédent *Insérer* . Le code est le même que le cas typé, mais la méthode a une signature différente dans la mesure où elle fait référence à un **JsonObject**.

         mToDoTable.delete(jsonItem);

Vous pouvez également supprimer une instance directement à l’aide de son ID :

         mToDoTable.delete(ID);

### <a name="json_get"></a>Comment : retourner toutes les lignes d’une table non typée

Le code suivant montre comment extraire une table entière. Dans la mesure où vous utilisez un tableau JSON, vous pouvez extraire sélectivement uniquement certaines colonnes de la table.

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

Le même jeu de filtrage, le filtrage et la pagination des méthodes qui sont disponibles pour le modèle de type sont disponibles pour le modèle de non typé.

##<a name="custom-api"></a>Comment : appeler une API personnalisée

Une API personnalisée vous permet de vous permet de définir des points de terminaison personnalisés qui exposent les fonctionnalités de serveur qui ne pas mapper à une opération d’insertion, mise à jour, supprimer ou opération de lecture. À l’aide d’une API personnalisée, vous avez davantage de contrôle sur la messagerie, notamment pour lire et définir des en-têtes HTTP message et définition d’un format de corps de message que JSON.

À partir d’un client Android, vous appelez la méthode **invokeApi** pour appeler le point de terminaison personnalisé de l’API. L’exemple suivant montre comment appeler un point de terminaison API nommé **completeAll**, qui retourne une classe de collection nommée **MarkAllResult**.

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

La méthode **invokeApi** est appelée sur le client, qui envoie une demande POST à la nouvelle API personnalisée. Le résultat retourné par l’API personnalisé s’affiche dans une boîte de dialogue de message, comme des erreurs. Autres versions de **invokeApi** vous permettent d’envoyer un objet dans le corps de la demande le cas échéant, spécifier la méthode HTTP et envoyer des paramètres de requête avec la demande. Les versions non typées de **invokeApi** sont également fournies.

##<a name="authentication"></a>Comment : ajouter une authentification à votre application

Didacticiels déjà décrivent en détail comment ajouter ces fonctionnalités.

Service d’application prend en charge l' [authentification des utilisateurs de l’application](app-service-mobile-android-get-started-users.md) à l’aide d’une variété de fournisseurs d’identité externes : Facebook, Google, Microsoft Account, Twitter et Azure Active Directory. Vous pouvez définir des autorisations sur les tables pour restreindre l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser l’identité des utilisateurs authentifiés pour mettre en œuvre des règles d’autorisation de votre back-end.

Deux flux d’authentification sont pris en charge : un flux du **serveur** et un **client** de flux. Le flux de serveur fournit l’expérience d’authentification la plus simple, car il repose sur l’interface web de fournisseurs identité.  Aucun SDK supplémentaires n’est requis pour implémenter l’authentification de serveur flux. L’authentification du serveur flux ne fournit pas une intégration en profondeur dans le périphérique mobile et est recommandée uniquement pour les scénarios du concept de la preuve.

Le flux de client permet une intégration plus poussée avec des fonctionnalités spécifiques au périphérique, tels que l’ouverture de session unique car il repose sur les kits de développement logiciel fournis par le fournisseur d’identité.  Par exemple, vous pouvez intégrer le SDK Facebook dans votre application mobile.  Le client mobile permute dans l’application Facebook et confirme votre ouverture de session avant le remplacement à votre application mobile.

Quatre étapes sont nécessaires pour activer l’authentification dans votre application :

- Enregistrer votre application pour l’authentification auprès d’un fournisseur d’identité.
- Configurer votre back-end de Service de l’application.
- Restreindre les autorisations de table pour les utilisateurs authentifiés uniquement sur le serveur de Service d’application principal.
- Ajoutez le code d’authentification pour votre application.

Vous pouvez définir des autorisations sur les tables pour restreindre l’accès à des opérations spécifiques aux utilisateurs authentifiés uniquement. Vous pouvez également utiliser le SID d’un utilisateur authentifié pour modifier les demandes.  Pour plus d’informations, consultez [mise en route de l’authentification] et à la documentation de faire du Kit de développement logiciel serveur.

### <a name="caching"></a>Comment : ajouter du code d’authentification pour votre application

Le code suivant démarre un processus de connexion de flux de serveur à l’aide du fournisseur de Google :

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

Obtenir l’ID de l’utilisateur connecté à partir d’un **MobileServiceUser** à l’aide de la méthode **getUserId** . Pour obtenir un exemple d’utilisation des perspectives d’appeler l’API de connexion asynchrone, consultez [mise en route de l’authentification].

### <a name="caching"></a>Comment : mettre en Cache les jetons d’authentification

La mise en cache des jetons d’authentification nécessite que vous stockez l’ID utilisateur et un jeton d’authentification localement sur le périphérique. Au prochain démarrage de l’application, vous vérifiez le cache, et si ces valeurs sont présentes, vous pouvez ignorer le journal dans la procédure et réalimenter le client avec ces données. Toutefois, ces données sont sensibles, et il doit être stocké chiffré de sécurité au cas où le téléphone est volé.

Vous pouvez voir un exemple complet de la procédure pour les jetons d’authentification du cache dans la [section de jetons d’authentification du Cache][7].

Lorsque vous essayez d’utiliser un jeton a expiré, vous recevez une réponse *401 non autorisé* . Vous pouvez gérer les erreurs d’authentification à l’aide de filtres.  Filtres interceptent les demandes vers le serveur de Service d’application principal. Le code de filtre teste la réponse pour un 401, déclenche le processus de connexion et puis reprend la demande ayant généré la 401. 

## <a name="adal"></a>Comment : authentifier les utilisateurs avec la bibliothèque d’authentification Active Directory

Vous pouvez utiliser la bibliothèque de l’authentification Active Directory (ADAL) pour connecter les utilisateurs dans votre application à l’aide d’Azure Active Directory. À l’aide d’une connexion de flux du client est souvent préférable à l’utilisation de la `loginAsync()` les méthodes qu’il fournit un aspect UX plus natif et permet la personnalisation supplémentaire.

1. Configurer votre back-end de l’application mobile pour DAS connexion en suivant le didacticiel [comment configurer le Service d’application pour la connexion de Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) . Veillez à terminer l’étape facultative de l’inscription d’une application client natif.

2. Installez ADAL en modifiant le fichier build.gradle pour inclure les définitions suivantes :

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

3. Ajoutez le code suivant à votre application, qui effectue les remplacements suivants :

* Remplacer **l’Insertion-autorité-ici** avec le nom du locataire dans lequel vous déployé votre application. Le format doit être https://login.windows.net/contoso.onmicrosoft.com. Cette valeur peut être copiée à partir de l’onglet domaine dans Azure Active Directory [portail classique Azure].

* Remplacez **Insertion-RESOURCE-ID-ici** l’ID client pour votre back-end de l’application mobile. Vous pouvez obtenir l’ID de client à partir de l’onglet **Avancé** , sous **Les paramètres Active Directory Azure** dans le portail.

* Remplacer **l’Insertion-CLIENT-ID-ici** avec l’ID de client que vous avez copié à partir de l’application client natif.

* Remplacer **l’Insertion-redirection-URI-ici** avec le point de terminaison de votre site _/.auth/login/done_ , à l’aide du schéma HTTPS. Cette valeur doit être similaire à _https://contoso.azurewebsites.net/.auth/login/done_.

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>Comment : ajouter la notification de transmission à votre application

Vous pouvez [lire la présentation] [ 6] qui décrit comment Microsoft Azure Notification concentrateurs prend en charge un grand nombre de notifications de type Pousser.  Dans [ce didacticiel][5], une notification de transmission est envoyée à tous les périphériques chaque fois qu’un enregistrement est inséré.

## <a name="how-to-add-offline-sync-to-your-app"></a>Comment : ajouter la synchronisation hors connexion pour votre application

Le didacticiel de démarrage rapide contient le code qui implémente la synchronisation hors connexion. Recherchez le préfixe avec les commentaires de code :

    // Offline Sync

En commentaire les lignes suivantes de code, vous pouvez implémenter la synchronisation en mode hors connexion, et vous pouvez ajouter du code semblable à un autre code d’applications Mobile.

##<a name="customizing"></a>Comment : personnaliser le client

Il existe plusieurs façons de personnaliser le comportement par défaut du client.

### <a name="headers"></a>Comment : personnaliser les en-têtes de demande

Configurer un **ServiceFilter** pour ajouter un en-tête HTTP personnalisé pour chaque requête :

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>Comment : personnaliser la sérialisation

Le client suppose que les noms de table, noms de colonne et données types sur le serveur principal tous correspondent exactement les objets de données définies dans le client. Il peut exister plusieurs raisons pourquoi les noms du serveur et le client peuvent ne pas correspondent. Dans votre cas, vous pouvez souhaiter effectuer les types de personnalisations suivants :

- Les noms de colonne utilisés dans la table de Service d’application ne correspondent pas les noms que vous utilisez dans le client.
- Utilisez une table de Service de l’application qui a un nom différent de celui de la classe, qu'il mappe sur le client.
- Activer la mise en majuscule automatique des propriétés.
- Ajouter des propriétés complexes à un objet.

### <a name="columns"></a>Comment : mapper des noms de client et le serveur différents

Supposons que votre code de client Java utilise des noms de style Java standard pour les propriétés de l’objet **ToDoItem** , telles que les propriétés suivantes :

- mId
- mText
- mComplete
- fonction DUREE

Sérialiser les noms des clients dans les noms JSON qui correspondent aux noms de colonne de la table **ToDoItem** sur le serveur. Le code suivant utilise le [gson] [ 3] bibliothèque d’annoter les propriétés :

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>Comment : mapper des noms de table différente entre le client et le serveur principal

Mapper le nom de la table client pour un nom de table de différents services mobiles à l’aide d’une substitution de la [getTable()] [ 4] méthode :

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>Comment : automatiser les mappages de nom de colonne

Vous pouvez spécifier une stratégie de conversion qui s’applique à toutes les colonnes à l’aide de la [gson] [ 3] API. La bibliothèque cliente Android utilise [gson] [ 3] dans les coulisses pour sérialiser des objets Java pour les données JSON avant que les données sont envoyées au Service d’application Azure.  Le code suivant utilise la méthode **setFieldNamingStrategy()** pour définir la stratégie. Cet exemple montre comment supprimer le caractère initial (un « m ») et puis romane minuscule du caractère suivant, pour chaque nom de champ. Par exemple, il ferait « intermédiaire » « id ».

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

Ce code doit être exécuté avant d’utiliser la **MobileServiceClient**.

### <a name="complex"></a>Comment : stocker une propriété d’objet ou un tableau dans une table

Jusqu'à présent, nos exemples de sérialisation ont impliqué des types primitifs comme les entiers et les chaînes.  Les types primitifs sont facilement sérialiser dans JSON.  Si nous voulons ajouter un objet complexe qui ne sérialiser automatiquement à JSON, nous devons fournir la méthode de sérialisation JSON.  Pour voir un exemple de procédure personnalisée de la sérialisation JSON, consultez le billet de blog [sérialisation de personnalisation à l’aide de la bibliothèque gson dans le client Mobile Services Android][2].

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[Retourner tous les éléments]: #showAll
[Filtrer les données retournées]: #filtering
[Tri de données renvoyée]: #sorting
[Renvoyer les données dans les pages]: #paging
[Sélectionner des colonnes spécifiques]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Mise en route de l’authentification]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Avenir]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html