## <a name="download-and-understand-the-arm-template"></a>Téléchargez et comprendre le modèle ARM

Vous pouvez télécharger le modèle ARM existant pour la création d’un VNet et deux sous-réseaux à partir de github, apportez les modifications, vous pouvez souhaitez et réutiliser. Pour ce faire, suivez les étapes ci-dessous.

1. Accédez à [la page modèle d’exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Cliquez sur **azuredeploy.json**, puis cliquez sur **RAW**.
3. Enregistrez le fichier à un un dossier local sur votre ordinateur.
4. Si vous êtes familiarisé avec les modèles ARM, passez à l’étape 7.
5. Ouvrez le fichier que vous venez d’enregistrer et de consulter le contenu sous les **paramètres** dans la ligne 5. Les paramètres de modèle ARM fournissent un espace réservé pour les valeurs qui peuvent être remplis au cours du déploiement.

    | Paramètre | Description |
    |---|---|
    | **emplacement** | Région Azure où sera créé le VNet |
    | **vnetName** | Nom de la nouvelle VNet |
    | **addressPrefix** | Espace d’adressage pour le VNet, au format CIDR |
    | **subnet1Name** | Nom de la première VNet |
    | **subnet1Prefix** | Bloc CIDR pour le premier sous-réseau |
    | **subnet2Name** | Nom de la seconde VNet |
    | **subnet2Prefix** | Bloc CIDR du deuxième sous-réseau |

    >[AZURE.IMPORTANT] Modèles ARM mis à jour dans github peuvent changer avec le temps. Vérifiez le modèle avant de l’utiliser.
    
6. Vérifiez le contenu sous **ressources** et notez les éléments suivants :

    - **type**. Type de ressource qui est créé par le modèle. Dans ce cas, **Microsoft.Network/virtualNetworks**, qui représentent un VNet.
    - **nom**. Nom de la ressource. Notez l’utilisation de **[parameters('vnetName')]**, qui signifie que le nom sera fourni en entrée par l’utilisateur ou d’un fichier de paramètres au cours du déploiement.
    - **Propriétés**. Liste des propriétés de la ressource. Ce modèle utilise les propriétés de l’espace et de sous-réseau adresse lors de la création de VNet.

7. Accédez à [la page de modèle d’exemple](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Cliquez sur **azuredeploy-paremeters.json**, puis cliquez sur **RAW**.
9. Enregistrez le fichier à un un dossier local sur votre ordinateur.
10. Ouvrez le fichier que vous venez d’enregistrer et de modifier les valeurs pour les paramètres. Utilisez les valeurs ci-dessous pour déployer le VNet décrit dans notre scénario.

        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }

11. Enregistrez le fichier.
  