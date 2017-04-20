<properties
    pageTitle="Résolution des problèmes de dégradation état sur Azure Traffic Manager"
    description="Comment faire pour résoudre des profils de Traffic Manager lorsqu’il se présente sous la forme d’une dégradation état."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Résolution des problèmes de dégradation état sur Azure Traffic Manager

Cet article décrit comment résoudre les problèmes d’un profil de gestionnaire de trafic Azure qui affiche un état dégradé. Dans ce scénario, considérez que vous avez configuré un profil de Traffic Manager pointant vers certains de vos services hébergés de cloudapp.net. Lorsque vous vérifiez l’état de santé de votre gestionnaire de trafic, vous voyez que l’état s’est dégradé.

![état dégradé](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Si vous passez dans l’onglet points de terminaison de ce profil, vous voyez un ou plusieurs points de terminaison avec un statut hors connexion :

![en mode hors connexion](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Présentation du Gestionnaire de trafic de sondes

- Traffic Manager considère comme un point de terminaison pour être en ligne que lorsque la sonde reçoit une réponse HTTP 200 par le chemin d’accès de la sonde. Toute autre réponse autre que 200 est un échec.
- Une redirection 30 x échoue, même si l’URL redirigée revient de 200.
- Pour les sondes de HTTPs, les erreurs de certificat sont ignorées.
- Le contenu réel de la trajectoire de la sonde n’a aucune importance, tant de 200 est retourné. Détection d’une URL vers un contenu statique comme « / / non-considération » est une technique courante. Contenu dynamique, comme les pages ASP, peut ne pas toujours retourne 200, même lorsque l’application est en bonne santé.
- Une meilleure solution consiste à définir le chemin d’accès de la sonde à quelque chose qui a assez logique pour déterminer que le site est vers le haut ou vers le bas. Dans l’exemple précédent, en définissant le chemin d’accès « / / non-considération », vous n’êtes test que w3wp.exe est de répondre. Cette sonde peut ne pas indique que votre application web est saine. Serait une meilleure option pour définir un chemin d’accès d’un quelque chose par exemple « / Probe.aspx » qui dispose d’une logique pour déterminer l’état de santé du site. Par exemple, vous pourriez utiliser les compteurs de performance à l’utilisation de l’UC ou mesurer le nombre de demandes ayant échoué. Ou bien, vous pourriez tenter d’accéder aux ressources de la base de données ou de l’état de session pour vous assurer que l’application web fonctionne.
- Si tous les points de terminaison dans un profil sont défectueux, puis Traffic Manager traite tous les points de terminaison comme sain et le trafic des itinéraires vers tous les points de terminaison. Ce comportement garantit que les problèmes avec le mécanisme de détection n’aboutissent pas à une panne complète de votre service.

## <a name="troubleshooting"></a>Résolution des problèmes

Pour résoudre un échec de sonde, vous avez besoin d’un outil qui affiche le code d’état HTTP retour à partir de l’URL de la sonde. Il existe de nombreux outils qui vous indiquent la réponse HTTP brute.

* [Fiddler](http://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

En outre, vous pouvez utiliser l’onglet réseau des outils de débogage F12 dans Internet Explorer pour afficher les réponses HTTP.

Pour cet exemple, nous voulons voir la réponse de nos URL sonde : http://watestsdp2008r2.cloudapp.net:80/sonde. L’exemple de PowerShell suivant illustre le problème.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Exemple de sortie :

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Notez que nous avons reçu une réponse de redirection. Comme indiqué précédemment, n’importe quel StatusCode autre que 200 est considéré comme un échec. Traffic Manager modifie l’état du point de terminaison sur hors connexion. Pour résoudre ce problème, vérifiez la configuration de site Web pour vous assurer que la bonne StatusCode peut retourner le chemin d’accès de la sonde. Reconfigurez la sonde Traffic Manager pour pointer vers un chemin d’accès qui renvoie de 200.

Si votre sonde utilise le protocole HTTPS, vous devrez peut-être désactiver la vérification pour éviter les erreurs SSL/TLS au cours de votre test de certificats. Les instructions PowerShell suivantes désactiver la validation de certificat pour la session en cours de PowerShell :

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Étapes suivantes

[À propos des méthodes de routage de trafic Traffic Manager](traffic-manager-routing-methods.md)

[Quel est le Traffic Manager](traffic-manager-overview.md)

[Services en nuage](http://go.microsoft.com/fwlink/?LinkId=314074)

[Applications Web Azure](https://azure.microsoft.com/documentation/services/app-service/web/)

[Opérations sur le Gestionnaire de trafic (référence des API reste)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Gestionnaire de trafic Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
