<properties
   pageTitle="Erste Schritte mit Azure DNS | Microsoft Azure"
   description="Erfahren Sie, wie Sie DNS-Zonen für Azure DNS erstellen. Dies ist eine schrittweise Anleitung für die Erstellung der ersten DNS-Zone, um mit dem Hosten der DNS-Domäne mithilfe der PowerShell zu beginnen."
   services="dns"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# Erste Schritte mit Azure DNS mithilfe der PowerShell

> [AZURE.SELECTOR]
- [Azure-Portal](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure-Befehlszeilenschnittstelle](dns-getstarted-create-dnszone-cli.md)

Die Domäne "contoso.com" kann mehrere DNS-Einträge, wie z. B. "mail.contoso.com" (für einen E-Mail-Server) und "www.contoso.com" (für eine Website), enthalten. Eine DNS-Zone wird zum Hosten der DNS-Einträge für eine bestimmte Domäne verwendet.

Um mit dem Hosten der Domäne zu beginnen, müssen wir zunächst eine DNS-Zone erstellen. Alle DNS-Einträge, die für eine bestimmte Domäne erstellt wurden, befinden sich in einer DNS-Zone für die Domäne.

Für diese Anweisungen wird Microsoft Azure PowerShell verwendet. Achten Sie darauf, dass Sie auf das neueste Azure PowerShell aktualisieren, um die Azure DNS-Cmdlets zu verwenden. Die gleichen Schritte können auch mithilfe des Azure-Portals oder der plattformübergreifenden Befehlsschnittstelle durchgeführt werden.

Die folgenden Schritte müssen ausgeführt werden, bevor Sie Azure DNS mit Azure PowerShell verwalten können.

Azure DNS verwendet den Azure-Ressourcen-Manager (ARM). Führen Sie diese Schritte für Azure PowerShell ab Version 1.0.0 aus. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md).

### Schritt 1
Melden Sie sich bei Ihrem Azure-Konto an (Sie werden zur Authentifizierung mit Ihren Anmeldeinformationen aufgefordert).

	PS C:\> Login-AzureRmAccount

### Schritt 2
Wählen Sie aus, welches Azure-Abonnement Sie verwenden möchten.

	PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

Sie können die verfügbaren Abonnements mit „Get-AzureRmSubscription“ auflisten.

### Schritt 3
Erstellen Sie eine neue Ressourcengruppe. (Überspringen Sie diesen Schritt, wenn Sie eine vorhandene Ressourcengruppe verwenden.)

	PS C:\> New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"

Der Azure-Ressourcen-Manager erfordert, dass alle Ressourcengruppen einen Speicherort angeben. Dieser wird als Standardspeicherort für Ressourcen in dieser Ressourcengruppe verwendet. Da alle DNS-Ressourcen global und nicht regional sind, hat die Auswahl des Speicherorts für die Ressourcengruppe jedoch keine Auswirkungen auf Azure DNS.

### Schritt 4
Der Azure DNS-Dienst wird vom Ressourcenanbieter "Microsoft.Network" verwaltet. Ihr Azure-Abonnement muss für die Verwendung dieses Ressourcenanbieters registriert werden, bevor Sie Azure DNS verwenden können. Dieser Schritt muss für jedes Abonnement einmal ausgeführt werden.

	PS c:> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

## Etags und Tags

### Etags
Angenommen, zwei Personen oder zwei Prozesse versuchen, einen DNS-Eintrag zur gleichen Zeit zu ändern. Welcher hat Vorrang? Und weiß derjenige, dass er gerade die Änderungen einer anderen Person überschrieben hat?

Azure DNS verwendet Etags, um gleichzeitige Änderungen an derselben Ressource sicher zu handhaben. Jeder DNS-Ressourcen (Zone oder Datensatzgruppe) ist ein Etag zugeordnet. Sobald eine Ressource abgerufen wird, wird auch sein Etag abgerufen. Beim Aktualisieren einer Ressource haben Sie die Möglichkeit, die Etags wieder zurückzugeben, damit Azure DNS überprüfen kann, ob das Etag auf dem Server übereinstimmt. Da jedes Update einer Ressource dazu führt, dass das Etag erneut generiert wird, weist eine Nichtübereinstimmung des Etags darauf hin, dass eine gleichzeitige Änderung vorgenommen wurde. Etags werden auch beim Erstellen einer neuen Ressource verwendet, um sicherzustellen, dass die Ressource nicht bereits vorhanden ist.

Standardmäßig verwendet Azure DNS PowerShell Etags, um gleichzeitige Änderungen an Zonen und Datensatzgruppen zu blockieren. Der optionale Switch „-Overwrite“ kann verwendet werden, um Etag-Überprüfungen zu unterdrücken. In diesem Fall werden gleichzeitig vorgenommene Änderungen überschrieben.

