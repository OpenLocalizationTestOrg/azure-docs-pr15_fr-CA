Le service de sauvegarde d’Azure comprend deux types de coffres-forts - le coffre-fort de la sauvegarde et la chambre forte de Services de récupération. Le coffre-fort de sauvegarde reçue en premier. Puis le coffre-fort de Services de récupération arrivée de prendre en charge les déploiements de gestionnaire de ressources de développé. Microsoft recommande l’utilisation de déploiements du Gestionnaire de ressources, sauf si vous avez spécifiquement besoin d’un déploiement classique.

| **Déploiement** | **Portail** | **Stockage en chambre forte** |
|-----------|------|-----|
|Classique|[Classique](https://manage.windowsazure.com)|Sauvegarde|
|Gestionnaire de ressources|[Azure](https://portal.azure.com)|Services de restauration|

> [AZURE.NOTE] Coffres-forts de sauvegarde ne peut pas protéger les solutions déployées par le Gestionnaire de ressources. Toutefois, vous pouvez utiliser un coffre-fort de Services de récupération pour protéger la variation déployé des serveurs et des ordinateurs virtuels.  
