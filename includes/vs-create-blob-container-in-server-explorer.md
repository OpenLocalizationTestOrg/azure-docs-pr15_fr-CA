Vous pouvez créer des files d’attente de stockage Azure à l’aide de l' **Explorateur de serveurs**Visual Studio.

![BLOB de l’Explorateur de serveurs][Image1]

1. Dans le menu **affichage** , choisissez **Explorateur de serveurs**.
2. Dans l’Explorateur de serveurs, développez le nœud **Azure** pour votre abonnement, développez le nœud de **stockage** et le nœud pour le compte de stockage que vous avez spécifié dans le service de stockage Azure connecté.
3. Sélectionnez le nœud **files d’attente** et choisissez de **Créer une file d’attente** dans le menu contextuel.
4. Entrez un nom pour le conteneur et cliquez sur **OK**.   

Par défaut, le nouveau conteneur est privé et vous devez spécifier votre clé d’accès pour télécharger les objets BLOB de ce conteneur de stockage. Si vous souhaitez que les fichiers soient dans le conteneur public, sélectionnez le conteneur dans **l’Explorateur de serveurs** et appuyez sur la touche `F4` pour afficher la fenêtre **Propriétés** . L' **accès Public à lire** la valeur **Blob**. Toute personne sur Internet peut voir les objets BLOB dans un conteneur public, mais vous pouvez les modifier ou les supprimer que si vous disposez de la clé d’accès approprié.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png