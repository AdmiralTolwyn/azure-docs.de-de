---
title: Verbinden von Excel über den Hive ODBC-Treiber mit Hadoop – Azure HDInsight
description: Erfahren Sie, wie Sie den Microsoft Hive ODBC-Treiber für Excel einrichten und zum Abfragen von Daten in HDInsight-Clustern von Microsoft Excel verwenden können.
keywords: Hadoop, Excel, Hive Excel, Hive ODBC
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 4153504e7d0fb6dff4b8a675b301f54fb3588e46
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39590866"
---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Die Big Data-Lösung von Microsoft integriert Microsoft Business Intelligence (BI)-Komponenten in Apache Hadoop-Cluster, die von Azure HDInsight bereitgestellt wurden. Ein Beispiel für diese Integration ist die Möglichkeit, Excel mit dem Hive-Data Warehouse eines Hadoop-Clusters in HDInsight über den Microsoft Hive Open Database Connectivity (ODBC) Driver zu verbinden.

Es ist ebenfalls möglich, die zu einem HDInsight-Cluster gehörigen Daten und andere Datenquellen, einschließlich anderer Hadoop-Cluster (nicht aus HDInsight), zu verbinden. Hierbei verwenden Sie in Excel das Add-In Microsoft Power Query für Excel. Informationen zur Installation und Verwendung von Power Query finden Sie unter [Verbinden von Excel mit HDInsight über Power Query][hdinsight-power-query].



