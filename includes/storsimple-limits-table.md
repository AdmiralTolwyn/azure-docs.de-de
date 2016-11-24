<!--author=alkohli last changed: 12/15/15-->

| Begrenzungsbezeichner | Begrenzung | Kommentare |
| --- | --- | --- |
| Maximale Anzahl von Anmeldeinformationen für das Speicherkonto |64 | |
| Maximale Anzahl von Volumecontainern |64 | |
| Maximale Anzahl von Volumes |255 | |
| Maximale Anzahl von Zeitplänen pro Bandbreitenvorlage |168 |Einen Zeitplan für jede Stunde, jeden Tag der Woche (24 * 7). |
| Maximale Größe eines mehrstufigen Volumes auf physischen Geräten |64 TB für 8100 und 8600 |8100 und 8600 sind physische Geräte. |
| Maximale Größe eines mehrstufigen Volumes auf virtuellen Geräten in Azure |30 TB für 8010  <br></br>  64 TB für 8020 |8010 und 8020 sind virtuelle Geräte in Azure, die Standardspeicher und Storage Premium verwenden. |
| Maximale Größe eines lokal fixierten Volumes auf physischen Geräten |9 TB für 8100  <br></br>  24 TB für 8600 |8100 und 8600 sind physische Geräte. |
| Maximale Anzahl von iSCSI-Verbindungen |512 | |
| Maximale Anzahl von iSCSI-Verbindungen von Initiatoren |512 | |
| Maximale Anzahl von Zugriffssteuerungsdatensätzen pro Gerät |64 | |
| Maximale Anzahl von Volumes pro Sicherungsrichtlinie |24 | |
| Maximale Anzahl von Sicherungen, die pro Sicherungsrichtlinie beibehalten werden |64 | |
| Maximale Anzahl von Zeitplänen pro Sicherungsrichtlinie |10 | |
| Maximale Anzahl von Momentaufnahmen beliebigen Typs, die pro Volume beibehalten werden können |256 |Dies beinhaltet lokale Momentaufnahmen und Cloud-Momentaufnahmen. |
| Maximale Anzahl von Momentaufnahmen, die in einem Gerät vorhanden sein können |10.000 | |
| Maximale Anzahl von Volumes, die für Sicherung, Wiederherstellung oder Klonen parallel verarbeitet werden können |16 |<ul><li>Wenn mehr als 16 Volumes vorhanden sind, werden sie nacheinander verarbeitet, sobald Verarbeitungsslots verfügbar werden.</li><li>Neue Sicherungen eines geklonten oder wiederhergestellten Volumes können erst stattfinden, nachdem der Vorgang abgeschlossen wurde. Allerdings sind für ein lokales Volume Sicherungen zulässig, nachdem das Volume online geschaltet wurde.</li></ul> |
| Wiederherstellungszeit für das Wiederherstellen und Klonen mehrstufiger Volumes |< 2 Minuten |<ul><li>Das Volume wird nach einem Wiederherstellungs- oder Klonvorgang unabhängig von der Volumegröße innerhalb von zwei Minuten verfügbar gemacht.</li><li>Die Volumeleistung kann am Anfang noch etwas beeinträchtigt sein, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann sich verbessern, wenn die Daten nach und nach aus der Cloud auf das StorSimple-Gerät übertragen werden.</li><li>Die Gesamtzeit, die zum Herunterladen der Metadaten benötigt wird, hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite der Cloudverbindung beeinflusst werden.</li><li>Der Wiederherstellungs- oder Klonvorgang ist abgeschlossen, wenn sich alle Metadaten auf dem Gerät befinden.</li><li>Sicherungsvorgänge können erst nach Abschluss des Wiederherstellungs- oder Klonvorgangs ausgeführt werden. |
| Wiederherstellungszeit für das Wiederherstellen lokal fixierter Volumes |< 2 Minuten |<ul><li>Das Volume wird nach dem Wiederherstellungsvorgang unabhängig von der Volumegröße innerhalb von zwei Minuten verfügbar gemacht.</li><li>Die Volumeleistung kann am Anfang noch etwas beeinträchtigt sein, da sich die meisten Daten und Metadaten noch in der Cloud befinden. Die Leistung kann sich verbessern, wenn die Daten nach und nach aus der Cloud auf das StorSimple-Gerät übertragen werden.</li><li>Die Gesamtzeit, die zum Herunterladen der Metadaten benötigt wird, hängt von der zugewiesenen Volumegröße ab. Metadaten werden auf das Gerät automatisch im Hintergrund mit einer Rate von 5 Minuten pro TB zugewiesener Volumedaten übertragen. Dieser Wert kann durch die Internetbandbreite der Cloudverbindung beeinflusst werden.</li><li>Im Gegensatz zu mehrschichtigen Volumes werden bei lokalen Volumes die Volumedaten auch lokal auf das Gerät heruntergeladen. Die Wiederherstellung ist abgeschlossen, wenn alle Daten des Datenträgers auf das Gerät geladen wurden.</li><li>Die Wiederherstellungsvorgänge können lange dauern, und die Gesamtzeit zum Abschließen der Wiederherstellung hängt von der Größe des bereitgestellten lokalen Volumes, der Internetbandbreite und den auf dem Gerät vorhandenen Daten ab. Sicherungsvorgänge auf lokal fixierten Volumes sind zulässig, während die Wiederherstellung ausgeführt wird. |
| Thin-Wiederherstellungsverfügbarkeit |Letztes Failover | |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von SSD-Ebene aus bereitgestellt)* |920/720 MB/s mit einer einzelnen 10-GbE-Netzwerkschnittstelle |Bis zu 2x mit MPIO und zwei Netzwerkschnittstellen. |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von HDD-Ebene aus bereitgestellt)* |120/250 MB/s | |
| Maximaler Client-Lese-/-Schreibdurchsatz (wenn von Cloud-Ebene aus bereitgestellt)* |11/41 MB/s |Lesedurchsatz hängt von Clients ab, die genügend E/A-Warteschlangentiefe generieren und verwalten müssen. |

&#42; Der maximale Durchsatz pro E/A-Typ wurde mit 100 %-Lese- und 100 %-Schreibszenarien gemessen. Der tatsächliche Durchsatz kann geringer sein und hängt von der E/A-Mischung und den Netzwerkbedingungen ab.



<!--HONumber=Nov16_HO3-->


