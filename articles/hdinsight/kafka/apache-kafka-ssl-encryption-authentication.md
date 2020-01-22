---
title: SSL-Verschlüsselung und -Authentifizierung für Apache Kafka – Azure HDInsight
description: Einrichten von SSL-Verschlüsselung für die Kommunikation zwischen Kafka-Clients und Kafka-Brokern sowie zwischen Kafka-Brokern. Einrichten von SSL-Authentifizierung von Clients.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 180b7c203755553c343e0f7fc65c93092b330124
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751325"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Einrichten von Secure Sockets Layer-Verschlüsselung (SSL) und -Authentifizierung für Apache Kafka in Azure HDInsight

In diesem Artikel erfahren Sie, wie Sie SSL-Verschlüsselung zwischen Apache Kafka-Clients und Apache Kafka-Brokern einrichten. Außerdem zeigt Ihnen der Artikel, wie Sie die Authentifizierung von Clients einrichten (auch als bidirektionaler SSL bezeichnet).

> [!Important]
> Es gibt zwei Clients, die Sie für Kafka-Anwendungen verwenden können: einen Java-Client und einen Konsolenclient. Nur der Java-Client `ProducerConsumer.java` kann SSL sowohl zum Erzeugen als auch nutzen verwenden. Der Konsolenproducerclient `console-producer.sh` funktioniert nicht mit SSL.

## <a name="apache-kafka-broker-setup"></a>Einrichtung des Apache Kafka-Brokers

Das Kafka-SSL-Brokersetup verwendet vier HDInsight-Cluster-VMs auf folgende Weise:

* Hauptknoten 0 – Zertifizierungsstelle (Certificate Authority, CA)
* Workerknoten 0, 1 und 2 – Broker

> [!Note] 
> In dieser Anleitung werden selbstsignierte Zertifikate verwendet, aber die sicherste Lösung ist die Verwendung von Zertifikaten, die von vertrauenswürdigen Zertifizierungsstellen ausgestellt wurden.

Die Zusammenfassung des Brokereinrichtungsvorgangs lautet wie folgt:

1. Die folgenden Schritte werden auf jedem der drei Workerknoten wiederholt:

    1. Generieren eines Zertifikats.
    1. Erstellen einer Anforderung zur Zertifikatsignierung.
    1. Senden der Anforderung zur Zertifikatsignierung an die Zertifizierungsstelle (ZS).
    1. Anmelden bei der Zertifizierungsstelle und Signieren des Zertifikats.
    1. Zurücksenden des signierten Zertifikats mit SCP auf den Workerknoten.
    1. Senden des öffentlichen Zertifikats der ZS mit SCP an den Workerknoten.

1. Sobald Sie über alle Zertifikate verfügen, legen Sie die Zertifikate im Zertifikatspeicher ab.
1. Navigieren Sie zu Ambari, und ändern Sie die Konfigurationen.

Schließen Sie die Brokereinrichtung mit folgenden detaillierten Anweisungen ab:

> [!Important]
> In den folgenden Codeausschnitten ist wnX eine Abkürzung für einen der drei Workerknoten und sollte entsprechend durch `wn0`, `wn1` oder `wn2` ersetzt werden. `WorkerNode0_Name` und `HeadNode0_Name` sollten durch die Namen der entsprechenden Computer ersetzt werden.

