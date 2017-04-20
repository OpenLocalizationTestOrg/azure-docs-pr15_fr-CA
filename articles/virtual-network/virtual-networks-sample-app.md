<properties
   pageTitle="Exemple d’Application pour une utilisation avec les environnements de limite de sécurité | Microsoft Azure"
   description="Déployer cette application web simple après la création d’un réseau de périmètre pour tester les scénarios de flux de trafic"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# <a name="sample-application-for-use-with-security-boundary-environments"></a>Exemple d’Application pour une utilisation avec les environnements de limite de sécurité

[Revenir à la Page de pratiques meilleure sécurité limite][HOME]

Ces scripts PowerShell peuvent s’exécuter localement sur les serveurs IIS01 et AppVM01 pour installer et configurer une application web très simple qui affiche une page html à partir du serveur de IIS01 front-end avec le contenu d’un serveur principal AppVM01.

Cette application sera fournit un environnement de test simple pour un grand nombre d’exemples de la DMZ et comment les modifications sur les points de terminaison, NSGs, UDR et pare-feu règles affecte les flux de trafic.

## <a name="firewall-rule-to-allow-icmp"></a>Règle de pare-feu pour autoriser ICMP
Cette instruction PowerShell simple peut être exécutée sur tout ordinateur virtuel de Windows pour autoriser le trafic ICMP (Ping). Ainsi, pour les tests et la résolution des problèmes en autorisant le protocole ping plus facile de passer par le pare-feu windows (pour la plupart des distros de Linux QU'ICMP est activée par défaut).

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Remarque :** Si vous utilisez le dessous des scripts, cet ajout de règle de pare-feu est la première instruction.

## <a name="iis01---web-application-installation-script"></a>IIS01 - Script d’Installation Web Application
Ce script est :

1.  Ouvrez IMCPv4 (Ping) sur le pare-feu windows de serveur local pour tester plus facilement
2.  Installer IIS et le .net Framework 4.5
3.  Créer une page web d’ASP.NET et un fichier Web.config
4.  Modifier le pool d’applications par défaut pour faciliter l’accès aux fichiers
5.  Définir l’utilisateur anonyme pour votre compte d’administrateur et le mot de passe

Ce script PowerShell doit être exécuté localement, alors que RDP avait dans IIS01.

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01 - fichier Script d’Installation serveur
Ce script configure le back-end pour cette application simple. Ce script est :

1.  Ouvrez IMCPv4 (Ping) sur le pare-feu pour des tests plus facile
2.  Créer un nouveau répertoire
3.  Créez un fichier texte à distance accès par la page web ci-dessus
4.  Définir des autorisations sur le répertoire et le fichier pour autoriser l’accès anonyme
5.  Désactiver la sécurité renforcée d’Internet Explorer pour autoriser la navigation plus facile à partir de ce serveur 

>[AZURE.IMPORTANT] **Meilleures pratiques**: ne jamais désactiver la sécurité renforcée d’Internet Explorer sur un serveur de production, plus il est généralement une mauvaise idée pour surfer sur le web à partir d’un serveur de production. En outre, ouvrir des partages de fichiers pour l’accès anonyme est une mauvaise idée, mais terminé ici par souci de simplicité.

Ce script PowerShell doit être exécuté localement, alors que RDP avait dans AppVM01. PowerShell est nécessaire pour être exécuté en tant qu’administrateur afin de garantir la réussite de l’exécution.
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## <a name="dns01---dns-server-installation-script"></a>DNS01 - Script d’Installation serveur DNS
Il n’y a aucun script inclus dans cet exemple d’application vous permet de configurer le serveur DNS. Si le test de règles de pare-feu, NSG ou UDR doit inclure le trafic DNS, le serveur de DNS01 doit être configuré manuellement. Le fichier xml de Configuration du réseau pour les deux exemples inclut DNS01 en tant que serveur DNS principal et le serveur DNS public hébergé par le niveau 3 en tant que serveur DNS secondaire. Le serveur DNS de 3 niveau serait être le serveur DNS utilisé pour le trafic non local et avec DNS01 pas le programme d’installation, aucun DNS local se produirait.

<!--Link References-->
[HOME]: ../best-practices-network-security.md
