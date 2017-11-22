
Die Größen A8 bis A11 und die Größen der H-Reihe werden auch als *rechenintensive Instanzen* bezeichnet. Die Hardware, auf der diese Größen ausgeführt werden, wurde für rechenintensive Anwendungen mit hoher Netzwerkauslastung konzipiert und optimiert. Hierzu zählen beispielsweise HPC-Clusteranwendungen (High Performance Computing), Modellierung und Simulationen. Die Reihen A8 bis A11 nutzen Intel Xeon E5-2670 mit 2,6 GHZ und die H-Reihe Intel Xeon E5-2667 v3 mit 3,2 GHz.  Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz und zur Netzwerkleistung der einzelnen Größen in dieser Gruppe. 

Virtuelle Azure-Computer der H-Reihe sind High Performing Computing-VMs der nächsten Generation für High-End-Berechnungsanforderungen, z.B. Molekülmodellierung und Strömungsdynamikberechnung. Diese virtuellen Computer mit 8 und 16 vCPUs basieren auf der Intel Haswell E5-2667 V3-Prozessortechnologie mit DDR4-Arbeitsspeicher und SSD-basiertem temporärem Speicher. 

Neben beträchtlicher CPU-Leistung bietet die H-Serie verschiedene Optionen für RDMA-Netzwerke mit niedriger Latenz unter Verwendung von FDR InfiniBand sowie verschiedene Speicherkonfigurationen für Berechnungsanforderungen mit hohem Speicherbedarf.



## <a name="h-series"></a>H-Reihe

ACU: 290 - 300

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Max. Datenträger-Datendurchsatz: IOPS | Maximale Anzahl NICs |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standard_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standard_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standard_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Standard_H16r* |16 |112 |2000 |64 |64 x 500 |4  |
| Standard_H16mr* |16 |224 |2000 |64 |64 x 500 |4 |

* Für MPI-Anwendungen ist ein dediziertes RDMA-Back-End-Netzwerk durch ein FDR InfiniBand-Netzwerk aktiviert, das eine äußerst geringe Latenz und eine hohe Bandbreite ermöglicht.

<br>



## <a name="a-series---compute-intensive-instances"></a>A-Serie: Rechenintensive Instanzen

ACU: 225

| Größe | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (HDD): GiB | Max. Anzahl Datenträger | Max. Datenträger-Datendurchsatz: IOPS | Maximale Anzahl NICs|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |32 |32 x 500 |2 |
| Standard_A9* |16 |112 |382 |64 |64 x 500 |4 |
| Standard_A10 |8 |56 |382 |32 |32 x 500 |2  |
| Standard_A11 |16 |112 |382 |64 |64 x 500 |4 |

* Für MPI-Anwendungen ist ein dediziertes RDMA-Back-End-Netzwerk durch ein FDR InfiniBand-Netzwerk aktiviert, das eine äußerst geringe Latenz und eine hohe Bandbreite ermöglicht.

<br>



