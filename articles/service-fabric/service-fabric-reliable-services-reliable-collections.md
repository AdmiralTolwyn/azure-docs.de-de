<properties
   pageTitle="Reliable Collections | Microsoft Azure"
   description="Zustandsbehaftete Service Fabric-Dienste bieten zuverlässige Auflistungen, die Ihnen das Schreiben hochverfügbarer, skalierbarer Cloudanwendungen mit kurzer Latenz ermöglichen."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/25/2016"
   ms.author="mcoskun"/>

# Einführung in Reliable Collections in zustandsbehafteten Azure Service Fabric-Diensten

Reliable Collections ermöglichen es Ihnen, hoch verfügbare, skalierbare Cloudanwendungen mit geringer Latenz auf die gleiche Weise wie Anwendungen für einen einzelnen Computer zu schreiben. Die Klassen im Namespace **Microsoft.ServiceFabric.Data.Collections** bieten eine Reihe sofort verwendbarer Auflistungen, die automatisch einen hochverfügbaren Zustand ermöglichen. Als Entwickler programmieren Sie lediglich die Reliable Collections-APIs. Reliable Collections verwalten anschließend den replizierten lokalen Zustand automatisch.

Der Hauptunterschied zwischen zuverlässigen Auflistungen und anderen Hochverfügbarkeitstechnologien (z. B. Redis, Azure-Tabellendienst und Azure-Warteschlangendienst) ist, dass der Zustand lokal in der Dienstinstanz gespeichert wird und gleichzeitig hoch verfügbar ist. Dies bedeutet Folgendes:

- Alle Lesevorgänge erfolgen lokal, was eine geringe Latenz und einen hohen Lesedurchsatz zur Folge hat.
- Alle Schreibvorgänge lösen die Mindestanzahl von Netzwerk-E/As aus, was eine geringe Latenz und einen hohen Schreibdurchsatz zur Folge hat.