1. Führen Sie das anfängliche Setup auf Hauptknoten 0 durch, der für HDInsight die Rolle der Zertifizierungsstelle (ZS) übernimmt.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Führen Sie das gleiche anfängliche Setup für jeden der Broker durch (Workerknoten 0, 1 und 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. Führen Sie auf allen Workerknoten die folgenden Schritte mit dem folgenden Codeausschnitt aus.
    1. Erstellen Sie einen Keystore, und füllen Sie ihn mit einem neuen privaten Zertifikat.
    1. Erstellen Sie eine Anforderung zur Zertifikatsignierung.
    1. Senden Sie die Anforderung zur Zertifikatsignierung mit SCP an die Zertifizierungsstelle (headnode0).

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Führen Sie auf dem ZS-Computer den folgenden Befehl aus, um die Dateien „ca-cert“ und „ca-key“ zu erstellen:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. Wechseln Sie zum ZS-Computer, und lassen Sie alle empfangenen Anforderungen zur Zertifikatsignierung ausführen:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Senden Sie die signierten Zertifikate von der ZS (headnode0) zurück an die Workerknoten.

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Senden Sie das öffentliche Zertifikat der ZS an die einzelnen Workerknoten.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Fügen Sie auf jedem Workerknoten das öffentliche Zertifikat der ZS dem Truststore und dem Keystore hinzu. Fügen Sie dann das eigene signierte Zertifikat des Workerknotens dem Keystore hinzu.

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Aktualisieren der Kafka-Konfiguration zur Verwendung von SSL und Neustarten der Broker

Sie haben jetzt jeden Kafka-Broker mit einem Keystore und Truststore eingerichtet sowie die richtigen Zertifikate importiert. Ändern Sie nun die zugehörigen Kafka-Konfigurationseigenschaften mit Ambari, und starten Sie die Kafka-Broker dann neu.

Führen Sie die folgenden Schritte aus, um die Konfigurationsänderung abzuschließen:

1. Melden Sie sich im Azure-Portal an, und wählen Sie Ihren Azure HDInsight Apache Kafka-Cluster aus.
1. Wechseln Sie in die Ambari-Benutzeroberfläche, indem Sie unter **Clusterdashboards** auf **Ambari Home** klicken.
1. Legen Sie unter **Kafka-Broker** die Eigenschaft **Listener** auf `PLAINTEXT://localhost:9092,SSL://localhost:9093` fest.
1. Legen Sie unter **Erweiterter Kafka-Broker** die Eigenschaft **security.inter.broker.protocol** auf `SSL` fest.

    ![Bearbeiten von Kafka-SSL-Konfigurationseigenschaften in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Legen Sie unter **Benutzerdefinierter Kafka-Broker** die Eigenschaft **ssl.client.auth** auf `required` fest. Dieser Schritt ist nur erforderlich, wenn Sie sowohl die Authentifizierung als auch die Verschlüsselung einrichten.

    ![Bearbeiten von Kafka-SSL-Konfigurationseigenschaften in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Fügen Sie unter **Advanced kafka-env** am Ende der Eigenschaft **kafka-env template** die folgenden Zeilen hinzu.

    ```config
    # Needed to configure IP address advertising
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=MyServerPassword123
    ssl.key.password=MyServerPassword123
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=MyServerPassword123
    ```

    ![Bearbeiten der Eigenschaft „kafka-env template“ in Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

1. Starten Sie alle Kafka-Broker neu.
1. Starten Sie den Adminclient mit Producer- und Consumer Optionen, um sicherzustellen, dass sowohl Producer als auch Consumer and Port 9093 funktionieren.

## <a name="client-setup-with-authentication"></a>Einrichten des Clients (mit Authentifizierung)

> [!Note]
> Die folgenden Schritte sind nur erforderlich, wenn Sie SSL-Verschlüsselung **und** Authentifizierung einrichten. Wenn Sie nur Verschlüsselung einrichten, wechseln Sie zu [Einrichten des Clients (ohne Authentifizierung)](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication).

Führen Sie zum Abschließen des Clientsetups die folgenden Schritte aus:

1. Melden Sie sich beim Clientcomputer (Standbyhauptknoten) an.
1. Erstellen Sie einen Java-Keystore, und rufen Sie ein signiertes Zertifikat für den Broker ab. Kopieren Sie dann das Zertifikat auf den virtuellen Computer, auf dem die Zertifizierungsstelle ausgeführt wird.
1. Wechseln Sie zum Zertifizierungsstellencomputer (aktiver Hauptknoten), um das Clientzertifikat zu signieren.
1. Wechseln Sie zum Clientcomputer (Standbyhauptknoten), und navigieren Sie zum Ordner `~/ssl`. Kopieren Sie das signierte Zertifikat auf den Clientcomputer.

```bash
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123"

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (active head node) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:MyServerPassword123

# Return to the client machine (standby head node), navigate to ~/ssl folder and copy signed cert from the CA (active head node) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Zeigen Sie schließlich die Datei `client-ssl-auth.properties` mit dem Befehl `cat client-ssl-auth.properties` an. Die Datei sollte folgende Zeilen enthalten:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=MyClientPassword123
ssl.key.password=MyClientPassword123
```

## <a name="client-setup-without-authentication"></a>Einrichten des Clients (ohne Authentifizierung)

Wenn Sie keine Authentifizierung benötigen, dienen folgende Schritte zum ausschließlichen Einrichten der SSL-Verschlüsselung:

1. Melden Sie sich beim Clientcomputer (hn1) an, und navigieren Sie zum Ordner `~/ssl`.
1. Kopieren Sie das signierte Zertifikat vom Computer der Zertifizierungsstelle (wn0) auf den Clientcomputer.
1. Importieren Sie das Zertifizierungsstellenzertifikat in den Truststore.
1. Importieren Sie das Zertifizierungsstellenzertifikat in den Keystore.

Diese Schritte werden im folgenden Codeausschnitt gezeigt.

```bash
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
```

Zeigen Sie schließlich die Datei `client-ssl-auth.properties` mit dem Befehl `cat client-ssl-auth.properties` an. Die Datei sollte folgende Zeilen enthalten:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=MyClientPassword123
```

## <a name="next-steps"></a>Nächste Schritte

* [Was ist Apache Kafka in HDInsight?](apache-kafka-introduction.md)
