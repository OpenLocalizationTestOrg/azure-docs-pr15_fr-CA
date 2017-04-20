<properties
   pageTitle="Vue d’ensemble du contrôle d’accès au magasin de données lac | Microsoft Azure"
   description="Comprendre comment accéder contrôle Azure magasin de données lac"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="access-control-in-azure-data-lake-store"></a>Contrôle d’accès au magasin de LAC de données Azure

Magasin de données lac implémente un modèle de contrôle d’accès qui dérive de très et, à son tour, à partir du modèle de contrôle d’accès POSIX. Cet article présente les notions de base du modèle de contrôle d’accès pour le magasin de données lac. Pour en savoir plus sur le très contrôle d’accès au modèle de voir [Très Permissions Guide](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listes de contrôle d’accès sur les fichiers et dossiers

Il existe deux types de listes de contrôle d’accès (ACL) - **Accès ACL** et **Par défaut des ACL**.

* **Accès ACL** – ces contrôle l’accès à un objet. Fichiers et dossiers ont accès aux ACL.

* **Par défaut les ACL** – « Modèle » des ACL associé à un dossier qui déterminent les listes ACL d’accès pour tous les éléments enfants créés sous ce dossier. Fichiers n’ont pas par défaut les ACL.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Les listes ACL d’accès et par défaut des ACL ont la même structure.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

>[AZURE.NOTE] Modification de l’ACL par défaut sur un parent n’affecte pas l’accès ACL ou l’ACL par défaut des éléments enfants qui existent déjà.

## <a name="users-and-identities"></a>Les utilisateurs et les identités

Chaque fichier et dossier a des autorisations distinctes pour ces identités :

* L’utilisateur propriétaire du fichier
* Le propriétaire du groupe
* Utilisateurs nommés
* Groupes nommés
* Tous les autres utilisateurs.

Les identités des utilisateurs et des groupes sont donc les identités d’Azure Active Directory (DAS), sauf indication contraire un « utilisateur », dans le contexte du lac de magasin de données, soit signifie un DAS ou un groupe de sécurité DAS.

## <a name="permissions"></a>Autorisations

Les autorisations sur un objet de système de fichiers sont **en lecture**, **écriture**, **exécution** et il peuvent être utilisés sur des fichiers et des dossiers comme indiqué dans le tableau ci-dessous

|            |    Fichier     |   Dossier |
|------------|-------------|----------|
| **Read (R)** | Peut lire le contenu d’un fichier | Nécessite de **lecture** et **exécution** répertorier le contenu du dossier.|
| **Écrire (W)** | Peut écrire ou ajouter à un fichier | Nécessite d' **écrire & exécuter** créer des éléments enfants dans un dossier. |
| **Exécuter (X)** | Ne signifie pas que quoi que ce soit dans le contexte du lac de magasin de données | Nécessaire pour parcourir les éléments enfants d’un dossier. |

### <a name="short-forms-for-permissions"></a>Formes abrégées pour les autorisations

**RWX**est utilisé pour indiquer une **lecture + écriture + exécuter**. Il existe un formulaire numérique plus condensé dans lequel **lecture = 4**, **écrire = 2**, et **Execute = 1** et leur somme représente les autorisations. Voici quelques exemples.

| Forme numérique | Forme abrégée |      Ce que cela signifie     |
|--------------|------------|------------------------|
| 7            | RWX        | Lecture + écriture + exécuter |
| 5            | R-X        | Accès en lecture / exécution         |
| 4            | R--        | En lecture                   |
| 0            | ---        | Aucune autorisation         |


### <a name="permissions-do-not-inherit"></a>N’héritent pas des autorisations

Dans le modèle de POSIX-style utilisé par le magasin de données lac, les autorisations pour un élément sont stockées sur l’élément lui-même. En d’autres termes, les autorisations pour un élément ne peut pas être héritées des éléments parents.

## <a name="common-scenarios-related-to-permissions"></a>Scénarios courants liés aux autorisations

Voici quelques scénarios courants de comprendre quelles sont les autorisations nécessaires pour effectuer certaines opérations sur un compte de banque de données lac.

### <a name="permissions-needed-to-read-a-file"></a>Autorisations nécessaires pour lire un fichier

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Pour le fichier à lire - l’appelant a besoin des autorisations de **lecture**
* Pour tous les dossiers dans la structure de dossiers qui contiennent le fichier - l’appelant a besoin des autorisations **d’exécution**

### <a name="permissions-needed-to-append-to-a-file"></a>Autorisations nécessaires pour ajouter à un fichier

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Pour le fichier à ajouter à - l’appelant a besoin des autorisations **d’écriture**
* Pour tous les dossiers qui contiennent le fichier - l’appelant a besoin des autorisations **d’exécution**

### <a name="permissions-needed-to-delete-a-file"></a>Autorisations nécessaires pour supprimer un fichier

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Pour le dossier parent - l’appelant a besoin des autorisations **écrire + exécuter**
* Pour tous les autres dossiers dans le chemin du fichier - l’appelant a besoin des autorisations **d’exécution**

>[AZURE.NOTE] Écrire les autorisations sur le fichier n’est pas nécessaire de supprimer le fichier tant que les deux conditions ci-dessus sont remplies.

### <a name="permissions-needed-to-enumerate-a-folder"></a>Autorisations nécessaires pour énumérer un dossier

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Pour le dossier d’énumérer - l’appelant a besoin des autorisations de **lecture + Execute**
* Pour tous les dossiers d’ancêtre - l’appelant a besoin des autorisations **d’exécution**

## <a name="viewing-permissions-in-the-azure-portal"></a>Autorisations d’affichage dans le portail Azure

À partir de la blade **d’Explorer des données** du magasin de données lac compte, cliquez sur **accès** pour voir les ACL pour un fichier ou un dossier. Dans la capture d’écran ci-dessous, cliquez sur accès pour afficher les listes ACL du dossier de **catalogue** sous le compte **mydatastore** .

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Ensuite, à partir de la blade **d’accès** , cliquez sur **Affichage Simple** pour afficher la vue plus simple.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Cliquez sur **Affichage avancé** pour afficher la vue plus avancée.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Le super utilisateur

Un super utilisateur a le plus de droits de tous les utilisateurs du magasin de données lac. Un super utilisateur :

* a = RWX autorisations à **tous les** fichiers et les dossiers
* peut modifier les autorisations sur un fichier ou un dossier.
* peut modifier le propriétaire utilisateur ou le groupe propriétaire d’un fichier ou un dossier.

Dans Azure, un compte de banque de données lac a plusieurs rôles d’Azure :

* Propriétaires
* Contributeurs
* Lecteurs
* Etc.

Tout le monde dans le rôle **propriétaires** d’un compte de banque de données lac est automatiquement un super utilisateur de ce compte. Pour en savoir plus sur Azure rôle de base de contrôle d’accès (RBAC), consultez [contrôle d’accès basée sur les rôles](../active-directory/role-based-access-control-configure.md).

## <a name="the-owning-user"></a>L’utilisateur propriétaire

L’utilisateur qui a créé l’élément est automatiquement l’utilisateur propriétaire de l’élément. Un utilisateur propriétaire peut :

* Modifier les autorisations d’un fichier qui appartient
* Modifier le groupe propriétaire d’un fichier qui est détenu, tant que l’utilisateur propriétaire est également un membre d’un groupe cible.

>[AZURE.NOTE] Propriétaire **ne peut pas** modifier l’utilisateur propriétaire d’un autre fichier propriétaire. Seuls les super utilisateurs peuvent modifier l’utilisateur propriétaire d’un fichier ou un dossier.

## <a name="the-owning-group"></a>Le propriétaire du groupe

Dans les listes ACL POSIX, chaque utilisateur est associé à un « groupe principal ». Par exemple, « alice » d’utilisateur peut appartenir au groupe « finance ». Alice peut appartenir à plusieurs groupes, mais un groupe est toujours désigné comme son groupe principal. Dans POSIX, lorsque Alice crée un fichier, le groupe propriétaire de ce fichier est défini à son groupe principal, qui dans ce cas est « finance ».
 
Lors de la création d’un nouvel élément de système de fichiers, données lac banque affecte une valeur au groupe propriétaire. 

* **Cas 1** : le dossier de la racine « / ». Ce dossier est créé lors de la création d’un compte de banque de données lac. Dans ce cas, le groupe propriétaire est défini pour l’utilisateur qui a créé le compte.
* **Cas n ° 2** (tous les autres cas) - lorsqu’un nouvel élément est créé, le groupe propriétaire est copié à partir du dossier parent.

Le groupe propriétaire peut être modifié par :
* Les super utilisateurs
* L’utilisateur propriétaire, si l’utilisateur propriétaire est également un membre d’un groupe cible.

## <a name="access-check-algorithm"></a>Algorithme de vérification d’accès

L’illustration suivante représente l’algorithme de vérification d’accès pour les comptes de la banque de données lac.

![Algorithme d’ACL de magasin de données lac](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>Le masque et les « autorisations effectives »

Le **masque** est une valeur RWX qui est utilisée pour limiter l’accès de **nommé utilisateurs**, le **groupe propriétaire**et **groupes nommés** lors de l’exécution de l’algorithme de vérification d’accès. Voici les concepts clés pour le masque. 

* Le masque crée des « autorisations effectives », c'est-à-dire qu’elle modifie les autorisations au moment de la vérification de l’accès.
* Le masque peut être modifié directement par le propriétaire du fichier et des super utilisateurs.
* Le masque a la possibilité de supprimer les autorisations pour créer l’autorisation effective. Le masque **ne peut pas** ajouter des autorisations pour l’autorisation effective. 

Examinons quelques exemples. Ci-dessous, le masque a **RWX**, qui signifie que le masque ne supprime pas toutes les autorisations. Notez que les autorisations effectives pour un utilisateur nommé groupe propriétaire et groupe nommé ne sont pas modifiées au cours de la vérification d’accès.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Dans l’exemple ci-dessous, le masque est défini à **R-X**. Par conséquent, il les **désactive l’autorisation en écriture** pour **nommé utilisateur**, **groupe propriétaire**et le **groupe nommé** au moment de l’accès de vérifier.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Pour référence, voici où le masque pour un fichier ou un dossier apparaît dans le portail Azure.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

>[AZURE.NOTE] Pour un nouveau compte de banque de données lac, le masque pour l’accès et l’ACL par défaut du dossier racine (« / ») sont par défaut = RWX.

## <a name="permissions-on-new-files-and-folders"></a>Autorisations sur les nouveaux fichiers et dossiers

Lorsqu’un nouveau fichier ou un dossier est créé sous un dossier existant, l’ACL par défaut sur le dossier parent détermine :

* ACL par défaut et l’accès d’un dossier enfant
* Les accès d’un fichier d’enfants (fichiers n’ont pas une ACL par défaut)

### <a name="a-child-file-or-folders-access-acl"></a>Un fichier de l’enfant ou d’accès ACL du dossier

Lorsqu’un dossier ou un fichier enfant est créé, les ACL par défaut du parent est copié en tant que fichier enfant ou d’accès ACL du dossier. En outre, si **l’utilisateur** dispose d’autorisations de RWX dans l’ACL par défaut du parent, il est complètement supprimé à partir de l’accès de l’élément enfant.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Dans la plupart des scénarios, les informations ci-dessus sont tout ce que vous devez savoir comment les accès d’un élément enfant est déterminée. Toutefois, si vous êtes familiarisé avec les systèmes POSIX et que vous voulez comprendre approfondie comment cette transformation est réalisée, consultez la section [rôle de Umask lors de la création de l’accès pour les nouveaux fichiers et dossiers](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) , plus loin dans cet article.
 

### <a name="a-child-folders-default-acl"></a>ACL par défaut d’un dossier enfant

Création d’un dossier enfant sous un dossier parent, les ACL par défaut de celle du dossier parent est copié, comme c’est le cas, à l’ACL par défaut du dossier enfant.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Rubriques avancées pour la compréhension des ACL dans le magasin de données lac

Voici quelques sujets avancés pour vous aider à comprendre comment les ACL sont déterminés pour les fichiers de banque de données lac ou des dossiers.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Rôle du umask lors de la création de l’accès pour les nouveaux fichiers et dossiers

Dans un système compatible POSIX, le concept général est que ce umask est une valeur de bit 9 sur le dossier parent permet de transformer l’autorisation de **l’utilisateur propriétaire**, **groupe propriétaire**et **autres** sur un nouveau fichier d’enfant ou d’accès ACL du dossier. Les bits d’un umask identifient quels bits à désactiver dans l’accès de l’élément enfant. Par conséquent, il est utilisé pour empêcher la propagation des autorisations de l’utilisateur, groupe, propriétaire propriétaire de façon sélective et autres.
  
Dans un système très, l’umask est généralement une option de configuration au niveau du site est contrôlée par les administrateurs. Magasin de données lac utilise un **compte à l’échelle umask** qui ne peut pas être modifié. Le tableau suivant montre les umask du magasin de données lac.

| Groupe d’utilisateurs  | Paramètre | Effet sur les accès du nouvel élément enfant |
|------------ |---------|---------------------------------------|
| Utilisateur propriétaire | ---     | Aucun effet                             |
| Groupe propriétaire| ---     | Aucun effet                             |
| Autres       | RWX     | Supprimer en lecture + écriture + exécuter         | 

L’illustration suivante montre cette umask en action. L’effet net est supprimer **lecture + écriture + exécuter** pour un **autre** utilisateur. Dans la mesure où l’umask actif n’a pas spécifié de bits pour un **utilisateur propriétaire** et le **propriétaire du groupe**, ces autorisations ne sont pas transformées.

![Magasin de données lac ACL](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### <a name="the-sticky-bit"></a>Le bit « sticky »

Le bit « sticky » est une fonctionnalité avancée d’un système de fichiers POSIX. Dans le contexte de LAC de magasin de données, il est peu probable que le bit « sticky » est nécessaire.

Le tableau ci-dessous illustre le fonctionne le bit « sticky » dans le magasin de données lac.

| Groupe d’utilisateurs         | Fichier    | Dossier |
|--------------------|---------|-------------------------|
| Bit « sticky » **OFF** | Aucun effet   | Aucun effet           |
| Bit « sticky » **sur**  | Aucun effet   | Empêche quiconque à l’exception des **super utilisateurs** et de l' **utilisateur propriétaire** d’un élément enfant de supprimer ou de renommer cet élément enfant.               |

Le bit « sticky » n’est pas affiché dans le portail Azure.

## <a name="common-questions-for-acls-in-data-lake-store"></a>Questions les plus fréquentes de l’ACL dans le magasin de données lac

Voici quelques questions qui apparaissent souvent en ce qui concerne les ACL dans le magasin de données lac.

### <a name="do-i-have-to-enable-support-for-acls"></a>Je dois activer la prise en charge des ACL ?

N° Contrôle d’accès via un ACL est toujours activée pour un compte de banque de données lac.

### <a name="what-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Quelles sont les autorisations nécessaires pour supprimer de manière récursive un dossier et son contenu ?

* Le dossier parent doit avoir **écrire + exécuter**.
* Le dossier à supprimer et tous les dossiers qu’il contient, nécessite **de lecture + écriture + exécuter**.
>[AZURE.NOTE] Suppression des fichiers dans des dossiers ne nécessite pas l’écriture sur ces fichiers. En outre, le dossier de la racine « / » ne peuvent **jamais** être supprimés.

### <a name="who-is-set-as-the-owner-of-a-file-or-folder"></a>Qui est défini comme le propriétaire d’un fichier ou un dossier ?

Le créateur d’un fichier ou un dossier devient le propriétaire.

### <a name="who-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Qui est défini comme le groupe propriétaire du fichier ou dossier lors de la création ?

Il est copié à partir du groupe propriétaire du dossier parent dans lequel le nouveau fichier ou dossier est créé.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Je suis l’utilisateur propriétaire d’un fichier, mais je n’ai pas les autorisations RWX que nécessaires. Que dois-je faire ?

L’utilisateur propriétaire peut simplement modifier les autorisations de fichier pour s’octroyer d’autorisations RWX que dont ils ont besoin.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Magasin de données lac prend en charge l’héritage des ACL ?

N°

### <a name="what-is-the-difference-between-mask-and-umask"></a>Quelle est la différence entre le masque et umask ?

| masque | umask|
|------|------|
| La propriété **mask** est disponible sur chaque fichier et dossier. | L' **umask** est une propriété du compte banque de données lac. Ainsi, il est uniquement un umask unique du magasin de données lac.    |
| La propriété mask sur un fichier ou un dossier peut être modifiée par l’utilisateur propriétaire ou d’un groupe propriétaire d’un fichier ou d’un super utilisateur. | La propriété umask ne peut pas être modifiée par tout utilisateur, même un super utilisateur. Il s’agit d’une valeur de constante non modifiable.|
| La propriété mask est utilisée au cours de l’algorithme de vérification d’accès lors de l’exécution pour déterminer si un utilisateur a le droit d’effectuer l’opération sur un fichier ou un dossier. Le rôle du masque est de créer des autorisations « efficaces » au moment de la vérification d’accès. | L’umask actif n’est pas utilisé lors de la vérification de l’accès à tout. L’umask est utilisée pour déterminer l’accès de nouveaux éléments enfants d’un dossier. |
| Le masque est une valeur 3 bits RWX qui s’applique à l’utilisateur nommé, groupe et utilisateur propriétaire au moment de la vérification d’accès.| L’umask est une valeur de bit 9 qui s’applique à l’utilisateur propriétaire, groupe propriétaire et d’autres d’un nouvel enfant.| 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Où puis-je obtenir plus d’informations sur le modèle de contrôle d’accès POSIX ?

* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.HTML](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [Guide d’autorisation très](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 

* [FORUM AUX QUESTIONS DE POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)

* [ACL POSIX sous Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)

* [ACL à l’aide de listes de contrôle d’accès sous Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Voir aussi

* [Vue d’ensemble Azure lac de magasin de données](data-lake-store-overview.md)

* [Mise en route de données Azure lac Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md)





