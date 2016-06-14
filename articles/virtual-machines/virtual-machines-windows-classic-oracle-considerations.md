<properties
pageTitle="Überlegungen zu Images für virtuelle Oracle-Computer | Microsoft Azure"
description="In diesem Artikel erhalten Sie vor der Bereitstellung Informationen zu unterstützten Konfigurationen und Einschränkungen für einen virtuellen Oracle-Computer unter Windows Server in Azure."
services="virtual-machines-windows"
documentationCenter=""
manager="timlt"
authors="rickstercdn"
tags="azure-service-management"/>

<tags
ms.service="virtual-machines-windows"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-windows"
ms.workload="infrastructure-services"
ms.date="05/17/2016"
ms.author="rclaus" />

#Verschiedene Überlegungen zu Images für virtuelle Oracle-Computer



Dieser Artikel enthält Überlegungen zu virtuellen Oracle-Computern in Azure, die auf von Oracle bereitgestellten Oracle-Software-Images basieren.

-  Images von virtuellen Oracle Database-Computern
-  Images von virtuellen Oracle WebLogic Server-Computern
-  Images von virtuellen Oracle JDK-Computern

##Images von virtuellen Oracle Database-Computern
### Clustering (RAC) wird nicht unterstützt

Azure unterstützt derzeit keine Oracle Real Application Cluster (RAC) der Oracle-Datenbank. Es sind nur eigenständige Instanzen von Oracle-Datenbanken möglich. Der Grund dafür ist, dass Azure derzeit die gemeinsame Nutzung virtueller Datenträger mit Lese-/Schreibzugriff über mehrere Instanzen virtueller Computer nicht unterstützt. Multicast-UDP wird ebenfalls nicht unterstützt.

### Keine statischen internen IP-Adressen

