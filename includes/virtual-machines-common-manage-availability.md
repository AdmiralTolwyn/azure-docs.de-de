## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Grundlegendes zu VM-Neustarts – Gegenüberstellung von Wartung und Ausfallzeit
Drei Szenarien können zu einer Beeinträchtigung virtueller Computer in Azure führen: eine ungeplante Hardwarewartung, eine unerwartete Ausfallzeit und eine geplante Wartung.

* **Ungeplante Hardwarewartung:** Tritt auf, wenn die Azure-Plattform den Ausfall einer Hardware- oder Plattformkomponente für einen physischen Computer prognostiziert. Daraufhin wird ein Ereignis für eine ungeplante Hardwarewartung initiiert, um die Auswirkungen auf virtuelle Computer, die die betroffene Hardware nutzen, möglichst gering zu halten. Die virtuellen Computer werden von Azure unter Verwendung von Livemigrationstechnologie von der fehlerhaften Hardware an einen fehlerfreien physischen Computer migriert. Bei der Livemigration wird der virtuelle Computer lediglich kurzzeitig angehalten. Arbeitsspeicher, geöffnete Dateien und bestehende Netzwerkverbindungen bleiben erhalten, die Leistung kann jedoch vor und/oder nach dem Ereignis beeinträchtigt sein. Falls die Livemigration nicht verwendet werden kann, kommt es bei dem virtuellen Computer wie unten beschrieben zu unerwarteter Ausfallzeit.


