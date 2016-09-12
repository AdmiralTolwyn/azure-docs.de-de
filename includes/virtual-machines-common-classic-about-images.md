

Images werden in Azure verwendet, um einen neuen virtuellen Computer mit einem Betriebssystem bereitzustellen. Ein Image kann auch über einen oder mehrere Datenträger verfügen. Images sind aus verschiedenen Quellen verfügbar:

-	Azure bietet Images im [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/) an. Dort finden Sie aktuelle Versionen von Windows Server und Distributionen des Linux-Betriebssystems. Einige Images enthalten außerdem Clientanwendungen wie SQL Server. Abonnenten von MSDN Benefit und MSDN Pay-as-You-Go haben Zugriff auf weitere Images.
-	Die Open-Source-Community bietet Images im [VM Depot](http://vmdepot.msopentech.com/List/Index) an.
-	Sie können auch eigene Images in Azure speichern und nutzen, indem Sie entweder einen vorhandenen virtuellen Azure-Computer als Image erfassen oder ein Image hochladen.

## Informationen zu VM- und Betriebssystem-Images

Zwei Typen von Images können in Azure verwendet werden: *VM-Images* und *Betriebssystem-Images*. Ein VM-Image enthält ein Betriebssystem und alle Datenträger, die an einen virtuellen Computer angefügt sind, wenn das Image erstellt wird. Ein VM-Image ist der neuere Imagetyp. Bevor VM-Images eingeführt wurden, konnte ein Image in Azure nur ein allgemeines Betriebssystem ohne zusätzliche Datenträger enthalten. Ein VM-Image, das nur ein verallgemeinertes Betriebssystem enthält, entspricht im Grunde dem ursprünglichen Typ von Image, dem Betriebssystem-Image.

Sie können basierend auf einem virtuellen Computer in Azure oder einem an anderer Stelle ausgeführten virtuellen Computer eigene Images erstellen, die Sie kopieren und hochladen. Wenn Sie ein Image verwenden möchten, um mehrere virtuelle Computer zu erstellen, müssen Sie es vor der Verwendung als Image durch eine Verallgemeinerung vorbereiten. Um ein Windows Server-Image zu erstellen, führen Sie den Befehl "Sysprep" auf dem Server aus, um es vor dem Hochladen der VHD-Datei zu verallgemeinern. Weitere Informationen zu Sysprep finden Sie unter [How to Use Sysprep: An Introduction](http://go.microsoft.com/fwlink/p/?LinkId=392030) (Verwenden von Sysprep: Einführung) und [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Sysprep-Unterstützung für Serverrollen). Sichern Sie den virtuellen Computer vor dem Ausführen von Sysprep. Das Erstellen eines Linux-Image hängt von der Verteilung ab. In der Regel müssen Sie eine Reihe von spezifischen Befehlen für die Verteilung ausführen und dann den Azure Linux-Agent.

<!---HONumber=AcomDC_0831_2016-->