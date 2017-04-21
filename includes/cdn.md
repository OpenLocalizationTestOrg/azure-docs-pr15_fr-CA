# <a name="using-cdn-for-azure"></a>À l’aide du CDN pour Azure

Le réseau de livraison de contenu (CDN) Azure offre aux développeurs une solution globale pour fournir un contenu de large bande passante en mettant en cache des objets BLOB et le contenu statique des instances de calcul au niveau des nœuds physiques dans les États-Unis, Europe, Asie, Australie et Amérique du Sud. Pour une liste des emplacements de noeud CDN, consultez [Emplacements de noeud Azure CDN].

Cette tâche comprend les étapes suivantes :

* [Étape 1 : Créer un compte de stockage](#Step1)
* [Étape 2 : Créer un nouveau point de terminaison CDN pour votre compte de stockage](#Step2)
* [Étape 3 : Accéder à votre contenu CDN](#Step3)
* [Étape 4 : Supprimer le contenu CDN](#Step4)

Avantages de l’utilisation du CDN pour mettre en cache les données Azure :

-   Meilleures performances et utilisateur expérience pour les utilisateurs finaux qui sont loin d’être une source de contenu et que vous utilisez des applications où de nombreuses « voyages internet » sont requis pour charger le contenu
-   Grande échelle distribué afin de mieux gérer la charge élevée instantanée, par exemple, au début d’un événement, tels que le lancement d’un produit

Les clients existants du CDN peuvent désormais utiliser le CDN Azure dans [Azure portal classique]. Le CDN est une fonctionnalité complémentaire de votre abonnement et a un autre [plan de facturation].

<a id="Step1"> </a>
<h2>Étape 1 : Créer un compte de stockage</h2>

Utilisez la procédure suivante pour créer un nouveau compte de stockage pour un abonnement Azure. Un compte de stockage permet d’accéder aux services de stockage Azure. Le compte de stockage représente le niveau le plus élevé de l’espace de noms pour accéder à chacun des composants du service de stockage Azure : Blob de services, les services de file d’attente et les services de la Table. Pour plus d’informations sur les services de stockage Azure, reportez-vous à la section [en utilisant les Services de stockage Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Pour créer un compte de stockage, vous devez être l’administrateur du service ou administrateur de collaboration pour l’abonnement associé.

> [AZURE.NOTE] Pour plus d’informations sur l’exécution de cette opération à l’aide de l’API de gestion de Service Azure, consultez la rubrique de référence de [Créer un compte de stockage](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx) .

**Pour créer un compte de stockage pour un abonnement Azure**

1.  Ouvrez une session dans [Azure portal classique].
2.  Dans le coin inférieur gauche, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez **Les Services de données**, puis cliquez sur **stockage**, puis **Création rapide**.

    La boîte de dialogue **Créer un compte de stockage** s’affiche.

    ![Créer le compte de stockage][create-new-storage-account]

4. Dans le champ **URL** , tapez un nom de sous-domaine. Cette entrée peut contenir des lettres minuscules et des nombres 24-3.

    Cette valeur devient le nom d’hôte dans l’URI qui est utilisée pour adresser les Blob, file d’attente ou un tableau des ressources pour l’abonnement. Pour répondre à une ressource de conteneur dans le service d’objet Blob, vous utiliseriez un URI au format suivant, où * &lt;StorageAccountLabel&gt; * fait référence à la valeur que vous avez tapé dans la zone **Entrez une URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt; *

    **Important :** Le libellé de l’URL du sous-domaine du compte de stockage URI de formulaires et doit être unique parmi tous les services hébergés dans Azure.

    Cette valeur est également utilisée comme le nom de ce compte de stockage dans le portail, ou lors de l’accès par programme de ce compte.

5.  Dans la liste déroulante **Région/affinité du groupe** , sélectionnez une région ou un groupe d’affinité pour le compte de stockage. Sélectionnez un groupe d’affinité au lieu d’une zone Si vous souhaitez que vos services de stockage dans le même centre de données avec d’autres services Windows Azure que vous utilisez. Cela peut améliorer les performances, et aucun frais ne sont supportés pour la sortie.  

    **Remarque :** Pour créer un groupe d’affinité, ouvrir la zone **paramètres** du portail de gestion, cliquez sur **groupes d’affinité**et puis cliquez sur **Ajouter un groupe d’affinité** ou **Ajouter**. Vous pouvez également créer et gérer des groupes d’affinité à l’aide de l’API de gestion de Service de Windows Azure. Pour plus d’informations, reportez-vous à la section [opérations sur des groupes d’affinité].

6. Dans la liste déroulante **d’abonnement** , sélectionnez l’abonnement qui sera utilisé avec le compte de stockage.
7.  Cliquez sur **créer un compte de stockage**. Le processus de création du compte de stockage peut prendre plusieurs minutes.
8.  Pour vérifier que le compte de stockage a été créé avec succès, vérifiez que le compte s’affiche dans les éléments répertoriés pour **un stockage** avec le statut **en ligne**.

<a id="Step2"> </a>
<h2>Étape 2 : Créer un nouveau point de terminaison CDN pour votre compte de stockage</h2>

Une fois que vous activez l’accès à un compte de stockage CDN ou service hébergé, tous les objets accessibles sont éligibles pour la mise en cache de bord CDN. Si vous modifiez un objet qui est actuellement mis en cache dans le CDN, le nouveau contenu ne sera pas disponible via le CDN jusqu'à ce que le CDN actualise son contenu lors de l’expiration de la période de durée de vie contenue mis en cache.

**Pour créer un nouveau point de terminaison CDN pour votre compte de stockage**

1. Dans [Azure portal classique], dans le volet de navigation, cliquez sur **CDN**.

2. Sur le ruban, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau** , sélectionnez les **Services d’application**, puis **CDN**, puis **Création rapide**.

3. Dans la liste déroulante du **Domaine d’origine** , sélectionnez le compte de stockage que vous avez créé dans la section précédente dans la liste de vos comptes de stockage disponible. 

4. Cliquez sur le bouton **créer** pour créer le nouveau point de terminaison.

5. Une fois le point de terminaison est créé, il apparaît dans la liste des points de terminaison pour l’abonnement. La vue liste affiche l’URL à utiliser pour accéder aux contenu mis en cache, ainsi que le domaine d’origine. 

    Le domaine d’origine est l’emplacement à partir duquel le CDN met en cache de contenu. Le domaine d’origine peut être un compte de stockage ou un service en nuage ; un compte de stockage est utilisé pour les besoins de cet exemple. Contenu de stockage est mis en cache sur les serveurs edge en fonction des paramètres de contrôle du cache que vous spécifiez, ou à l’heuristique par défaut du réseau mise en cache. 


    > [AZURE.NOTE] La configuration créée pour le point de terminaison immédiatement ne sera pas disponible. Il peut prendre jusqu'à 60 minutes pour l’enregistrement de se propager à travers le réseau CDN. Les utilisateurs qui tentent d’utiliser le nom de domaine CDN immédiatement peuvent s’afficher code d’état 400 (demande incorrecte) jusqu'à ce que le contenu est disponible via le CDN.

<a id="Step3"> </a>
<h2>Étape 3 : Accès CDN contenu</h2> 

Pour accéder au contenu mis en cache sur le CDN, utiliser l’URL de CDN fourni dans le portail. L’adresse d’un objet blob mis en cache sera semblable au suivant :

http://< ;*CDNNamespace*\>.vo.msecnd.net/ <*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Étape 4 : Suppression du contenu du CDN</h2>

Si vous ne souhaitez plus mettre en cache un objet dans l’Azure réseau CDN (Content Delivery), vous pouvez prendre une des étapes suivantes :

-   Pour un blob Azure, vous pouvez supprimer l’objet blob à partir du conteneur public.
-   Vous pouvez rendre le conteneur privé et non public. Pour plus d’informations, voir [Restreindre l’accès à des conteneurs et des objets BLOB](https://azure.microsoft.com/documentation/articles/storage-manage-access-to-resources/#restrict-access-to-containers-and-blobs) .
-   Vous pouvez désactiver ou supprimer le point de terminaison CDN via le portail de gestion.
-   Vous pouvez modifier votre service hébergé pour ne plus répondre aux demandes de l’objet.

Un objet déjà mis en cache dans le CDN restera en mémoire cache jusqu'à l’expiration de la période de durée de vie de l’objet. Lorsque la période de durée de vie expire, le CDN vérifie si le point de terminaison CDN est toujours valide et l’objet toujours accessible de manière anonyme. S’il n’est pas le cas, l’objet ne sera pas plus caché.

La possibilité de purger immédiatement le contenu n’est actuellement pas pris en charge sur le portail de gestion Azure. Veuillez contacter le [support de Azure](https://azure.microsoft.com/support/options/) si vous souhaitez purger immédiatement le contenu. 

## <a name="additional-resources"></a>Ressources supplémentaires

-   [Comment faire pour créer un groupe d’affinité dans Azure]
-   [Comment : gérer des comptes de stockage pour un abonnement Azure]
-   [À propos de l’API de gestion de Service]
-   [Comment mapper le contenu CDN pour un domaine personnalisé]

  [Create Storage Account]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/
  [Emplacements de noeud Azure CDN]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Azure portal classique]: https://manage.windowsazure.com/
  [plan de facturation]: /pricing/calculator/?scenario=full
  [Comment faire pour créer un groupe d’affinité dans Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Overview of the Azure CDN]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [À propos de l’API de gestion de Service]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Comment mapper le contenu CDN pour un domaine personnalisé]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Previous Management Portal]: ../../Shared/Media/previous-portal.png
