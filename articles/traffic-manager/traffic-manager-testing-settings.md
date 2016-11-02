<properties 
   pageTitle="Testen der Traffic Manager-Einstellungen | Microsoft Azure"
   description="In diesem Artikel erfahren Sie, wie Sie Traffic Manager-Einstellungen testen."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Testen der Traffic Manager-Einstellungen

Die beste Möglichkeit, um Ihre Traffic Manager-Einstellungen zu testen, besteht darin, einige Clients einzurichten und die im Profil enthaltenen Endpunkte, die aus Cloud-Diensten und Websites bestehen, nacheinander zu beenden. Die folgenden Tipps unterstützen Sie beim Testen Ihres Traffic Manager-Profils:

## Grundlegende Testschritte

- **Stellen Sie die DNS-Gültigkeitsdauer (TTL) sehr niedrig ein**, sodass die Änderungen schnell propagiert werden, beispielsweise auf 30 Sekunden.
- **Halten Sie die IP-Adressen Ihrer Azure Cloud-Dienste und Websites** für das Profil bereit, das Sie testen.
- **Verwenden Sie Tools, mit denen Sie einen DNS-Namen in eine IP-Adresse auflösen können**, und zeigen Sie diese Adresse an. Überprüfen Sie, ob der Name Ihrer Unternehmensdomäne in IP-Adressen der im Profil enthaltenen Endpunkte aufgelöst wird. Die Auflösung sollte in Übereinstimmung mit der Routingmethode für Datenverkehr Ihres Traffic Manager-Profils erfolgen. Wenn Sie einen Computer verwenden, der Windows ausführt, können Sie das Tool Nslookup.exe an einer Befehls- oder Windows PowerShell-Eingabeaufforderung ausführen. Weitere öffentlich verfügbare Tools, mit denen Sie eine IP-Adresse herausfinden können, sind ebenfalls im Internet verfügbar.

### So überprüfen Sie ein Traffic Manager-Profil mit "nslookup"

1. Öffnen Sie eine Eingabeaufforderung oder eine Windows PowerShell-Eingabeaufforderung als Administrator.
2. Geben Sie `ipconfig /flushdns` ein, um den DNS-Auflösungscache zu leeren.
3. Geben Sie `nslookup <your Traffic Manager domain name>` ein. Mit dem folgenden Befehl wird beispielsweise ein Domänenname mit dem Präfix *myapp.contoso* überprüft: nslookup myapp.contoso.trafficmanager.net Das Ergebnis sieht in der Regel wie folgt aus:
   - Der DNS-Name und die IP-Adresse des DNS-Servers, auf den zugegriffen wird, um diesen Traffic Manager-Domänennamen aufzulösen.
   - Den Traffic Manager-Domänennamen, die Sie in der Befehlszeile nach "Nslookup" und die IP-Adresse in der Traffic Manager-Domäne aufgelöst wird eingegeben. Die zweite IP-Adresse ist für die Überprüfung wichtig. Es sollte eine öffentliche virtuelle IP-Adresse (VIP) Adresse für eines der Cloud-Dienste oder Websites in Traffic Manager-Profil, das Sie testen übereinstimmen.

## Testen von Routingmethoden für Datenverkehr

### So testen Sie die Routingmethode für Datenverkehr "Failover"

1. Alle Endpunkte müssen ausgeführt werden.
2. Verwenden Sie einen einzelnen Client.
3. Fordern Sie die DNS-Auflösung für den Namen Ihrer Unternehmensdomäne mithilfe des Tools "Nslookup.exe" oder eines ähnlichen Hilfsprogramms an.
4. Stellen Sie sicher, dass die aufgelöste IP-Adresse, die Sie abrufen, zum primären Endpunkt gehört.
5. Fahren Sie den primären Endpunkt herunter bzw. entfernen Sie die Überwachungsdatei, sodass Traffic Manager davon ausgeht, dass der Dienst nicht in Betrieb ist.
6. Warten Sie den Zeitraum der im Traffic Manager-Profile angegebenen DNS-Gültigkeitsdauer zuzüglich weiterer zwei Minuten ab. Wenn Ihre DNS-Gültigkeitsdauer z. B. 300 Sekunden (5 Minuten) beträgt, müssen Sie 7 Minuten warten.
7. Leeren Sie den DNS-Clientcache, und fordern Sie dann DNS-Auflösung an. In Windows können Sie den DNS-Cache mit dem Befehl "ipconfig /flushdns" leeren, den Sie an einer Eingabeaufforderung oder einer Windows PowerShell-Eingabeaufforderung aufrufen.
8. Stellen Sie sicher, dass die abgerufene IP-Adresse zum sekundären Endpunkt gehört.
9. Wiederholen Sie diesen Vorgang, indem Sie den sekundären Endpunkt beenden, dann den tertiären Endpunkt usw. In jedem dieser Fälle muss sichergestellt werden, dass von der DNS-Auflösung die IP-Adresse des nächsten Endpunkts in der Liste zurückgegeben wird. Wenn alle Endpunkte nicht betriebsbereit sind, sollte erneut die IP-Adresse des primären Endpunkts abgerufen werden.

### So testen Sie die Routingmethode für Datenverkehr "Roundrobin"

1. Alle Endpunkte müssen ausgeführt werden.
2. Verwenden Sie einen einzelnen Client.
3. Fordern Sie die DNS-Auflösung für Ihre Unternehmensdomäne mithilfe des Tools "Nslookup.exe" oder eines ähnlichen Hilfsprogramms an.
4. Stellen Sie sicher, dass die erhaltene IP-Adresse eine Adresse aus Ihrer Liste ist.
5. Leeren Sie den DNS-Clientcache, und wiederholen Sie dann immer wieder die Schritte 3 und 4. Für jeden Ihrer Endpunkte sollte eine andere IP-Adresse zurückgegeben werden. Anschließend wird der Prozess wiederholt.

### So testen Sie die Routingmethode für Datenverkehr "Leistung"

Um die Routingmethode für Datenverkehr "Leistung" effizient zu testen, müssen Sie weltweit über Clients verfügen. Sie können Clients in Azure erstellen, die versuchen, Ihre Dienste mithilfe des Domänennamens Ihres Unternehmens aufzurufen. Bei einem globalen Unternehmen können Sie sich auch remote bei Clients in anderen Teilen der Welt anmelden und den Test über diese Clients durchführen.

Sie können kostenlose webbasierte DNS-Lookup- und Analysedienste nutzen. Mit einigen davon können Sie die DNS-Namensauflösung von verschiedenen Standorten aus überprüfen. Führen Sie beispielsweise eine Suche nach "DNS-Lookup" aus, um Beispiele zu erhalten. Sie können auch eine Drittanbieterlösung wie Gomez oder Keynote verwenden, um sicherzustellen, dass der Datenverkehr von den Profilen wie erwartet verteilt wird.

## Nächste Schritte

[Leistungsüberlegungen zu Traffic Manager](traffic-manager-performance-considerations.md)

[Problembehandlung beim Status „Heruntergestuft“ in Traffic Manager](traffic-manager-troubleshooting-degraded.md)




 

<!---HONumber=AcomDC_0824_2016-->