Auf der Ebene der REST-API von Azure DNS werden Etags mithilfe von HTTP-Headern angegeben. Ihr Verhalten ist in der folgenden Tabelle angegeben:

|Header|Verhalten|
|------|--------|
|Keine|PUT ist immer erfolgreich (keine Etag-Prüfung)|
|If-match <etag>|PUT ist nur erfolgreich, wenn die Ressource vorhanden ist und das Etag übereinstimmt.|
|If-match * |PUT ist nur erfolgreich, wenn Ressourcen vorhanden sind.|
|If-none-match |* PUT ist nur erfolgreich, wenn die Ressource nicht vorhanden ist.|

### Tags
Tags unterscheiden sich von Etags. Tags sind eine Liste von Name-Wert-Paaren, die vom Azure-Ressourcen-Manager zum Beschriften von Ressourcen zu Abrechnungs- oder Gruppierungszwecken verwendet werden. Weitere Informationen zu Tags finden Sie unter "Verwenden von Tags zum Organisieren von Azure-Ressourcen". Azure DNS PowerShell unterstützt Tags für Zonen und Datensatzgruppen, die mit den Optionsparametern "-Tag" angegeben wurden. Das folgende Beispiel zeigt, wie Sie eine DNS-Zone mit zwei Tags erstellen, "project = demo" und "env = test":

	PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )


## Erstellen einer DNS-Zone

Eine DNS-Zone wird mit dem Cmdlet „New-AzureRmDnsZone“ erstellt. Im folgenden Beispiel erstellen wir eine DNS-Zone namens "contoso.com" in der Ressourcengruppe namens "MyResourceGroup":<BR>

	PS C:\> New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

>[AZURE.NOTE] In Azure DNS sollten Zonennamen ohne abschließenden "." angegeben werden. Beispiel: "contoso.com" anstatt "contoso.com.".<BR>


Die DNS-Zone wurde nun in Azure DNS erstellt. Beim Erstellen einer DNS-Zone werden auch die folgenden DNS-Einträge erstellt:<BR>

- Der "Start of Authority" (SOA)-Eintrag. Dieser ist im Stamm jeder DNS-Zone vorhanden.
- Die autoritativen Namenserver (NS)-Einträge. Diese zeigen, welche Namenserver die Zone hosten. Azure DNS verwendet einen Pool von Namenservern, sodass verschiedene Namenserver verschiedenen Zonen in Azure DNS zugewiesen werden können. Weitere Informationen finden Sie unter [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md).<BR>

Verwenden Sie „Get-AzureRmDnsRecordSet“, um diese Einträge anzuzeigen:

	PS C:\> Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
	RecordType        : SOA
	Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
	Tags              : {}

	Name              : @
	ZoneName          : contoso.com
	ResourceGroupName : MyResourceGroup
	Ttl               : 3600
	Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
	RecordType        : NS
	Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
	Tags              : {}

>[AZURE.NOTE] Datensatzgruppen am Stamm (oder der "Spitze") einer DNS-Zone verwenden "@" als Datensatzgruppennamen.


Nach der Erstellung Ihrer ersten DNS-Zone können Sie sie mit DNS-Tools wie nslookup, dig oder mit dem [Resolve-DnsName PowerShell-Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) testen.<BR>

Wenn Sie Ihre Domäne noch nicht delegiert haben, um die neue Zone in Azure DNS zu verwenden, müssen Sie die DNS-Abfrage direkt auf einen der Namenserver für die Zone leiten. Die Namenserver für die Zone werden in den NS-Einträgen angegeben, wie von „Get-AzureRmDnsRecordSet“ oben aufgeführt. Achten Sie darauf, die korrekten Werte für die Zone im nachstehenden Befehl zu ersetzen.<BR>

		C:\> nslookup
		> set type=SOA
		> server ns1-01.azure-dns.com
		> contoso.com

		Server: ns1-01.azure-dns.com
		Address:  208.76.47.1

		contoso.com
        		primary name server = ns1-01.azure-dns.com
        		responsible mail addr = msnhst.microsoft.com
        		serial  = 1
        		refresh = 900 (15 mins)
        		retry   = 300 (5 mins)
        		expire  = 604800 (7 days)
        		default TTL = 300 (5 mins)

## Nächste Schritte

[Erste Schritte beim Erstellen von Datensatzgruppen und Einträgen](dns-getstarted-create-recordset.md)<BR> [Verwalten von DNS-Zonen](dns-operations-dnszones.md)<BR> [Verwalten von DNS-Einträgen](dns-operations-recordsets.md)<BR> [Automatisieren von Azure-Vorgängen mit dem .NET SDK](dns-sdk.md)<BR> [Referenz zur Azure DNS-REST-API](https://msdn.microsoft.com/library/azure/mt163862.aspx)
 

<!---HONumber=AcomDC_0406_2016-->