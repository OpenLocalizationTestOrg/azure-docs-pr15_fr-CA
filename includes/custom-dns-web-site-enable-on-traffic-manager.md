Une fois les enregistrements de votre nom de domaine ont été propagées, vous devez être en mesure d’utiliser votre navigateur pour vérifier que le nom de votre domaine personnalisé peut être utilisé pour accéder à votre application web dans le Service d’application Azure.

> [AZURE.NOTE] Il peut prendre un certain temps pour votre CNAME pour se propager à travers le système DNS. Vous pouvez utiliser un service tel que <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement CNAME n’est disponible.

Si vous n’avez pas encore ajouté de votre application web sous la forme d’un point de terminaison de Traffic Manager, vous devez le faire avant la résolution de noms fonctionnent, que les itinéraires de nom de domaine personnalisé au Gestionnaire de trafic. Traffic Manager achemine ensuite à votre application web. Pour ajouter votre application web sous la forme d’un point de terminaison dans votre profil de Traffic Manager, utilisez les informations dans [Ajouter ou supprimer les points de terminaison](../articles/traffic-manager/traffic-manager-endpoints.md) .

> [AZURE.NOTE] Si votre application web n’apparaît pas lors de l’ajout d’un point de terminaison, vérifiez qu’il est configuré pour le mode plan de Service d’application **Standard** . Vous devez utiliser le mode **Standard** pour votre application web pour fonctionner avec le Gestionnaire de trafic.

1. Dans votre navigateur, ouvrez le [Portail Azure](https://portal.azure.com).

1. Dans l’onglet **Applications du Web** , cliquez sur le nom de votre application web et sélectionnez **paramètres**puis sélectionnez **Custom domains**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

1. Dans la lame **Custom domains** , cliquez sur le **nom d’hôte de l’ajouter**.
    
1. Permet d’entrer le nom de domaine Traffic Manager à associer à cette application web, utilisez les zones de texte de **nom d’hôte** .

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

1. Cliquez sur **Valider** pour enregistrer la configuration de nom de domaine.

7.  Après avoir cliqué sur **Valider** Azure va lancer des processus de vérification du domaine. Il vérifiera propriétaire de domaine ainsi que les succès de rapport et de la disponibilité du nom d’hôte ou erreur détaillé avec des guidence comment corriger l’erreur.    

8.  Si la validation réussit **Ajouter hostname** bouton devient actif et vous pourrez le nom d’hôte de l’affectation. Accédez maintenant à votre nom de domaine personnalisé dans un navigateur. Vous devez maintenant voir votre exécution de l’application à l’aide de votre nom de domaine personnalisé. 

    Une fois la configuration terminée, le nom de domaine personnalisé apparaît dans la section **noms de domaine** de votre application web.

À ce stade, vous devez pouvoir permet d’entrer le nom de domaine de Traffic Manager dans votre navigateur et voir nécessaire avec succès à votre application web.