Azure weist jedem virtuellen Computer eine interne IP-Adresse zu. Wenn der virtuelle Computer Teil eines virtuellen Netzwerks ist, ist die IP-Adresse des virtuellen Computers dynamisch und kann sich nach dem Neustart des virtuellen Computers unter Umständen ändern. Dies kann Probleme verursachen, da Oracle Database eine statische IP-Adresse erwartet. Um dieses Problem zu vermeiden, sollten Sie den virtuellen Computer einem Azure Virtual Network hinzufügen. Weitere Informationen finden Sie unter [Virtuelles Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) und [Erstellen eines virtuellen Netzwerks in Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

### Optionen für die Konfiguration angefügter Datenträger

Sie können Datendateien auf dem Betriebssystemdatenträger des virtuellen Computers oder auf angefügten Datenträgern ablegen. Angefügte Laufwerke bieten unter Umständen eine bessere Leistung und Flexibilität in Bezug auf die Größe als der Betriebssystemdatenträger. Der Betriebssystemdatenträger ist ggf. nur für Datenbanken mit einer Größe von unter 10 GB vorzuziehen.

Angefügte Datenträger nutzen den Azure-BLOB-Speicherdienst. Auf jedem Datenträger können theoretisch bis zu 500 E/A-Vorgänge pro Sekunde (IOPS) ausgeführt werden. Die Leistung angefügter Datenträger ist möglicherweise zunächst nicht optimal, aber die IOPS-Leistung kann sich nach einer Anlaufperiode von ca. 60 - 90 Minuten kontinuierlichen Betriebs beträchtlich verbessern. Wenn ein Datenträger anschließend außer Betrieb bleibt, nimmt die IOPS-Leistung möglicherweise ab, bis eine weitere Anlaufperiode mit kontinuierlichem Betrieb erfolgt. Kurz gesagt, je aktiver ein Datenträger ist, desto höher ist die Wahrscheinlichkeit, dass er die optimale IOPS-Leistung erreicht.

Es ist zwar das einfachste Verfahren, einen einzelnen Datenträger an den virtuellen Computer anzufügen und auf diesem Datenträger Datenbankdateien abzulegen, aber die Leistung wird durch dieses Verfahren am stärksten beschränkt. Stattdessen können Sie häufig die effektive IOPS-Leistung verbessern, wenn Sie mehrere angefügte Datenträger verwenden, Datenbankdaten auf diesen verteilen und dann Oracle Automatic Storage Management (ASM) verwenden. Weitere Informationen finden Sie unter [Oracle Automatic Storage Management Overview](http://www.oracle.com/technetwork/database/index-100339.html) (in englischer Sprache). Zwar kann das Striping mehrerer Datenträger auf Betriebssystemebene verwendet werden, aber hiervon wird abgeraten, da eine Verbesserung der IOPS-Leistung durch dieses Verfahren nicht nachgewiesen ist.

Sie können zwei verschiedene Verfahren zum Anfügen mehrerer Datenträger verwenden, je nachdem, ob Sie der Leistung von Lesevorgängen oder der Leistung von Schreibvorgängen für die Datenbank Vorrang einräumen:

- **Nur Oracle ASM** führt wahrscheinlich zu einer besseren Leistung bei Schreibvorgängen, jedoch geringerer IOPS-Leistung bei Lesevorgängen im Vergleich zur Verwendung von Datenträgerarrays. In der folgenden Abbildung ist diese Anordnung logisch dargestellt. ![](media/virtual-machines-windows-classic-oracle-considerations/image2.png)

>[AZURE.IMPORTANT] Bewerten Sie den Kompromiss zwischen Schreibleistung und Leseleistung von Fall zu Fall. Die tatsächlichen Ergebnisse können variieren.

### Überlegungen zu Hochverfügbarkeit und Notfallwiederherstellung

Wenn Sie Oracle Database auf virtuellen Azure-Computern verwenden, müssen Sie eine Lösung für Hochverfügbarkeit und Notfallwiederherstellung implementieren, um Ausfallzeiten zu vermeiden. Sie sind außerdem für das Sichern der eigenen Daten und Anwendung verantwortlich.

Hochverfügbarkeit und Notfallwiederherstellung für Oracle Database Enterprise Edition (ohne RAC) in Azure kann mithilfe von [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) oder [Oracle GoldenGate](http://www.oracle.com/technetwork/middleware/goldengate) mit zwei Datenbanken auf zwei eigenen virtuellen Computern erreicht werden. Beide virtuellen Computer sollten sich in demselben [Clouddienst](virtual-machines-linux-classic-connect-vms.md) und in demselben [virtuellen Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) befinden, um sicherzustellen, dass sie über die private statische IP-Adresse Zugriff aufeinander haben. Darüber hinaus wird empfohlen, die virtuellen Computer in derselben [Verfügbarkeitsgruppe](virtual-machines-windows-manage-availability.md) zu platzieren, damit sie von Azure in eigenen Fehlerdomänen und Upgradedomänen angeordnet werden können. Beachten Sie, dass nur virtuelle Computer in demselben Clouddienst Mitglieder derselben Verfügbarkeitsgruppe sein können. Jeder virtuelle Computer muss mindestens 2 GB Arbeitsspeicher und 5 GB Speicherplatz aufweisen.

Mit Oracle Data Guard kann Hochverfügbarkeit mit einer primären Datenbank auf einem virtuellen Computer, einer sekundären Datenbank (Standbydatenbank) auf einem weiteren virtuellen Computer und unidirektionaler Replikation zwischen diesen Komponenten erzielt werden. Das Ergebnis ist Lesezugriff auf die Kopie der Datenbank. Mit Oracle GoldenGate können Sie bidirektionale Replikation zwischen den beiden Datenbanken konfigurieren. Informationen zum Einrichten einer Lösung für Hochverfügbarkeit für die Datenbanken mithilfe dieser Tools finden Sie in der Dokumentation zu [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) und [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) auf der Oracle-Website. Wenn Sie Schreibzugriff auf die Kopie der Datenbank benötigen, können Sie [Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html) verwenden.

##Images von virtuellen Oracle WebLogic Server-Computern

-  **Clustering wird nur in der Enterprise Edition unterstützt.** Sie sind nur für die Verwendung von WebLogic-Clustern lizenziert, wenn Sie die Enterprise Edition von WebLogic Server verwenden. Verwenden Sie Clustering nicht mit der WebLogic Server Standard Edition.

-  **Verbindungstimeouts:** Wenn die Anwendung Verbindungen mit öffentlichen Endpunkten eines anderen Azure-Clouddiensts (z. B. einem Dienst auf Datenbankebene) erfordert, werden diese Verbindungen möglicherweise nach 4 Minuten Inaktivität von Azure geschlossen. Dies wirkt sich eventuell auf Features und Anwendungen aus, die Verbindungspools erfordern, da Verbindungen, die länger als dieses Limit inaktiv sind, möglicherweise nicht mehr gültig sind. Wenn sich dies auf die Anwendung auswirkt, sollten Sie das Anwenden von Keep-Alive-Logik auf die Verbindungspools in Betracht ziehen.

	Wenn sich ein Endpunkt *innerhalb* der Bereitstellung des Azure-Clouddiensts befindet (z. B. ein eigenständiger virtueller Datenbankcomputer in *demselben* Clouddienst wie die virtuellen WebLogic-Computer), handelt es sich bei der Verbindung um eine direkte Verbindung. Diese erfordert keinen Azure-Load Balancer, und es tritt kein Timeout der Verbindung auf.

-  **UDP-Multicast wird nicht unterstützt.** Azure unterstützt UDP-Unicasting, jedoch weder Multicasting noch Broadcasting. WebLogic Server kann die UDP-Unicast-Funktionen von Azure nutzen. Um optimale Ergebnisse bei der Nutzung von UDP-Unicast zu erzielen, empfiehlt es sich, eine statische Größe des WebLogic-Clusters zu verwenden oder nicht mehr als 10 verwaltete Server in den Cluster aufzunehmen.

-  **WebLogic Server erwartet, dass für den T3-Zugriff (z. B. bei Verwendung von Enterprise JavaBeans) öffentliche und private Ports identisch sind.** Beispielszenario: Eine Anwendung auf Dienstebene (EJB) wird in einem WebLogic-Cluster, der aus mindestens zwei verwalteten Servern besteht, in einem Clouddienst mit dem Namen **SLWLS** ausgeführt. Die Clientebene befindet sich in einem anderen Clouddienst, und es wird ein einfaches Java-Programm ausgeführt, das versucht, EJB auf der Dienstebene aufzurufen. Da die Dienstebene einen Lastenausgleich erfordert, muss für die virtuellen Computer im WebLogic Server-Cluster ein öffentlicher Endpunkt mit Lastenausgleich erstellt werden. Wenn der private Port, den Sie für diesen Endpunkt angeben, nicht mit dem öffentlichen Port identisch ist (z. B. 7006:7008), tritt ein Fehler wie der folgende auf:

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	Der Grund dafür ist, dass WebLogic Server für jeden T3-Remotezugriff erwartet, dass der Port für das Load Balancer-Modul und der Port des verwalteten WebLogic-Servers identisch sind. Im obigen Fall greift der Client auf Port 7006 (den Port für das Load Balancer-Modul) zu, und der verwaltete Server lauscht an Port 7008 (dem privaten Port). Beachten Sie, dass diese Einschränkung nur für T3-Zugriff und nicht für HTTP gilt.

	Sie können dieses Problem mit einer der folgenden Problemumgehungen vermeiden:

	-  Verwenden Sie für Endpunkte mit Lastenausgleich, die für T3-Zugriff vorgesehen sind, die gleiche Nummer für den öffentlichen und privaten Port.

	-  Schließen Sie beim Starten von WebLogic Server den folgenden JVM-Parameter ein:

			-Dweblogic.rjvm.enableprotocolswitch=true

Weitere Informationen finden Sie im KB-Artikel **860340.1** unter <http://support.oracle.com>.

-  **Einschränkungen für dynamisches Clustering und Lastenausgleich.** Angenommen, Sie möchten in WebLogic Server einen dynamischen Cluster verwenden und ihn in Azure über einen einzelnen Endpunkt mit Lastenausgleich verfügbar machen. Dies ist möglich, solange Sie für jeden verwalteten Server eine feste Portnummer (keine dynamisch aus einem Bereich zugewiesene Nummer) verwenden und nicht mehr verwaltete Server starten, als Computer vorhanden sind, die vom Administrator nachverfolgt werden (d. h. nicht mehr als einen verwalteten Server pro virtuellem Computer). Wenn die Konfiguration bewirkt, dass mehr WebLogic-Server gestartet werden, als virtuelle Computer vorhanden sind (d. h., wenn mehrere WebLogic Server-Instanzen denselben virtuellen Computer verwenden), kann nicht mehr als eine dieser WebLogic Server-Instanzen an eine angegebene Portnummer gebunden werden, und die anderen Instanzen auf diesem virtuellen Computer schlagen fehl.

	Wenn Sie hingegen festlegen, dass der Admin-Server automatisch den verwalteten Servern eindeutige Portnummern zuweist, ist kein Lastenausgleich möglich. Der Grund dafür ist, dass diese Konfiguration das Zuordnen eines einzelnen öffentlichen Ports zu mehreren privaten Ports erfordert. Dies wird jedoch von Azure nicht unterstützt.

-  **Mehrere Instanzen von Weblogic Server auf einem virtuellen Computer.** Je nach den Anforderungen der Bereitstellung sollten Sie die Möglichkeit in Betracht ziehen, mehrere Instanzen von WebLogic Server auf demselben virtuellen Computer auszuführen, sofern die Größe des virtuellen Computers ausreicht. Beispielsweise können Sie auf einem virtuellen Computer mittlerer Größe, der zwei Kerne enthält, zwei Instanzen von WebLogic Server ausführen. Es wird jedoch davon abgeraten, nur einen virtuellen Computer zu verwenden, auf dem mehrere Instanzen von WebLogic Server ausgeführt werden, da hierdurch einzelne Fehlerquellen in die Architektur eingeführt werden. Ein besserer Ansatz ist die Verwendung von mindestens zwei virtuellen Computern. Auf jedem virtuellen Computer könnten dann mehrere Instanzen von WebLogic Server ausgeführt werden. Jede dieser Instanzen von WebLogic Server kann dennoch Teil desselben Clusters sein. Beachten Sie jedoch, dass Azure derzeit nicht für den Lastenausgleich von Endpunkten verwendet werden kann, die durch WebLogic Server-Bereitstellungen auf demselben virtuellen Computer verfügbar gemacht werden. Der Grund dafür ist, dass der Azure-Load Balancer die Verteilung der Server mit Lastenausgleich auf eindeutige virtuelle Computer erfordert.

##Images von virtuellen Oracle JDK-Computern

-  **Neueste Updates von JDK 6 und 7.** Es wird zwar empfohlen, die neueste öffentliche unterstützte Version von Java (derzeit Java 8) zu verwenden, aber für Azure werden auch JDK 6- und JDK 7-Images verfügbar gemacht. Diese sind für ältere Anwendungen vorgesehen, die noch nicht auf JDK 8 aktualisiert werden können. Updates früherer JDK-Images sind möglicherweise nicht mehr für die allgemeine Öffentlichkeit verfügbar. Aufgrund der Partnerschaft von Microsoft mit Oracle bieten die von Azure bereitgestellten JDK 6- und JDK 7-Images jedoch ein neueres nicht öffentliches Update, das Oracle normalerweise nur einer bestimmten Gruppe unterstützter Oracle-Kunden anbietet. Im Lauf der Zeit werden neuere Version der JDK-Images mit aktualisierten Versionen von JDK 6 und 7 zur Verfügung gestellt.

	Beachten Sie, dass das in diesen JDK 6- und JDK 7-Images verfügbare JDK und die von ihnen abgeleiteten virtuellen Computer und Images nur in Azure verwendet werden können.

-  **64-Bit-JDK.** Die von Azure bereitgestellten Images virtueller Oracle WebLogic Server-Computer und virtueller Oracle JDK-Computer enthalten die 64-Bit-Versionen von Windows Server und dem JDK.

##Zusätzliche Ressourcen
[Images des virtuellen Oracle-Computers für Azure](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->