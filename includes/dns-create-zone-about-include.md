Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier. Pour démarrer l’hébergement de votre domaine, vous devez créer une zone DNS. Tout enregistrement DNS créé pour un domaine particulier sera à l’intérieur d’une zone DNS pour le domaine. 

Par exemple, le domaine « contoso.com » peut contenir un nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web). 


## <a name="names"></a>À propos des noms de zone DNS
 
- Le nom de la zone doit être unique dans le groupe de ressources, et que la zone ne doit pas déjà exister. Dans le cas contraire, l’opération échouera.

- Le même nom de zone peut être réutilisé dans un groupe de ressources différent ou un autre abonnement Azure. 

- Dans le cas où plusieurs zones partagent le même nom, chaque instance est affecté les adresses de serveur de nom différent, et qu’une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez [Delegate un domaine DNS d’Azure](../articles/dns/dns-domain-delegation.md).