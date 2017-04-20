Une fois les enregistrements de votre nom de domaine ont été propagées, vous devez les associer à votre application Web. Utilisez les étapes suivantes pour activer les noms de domaine à l’aide de votre navigateur web.

> [AZURE.NOTE] Il peut prendre un certain temps pour les enregistrements TXT créé dans les étapes précédentes pour se propager à travers le système DNS. Impossible d’ajouter le nom de domaine pour votre application web jusqu'à ce que l’enregistrement TXT a propagé. Si vous utilisez un enregistrement A, vous ne pouvez pas ajouter le nom de domaine enregistrement A pour votre application web jusqu'à ce que l’enregistrement TXT créé à l’étape précédente a propagé.
>
> Vous pouvez utiliser un service de <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> pour vérifier que l’enregistrement TXT est disponible.

1. Dans votre navigateur, ouvrez le [Portail Azure](https://portal.azure.com).

2. Dans l’onglet **Applications du Web** , cliquez sur le nom de votre application web et sélectionnez **les domaines personnalisés**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. Dans la lame **Custom domains** , cliquez sur le **nom d’hôte de l’ajouter**.
    
4. Permet d’entrer les noms de domaine à associer à cette application web, utilisez les zones de texte de **nom d’hôte** .

    ![](./media/custom-dns-web-site/add-custom-domain.png)

6.  Cliquez sur **Valider**.

7.  Après avoir cliqué sur **Valider** Azure va lancer des processus de vérification du domaine. Il vérifiera propriétaire de domaine ainsi que les succès de rapport et de la disponibilité du nom d’hôte ou erreur détaillé avec des guidence comment corriger l’erreur.    

À ce stade, vous devez pouvoir Entrez le nom de domaine personnalisé dans votre navigateur et voir nécessaire avec succès à votre application web.
