---
title: Ausführen beliebiger Windows-Apps auf jedem Gerät mit Azure RemoteApp | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe von Azure RemoteApp jede Windows-App für Benutzer freigeben können.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin
editor: ''

ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: elizapo

---
# Mit Azure RemoteApp jede Windows-Anwendung auf jedem Gerät ausführen
> [!IMPORTANT]
> Azure RemoteApp wird eingestellt. Details finden Sie in der [Ankündigung](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Sie können eine Windows-Anwendung überall und auf jedem Gerät ausführen – einfach, indem Sie Azure RemoteApp verwenden. Ob eine vor zehn Jahren geschriebene benutzerdefinierte Anwendung oder Office-App – Ihre Benutzer sind für diese Anwendungen nicht länger an ein bestimmtes Betriebssystem (wie Windows XP) gebunden.

Dank Azure RemoteApp können Ihre Benutzer auch ihre eigenen Android- oder Apple-Geräte verwenden und die gleiche Nutzererfahrung wie mit Windows (oder Windows Phones) machen. Dies geschieht durch Hosten der Windows-Anwendung in einer Sammlung virtueller Windows-Computer in Azure – auf die Benutzer überall zugreifen können, wo eine Internetverbindung besteht.

Lesen Sie weiter, um zu erfahren, wie genau dies funktioniert.

In diesem Artikel geben wir Access für alle Benutzer frei. Sie können jedoch jede BELIEBIGE Anwendung verwenden. Solange Sie die App auf einem Windows Server 2012 R2-Computer installieren können, können Sie sie mithilfe der nachfolgenden Schritte freigeben. Sie können die[App-Anforderungen](remoteapp-appreqs.md) überprüfen, um sicherzustellen, dass Ihre App funktioniert.

Bitte beachten Sie, dass da Access eine Datenbank ist und die Datenbank hilfreich sein soll, wir einige zusätzliche Schritte unternehmen, um Benutzern den Zugriff auf die Access-Datenfreigabe zu ermöglichen. Wenn Ihre App keine Datenbank ist oder es nicht erforderlich ist, dass die Benutzer auf eine Dateifreigabe zugreifen können, können Sie die Schritte in diesem Tutorial überspringen.

> [!NOTE]
> <a name="note"></a>Für dieses Tutorial benötigen Sie ein Azure-Konto:
> 
> * Sie können [ein Azure-Konto kostenlos erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F): Sie erhalten ein Guthaben, das Sie zum Ausprobieren zahlungspflichtiger Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste wie Websites nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind.
> * Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Ihr MSDN-Abonnement beinhaltet ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste verwenden können.
> 
> 

## Erstellen einer RemoteApp-Sammlung
Zunächst erstellen Sie eine Sammlung. Die Sammlung fungiert als Container für Ihre Anwendungen und Ihre Benutzer. Jede Sammlung basiert auf einem Image. Sie können ein eigenes Image erstellen oder ein Image verwenden, das in Ihrem Abonnement zur Verfügung steht. Für dieses Tutorial verwenden wir das Office 2013-Testimage. Es enthält die Anwendung, die wir gemeinsam nutzen möchten.

1. Scrollen Sie im Azure-Portal im linken Navigationsbereich nach unten, bis RemoteApp angezeigt wird. Öffnen Sie die Seite.
2. Klicken Sie auf **Erstellen einer RemoteApp-Sammlung**.
3. Klicken Sie auf **Schnellerfassung** und geben Sie einen Namen für die Sammlung ein.
4. Wählen Sie die Region, die Sie verwenden möchten, um Ihre Sammlung zu erstellen. Wählen Sie für optimale Ergebnisse die Region, die dem Standort geografisch am nächsten ist, an dem die Benutzer auf die Anwendung zugreifen. In diesem Tutorial befinden sich die Benutzer beispielsweise in Redmond, Washington. Die nächstgelegene Azure-Region ist **West US**.
5. Wählen Sie den Abrechnungsplan aus, den Sie verwenden möchten. Der grundlegende Abrechnungsplan sieht 16 Benutzer auf einer großen Azure-VM voraus, während der standardmäßige Abrechnungsplan 10 Benutzer auf eine große Azure-VM vorsieht. Als allgemeines Beispiel funktioniert der Standardplan gut bei einem Dateneintrags-Workflow. Bei einer Produktivitätsanwendung wie Office sollten Sie den Standardplan nehmen.
6. Wählen Sie abschließend das Office 2013 Professional-Image. Dieses Image enthält Office 2013-Anwendungen. Nur zur Erinnerung: Dieses Image ist nur für Testsammlungen und Machbarkeitsstudien geeignet. Sie können dieses Image nicht für eine Produktionssammlung verwenden.
7. Klicken Sie nun auf **RemoteApp-Sammlung erstellen**.

![Erstellen einer Cloudsammlung über RemoteApp](./media/remoteapp-anyapp/ra-anyappcreatecollection.png)

Das Erstellen Ihrer Sammlung wird gestartet. Dies kann bis zu einer Stunde dauern.

Sie nun können Ihre Benutzer hinzufügen.

## Die Anwendung für Benutzer freigeben
Nachdem Ihre Sammlung erfolgreich erstellt wurde, ist es Zeit, Access für Benutzer zu veröffentlichen und die Benutzer hinzuzufügen, die Zugriff haben sollen.

Wenn Sie während der Sammlungserstellung vom Azure-RemoteApp-Knoten weg navigiert sind, finden Sie den Weg dorthin von der Azure-Homepage wieder zurück.

1. Klicken Sie auf die zuvor erstellte Sammlung, um zusätzliche Optionen zu erhalten, und konfigurieren Sie die Sammlung.
   ![Eine neue RemoteApp-Cloudsammlung](./media/remoteapp-anyapp/ra-anyappcollection.png)
2. Auf der Registerkarte **Veröffentlichen** klicken Sie auf **Veröffentlichen** unten auf dem Bildschirm, und klicken Sie dann auf **Startmenüprogramme veröffentlichen**.
   ![Veröffentlichen Sie ein RemoteApp-Programm](./media/remoteapp-anyapp/ra-anyapppublish.png)
3. Wählen Sie die Anwendungen aus, die Sie aus der Liste veröffentlichen möchten. Für unsere Zwecke haben wir Access ausgewählt. Klicken Sie auf **Fertig stellen**. Warten Sie, bis die Veröffentlichung der Anwendungen abgeschlossen ist.
   ![Access in RemoteApp veröffentlichen](./media/remoteapp-anyapp/ra-anyapppublishaccess.png)
4. Gehen Sie nach der Veröffentlichung der Anwendung weiter zur Registerkarte **Benutzerzugriff**, um alle Benutzer hinzuzufügen, die Zugriff auf Ihre Anwendungen benötigen. Geben Sie Benutzernamen (E-Mail-Adresse) für Ihre Benutzer ein, und klicken Sie dann auf **Speichern**.

![Hinzufügen von Benutzern in RemoteApp](./media/remoteapp-anyapp/ra-anyappaddusers.png)

1. Jetzt müssen Sie Ihren Benutzern mitteilen, wie sie auf die neuen Apps zugreifen können. Dazu senden Sie Ihren Benutzern eine E-Mail, die auf die Download-URL für den Remotedesktop-Client verweist.
   ![Die Download-URL des Clients für RemoteApp](./media/remoteapp-anyapp/ra-anyappurl.png)

## Konfigurieren Sie den Zugriff auf Access
Einige Anwendungen benötigen eine zusätzliche Konfiguration, nachdem Sie sie über RemoteApp bereitgestellt haben. Wir werden speziell für Access eine Dateifreigabe auf Azure erstellen, auf die jeder Benutzer zugreifen kann. (Wenn Sie dies nicht tun möchten, können Sie eine [Hybrid-Sammlung](remoteapp-create-hybrid-deployment.md) [anstelle unserer Cloudsammlung] erstellen, mit der die Benutzer Zugriff auf Dateien und Informationen in Ihrem lokalen Netzwerk erhalten.) Anschließend sollen die Benutzer dem Azure-Dateisystem ein lokales Laufwerk auf ihrem Computer zuordnen.

Den ersten Teil führen Sie als Administrator aus. Dann müssen Ihre Benutzer einige Schritte durchführen.

1. Legen Sie los, indem Sie die Befehlszeilenschnittstelle (cmd.exe) veröffentlichen. Wählen Sie in der Registerkarte **Veröffentlichen** **Cmd** aus und klicken Sie dann auf **Veröffentlichen > Programm mit Pfad veröffentlichen**.
2. Geben Sie den Namen der Anwendung und den Pfad ein. Verwenden Sie zu diesem Zweck "File Explorer" als Name und "% SYSTEMDRIVE%\\windows\\explorer.exe" als Pfad.  
   ![Veröffentlichen Sie die Datei cmd.exe.](./media/remoteapp-anyapp/ra-publishcmd.png)
3. Nun müssen Sie ein Azure-[Speicherkonto](../storage/storage-create-storage-account.md) erstellen. Wir haben unseres "accessstorage" genannt. Wählen Sie einen Namen, der für Sie von Bedeutung ist. (Denken Sie an Highlander: Es kann nur einen "accessstorage" geben.)  
   ![Unser Azure-Speicherkonto.](./media/remoteapp-anyapp/ra-anyappazurestorage.png)
4. Kehren Sie zurück zu Ihrem Dashboard, sodass Sie den Pfad zu Ihrem Speicherort (Endpunkt) abrufen können. Da Sie diesen gleich benötigen, kopieren Sie ihn irgendwo hin.  
   ![Der Speicherkontopfad](./media/remoteapp-anyapp/ra-anyappstoragelocation.png)
5. Nachdem das Speicherkonto erstellt wurde, benötigen Sie als Nächstes den primären Zugriffsschlüssel. Klicken Sie auf **Zugriffstasten verwalten** und kopieren Sie dann den primären Zugriffsschlüssel.
6. Nun legen Sie den Kontext des Speicherkontos fest und erstellen eine neue Dateifreigabe für Access. Führen Sie die folgenden Cmdlets in einem Windows PowerShell-Fenster mit erhöhten Rechten aus:
   
        $ctx=New-AzureStorageContext <account name> <account key>
        $s = New-AzureStorageShare <share name> -Context $ctx
   
    Für uns sind dies die Cmdlets, die wir ausführen:
   
        $ctx=New-AzureStorageContext accessstorage <key>
        $s = New-AzureStorageShare <share name> -Context $ctx

Jetzt ist der Benutzer an der Reihe. Erstens müssen Ihre Benutzer einen [RemoteApp-Client](remoteapp-clients.md) installieren. Als Nächstes müssen die Benutzer ein Laufwerk von ihrem Konto zu dieser von Ihnen erstellten Azure-Dateifreigabe zuordnen und ihre Access-Dateien hinzufügen. So wird es gemacht:

1. Greifen Sie im RemoteApp-Client auf die veröffentlichten Anwendungen zu. Starten Sie das cmd.exe-Programm.
2. Führen Sie den folgenden Befehl zum Zuordnen eines Laufwerks Ihres Computers zur Dateifreigabe aus:
   
        net use z: \<accountname>.file.core.windows.net<share name> /u:<user name> <account key>
   
    Wenn Sie den Parameter **/persistent** auf "yes" festlegen, wird das zugeordnete Laufwerk sitzungsübergreifend beibehalten.
3. Starten Sie jetzt die Datei-Explorer-Anwendung von RemoteApp. Kopieren Sie alle Access-Dateien, die Sie in der freigegebenen Anwendung für die Dateifreigabe verwenden möchten. ![Access-Dateien in eine Azure-Freigabe einstellen](./media/remoteapp-anyapp/ra-anyappuseraccess.png)
4. Öffnen Sie zum Schluss Access, und öffnen Sie dann die Datenbank, die Sie soeben freigegeben haben. Ihre ausgeführten Access-Daten sollten nun in der Cloud angezeigt werden. ![Eine echte Access-Datenbank, die in der Cloud ausgeführt wird](./media/remoteapp-anyapp/ra-anyapprunningaccess.png)

Sie können Access jetzt auf jedem Ihrer Geräte benutzen - Sie müssen nur sicherstellen, dass Sie einen RemoteApp-Client installiert haben.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
Nun, da Sie eine Sammlung erstellen können, versuchen Sie das Erstellen einer [Sammlung, die Office 365 verwendet](remoteapp-tutorial-o365anywhere.md). Oder Sie erstellen eine [Hybrid-Sammlung](remoteapp-create-hybrid-deployment.md), die auf Ihr lokales Netzwerk zugreifen kann.

<!--Image references-->


<!---HONumber=AcomDC_0817_2016-->