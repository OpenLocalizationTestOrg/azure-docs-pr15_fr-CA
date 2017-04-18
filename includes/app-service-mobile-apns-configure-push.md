

1. Sur votre Mac, lancez le **Trousseau d’accès**. Ouvrir **Mes certificats** sous **catégorie** dans la barre de gauche navigationn. Trouver le certificat SSL que vous avez téléchargé dans la section précédente et fournir son contenu. Sélectionnez uniquement le certificat (ne sélectionnez pas la clé privée) et de [l’Exporter](https://support.apple.com/kb/PH20122?locale=en_US).

2. Dans le [portail Azure](https://portal.azure.com/), cliquez sur **Parcourir tous les** > **Application Services** > votre back-end de l’application Mobile. Sous **paramètres**, cliquez sur **Distribuer des Service d’application**, puis cliquez sur votre nom de concentrateur de notification. Aller à **Apple Push Notification Services** > **Télécharger le certificat**. Télécharger le fichier .p12, en sélectionnant le bon **Mode** (selon si votre client SSL certificat précédemment est Production ou Sandbox). Enregistrer les modifications.

Votre service est maintenant configuré pour fonctionner avec des notifications de type Pousser sur iOS !

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png
