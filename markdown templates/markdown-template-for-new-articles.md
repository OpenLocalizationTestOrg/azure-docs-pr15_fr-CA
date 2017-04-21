<properties
   pageTitle="Titre de la page qui s’affiche dans les résultats de recherche et onglet navigateur"
   description="Description de l’article qui sera affichée sur les pages d’accueil et dans la plupart des résultats de la recherche"
   services="service-name"
   documentationCenter="dev-center-name"
   authors="GitHub-alias-of-only-one-author"
   manager="manager-alias"
   editor=""/>

<tags
   ms.service="required"
   ms.devlang="may be required"
   ms.topic="article"
   ms.tgt_pltfrm="may be required"
   ms.workload="required"
   ms.date="mm/dd/yyyy"
   ms.author="Your MSFT alias or your full email address;semicolon separates two or more"/>

# <a name="markdown-template-article-heading-1-or-h1-heading-at-the-top-of-the-article"></a>Modèle de démarque (article titre 1 ou H1) : titre en haut de l’article

Pour copier les démarques à partir de ce modèle, copiez l’article dans votre local mis en pension, ou cliquez sur le bouton Raw dans l’UI GitHub et copier la démarque.

  ![Texte de remplacement ; ne pas laisser vide. Décrire l’image.][8]

Paragraphe d’introduction : Lorem dolor amet, adipiscing public concerné. Phasellus risus de nulla interdum, lacinia Portal nisl imperdiet fichier .sed. Mauris dolor mauris, tempus sed lacinia nec, felis de non euismod. Nées siège Portal ultrices. Maecenas neque nulla, condimentum un curriculum vitae ipsum sit amet, aliquet de dignissim nisi.

## <a name="heading-2-h2"></a>Titre 2 (H2)

Aenean sit amet leo nec purus odio de gravida placerat fermentum CA. Aenean tellus lectus, faucibus dans rhoncus dans faucibus sed urna.  volutpat mi id purus ultrices iaculis nec non neque. [Texte du lien pour le lien en dehors de azure.microsoft.com](http://weblogs.asp.net/scottgu). Dolor dictum de Nullam à aliquam pharetra. Vivamus CA hendrerit mauris [exemple de texte de lien pour le lien vers un article dans un dossier de service](../articles/expressroute/expressroute-bandwidth-upgrade.md).

Obtenir plus de 10 fois le trafic à partir de [Google]  [ gog] qu’à partir de [Yahoo]  [ yah] ou [MSN] [msn].

> [AZURE.NOTE] Texte de la note en retrait.  Le mot « note » sera ajouté au cours de la publication. Ut UE pretium lacus. Nullam purus est, iaculis sed est veau, euismod vehicula odio. Curabitur lacinia, erat tristique iaculis rutrum, nisi erat-sem-sodales, turpis de condimentum de l’Union européenne nisi un purus.

1. Aenean sit amet leo nec odio de gravida **Purus** placerat fermentum CA.

2. Aenean tellus lectus, faucius dans **Rhoncus** dans faucibus sed urna. Suspendisse volutpat mi id purus ultrices iaculis nec non neque.

    ![Texte de remplacement ; ne pas laisser vide. Voiture de collecteur en rouge de la course.][5]

3. Dolor dictum de Nullam à aliquam pharetra. Vivamus CA hendrerit mauris. Sed dolor dui, condimentum et varius, un vehicula à nisl.

    ![Texte de remplacement ; ne pas laisser vide][6]


Suspendisse volutpat mi id purus ultrices iaculis nec non neque. Dolor dictum de Nullam à aliquam pharetra. Vivamus CA hendrerit mauris. Otrus informatus : [1 lien à une autre rubrique de documentation azure.microsoft.com](virtual-machines-windows-hero-tutorial.md)

## <a name="heading-h2"></a>Titre (H2)

Ut UE pretium lacus. Nullam purus est, iaculis sed est veau, euismod vehicula odio.

1. Curabitur lacinia, erat tristique iaculis rutrum, nisi erat-sem-sodales, turpis de condimentum de l’Union européenne nisi un purus.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. Vestibulum ante ipsum primis dans faucibus orci luctus et ultrices posuere cubilia.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }


    > [AZURE.NOTE] Duis sed diam non <i>nisl molestie</i> pharetra eget une est. [Lien 2 à une autre rubrique de documentation azure.microsoft.com](web-sites-custom-domain-name.md)


Quisque commodo eros veau lectus euismod auctor eget sit amet leo. Proin faucibus suscipit tellus dignissim ultrices.

## <a name="heading-2-h2"></a>Titre 2 (H2)

1. Maecenas condimentum de sed nisi. Potenti de Suspendisse.

  + Fusce
  + Malesuada
  + SEM

2. Nullam de hendrerit de tempus tellus massa l’Union européenne.

    ![Texte de remplacement ; ne pas laisser vide. Exemple d’une vidéo de Channel 9.][7]

3. Quisque felis enim, fermentum ut aliquam nec, pellentesque pulvinar magna.




<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Vestibul ante ipsum primis dans faucibus orci luctus et ultrices posuere cubilia Curae ; Nullam ultricies, ipsum vitae volutpat hendrerit, eros de pretium purus diam, un curriculum vitae tincidunt nulla lorem sed turpis : [3 lien à une autre rubrique de documentation azure.microsoft.com](storage-whatis-account.md).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/        
[yah]: http://search.yahoo.com/  
[msn]: http://search.msn.com/    