![Abbildung der Weiterentwicklung von Auflistungen.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Reliable Collections können als natürliche Weiterentwicklung der **System.Collections**-Klassen betrachtet werden: Der neue Satz von Auflistungen wurde für Cloudanwendungen und Umgebungen mit mehreren Computern konzipiert, ohne die Komplexität für den Entwickler zu erhöhen. Reliable Collections sind damit:

- Repliziert: Zustandsänderungen werden für hohe Verfügbarkeit repliziert.
- Persistent gespeichert: Daten werden persistent auf Datenträgern gespeichert, um sie vor größeren Ausfällen (z. B. einem Stromausfall im Rechenzentrum) zu schützen.
- Asynchron: APIs sind asynchron, um sicherzustellen, dass Threads bei einer E/A nicht blockiert werden.
- Transaktional: APIs nutzen die Abstraktion von Transaktionen, damit Sie mehrere Reliable Collections auf einfache Weise in einem Dienst verwalten können.

Zuverlässige Auflistungen zeichnen sich durch von Beginn an starke Konsistenzgarantien aus, was die Argumentation hinsichtlich Anwendungszuständen erleichtert. Eine starke Konsistenz wird erreicht, indem Transaktionscommits erst abgeschlossen werden, nachdem die gesamte Transaktion in einem Mehrheitsquorum von Replikaten (einschließlich des primären Replikats) protokolliert wurde. Um eine schwächere Konsistenz zu erreichen, können Anwendungen eine Bestätigung zurück an den Client/Antragsteller senden, bevor der asynchrone Commit zurückgegeben wird.

Die Reliable Collections-APIs sind eine Weiterentwicklung der APIs für gleichzeitige Auflistungen (im Namespace **System.Collections.Concurrent**):

- Asynchron: Gibt eine Aufgabe zurück, da die Vorgänge im Gegensatz zu gleichzeitigen Auflistungen repliziert und persistent gespeichert werden.
- Keine out-Parameter: Gibt mithilfe von `ConditionalValue<T>` anstelle von out-Parametern "Bool" und einen Wert zurück. `ConditionalValue<T>` entspricht `Nullable<T>`, erfordert aber für "struct" nicht "T".
- Transaktionen: Verwendet ein Transaktionsobjekt, um dem Benutzer Gruppenaktionen für mehrere zuverlässige Auflistungen in einer Transaktion zu ermöglichen.

Aktuell enthält **Microsoft.ServiceFabric.Data.Collections** zwei Auflistungen:

- [Zuverlässiges Wörterbuch](https://msdn.microsoft.com/library/azure/dn971511.aspx): stellt eine replizierte, transaktionale und asynchrone Auflistung von Schlüsselwertpaaren dar. Ähnlich wie bei **ConcurrentDictionary** können der Schlüssel und der Wert von beliebigem Typ sein.
- [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx): Stellt eine replizierte, transaktionale und asynchrone strenge FIFO (First In, First Out)-Warteschlange dar. Ähnlich wie bei **ConcurrentQueue** kann der Wert von beliebigem Typ sein.

## Isolationsgrade
Der Isolationsgrad ist ein Maß für den erzielten Grad an Isolation. Isolation bedeutet, dass sich eine Transaktion wie in einem System verhält, das jeweils nur eine Transaktion erlaubt.

Zuverlässige Auflistungen wählen automatisch je nach Vorgang und Rolle des Replikats den Isolationsgrad für einen gegebenen Lesevorgang.

Es gibt zwei Isolationsstufen, die von zuverlässigen Auflistungen unterstützt werden:

- **Wiederholbarer Lesevorgang**: Gibt an, dass Anweisungen keine Daten lesen können, die geändert wurden, für die aber von anderen Transaktionen noch kein Commit ausgeführt wurde. Darüber hinaus können von der aktuellen Transaktion gelesene Daten erst nach Abschluss dieser von anderen Transaktionen geändert werden. Weitere Informationen finden Sie unter [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Momentaufnahme**: Gibt an, dass von Anweisungen in einer Transaktion gelesene Daten der im Hinblick auf Transaktionen konsistenten Version der Daten entsprechen, die zu Beginn der Transaktion vorhanden waren. Die Transaktion kann nur Datenänderungen erkennen, die vor dem Start der Transaktion festgeschrieben wurden. Nach dem Start der aktuellen Transaktion von anderen Transaktionen vorgenommene Datenänderungen sind für Anweisungen, die in der aktuellen Transaktion ausgeführt werden, nicht sichtbar. Es erscheint daher, als ob die Anweisungen in einer Transaktion eine Momentaufnahme der festgeschriebenen Daten erhalten, die zu Beginn der Transaktion vorhanden waren. Momentaufnahmen sind über zuverlässige Sammlungen hinweg konsistent. Weitere Informationen finden Sie unter [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Das zuverlässige Wörterbuch und die zuverlässige Warteschlange unterstützen beide "Read Your Writes". Mit anderen Worten sind jegliche Schreibvorgänge innerhalb einer Transaktion für den nachfolgenden Lesevorgang sichtbar, wenn dieser derselben Transaktion angehört.

### Zuverlässiges Wörterbuch
| Vorgang\\Rolle | Primär | Sekundär |
| --------------------- | :--------------- | :--------------- |
| Lesevorgang für eine einzelne Entität | Wiederholbarer Lesevorgang | Momentaufnahme |
| Aufzählung\\Anzahl | Momentaufnahme | Momentaufnahme |

### Zuverlässige Warteschlange
| Vorgang\\Rolle | Primär | Sekundär |
| --------------------- | :--------------- | :--------------- |
| Lesevorgang für eine einzelne Entität | Momentaufnahme | Momentaufnahme |
| Aufzählung\\Anzahl | Momentaufnahme | Momentaufnahme |

## Persistenzmodell
Reliable State Manager und Reliable Collections basieren auf einem Persistenzmodell, das als Protokoll und Prüfpunkt bezeichnet wird. Bei diesem Modell wird jede Zustandsänderung auf dem Datenträger protokolliert und nur im Arbeitsspeicher angewendet. Der vollständige Zustand selbst wird nur gelegentlich persistent gespeichert (auch als Prüfpunkt bezeichnet). Der Vorteil ist:

- Deltas werden zur Leistungsverbesserung in sequenzielle Nur-Anhängen-Schreibvorgänge auf dem Datenträger umgewandelt.

Zum besseren Verständnis des Protokoll- und Prüfpunktmodells sehen wir uns zunächst das Szenario des Endlosdatenträgers an. Reliable State Manager protokolliert jeden Vorgang, bevor dieser repliziert wird. Dies ermöglicht es Reliable Collections, den Vorgang nur im Arbeitsspeicher anzuwenden. Da Protokolle persistent sind, verfügt der zuverlässige Zustands-Manager selbst dann, wenn das Replikat aufgrund eines Fehlers neu gestartet werden muss, über ausreichend Informationen in seinen Protokollen, um alle im Replikat verloren gegangenen Vorgänge zu wiederholen. Da es sich um einen Endlosdatenträger handelt, müssen Protokolleinträge nie entfernt werden. Reliable Collections verwalten lediglich den Zustand im Speicher.

Sehen wir uns jetzt das Szenario mit begrenzten Datenträgern an. Irgendwann ist der gesamte Speicherplatz auf dem Datenträger von Reliable State Manager belegt. Bevor dies geschieht, muss Reliable State Manager sein Protokoll kürzen, um Platz für neuere Datensätze zu schaffen. Er fordert die Reliable Collections auf, ihren Zustand im Arbeitsspeicher auf dem Datenträger zu prüfen. Es obliegt den zuverlässigen Auflistungen, den Zustand bis zu diesem Punkt persistent zu speichern. Sobald die zuverlässigen Auflistungen ihre Prüfpunkte abgeschlossen haben, kann der zuverlässige Zustands-Manager das Protokoll kürzen, um Speicherplatz freizugeben. Wenn das Replikat neu gestartet werden muss, stellen zuverlässige Auflistungen den Zustand ihres Prüfpunkts wieder her. Zudem stellt der zuverlässige Zustands-Manager alle seit dem Prüfpunkt vorgenommenen Zustandsänderungen wieder her.

## Sperren
In Reliable Collections bestehen alle Transaktionen aus zwei Phasen: Die von einer Transaktion angeforderten Sperren werden erst aufgehoben, wenn die Transaktion durch einen Abbruch oder einen Commit beendet wird.

Zuverlässige Auflistungen verwenden immer exklusive Sperren. Für Lesevorgänge hängt die Sperrung von verschiedenen Faktoren ab. Jegliche Lesevorgänge, die mithilfe der Momentaufnahmeisolierung ausgeführt wurden, sind frei von Sperren. Bei allen wiederholbaren Lesevorgängen werden standardmäßig freigegebene Sperren angewendet. Bei Lesevorgängen, die wiederholbare Lesevorgänge unterstützen, können Benutzer anstelle der freigegebenen Sperre eine Aktualisierungssperre anfordern. Eine Aktualisierungssperre ist eine asymmetrische Sperre, mit der eine häufig auftretende Form von Deadlock verhindert wird. Der Deadlock tritt auf, wenn mehrere Transaktionen Ressourcen für potenzielle Updates zu einem späteren Zeitpunkt sperren.

Die Kompatibilitätsmatrix für Sperren finden Sie unten:

| Anforderung\Gewährt | Keine | Shared | Aktualisieren | Exklusiv |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Shared  | Kein Konflikt | Kein Konflikt | Konflikt: | Konflikt: |
| Aktualisieren | Kein Konflikt | Kein Konflikt | Konflikt: | Konflikt: |
| Exklusiv | Kein Konflikt | Konflikt: | Konflikt: | Konflikt: |

Hinweis: Zum Erkennen von Deadlocks werden in den Reliable Collections-APIs Timeoutargumente verwendet. Angenommen, zwei Transaktionen (T1 und T2) versuchen, K1 zu lesen und zu aktualisieren. Beide können ein Deadlock durchführen, da im Endeffekt beide die freigegebene Sperre haben. In diesem Fall tritt bei einem oder beiden Vorgängen ein Timeout auf.

Das vorangegangene Deadlockszenario ist ein hervorragendes Beispiel, wie Aktualisierungssperren Deadlocks verhindern können.

## Recommendations

- Ändern Sie kein benutzerdefiniertes Objekt, das von Lesevorgängen (z.B. `TryPeekAsync` oder `TryGetValueAsync`) zurückgegeben wurde. Zuverlässige Auflistungen geben ebenso wie gleichzeitige Auflistungen anstelle einer Kopie einen Verweis auf die Objekte zurück.
- Tiefenkopieren Sie zurückgegebene benutzerdefinierte Objekte, bevor Sie diese ändern. Da bei Strukturen und integrierten Typen eine Wertübergabe erfolgt, ist hier keine Tiefenkopie erforderlich.
- Verwenden Sie `TimeSpan.MaxValue` nicht für Timeouts. Timeouts sollten verwendet werden, um Deadlocks zu erkennen.
- Erstellen Sie keine Transaktion innerhalb der `using`-Anweisung einer anderen Transaktion, da dies zu Deadlocks führen kann.
- Stellen Sie sicher, die Ihre `IComparable<TKey>`-Implementierung richtig ist. Dies ist erforderlich, damit das System Prüfpunkte zusammenfügen kann.
- Sie sollten zwecks Notfallwiederherstellung die Verwendung der Funktionen „Backup“ und „Wiederherstellung“ in Betracht ziehen.

hier folgen einige Punkte, die es zu beachten gilt:

- Das Standardtimeout beträgt 4 Sekunden für alle Reliable Collections-APIs. Die meisten Benutzer sollten diesen Wert nicht überschreiben.
- Das Standardabbruchtoken ist `CancellationToken.None` in allen APIs für zuverlässige Auflistungen.
- Der Schlüsseltyp-Parameter (*TKey*) für ein zuverlässiges Wörterbuch muss `GetHashCode()` und `Equals()` richtig implementieren. Schlüssel müssen unveränderlich sein.
- Zum Erreichen einer hohen Verfügbarkeit der zuverlässigen Auflistungen sollte jeder Dienst mindestens ein Ziel und eine Mindestgröße von 3 bei der Replikatgruppe haben.
- Lesevorgänge auf dem sekundären Replikat dürfen Versionen lesen, die nicht im Quorum committet wurden. Dies bedeutet, dass Datenversionen, die von einem einzelnen sekundären Replikat gelesen werden, falsch weiterverarbeitet werden können. Da Lesevorgänge von primären Replikaten immer stabil sind, können hier nie fehlerhafte Versionen auftreten.

## Nächste Schritte

- [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
- [Sichern und Wiederherstellen von Reliable Services (Notfallwiederherstellung)](service-fabric-reliable-services-backup-restore.md)
- [Konfigurieren des Reliable State Managers](service-fabric-reliable-services-configuration.md)
- [Erste Schritte mit Web-API-Diensten von Service Fabric](service-fabric-reliable-services-communication-webapi.md)
- [Erweiterte Verwendung des Reliable Services-Programmiermodells](service-fabric-reliable-services-advanced-usage.md)
- [Entwicklerreferenz für zuverlässige Auflistungen](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0518_2016-->