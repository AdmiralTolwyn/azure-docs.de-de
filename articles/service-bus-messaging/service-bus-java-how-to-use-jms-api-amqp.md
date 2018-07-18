---
title: Verwenden von AMQP 1.0 mit der Java Service Bus-API | Microsoft Docs
description: Erfahren Sie, wie Sie den Java Message Service (JMS) mit Azure Service Bus und Advanced Message Queuing Protocol (AMQP) 1.0 verwenden.
services: service-bus-messaging
documentationcenter: java
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: be766f42-6fd1-410c-b275-8c400c811519
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 0848facd764c4fb0d7f95c1ae89ecb02a32257e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23044175"
---
# <a name="how-to-use-the-java-message-service-jms-api-with-service-bus-and-amqp-10"></a>Verwenden der JMS (Java Message Service)-API mit Service Bus und AMQP 1.0
AMQP (Advanced Message Queuing Protocol) 1.0 ist ein effizientes, zuverlässiges Messagingprotokoll auf Wire-Ebene, mit dem Sie robuste und plattformübergreifende Messaginganwendungen erstellen können.

Unterstützung für AMQP 1.0 im Service Bus bedeutet, dass Sie die gebrokerten Messagingfunktionen für Warteschlangen und Veröffentlichen/Abonnieren mithilfe eines effizienten binären Protokolls auf unterschiedlichen Plattformen nutzen können. Zudem können Sie Anwendungen erstellen, deren Komponenten mit einer Mischung aus Sprachen, Frameworks und Betriebssystemen erstellt wurden.

In diesem Artikel wird beschrieben, wie die Messagingfunktionen von Service Bus (Warteschlange und Veröffentlichen/Abonnieren von Themen) in Java-Anwendungen mit dem beliebten API-Standard Java Message Service (JMS) verwendet werden. In einer [separaten Anleitung](service-bus-amqp-dotnet.md) wird erklärt, wie Sie dieselbe Aufgabe mithilfe der .NET-API für Service Bus durchführen. Sie können diese beiden Anleitungen verwenden, um weitere Informationen zur plattformübergreifenden Nachrichtenübermittlung unter Verwendung von AMQP 1.0 zu erhalten.

