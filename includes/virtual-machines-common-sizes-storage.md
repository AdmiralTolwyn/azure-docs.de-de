Datenspeicheroptimierte VM-Größen eignen sich dank hohem Datenträgerdurchsatz und hoher E/A-Raten perfekt für Big Data-, SQL- und NoSQL-Datenbanken. Dieser Artikel enthält Informationen zur Anzahl von vCPUs, Datenträgern und NICs sowie zum Speicherdurchsatz und zur Netzwerkleistung der einzelnen Größen in dieser Gruppe. 

Die Ls-Reihe bietet bis zu 32 vCPUs aus der [Intel® Xeon® E5 v3-Prozessorfamilie](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Die Ls-Serie erreicht die gleiche CPU-Leistung wie die G/GS-Serie und bietet 8 GiB Arbeitsspeicher pro vCPU.  

## <a name="ls-series"></a>Ls-Serie

ACU: 180 - 240
 
| Größe          | vCPU | Arbeitsspeicher: GiB | Temporärer Speicher (SSD): GiB | Max. Anzahl Datenträger | Maximaler Durchsatz (Cache und temporärer Speicher): IOPS/MBit/s (Cachegröße in GiB) | Maximaler Datenträgerdurchsatz ohne Cache: IOPS / MB/s | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbps) | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | N/V/N/V (0)          | 5.000 / 125                               | 2/4000       | 
| Standard_L8s  | 8    | 64   | 1.388 | 16             | N/V/N/V (0)          | 10.000/250                              | 4/8000  | 
| Standard_L16s | 16   | 128  | 2.807 | 32             | N/V/N/V (0)          | 20.000/500                              | 8/6000–16000 &#8224; | 
| Standard_L32s* | 32 | 256  | 5.630 | 64             | N/V/N/V (0)          | 40.000/1.000                            | 8/20000 | 
 

Der mit einem virtuellen Computer der Ls-Serie maximal mögliche Datenträgerdurchsatz kann durch Anzahl, Größe und Striping angefügter Datenträger beschränkt sein. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../articles/virtual-machines/windows/premium-storage.md). 

*Instanz wird isoliert auf dedizierter Hardware ausgeführt, die für einen einzigen Kunden bereitgestellt wird.

