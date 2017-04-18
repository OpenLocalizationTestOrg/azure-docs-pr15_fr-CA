###<a name="server-auth"></a>Comment : s’authentifier auprès d’un fournisseur (flux de serveur)

Pour que les applications Mobile à gérer le processus d’authentification dans votre application, vous devez enregistrer votre application auprès de votre fournisseur d’identité. Puis dans votre Service d’application Azure, vous devez configurer l’ID de l’application et du secret fourni par votre fournisseur.
Pour plus d’informations, consultez le didacticiel [authentification ajouter à votre application].

Une fois que vous avez enregistré votre fournisseur d’identité, appelez simplement la méthode .login() avec le nom de votre fournisseur. Par exemple, se connecter avec Facebook utiliser le code suivant.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Si vous utilisez un fournisseur d’identité que Facebook, modifiez la valeur passée à la méthode de connexion ci-dessus pour une des opérations suivantes : `microsoftaccount`, `facebook`, `twitter`, `google`, ou `aad`.

Dans ce cas, Service d’application Azure gère le flux d’authentification OAuth 2.0 en affichant la page de connexion du fournisseur sélectionné et en générant un jeton d’authentification de Service d’application après la connexion réussie avec le fournisseur d’identité. La fonction d’ouverture de session, lorsque vous avez terminé, retourne un objet JSON (utilisateur) qui expose les ID utilisateur et le Service d’application jeton d’authentification dans les champs nom d’utilisateur et authenticationToken, respectivement. Ce jeton peut être mis en cache et réutilisé jusqu'à son expiration.

###<a name="client-auth"></a>Comment : s’authentifier auprès d’un fournisseur (flux Client)

Votre application peut également indépendamment de contacter le fournisseur d’identité et fournissez le jeton retourné à votre application de Service pour l’authentification. Ce flux client vous permet de fournir une expérience de connexion unique pour les utilisateurs ou pour extraire des données de l’utilisateur supplémentaires à partir du fournisseur d’identité.

#### <a name="social-authentication-basic-example"></a>Exemple de base de l’authentification social

Cet exemple utilise un client de Facebook SDK pour l’authentification :

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Cet exemple suppose que le jeton fourni par le Kit de développement logiciel de fournisseur respectif est stocké dans la variable de jeton.

#### <a name="microsoft-account-example"></a>Exemple de Account Microsoft

L’exemple suivant utilise le Live SDK, qui prend en charge single-sign-on pour les applications Windows Store à l’aide de Microsoft Account :

```
WL.login({ scope: "wl.basic"}).then(function (result) {
      client.login(
            "microsoftaccount",
            {"authenticationToken": result.session.authentication_token})
      .done(function(results){
            alert("You are now logged in as: " + results.userId);
      },
      function(error){
            alert("Error: " + err);
      });
});
```

Cet exemple obtient un jeton de Live Connect, qui est fourni au Service de votre application en appelant la fonction d’ouverture de session.

###<a name="auth-getinfo"></a>Comment : obtenir des informations sur l’utilisateur authentifié

Les informations d’authentification de l’utilisateur actuel peuvent être récupérées à partir de la `/.auth/me` le point de terminaison à l’aide de n’importe quelle méthode d’AJAX.  Veillez à définir le `X-ZUMO-AUTH` en-tête à votre jeton d’authentification.  Le jeton d’authentification est stocké dans `client.currentUser.mobileServiceAuthenticationToken`.  Par exemple, pour utiliser l’API d’extraction :

```
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

FETCH est disponible sous la forme d’un package npm ou à partir de CDNJS de navigateur. Vous pouvez également utiliser une autre API AJAX ou jQuery pour extraire les informations.  Les données seront reçues sous la forme d’un objet JSON.