## <a name="get-started-with-service-bus"></a>Erste Schritte mit Service Bus
In diesem Leitfaden wird davon ausgegangen, dass Sie bereits einen Service Bus-Namespace haben, der eine Warteschlange mit dem Namen **queue1** enthält. Falls nicht, können Sie [den Namespace und die Warteschlange](service-bus-create-namespace-portal.md) im [Azure-Portal](https://portal.azure.com) erstellen. Weitere Informationen zum Erstellen von Namespaces und Warteschlangen für Service Bus finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md).

> [!NOTE]
> Partitionierte Warteschlangen und Themen unterstützen zudem AMQP. Weitere Informationen finden Sie unter [Partitionierte Messagingentitäten](service-bus-partitioning.md) und [AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus](service-bus-partitioned-queues-and-topics-amqp-overview.md).
> 
> 

## <a name="downloading-the-amqp-10-jms-client-library"></a>Herunterladen der AMQP 1.0 JMS-Clientbibliothek
Informationen zum Herunterladen der neuesten Version der Apache Qpid JMS AMQP 1.0-Clientbibliothek finden Sie unter [https://qpid.apache.org/download.html](https://qpid.apache.org/download.html).

Folgende vier JAR-Dateien müssen aus dem Apache Qpid JMS AMQP 1.0-Verteilungsarchiv zu dem Java-KLASSENPFAD hinzugefügt werden, wenn JMS-Anwendungen mit Service Bus erstellt und ausgeführt werden:

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-amqp-1-0-client-[version].jar
* qpid-amqp-1-0-client-jms-[version].jar
* qpid-amqp-1-0-common-[version].jar

## <a name="coding-java-applications"></a>Programmieren von Java-Anwendungen
### <a name="java-naming-and-directory-interface-jndi"></a>JNDI (Java Naming and Directory Interface; Java Benennungs- und Verzeichnisschnittstelle)
JMS verwendet die Java Naming and Directory Interface (JNDI), um eine Trennung zwischen logischen und physischen Namen umzusetzen. Zwei Typen von JMS-Objekten werden mit JNDI aufgelöst: ConnectionFactory und Destination. JNDI verwendet ein Anbietermodell, das Sie mit verschiedenen Verzeichnisdiensten verbinden können, um Namensauflösungsfunktionen zu implementieren. Die Apache Qpid JMS AMQP 1.0-Bibliothek enthält einen einfachen JNDI-Anbieter, der mithilfe von properties-Dateien im folgenden Format konfiguriert wird:

```
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="configure-the-connectionfactory"></a>Konfigurieren der ConnectionFactory
Der Eintrag zum Definieren einer **ConnectionFactory** in der Qpid Properties-Datei für den JNDI-Anbieter hat das folgende Format:

```
connectionfactory.[jndi_name] = [ConnectionURL]
```

Dabei bedeuten **[jndi_name]** und **[ConnectionURL]** Folgendes:

* **[jndi_name]:** der logische Name der ConnectionFactory. Dieser Name wird in der Java-Anwendung mithilfe der JNDI-Methode IntialContext.lookup() aufgelöst.
* **[ConnectionURL]:** Diese URL liefert der JMS-Bibliothek die vom AMQP-Broker benötigten Informationen.

**ConnectionURL** hat das folgende Format:

```
amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
```
Dabei bedeuten **[namespace]**, **[SASPolicyName]** und **[SASPolicyKey]** Folgendes:

* **[namespace]**: Der Service Bus-Namespace.
* **[SASPolicyName]**: Der Name der SAS-Richtlinie (Shared Access Signature) der Warteschlange.
* **[SASPolicyKey]**: Der Shared Access Signature-Richtlinienschlüssel der Warteschlange.

> [!NOTE]
> Sie müssen das Kennwort manuell als URL codieren. Ein nützliches URL-Codierungshilfsprogramm ist unter [http://www.w3schools.com/tags/ref_urlencode.asp](http://www.w3schools.com/tags/ref_urlencode.asp) verfügbar.
> 
> 

#### <a name="configure-destinations"></a>Konfigurieren von Zielen
Der Eintrag zum Definieren eines Ziels in der Qpid Properties-Datei für den JNDI-Anbieter hat das folgende Format:

```
queue.[jndi_name] = [physical_name]
```

oder

```
topic.[jndi_name] = [physical_name]
```

Wobei **[jndi\_name]** und **[physical\_name]** die folgenden Bedeutungen haben:

* **[jndi_name]:** der logische Name des Ziels. Dieser Name wird in der Java-Anwendung mithilfe der JNDI-Methode IntialContext.lookup() aufgelöst.
* **[physical_name]:** der Name der Service Bus-Entität, mit der die Anwendung Nachrichten austauscht.

> [!NOTE]
> Beim Empfang von einem Service Bus-Themenabonnement sollte der in JNDI angegebene physische Name dem Themennamen entsprechen. Der Abonnementname wird bei der Erstellung des Abonnements im JMS-Anwendungscode angegeben. Im [Entwicklerhandbuch für Service Bus AMQP 1.0](service-bus-amqp-dotnet.md) finden Sie weitere Details zum Arbeiten mit Service Bus-Themen in JMS.
> 
> 

### <a name="write-the-jms-application"></a>Schreiben der JMS-Anwendung
Für die Verwendung von JMS mit Service Bus werden keine speziellen Programmierschnittstellen oder Optionen benötigt. Es gibt jedoch einige Einschränkungen, die an späterer Stelle erläutert werden. Wie bei allen JMS-Anwendungen müssen Sie zuerst die JNDI-Umgebung konfigurieren, um eine **ConnectionFactory** und Ziele auflösen zu können.

#### <a name="configure-the-jndi-initialcontext"></a>Konfigurieren des JNDI InitialContext
Zur Konfiguration der JNDI-Umgebung wird eine Hashtabelle mit Konfigurationsinformationen an den Konstruktor der Klasse javax.naming.InitialContext übergeben. Obligatorisch sind in der Hashtabelle zwei Elemente: der Klassenname der Initial Context Factory und die Anbieter-URL. Der folgende Code zeigt, wie Sie die JNDI-Umgebung mithilfe des auf Eigenschaftendateien basierenden JNDI-Anbieters Qpid mit der Eigenschaftendatei **servicebus.properties** konfigurieren.

```java
Hashtable<String, String> env = new Hashtable<String, String>(); 
env.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory"); 
env.put(Context.PROVIDER_URL, "servicebus.properties"); 
InitialContext context = new InitialContext(env);
``` 

### <a name="a-simple-jms-application-using-a-service-bus-queue"></a>Einfache JMS-Anwendung mit einer Service Bus-Warteschlange
Das folgende Beispielprogramm sendet JMS-TextMessages an eine Service Bus-Warteschlange mit dem logischen JNDI-Namen QUEUE und empfängt die zurückkommenden Nachrichten.

```java
// SimpleSenderReceiver.java

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Hashtable;
import java.util.Random;

public class SimpleSenderReceiver implements MessageListener {
    private static boolean runReceiver = true;
    private Connection connection;
    private Session sendSession;
    private Session receiveSession;
    private MessageProducer sender;
    private MessageConsumer receiver;
    private static Random randomGenerator = new Random();

    public SimpleSenderReceiver() throws Exception {
        // Configure JNDI environment
        Hashtable<String, String> env = new Hashtable<String, String>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, 
                   "org.apache.qpid.amqp_1_0.jms.jndi.PropertiesFileInitialContextFactory");
        env.put(Context.PROVIDER_URL, "servicebus.properties");
        Context context = new InitialContext(env);

        // Look up ConnectionFactory and Queue
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        Destination queue = (Destination) context.lookup("QUEUE");

        // Create Connection
        connection = cf.createConnection();

        // Create sender-side Session and MessageProducer
        sendSession = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        sender = sendSession.createProducer(queue);

        if (runReceiver) {
            // Create receiver-side Session, MessageConsumer,and MessageListener
            receiveSession = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            receiver = receiveSession.createConsumer(queue);
            receiver.setMessageListener(this);
            connection.start();
        }
    }

    public static void main(String[] args) {
        try {

            if ((args.length > 0) && args[0].equalsIgnoreCase("sendonly")) {
                runReceiver = false;
            }

            SimpleSenderReceiver simpleSenderReceiver = new SimpleSenderReceiver();
            System.out.println("Press [enter] to send a message. Type 'exit' + [enter] to quit.");
            BufferedReader commandLine = new java.io.BufferedReader(new InputStreamReader(System.in));

            while (true) {
                String s = commandLine.readLine();
                if (s.equalsIgnoreCase("exit")) {
                    simpleSenderReceiver.close();
                    System.exit(0);
                } else {
                    simpleSenderReceiver.sendMessage();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private void sendMessage() throws JMSException {
        TextMessage message = sendSession.createTextMessage();
        message.setText("Test AMQP message from JMS");
        long randomMessageID = randomGenerator.nextLong() >>>1;
        message.setJMSMessageID("ID:" + randomMessageID);
        sender.send(message);
        System.out.println("Sent message with JMSMessageID = " + message.getJMSMessageID());
    }

    public void close() throws JMSException {
        connection.close();
    }

    public void onMessage(Message message) {
        try {
            System.out.println("Received message with JMSMessageID = " + message.getJMSMessageID());
            message.acknowledge();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}    
```

### <a name="run-the-application"></a>Ausführen der Anwendung
Das Ergebnis nach Ausführen der Anwendung sieht folgendermaßen aus:

```
> java SimpleSenderReceiver
Press [enter] to send a message. Type 'exit' + [enter] to quit.

Sent message with JMSMessageID = ID:2867600614942270318
Received message with JMSMessageID = ID:2867600614942270318

Sent message with JMSMessageID = ID:7578408152750301483
Received message with JMSMessageID = ID:7578408152750301483

Sent message with JMSMessageID = ID:956102171969368961
Received message with JMSMessageID = ID:956102171969368961
exit
```

## <a name="cross-platform-messaging-between-jms-and-net"></a>Plattformübergreifendes Messaging mit JMS und .NET
In diesem Leitfaden wurde bisher gezeigt, wie Nachrichten mit JMD an den Service Bus gesendet und von diesem empfangen werden. Ein wesentlicher Vorteil von AMQP 1.0 besteht darin, dass Anwendungen aus Komponenten erstellt werden können, die in unterschiedlichen Sprachen geschrieben wurden. Weiterhin werden Nachrichten zuverlässig und sicher ausgetauscht.

Mithilfe der zuvor beschriebenen beispielhaften JMS-Anwendung und einer ähnlichen .NET-Anwendung aus einem separaten Artikel [Verwenden von Service Bus aus .NET mit AMQP 1.0](service-bus-amqp-dotnet.md) können Nachrichten zwischen .NET und Java ausgetauscht werden. Lesen Sie diesen Artikel, um weitere Informationen zum plattformübergreifenden Messaging mit Service Bus und AMQP 1.0 zu erhalten.

### <a name="jms-to-net"></a>Messaging von JMS nach .NET
So funktioniert das Messaging von JMS nach .NET:

* Starten Sie die .NET-Beispielanwendung ohne Befehlszeilenargumente.
* Starten Sie anschließend die beispielhafte Java-Anwendung mit dem Befehlszeilenargument "sendonly". Die Anwendung empfängt in diesem Modus keine Nachrichten aus der Warteschlange, sie sendet lediglich Nachrichten.
* Drücken Sie mehrmals die **EINGABETASTE** in der Java-Anwendungskonsole, sodass die Nachrichten gesendet werden.
* Diese Nachrichten werden anschließend von der .NET-Anwendung empfangen.

#### <a name="output-from-jms-application"></a>Ausgabe der JMS-Anwendung
```
> java SimpleSenderReceiver sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with JMSMessageID = ID:4364096528752411591
Sent message with JMSMessageID = ID:459252991689389983
Sent message with JMSMessageID = ID:1565011046230456854
exit
```

#### <a name="output-from-net-application"></a>Ausgabe der .NET-Anwendung
```
> SimpleSenderReceiver.exe    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with MessageID = 4364096528752411591
Received message with MessageID = 459252991689389983
Received message with MessageID = 1565011046230456854
exit
```

### <a name="net-to-jms"></a>Messaging von .NET nach JMS
So funktioniert die Nachrichtenübermittlung von .NET nach JMS:

* Starten Sie die .NET-Beispielanwendung mit dem Befehlszeilenargument "sendonly". Die Anwendung empfängt in diesem Modus keine Nachrichten aus der Warteschlange, sie sendet lediglich Nachrichten.
* Starten Sie die beispielhafte Java-Anwendung ohne Befehlszeilenargumente.
* Drücken Sie mehrmals die **EINGABETASTE** in der .NET-Anwendungskonsole, sodass die Nachrichten gesendet werden.
* Diese Nachrichten werden anschließend von der Java-Anwendung empfangen.

#### <a name="output-from-net-application"></a>Ausgabe der .NET-Anwendung
```
> SimpleSenderReceiver.exe sendonly
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Sent message with MessageID = d64e681a310a48a1ae0ce7b017bf1cf3    
Sent message with MessageID = 98a39664995b4f74b32e2a0ecccc46bb
Sent message with MessageID = acbca67f03c346de9b7893026f97ddeb
exit
```

#### <a name="output-from-jms-application"></a>Ausgabe der JMS-Anwendung
```
> java SimpleSenderReceiver    
Press [enter] to send a message. Type 'exit' + [enter] to quit.
Received message with JMSMessageID = ID:d64e681a310a48a1ae0ce7b017bf1cf3
Received message with JMSMessageID = ID:98a39664995b4f74b32e2a0ecccc46bb
Received message with JMSMessageID = ID:acbca67f03c346de9b7893026f97ddeb
exit
```

## <a name="unsupported-features-and-restrictions"></a>Nicht unterstützte Funktionen und Einschränkungen
Bei der Verwendung von JMS über AMQP 1.0 mit Service Bus gelten die folgenden Einschränkungen:

* Pro **Sitzung** ist nur ein **MessageConsumer** oder **MessageProducer** erlaubt. Falls Sie mehrere **MessageProducer** oder **MessageConsumer** in einer Anwendung benötigen, müssen Sie für diese jeweils eine eigene **Sitzung** erstellen.
* Flüchtige Themenabonnements werden momentan nicht unterstützt.
* **MessageSelectors** werden momentan nicht unterstützt.
* Temporäre Ziele (z.B. **TemporaryQueue** und **TemporaryTopic**) werden ebenso wie die **QueueRequestor**- und **TopicRequestor**-APIs, die diese verwenden, nicht unterstützt.
* Durchgeführte Sitzungen und verteilte Transaktionen werden nicht unterstützt.

## <a name="summary"></a>Zusammenfassung
In diesem Leitfaden wurde gezeigt, wie die gebrokerten Messagingfunktionen von Service Bus (Warteschlange und Themen veröffentlichen/abonnieren) aus Java-Anwendungen mit der beliebten Standard-Programmierschnittstelle JMS und AMQP 1.0 verwendet werden.

Sie können Service Bus AMQP 1.0 auch mit anderen Sprachen verwenden, unter anderem .NET, C, Python und PHP. Komponenten, die mit diesen verschiedenen Sprachen geschrieben wurden, können mit der AMQP 1.0-Unterstützung in Service Bus Nachrichten zuverlässig und bei voller Vertraulichkeit austauschen.

## <a name="next-steps"></a>Nächste Schritte
* [AMQP 1.0-Unterstützung in Azure Service Bus](service-bus-amqp-overview.md)
* [Verwenden von AMQP 1.0 mit der .NET-Programmierschnittstelle für Service Bus](service-bus-dotnet-advanced-message-queuing.md)
* [Entwicklerhandbuch für Service Bus AMQP 1.0](service-bus-amqp-dotnet.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)
* [Java Developer Center](https://azure.microsoft.com/develop/java/)