**Voraussetzungen**:

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Einen HDInsight-Cluster**. Hinweise zum Erstellen finden Sie unter [Erste Schritte mit Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Eine Arbeitsstation** mit Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Installieren des Microsoft Hive ODBC-Treibers
Laden Sie aus dem [Download Center][hive-odbc-driver-download] den Microsoft Hive ODBC-Treiber herunter, und installieren Sie ihn.

Dieser Treiber kann unter 32- oder 64-Bit-Versionen von Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 und Windows Server 2012 installiert werden. Der Treiber ermöglicht das Herstellen einer Verbindung mit Azure HDInsight. Installieren Sie die Version, die der Version der Anwendung entspricht, in der Sie den ODBC-Treiber verwenden. In diesem Lernprogramm wird der Treiber aus Office Excel verwendet.

## <a name="create-hive-odbc-data-source"></a>Erstellen einer Hive ODBC-Datenquelle
Die folgenden Schritte zeigen Ihnen, wie Sie eine Hive-ODBC-Datenquelle erstellen können.

1. Drücken Sie unter Windows 8 oder Windows 10 die Windows-Taste, um den Startbildschirm zu öffnen, und geben Sie dann **Datenquellen**ein.
2. Klicken Sie auf **ODBC-Datenquellen einrichten (32-Bit)** oder **ODBC-Datenquellen einrichten (64-Bit)**, je nach verwendeter Office-Version. Wenn Sie Windows 7 verwenden, wählen Sie die **ODBC-Datenquellen (32-Bit)** oder **ODBC-Datenquellen (64-Bit)** unter **Verwaltungstools** aus. Hierdurch wird das Dialogfeld **ODBC-Datenquellen-Administrator** aufgerufen.
   
    ![ODBC-Datenquellen-Administrator](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Konfigurieren eines DSN mithilfe des ODBC-Datenquellen-Administrators")

3. Klicken Sie aus Benutzer-DNS auf **Hinzufügen**, um den Assistenten **Neue Datenquelle erstellen** zu öffnen.
4. Wählen Sie **Microsoft Hive ODBC-Treiber**, und klicken Sie dann auf **Fertig stellen**. Hierdurch wird das DNS-Setup-Dialogfeld **Microsoft Hive ODBC-Treiber** geöffnet.
5. Geben Sie folgende Werte ein bzw. wählen diese aus:
   
   | Eigenschaft | BESCHREIBUNG |
   | --- | --- |
   |  Name der Datenquelle |Geben Sie einen Namen für die Datenquelle an. |
   |  Host |Geben Sie „&lt;HDInsightClusterName>.azurehdinsight.net“ ein. Beispiel: myHDICluster.azurehdinsight.net |
   |  Port |Verwenden Sie <strong>443</strong>. (Dieser Port wurde von 563 in 443 geändert.) |
   |  Datenbank |Verwenden Sie <strong>Standard</strong>. |
   |  Mechanismus |Wählen Sie <strong>Azure HDInsight Service</strong> aus. |
   |  Benutzername |Geben Sie Ihren HTTP-Benutzernamen für den HDInsight-Cluster an. Der Standard-Benutzername lautet <strong>admin</strong>. |
   |  Password |Geben Sie Ihr Benutzerkennwort für den HDInsight-Cluster an. |
   
    </table>
   
    Sie müssen einige wichtige Parameter berücksichtigen, wenn Sie auf **Erweiterte Optionen**klicken:
   
   | Parameter | BESCHREIBUNG |
   | --- | --- |
   |  Use Native Query |Wenn diese Option ausgewählt ist, versucht der ODBC-Treiber NICHT, TSQL in HiveQL zu konvertieren. Verwenden Sie diese Option nur, wenn Sie sich absolut sicher sind, dass Sie reine HiveQL-Anweisungen absenden. Wenn Sie eine Verbindung zu SQL Server oder Azure SQL Database herstellen, sollten Sie die Option nicht aktivieren. |
   |  Rows fetched per block |Wenn Sie viele Datensätze abrufen, ist es möglicherweise erforderlich, diesen Parameter zu optimieren, um optimale Leistung zu garantieren. |
   |  Default string column length, Binary column length, Decimal column scale |Längen und Genauigkeiten der Datentypen können beeinflussen, wie die Daten zurückgegeben werden. Bei zu geringer Genauigkeit und/oder Abschneiden werden falsche Informationen zurückgegeben. |

    ![Erweiterte Optionen](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Erweiterte DSN-Konfigurationsoptionen")

1. Klicken Sie auf **Test** , um die Datenquelle zu testen. Wenn die Datenquelle korrekt konfiguriert wurde, wird *TESTS ERFOLGREICH ABGESCHLOSSEN.* angezeigt.
2. Klicken Sie auf **OK** , um den Testdialog zu schließen. Die neue Datenquelle wird im **ODBC-Datenquellen-Administrator** aufgeführt.
3. Klicken Sie auf **OK** , um den Assistenten zu beenden.

## <a name="import-data-into-excel-from-hdinsight"></a>Importieren von Daten aus HDInsight in Excel
In den folgenden Schritten wird beschrieben, wie Sie mithilfe der ODBC-Datenquelle, die Sie im vorangegangenen Abschnitt erstellt haben, Daten aus einer Hive-Tabelle in eine Excel-Arbeitsmappe importieren.

1. Öffnen Sie eine neue oder bereits vorhandene Arbeitsmappe in Excel.
2. Klicken Sie auf der Registerkarte **Daten** auf **Daten abrufen**, anschließend auf **Aus anderen Quellen** und dann auf **Aus ODBC**, um den **Datenverbindungs-Assistenten** zu starten.
   
    ![Öffnen des Datenverbindungs-Assistenten](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Öffnen des Datenverbindungs-Assistenten")
4. Wählen Sie den im letzten Abschnitt erstellten Datenquellennamen aus, und klicken Sie dann auf **OK**.
5. Geben Sie den Hadoop-Benutzernamen (der Standardname lautet „admin“) und das zugehörige Kennwort ein, und klicken Sie dann auf **Verbinden**.
6. Erweitern Sie im Navigator die Option **HIVE**, erweitern Sie **Standard**, klicken Sie auf **hivesampletable**, und klicken Sie dann auf **Laden**. Es dauert einige Zeit, bis die Daten in Excel importiert werden.

    ![HDInsight-Hive-ODBC-Navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "Öffnen des Datenverbindungs-Assistenten")


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie den Microsoft Hive ODBC-Treiber verwenden, um Daten aus dem HDInsight-Dienst nach Excel zu übertragen. Ebenso können Sie Daten aus dem HDInsight-Dienst in eine SQL-Datenbank übertragen. Es ist außerdem möglich, Daten in einen HDInsight-Dienst hochzuladen. Weitere Informationen finden Sie unter:

* [Visualize Hive data with Microsoft Power BI in Azure HDInsight (Visualisieren von Hive-Daten mit Microsoft Power BI in Azure HDInsight)](apache-hadoop-connect-hive-power-bi.md).
* [Visualisieren von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Use Zeppelin to run Hive queries in Azure HDInsight (Verwenden von Zeppelin zum Ausführen von Hive-Abfragen in Azure HDInsight)](./../hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Hadoop mithilfe von Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connect to Azure HDInsight and run Hive queries using Data Lake Tools for Visual Studio (Verbinden mit Azure HDInsight und Ausführen von Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio)](apache-hadoop-visual-studio-tools-get-started.md).
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Upload data to HDInsight (Hochladen von Daten in HDInsight)](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698


