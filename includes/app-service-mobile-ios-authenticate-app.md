**Objective-C**: 

1. Sur votre Mac, ouvrez _QSTodoListViewController.m_ dans Xcode et ajoutez la méthode suivante. Modifier _google_ à _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ si vous n’utilisez pas de Google comme votre fournisseur d’identité. Si vous utilisez Facebook, [vous devrez d’autorisation des domaines de Facebook dans votre application](https://developers.facebook.com/docs/ios/ios9#whitelist).

            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
            
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }


2. Remplacer `[self refresh]` dans `viewDidLoad` dans _QSTodoListViewController.m_ , avec les éléments suivants :

            [self loginAndGetData];

3. Appuyez sur _exécuter_ pour démarrer l’application, puis ouvrez une session. Lorsque vous êtes connecté, il se peut que vous devez être en mesure d’afficher la liste Todo et mettre à jour.

**Swift**:

1. Sur votre Mac, ouvrez _ToDoTableViewController.swift_ dans Xcode et ajoutez la méthode suivante. Modifier _google_ à _microsoftaccount_, _twitter_, _facebook_ou _windowsazureactivedirectory_ si vous n’utilisez pas de Google comme votre fournisseur d’identité. Si vous utilisez Facebook, [vous devrez d’autorisation des domaines de Facebook dans votre application](https://developers.facebook.com/docs/ios/ios9#whitelist).
        
            func loginAndGetData() {
                
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
                
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }


2. Supprimer les lignes de `self.refreshControl?.beginRefreshing()` et `self.onRefresh(self.refreshControl)` à la fin de `viewDidLoad()` dans _ToDoTableViewController.swift_. Ajoutez un appel à `loginAndGetData()` à leur place :

            loginAndGetData()

3. Appuyez sur _exécuter_ pour démarrer l’application, puis ouvrez une session. Lorsque vous êtes connecté, il se peut que vous devez être en mesure d’afficher la liste Todo et mettre à jour.
