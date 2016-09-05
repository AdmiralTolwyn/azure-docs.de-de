<properties
   pageTitle="Mit PowerShell im klassischen Bereitstellungsmodell Ihre Reverse-DNS-Einträge für Ihre Dienste verwalten | Microsoft Azure"
   description="Mit PowerShell im klassischen Bereitstellungsmodell Reverse-DNS-Einträge oder PTR-Einträge für Azure-Dienste verwalten "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/09/2016"
   ms.author="smalone" />

# Mit PowerShell Ihre Reverse-DNS-Einträge für Ihre Dienste (klassisch) verwalten

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)] <BR> [AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)] <BR> [AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] Erfahren Sie, wie Sie [diese Schritte mit dem Resource Manager-Modell ausführen](dns-reverse-dns-record-operations-ps.md).

## Überprüfung der Reverse-DNS-Einträge
Um sicherzustellen, dass kein Dritter Reverse-DNS-Einträge erstellen kann, die Ihren Domänen zugeordnet sind, erlaubt Azure die Erstellung von Reverse-DNS-Einträgen nur, wenn Folgendes zutrifft:

- Der Reverse-DNS-FQDN ist der Name des Clouddiensts, für den er angegeben wurde, oder ein beliebiger Clouddienstname innerhalb des gleichen Abonnements (beispielsweise „contosoapp1.cloudapp.net.“).
- Der Reverse-DNS-FQDN wird vorwärts zum Namen oder zur IP-Adresse des Clouddiensts aufgelöst, für den er angegeben wurde – oder zu einem beliebigen Clouddienstnamen bzw. zu einer IP-Adresse innerhalb des gleichen Abonnements. Der Reverse-DNS-Wert lautet also beispielsweise „app1.contoso.com.“ (ein CName-Alias für „contosoapp1.cloudapp.net“).

Überprüfungen werden nur durchgeführt, wenn die Reverse-DNS-Eigenschaft für einen Clouddienst eingerichtet oder geändert wird. Es finden keine regelmäßigen Neuüberprüfungen statt.

## Hinzufügen von Reverse-DNS zu vorhandenen Clouddiensten
Sie können einem vorhandenen Clouddienst einen Reverse-DNS-Eintrag mit dem Cmdlet „Set-AzureService“ hinzufügen:

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Erstellen eines Clouddiensts mit Reverse-DNS
Sie können einen neuen Clouddienst mit der angegebenen Reverse-DNS-Eigenschaft hinzufügen, indem Sie das Cmdlet Set-AzureService verwenden:

	PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## Anzeigen von Reverse-DNS für vorhandene Clouddienste
Sie können den konfigurierten Wert für einen vorhandenen Clouddienst mithilfe des Cmdlets „Get-AzureService“ anzeigen:

	PS C:\> Get-AzureService "contosoapp1"

## Entfernen von Reverse-DNS aus vorhandenen Clouddiensten
Sie können eine Reverse-DNS-Eingenschaft mit dem Cmdlet Set-AzureService aus einem vorhandenen Clouddienst entfernen. Dies erfolgt, indem der Wert für die Reverse-DNS-Eigenschaft auf „leer“ gesetzt wird:

	PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [HÄUFIG GESTELLTE FRAGEN](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

<!---HONumber=AcomDC_0824_2016-->