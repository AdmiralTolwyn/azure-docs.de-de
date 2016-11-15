<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>So erstellen Sie ein Volume
1. Klicken Sie auf dem Gerät auf der Seite **Schnellstart** auf **Volume hinzufügen**. Dadurch wird der Assistent "Volume hinzufügen" gestartet.
2. Gehen Sie im Assistenten "Volume hinzufügen" unter **Grundlegende Einstellungen**folgendermaßen vor:
   
   1. Geben Sie den **Namen** für das Volume ein.
   2. Geben Sie die **Bereitgestellte Kapazität** für das Volume in GB oder TB an. Die Volumekapazität muss für ein physisches Gerät zwischen 1 GB und 64 TB liegen.
   3. Wählen Sie aus der Dropdownliste den **Verwendungstyp** für das Volume aus. 
   4. Wenn Sie dieses Volume für archivierte Daten verwenden, aktivieren Sie das Kontrollkästchen **Use this volume for less frequently accessed archival data** . Wählen Sie für alle anderen Verwendungstypen einfach **Mehrstufiges Volume**aus. (Mehrstufige Volumes wurden früher primäre Volumes genannt).
      
        ![Volume hinzufügen](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. Klicken Sie auf das Pfeilsymbol  ![Pfeilsymbol](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) , um zur nächsten Seite zu wechseln.
3. Fügen Sie im Dialogfeld **Zusätzliche Einstellungen** einen neuen Datensatz für die Zugriffssteuerung (Access Control Record, ACR) hinzu:
   
   1. Geben Sie einen **Namen** für den ACR ein.
   2. Geben Sie unter **iSCSI-Initiatorname**den qualifizierten iSCSI-Namen (IQN) des Windows-Hosts an. Wenn Sie den IQN nicht kennen, fahren Sie mit [Abrufen des IQNs eines Windows Server-Hosts](#get-the-iqn-of-a-windows-server-host)fort.
   3. Es wird empfohlen, eine Standardsicherung zu aktivieren, indem Sie das Kontrollkästchen **Standardsicherung für dieses Volume aktivieren** aktivieren. Die Standardsicherung erstellt eine Richtlinie, die jeden Tag um 22:30 Uhr ausgeführt wird (Uhrzeit des Geräts) und eine Cloud-Momentaufnahme dieses Volumes erstellt.
      
      > [!NOTE]
      > Nachdem die Sicherung hier aktiviert wurde, kann sie nicht zurückgesetzt werden. Sie müssen das Volume bearbeiten, um diese Einstellung zu ändern.
      > 
      > 
      
        ![Volume hinzufügen](./media/storsimple-create-volume/AddVolume2-include.png)
4. Klicken Sie auf das Häkchensymbol  ![Häkchensymbol](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Ein Volume wird mit den angegebenen Einstellungen erstellt.

![Video verfügbar](./media/storsimple-create-volume/Video_icon.png) **Video verfügbar**

Sie können sich [hier](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/)ein Video anschauen, in dem das Erstellen eines StorSimple-Volumes demonstriert wird.



<!--HONumber=Nov16_HO2-->


