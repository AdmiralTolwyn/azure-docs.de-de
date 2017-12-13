| **Hardware** | |
| --- |---|
| Anzahl von CPU-Kernen| 8 |
| RAM| 12 GB|
| Anzahl der Datenträger | 3 <br><br> - Betriebssystem-Datenträger<br> - Prozessservercache-Datenträger<br> - Aufbewahrungslaufwerk (für Failback)|
| Freier Speicherplatz (Prozessservercache) | 600 GB
| Freier Speicherplatz (Aufbewahrungslaufwerk) | 600 GB|
| **Software** | |
| Betriebssystemversion | Windows Server 2012 R2 <br> Windows Server 2016 |
| Gebietsschema des Betriebssystems | Englisch (en-us)|
| VMware vSphere PowerCLI-Version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V |
| Gruppenrichtlinien| Die folgenden Gruppenrichtlinien sollten auf dem Server nicht aktiviert werden. <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> **Hinweis:** Weitere Informationen zu diesen Gruppenrichtlinien finden Sie [hier](https://technet.microsoft.com/en-us/library/gg176671(v=ws.10).aspx).|
| IIS-Konfiguration (Internetinformationsdienste) | - Keine bereits vorhandene Standardwebsite <br> - Aktivieren der [anonymen Authentifizierung](https://technet.microsoft.com/en-us/library/cc731244(v=ws.10).aspx) <br> - Aktivieren der Einstellung [FastCGI](https://technet.microsoft.com/en-us/library/cc753077(v=ws.10).aspx)  <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen<br>|
| **Netzwerk** | |
| Art der Netzwerkschnittstellenkarte | VMXNET3 (bei Bereitstellung als virtueller VMware-Computer) |
| Art der IP-Adresse | Statisch |
| Zugriff auf das Internet | Der Server muss Zugriff auf die folgenden URLs haben (entweder direkt oder über einen Proxyserver): <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (für horizontal hochskalierte Prozessserver nicht erforderlich) <br> - time.nist.gov <br> - time.windows.com |
| Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport)|
