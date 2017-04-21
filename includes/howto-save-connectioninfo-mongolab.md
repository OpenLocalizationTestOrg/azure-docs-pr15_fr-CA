Alors qu’il est possible de coller un URI MongoLab dans votre code, nous vous recommandons de configuration dans l’environnement pour faciliter la gestion. De cette façon, si l’URI est modifié, vous pouvez mettre à jour via le portail Azure sans passer par le code.


1. Dans le portail d’Azure, sélectionnez **Les applications Web**.
1. Cliquez sur le nom de l’application web dans la liste des applications Web.  
![WebAppEntry][entry-website]  
Le tableau de bord Web App s’affiche.

1. Dans la barre de menus, cliquez sur **configurer** .  
![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]

1. Faites défiler jusqu'à la section de chaînes de connexion.  
![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]

1. Pour **nom**, entrez MONGOLAB_URI.
1. Pour **valeur**, collez la chaîne de connexion que nous avons obtenus dans la section précédente.
1. Sélectionnez **Personnaliser** dans la liste déroulante **Type** (au lieu de la valeur par défaut **SQLAzure**).
1. Dans la barre d’outils, cliquez sur **Enregistrer** .  
![SaveWebApp][button-website-save]

**Remarque :** Azure ajoute la **CUSTOMCONNSTR\_ ** de préfixe à cette variable, ce qui explique pourquoi le code ci-dessus références **CUSTOMCONNSTR\_MONGOLAB_URI.**

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png