* **Unerwartete Ausfallzeit:** Tritt in seltenen Fällen auf, wenn die dem virtuellen Computer zugrunde liegende Hardware oder physische Infrastruktur ausgefallen ist. Diese können Ausfälle des lokalen Netzwerks, Datenträgers oder andere Fehler auf Rackebene umfassen. Wenn ein Fehler dieser Art festgestellt wird, migriert die Azure-Plattform Ihren virtuellen Computer automatisch zu einem fehlerfreien physischen Computer in demselben Datencenter. Dieser Vorgang ist mit einer gewissen Ausfallzeit (Neustart) und in manchen Fällen mit dem Verlust des temporären Laufwerks verbunden. Die angefügten (Betriebssystem-)Datenträger bleiben in jedem Fall erhalten. 

  Für virtuelle Computer kann es im unwahrscheinlichen Fall eines Ausfalls bzw. Notfalls, von dem ein gesamtes Datencenter oder sogar eine ganze Region betroffen ist, auch zu Ausfallzeiten kommen. Für diese Szenarien verfügt Azure über Schutzoptionen, z.B. [Verfügbarkeitszonen](../articles/availability-zones/az-overview.md) und [Regionspaare](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

* **Geplante Wartungsereignisse:** Hierbei handelt es sich um regelmäßige Updates, die von Microsoft für die zugrunde liegende Azure-Plattform ausgeführt werden können, um die Verfügbarkeit, Leistung und Sicherheit der Plattforminfrastruktur, unter der die virtuellen Computer ausgeführt werden, zu verbessern. Die meisten dieser Updates werden ohne Auswirkungen auf die virtuellen Computer oder Clouddienste ausgeführt. (Weitere Informationen finden Sie unter [Wartung mit direkter Migration und Beibehaltung der virtuellen Computer](https://docs.microsoft.com/azure/virtual-machines/windows/preserving-maintenance)). Die Azure-Plattform versucht zwar, möglichst immer die Wartung mit direkter Migration und Beibehaltung der virtuellen Computer zu verwenden, in seltenen Fällen ist jedoch ein Neustart des virtuellen Computers erforderlich, um die erforderlichen Updates auf die zugrunde liegende Infrastruktur anzuwenden. In diesem Fall können Sie eine geplante Azure-Wartung mit erneuter Bereitstellung nach der Wartung durchführen, indem Sie die Wartung für die entsprechenden virtuellen Computer im geeigneten Zeitfenster initiieren. Weitere Informationen finden Sie unter [Geplante Wartung für virtuelle Windows-Computer](https://docs.microsoft.com/azure/virtual-machines/windows/planned-maintenance/).


Um die Downtime aufgrund eines oder mehrerer dieser Ereignisse zu verringern, sollten Sie die folgenden bewährten Methoden befolgen, um die Hochverfügbarkeit virtueller Computer zu gewährleisten:

* [Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz]
* [Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe]
* [Azure-Metadatendienst: Geplante Ereignisse (Vorschau) für Windows-VMs] (https://docs.microsoft.com/azure/virtual-machines/virtual-machines-scheduled-events)
* [Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen]
* [Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen]
* [Verwenden von Verfügbarkeitszonen als Schutz vor Ausfällen auf Datencenterebene]

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz
Um Redundanz für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Durch diese Konfiguration in einem Datencenter wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der Azure-SLA zugesicherte Verfügbarkeit von 99,95 Prozent eingehalten wird. Weitere Informationen finden Sie unter [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

> [!IMPORTANT]
> Vermeiden Sie es, virtuelle Computer, die eine Einzelinstanz darstellen, alleine einer Verfügbarkeitsgruppe zuzuordnen. Virtuelle Computer in dieser Konfiguration erfüllen nicht die Anforderungen für eine zugesicherte SLA und sind während geplanter Azure-Wartungsereignisse nicht verfügbar – es sei denn, ein einzelner virtueller Computer verwendet [Azure Storage Premium](../articles/virtual-machines/windows/premium-storage.md). Für einzelne virtuelle Computer mit Storage Premium gilt die Azure-SLA.

Jeder virtuelle Computer in der Verfügbarkeitsgruppe wird einer **Updatedomäne** (UD) und einer **Fehlerdomäne** (FD) der zugrunde liegenden Azure-Plattform zugewiesen. Für eine bestimmte Verfügbarkeitsgruppe werden fünf nicht von Benutzern konfigurierbare Updatedomänen standardmäßig zugewiesen (Resource Manager-Bereitstellungen können dann vergrößert werden, um bis zu 20 Updatedomänen bereitzustellen), um die Gruppen mit virtuellen Computern und die zugehörigen physischen Hardwareelemente zu kennzeichnen, die gleichzeitig neu gestartet werden können. Wenn innerhalb einer Verfügbarkeitsgruppe mehr als fünf virtuelle Computer konfiguriert sind, wird der sechste virtuelle Computer in derselben Updatedomäne wie der erste virtuelle Computer gespeichert, der siebte in derselben Updatedomäne wie der zweite virtuelle Computer usw. Während einer geplanten Wartung werden die Updatedomänen unter Umständen nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine Updatedomäne neu gestartet. Bei einer neu gestarteten Updatedomäne wird 30 Minuten gewartet, bevor die Wartung für eine andere Updatedomäne initiiert wird.

Mit Fehlerdomänen wird die Gruppe der virtuellen Computer definiert, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. Standardmäßig sind die innerhalb Ihrer Verfügbarkeitsgruppe konfigurierten virtuellen Computer auf bis zu drei Fehlerdomänen bei Resource Manager-Bereitstellungen verteilt (zwei Fehlerdomänen bei klassischen Bereitstellungen). Auch wenn Verfügbarkeitsgruppen Ihre Anwendung nicht gänzlich vor Fehlern des Betriebssystems oder der Anwendung selbst schützen können, verringern sie doch die Auswirkungen von potenziellen Hardwarefehlern, Netzwerkausfällen oder Stromunterbrechungen.

<!--Image reference-->
   ![Schematische Darstellung der Konfiguration mit Updatedomäne und Fehlerdomäne](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe
Falls Sie derzeit VMs mit nicht verwalteten Datenträgern verwenden, empfehlen wir Ihnen dringend, [VMs in der Verfügbarkeitsgruppe für die Verwendung von Managed Disks zu konvertieren](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md).

[Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) ermöglicht eine bessere Zuverlässigkeit für Verfügbarkeitsgruppen, indem sichergestellt wird, dass die Datenträger virtueller Computer in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um einzelne Fehlerquellen zu vermeiden. Hierzu werden die Datenträger automatisch in verschiedenen Speicherclustern angeordnet. Wenn ein Speichercluster aufgrund eines Hardware- oder Softwarefehlers ausfällt, treten nur für die VM-Instanzen mit Datenträgern auf diesen Stamps Fehler auf.

![Fehlerdomänen für verwaltete Datenträger](./media/virtual-machines-common-manage-availability/md-fd.png)

> [!IMPORTANT]
> Die Anzahl von Fehlerdomänen für verwaltete Verfügbarkeitsgruppen variieren je nach Region: zwei oder drei pro Region. In der folgenden Tabelle ist die Anzahl pro Region aufgeführt:

[!INCLUDE [managed-disks-common-fault-domain-region-list](managed-disks-common-fault-domain-region-list.md)]

Gehen Sie wie folgt vor, wenn Sie planen, VMs mit [nicht verwalteten Datenträgern](../articles/virtual-machines/windows/about-disks-and-vhds.md#types-of-disks) zu verwenden: Halten Sie sich an die unten angegebenen bewährten Methoden für Storage-Konten, bei denen virtuelle Festplatten (VHDs) von VMs als [Seitenblobs](https://docs.microsoft.com/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) gespeichert werden.

1. **Alle Datenträger (Betriebssystem und Daten) müssen einem virtuellen Computer im selben Speicherkonto zugeordnet sein**
2. **Überprüfen Sie die [Grenzwerte](../articles/storage/common/storage-scalability-targets.md) für die Anzahl von nicht verwalteten Datenträgern eines Storage-Kontos**, bevor Sie einem Speicherkonto weitere VHDs hinzufügen.
3. **Verwenden Sie mehrere Speicherkonten für jeden virtuellen Computer in einer Verfügbarkeitsgruppe.** Geben Sie Storage-Konten mit mehreren VMs in derselben Verfügbarkeitsgruppe nicht für die gemeinsame Nutzung frei. Für VMs, die über verschiedene Verfügbarkeitsgruppen verteilt sind, können Speicherkonten freigegeben werden, solange die oben genannten bewährten Methoden befolgt werden.

## <a name="configure-each-application-tier-into-separate-availability-sets"></a>Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen
Wenn Ihre virtuellen Computer fast alle identisch sind und die Anwendung auf die gleiche Weise unterstützen, wird empfohlen, für jede einzelne Anwendungsebene eine Verfügbarkeitsgruppe zu konfigurieren.  Wenn Sie eine Verfügbarkeitsgruppe mit zwei verschiedenen Ebenen anlegen, können alle virtuellen Computer derselben Anwendungsebene zur gleichen Zeit neu gestartet werden. Indem Sie mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe für jede Ebene konfigurieren, gewährleisten Sie, dass mindestens ein virtueller Computer pro Ebene verfügbar ist.

Beispielsweise können Sie alle virtuellen Computer aus dem Front-End der Anwendung, auf denen IIS, Apache und Nginx ausgeführt werden, einer einzelnen Verfügbarkeitsgruppe zuordnen. Stellen Sie sicher, dass derselben Verfügbarkeitsgruppe nur virtuelle Front-End-Computer zugeordnet werden. Stellen Sie analog dazu sicher, dass sich virtuelle Computer der Datenebene in einer eigenen Verfügbarkeitsgruppe befinden. Dazu gehören beispielsweise virtuelle SQL Server-Computer oder virtuelle MySQL-Computer.

<!--Image reference-->
   ![Anwendungsebenen](./media/virtual-machines-common-manage-availability/application-tiers.png)

## <a name="combine-a-load-balancer-with-availability-sets"></a>Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen
Kombinieren Sie den Azure-Lastenausgleich ( [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) ) mit einer Verfügbarkeitsgruppe, um höchste Anwendungsresilienz zu erzielen. Der Azure-Lastenausgleich verteilt den Datenverkehr auf mehrere virtuelle Computer. In die virtuellen Computer der Standardebene ist der Azure-Lastenausgleich bereits integriert. Der Azure Load Balancer ist aber nicht auf allen Ebenen des virtuellen Computers verfügbar. Weitere Informationen zum Lastenausgleich zwischen virtuellen Computern finden Sie unter [Lastenausgleich zwischen virtuellen Computern](../articles/virtual-machines/virtual-machines-linux-load-balance.md).

Wenn der Lastenausgleich nicht für die gleichmäßige Verteilung des Datenverkehrs auf mehrere virtuelle Computer konfiguriert ist, wirkt sich ein geplantes Wartungsereignis schließlich auf den einzigen virtuellen Computer, der den Datenverkehr aufrechterhält, aus und führt zu einem Ausfall der Anwendungsebene. Werden dagegen mehrere virtuelle Computer derselben Ebene demselben Lastenausgleich und derselben Verfügbarkeitsgruppe zugeordnet, wird der Datenverkehr kontinuierlich von mindestens einer Instanz aufrechterhalten.

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Verwenden von Verfügbarkeitszonen als Schutz vor Ausfällen auf Datencenterebene

Mit [Verfügbarkeitszonen](../articles/availability-zones/az-overview.md) (Vorschauversion), bei denen es sich um eine Alternative zu Verfügbarkeitsgruppen handelt, wird der Grad an Kontrolle erweitert, der Ihnen beim Aufrechterhalten der Verfügbarkeit von Anwendungen und Daten auf Ihren VMs zur Verfügung steht. Eine Verfügbarkeitszone ist eine physisch getrennte Zone in einer Azure-Region. Pro unterstützter Azure-Region sind drei Verfügbarkeitszonen vorhanden. Jede Verfügbarkeitszone verfügt über eine eigene Stromversorgung, Netzwerkumgebung und Kühlung und ist von den anderen Verfügbarkeitszonen der Azure-Region logisch getrennt. Indem Sie Ihre Lösungen für die Verwendung von replizierten VMs in Zonen gestalten, können Sie Ihre Apps und Daten vor dem Verlust eines Datencenters schützen. Wenn eine Zone kompromittiert ist, sind replizierte Apps und Daten sofort in einer anderen Zone verfügbar. 

![Verfügbarkeitszonen](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

[!INCLUDE [availability-zones-preview-statement.md](availability-zones-preview-statement.md)]

Informieren Sie sich über das Bereitstellen einer [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md)- oder [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md)-VM in einer Verfügbarkeitszone.


<!-- Link references -->
[Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen]: #configure-each-application-tier-into-separate-availability-sets
[Kombinieren des Lastenausgleichs mit Verfügbarkeitsgruppen]: #combine-a-load-balancer-with-availability-sets
[Avoid single instance virtual machines in availability sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[Verwenden von verwalteten Datenträgern für virtuelle Computer in einer Verfügbarkeitsgruppe]: #use-managed-disks-for-vms-in-an-availability-set
[Verwenden von Verfügbarkeitszonen als Schutz vor Ausfällen auf Datencenterebene]: #use-availability-zones-to-protect-from-datacenter-level-failures
