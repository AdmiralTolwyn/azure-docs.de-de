---
title: Rechenintensive Java-Anwendung auf einem virtuellen Computer | Microsoft Docs
description: "Erfahren Sie, wie Sie einen virtuellen Azure-Computer erstellen können, der eine rechenintensive Java-Anwendung ausführt, die durch eine andere Java-Anwendung überwacht werden kann."
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ccccdf58fbb84605bc5dff29d870b373134f1f97
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Ausführen einer rechenintensiven Aufgabe in Java auf einem virtuellen Computer
> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager- und klassische Bereitstellung](../../../resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Mit Azure können Sie einen virtuellen Computer zum Verarbeiten rechenintensiver Aufgaben verwenden. Ein virtueller Computer kann beispielsweise Aufgaben verarbeiten und Clientcomputern oder mobilen Anwendungen Ergebnisse bereitstellen. Nach dem Lesen dieses Artikels wissen Sie, wie ein virtueller Computer erstellt wird, der eine rechenintensive Java-Anwendung ausführt, die durch eine andere Java-Anwendung überwacht werden kann.

Dieses Tutorial setzt voraus, dass Sie wissen, wie Java-Konsolenanwendungen erstellt werden, und dass Sie Bibliotheken in Ihre Java-Anwendung importieren und Java-Archive (JAR) generieren können. Kenntnisse zu Microsoft Azure werden nicht vorausgesetzt.

Sie erhalten Informationen zu folgenden Themen:

* Erstellen eines virtuellen Computers, auf dem bereits ein Java Development Kit (JDK) installiert ist
* Remoteanmeldung an Ihrem virtuellen Computer
* Erstellen eines Service Bus-Namespace
* Erstellen einer Java-Anwendung, die eine rechenintensive Aufgabe ausführt
* Erstellen einer Java-Anwendung, die den Fortschritt der rechenintensiven Aufgabe überwacht
* Ausführen der Java-Anwendungen
* Anhalten der Java-Anwendungen

Dieses Lernprogramm verwendet das "Traveling Salesman"-Problem für die rechenintensive Aufgabe. Es folgt ein Beispiel für die Java-Anwendung, in der die rechenintensive Aufgabe ausgeführt wird.

!["Traveling Salesman"-Problemlösung][solver_output]

Es folgt ein Beispiel für die Java-Anwendung, die die rechenintensive Aufgabe überwacht.

!["Traveling Salesman"-Problemclient][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>So erstellen Sie einen virtuellen Computer
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie nacheinander auf **Ressource erstellen**, **Compute**, **Virtueller Computer** und **Aus Katalog**.
3. Wählen Sie im Dialogfeld **Image ds virtuellen Computers auswählen** die Option **JDK 7 Windows Server 2012** aus.
   Beachten Sie, dass **JDK 6 Windows Server 2012** verfügbar ist, wenn Sie Legacyanwendungen haben, die noch nicht in JDK 7 ausgeführt werden können.
4. Klicken Sie auf **Weiter**.

5. Gehen Sie im Dialogfeld **Konfiguration des virtuellen Computers** wie folgt vor:
   1. Geben Sie einen Namen für den virtuellen Computer an.
   2. Geben Sie die Größe für den virtuellen Computer an.
   3. Geben Sie im Feld **Benutzername** einen Namen für den Administrator ein. Merken Sie sich diesen Namen und das als nächstes eingegebene Kennwort. Sie benötigen diese Daten, wenn Sie sich von einem Remotestandort aus an dem virtuellen Computer anmelden.
   4. Geben Sie im Feld **Neues Kennwort** ein Kennwort ein, und geben Sie dieses erneut im Feld **Kennwort bestätigen** ein. Dies ist das Kennwort für das Administratorkonto.
   5. Klicken Sie auf **Weiter**.

6. Gehen Sie im nächsten Dialogfeld **Konfiguration des virtuellen Computers** wie folgt vor:
   1. Verwenden Sie für den **Clouddienst** die Standardeinstellung **Neuen Clouddienst erstellen**.
   2. Der Wert für **DNS-Name des Clouddiensts** muss auf cloudapp.net eindeutig sein. Ändern Sie wenn nötig diesen Wert, sodass Azure angibt, dass er eindeutig ist.
   3. Geben Sie eine Region, eine Affinitätsgruppe oder ein virtuelles Netzwerk an. Geben Sie für dieses Lernprogramm als Region **West-USA**an.
   4. Wählen Sie unter **Speicherkonto** die Option **Automatisch generiertes Speicherkonto verwenden** aus.
   5. Wählen Sie unter **Verfügbarkeitsgruppe** die Option **(Keine)** aus.
   6. Klicken Sie auf **Weiter**.

7. Gehen Sie im letzten Dialogfeld **Konfiguration des virtuellen Computers** wie folgt vor:
   1. Akzeptieren Sie die Standardeinträge für Endpunkte.
   2. Klicken Sie auf **Fertig stellen**.

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>So melden Sie sich von einem Remotestandort aus an Ihrem virtuellen Computer an
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie auf **Virtuelle Computer**.
3. Klicken Sie auf den Namen des virtuellen Computers, an dem Sie sich anmelden möchten.
4. Klicken Sie auf **Verbinden**.
5. Befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. Wenn Sie zur Eingabe des Administratornamens und des Kennworts aufgefordert werden, verwenden Sie die Werte, die Sie beim Erstellen des virtuellen Computers bereitgestellt haben.

Beachten Sie, dass die Azure Service Bus-Funktion erfordert, dass das Baltimore CyberTrust-Stammzertifikat als Teil des JRE- **cacerts** -Stores installiert wird. Dieses Zertifikat ist automatisch in der in diesem Lernprogramm verwendeten JRE (Java Runtime Environment) enthalten. Falls dieses Zertifikat nicht in Ihrem JRE-**cacerts**-Speicher vorhanden ist, finden Sie Informationen zum Hinzufügen (sowie zum Anzeigen der Zertifikate in Ihrem cacerts-Store) unter [Hinzufügen eines Zertifikats zum Java-Zertifizierungsstellen-Zertifikatspeicher][add_ca_cert].

## <a name="how-to-create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace
Um Service Bus-Warteschlangen in Azure zu verwenden, müssen Sie zunächst einen Dienstnamespace erstellen. Ein Dienstnamespace ist eine Bereichseinheit zur Adressierung von Servicebus-Ressourcen innerhalb Ihrer Anwendung.

So erstellen Sie einen Dienstnamespace:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie unten links im Navigationsbereich des Azure-Portals auf **Service Bus, Zugriffssteuerung und Cache**.
3. Klicken Sie im Azure-Portal links oben auf den Knoten **Service Bus** und dann auf die Schaltfläche **Neu**.  
   ![Screenshot des Service Bus-Knotens][svc_bus_node]
4. Geben Sie im Dialogfeld **Neuen Dienstnamespace erstellen** einen **Namespace** ein, und klicken Sie dann auf die Schaltfläche **Verfügbarkeit prüfen**, um sicherzustellen, dass er eindeutig ist.  
   ![Screenshot des Erstellens eines neuen Namespace][create_namespace]
5. Nachdem Sie sichergestellt haben, dass der Namespace verfügbar ist, wählen Sie das Land oder die Region aus, in dem bzw. der Ihr Namespace gehostet werden soll, und klicken Sie dann auf die Schaltfläche **Namespace erstellen**.  
   
   Der erstellte Namespace wird dann im Azure-Portal angezeigt und nach einem Moment aktiviert. Warten Sie, bis **Active** als Status angezeigt wird, bevor Sie mit dem nächsten Schritt fortfahren.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Abrufen der Standard-Anmeldeinformationen für die Namespaceverwaltung
Um Verwaltungsvorgänge im neuen Namespace auszuführen, wie zum Beispiel das Erstellen einer Warteschlange, müssen Sie die Verwaltungsanmeldeinformationen für den Namespace abrufen.

1. Klicken Sie im linken Navigationsbereich auf den Knoten **Service Bus**, um die Liste der verfügbaren Namespaces anzuzeigen.
   ![Screenshot der verfügbaren Namespaces][avail_namespaces]
2. Wählen Sie in der angezeigten Liste den Namespace, den Sie gerade erstellt haben.
   ![Screenshot der Namespaceliste][namespace_list]
3. Im Bereich **Eigenschaften** auf der rechten Seite werden die Eigenschaften des neuen Namespace aufgelistet.
   ![Screenshot des Eigenschaftenbereichs][properties_pane]
4. Der **Standardschlüssel** ist ausgeblendet. Klicken Sie auf die Schaltfläche **Anzeigen** , um die Sicherheitsanmeldeinformationen anzuzeigen.
   ![Screenshot des Standardschlüssels][default_key]
5. Notieren Sie sich den **Standardaussteller** und den **Standardschlüssel**, da Sie diese Informationen weiter unten zum Durchführen von Vorgängen mit dem Namespace verwenden werden.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Erstellen einer Java-Anwendung für die Ausführung einer rechenintensiven Aufgabe
1. Laden Sie auf dem Entwicklungscomputer (der nicht mit dem erstellten virtuellen Computer identisch sein muss) das [Azure-SDK für Java](https://azure.microsoft.com/develop/java/)herunter.
2. Erstellen Sie eine Java-Konsolenanwendung mithilfe des Beispielcodes am Ende dieses Abschnitts. In diesem Tutorial wird **TSPSolver.java** als Java-Dateiname verwendet. Ändern Sie die Platzhalter **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** und **your\_service\_bus\_key** so, dass Ihre Service Bus-Werte für **Namespace**, **Standardaussteller** und **Standardschlüssel** verwendet werden.
3. Exportieren Sie die Anwendung nach der Programmierung in ein ausführbares Java-Archiv (JAR), und packen Sie die erforderlichen Bibliotheken in die erzeugte JAR-Datei. In diesem Tutorial wird **TSPSolver.jar** als Name der erzeugten JAR-Datei verwendet.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Erstellen einer Java-Anwendung zum Überwachen des Fortschritts der rechenintensiven Aufgabe
1. Erstellen Sie auf dem Entwicklungscomputer eine Java-Konsolenanwendung mithilfe des Beispielcodes am Ende dieses Abschnitts. In diesem Tutorial wird **TSPClient.java** als Java-Dateiname verwendet. Wie oben gezeigt, ändern Sie die Platzhalter **your\_service\_bus\_namespace**, **your\_service\_bus\_owner** und **your\_service\_bus\_key** so, dass Ihre Service Bus-Werte für **Namespace**, **Standardaussteller** und **Standardschlüssel** verwendet werden.
2. Exportieren Sie die Anwendung in ein ausführbares Java-Archiv (JAR), und packen Sie die erforderlichen Bibliotheken in die erzeugte JAR-Datei. In diesem Tutorial wird **TSPClient.jar** als Name der erzeugten JAR-Datei verwendet.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Ausführen der Java-Anwendungen
Führen Sie die rechenintensive Anwendung aus, zunächst um die Warteschlage zu erstellen, dann um das "Traveling Salesman"-Problem zu lösen. Dadurch wird die aktuelle beste Route zur Service Bus-Warteschlange hinzugefügt. Während die rechenintensive Anwendung ausgeführt wird (oder danach), führen Sie den Client aus, um Ergebnisse aus der Service Bus-Warteschlange anzuzeigen.

### <a name="to-run-the-compute-intensive-application"></a>So führen Sie die rechenintensive Anwendung aus
1. Melden Sie sich am virtuellen Computer an.
2. Erstellen Sie einen Ordner, in dem die Anwendung ausgeführt wird. Zum Beispiel **c:\TSP**.
3. Kopieren Sie **TSPSolver.jar** nach **c:\TSP**.
4. Erstellen Sie eine Datei namens **c:\TSP\cities.txt** mit folgendem Inhalt.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. Wechseln Sie an einer Eingabeaufforderung zum Verzeichnis "c:\TSP".
6. Stellen Sie sicher, dass sich der Ordner "bin" der JRE in der PATH-Umgebungsvariable befindet.
7. Sie müssen die Service Bus-Warteschlange erstellen, bevor Sie die TSP-Solver-Permutationen ausführen. Führen Sie den folgenden Befehl aus, um die Service Bus-Warteschlange zu erstellen:
   
        java -jar TSPSolver.jar createqueue
8. Da die Warteschlange jetzt erstellt wurde, können Sie die TSP-Solver-Permutationen ausführen. Führen Sie beispielsweise folgenden Befehl aus, um den Solver für acht Städte auszuführen.
   
        java -jar TSPSolver.jar 8
   
   Falls Sie keine Zahl angeben, wird er für zehn Städte ausgeführt. Wenn der Solver aktuelle kürzeste Routen findet, fügt er sie der Warteschlange hinzu.

> [!NOTE]
> Je größer die angegebene Zahl, desto länger wird der Solver ausgeführt. Die Ausführung für 14 Städte könnte zum Beispiel mehrere Minuten dauern, und die Ausführung für 15 Städte könnte mehrere Stunden dauern. Wird der Wert auf 16 oder darüber hinaus erhöht, könnte die Ausführung mehrere Tage (und schließlich Wochen, Monate und Jahre) dauern. Dies liegt an der schnellen Zunahme der Anzahl der Permutationen, die vom Solver analysiert werden, wenn die Anzahl der Städte zunimmt.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Ausführen der überwachenden Clientanwendung
1. Melden Sie sich an dem Computer an, auf dem die Clientanwendung ausgeführt wird. Dieser Computer muss nicht zwingend mit dem Computer identisch sein, auf dem die **TSPSolver** -Anwendung ausgeführt wird.
2. Erstellen Sie einen Ordner, in dem die Anwendung ausgeführt wird. Zum Beispiel **c:\TSP**.
3. Kopieren Sie **TSPClient.jar** nach **c:\TSP**.
4. Stellen Sie sicher, dass sich der Ordner "bin" der JRE in der PATH-Umgebungsvariable befindet.
5. Wechseln Sie an einer Eingabeaufforderung zum Verzeichnis "c:\TSP".
6. Führen Sie den folgenden Befehl aus:
   
        java -jar TSPClient.jar
   
    Geben Sie optional die Pause zwischen den Überprüfungen der Warteschlange in Minuten an, indem Sie ein Befehlszeilenargument übergeben. Die Standardpause beim Überprüfen der Warteschlange beträgt drei Minuten. Dieser Wert wird verwendet, wenn kein Befehlszeilenargument an **TSPClient** übergeben wird. Wenn Sie einen anderen Wert für das Intervall verwenden möchten, zum Beispiel eine Minute, führen Sie den folgenden Befehl aus:
   
        java -jar TSPClient.jar 1
   
    Der Client wird ausgeführt, bis er die Warteschlangenmeldung "Complete" erhält. Beachten Sie, dass Sie beim Ausführen mehrerer Instanzen des Solvers ohne Ausführen des Clients den Client möglicherweise mehrfach ausführen müssen, um die Warteschlange vollständig zu leeren. Alternativ können Sie auch die Warteschlange löschen und dann neu erstellen. Um die Warteschlange zu löschen, führen Sie den folgenden **TSPSolver**-Befehl (nicht **TSPClient**-Befehl) aus.
   
        java -jar TSPSolver.jar deletequeue
   
    Der Solver wird ausgeführt, bis alle Routen untersucht wurden.

## <a name="how-to-stop-the-java-applications"></a>Anhalten der Java-Anwendungen
Für Solver- und Clientanwendungen können Sie **Strg+C** drücken, um die Anwendung vor dem normalen Abschluss zu beenden.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
